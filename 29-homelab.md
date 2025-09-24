---
title: Day 29 HomeLab - 
description: Placeholder
date: 2025-09-23
categories: []
author: moses
tags: []
hideToc: true
---

# Vertical Farming

Got the rotating top now... still have probably a day or two of printing before this is done.

Tried to mention yesterday that I found a youtube channel about making RC construction vehicles and now I want to explore automating that process...

They showed a sandwich making process with them and I want to feed the homeless again with robot made sandwiches this time...

I also got a small mechatronics kit to make it easier to prototye different types of robots quickly.

# Rosalynn Carter (Rosalynn Wheels For Change)

I have been thinking about an idea a friend gave me for making hemp carts for the homeless that have certain features and I would like to explore that more again.

Need to contact hemp growers and see if I can get a bunch of stims so I can make plastics with them...

# DIY Smart Glasses Meta Glasses

This is a cool and interesting repo on Github. I decided to make some out of cardboard basically since the printer is still going.

- [Cool Repo on Github](https://github.com/BasedHardware/omi/tree/main/omiGlass)

That way I can move the plant assistant to the glasses and experiment with taking photos of the packaging and having it help.

V0 is just a combination of:

1. Raspberry Pi 0
2. Google AI Vision Bonnet (Didn't use this as its old and I'll recycle it into another project)
3. Clear OLED scren
4. Camera

## Nuttall AI

Upgrading the Moses Grows wordpress plant plugin to include AI generated details on how to take care of plants which will help the system learn.

# Sage Executor (Docker + FastAPI)

Another cool little project inspired by a new follower on Gitub and my love for calculators and the Reimann Hypothsis.

A minimal, sandboxed HTTP API that executes SageMath code inside a container and returns stdout/stderr and generated files. Uses FastAPI and `subprocess` to invoke `sage -c` with resource/time limits.

> ⚠️ This is an MVP for a single-tenant container (one user per container). For multi-user isolation, run **one container per user/session** behind a gateway.

---

## Project Layout

```
.
├── Dockerfile
├── requirements.txt
├── entrypoint.sh
└── app/
    ├── main.py
    └── sandbox.py
```

---

## Dockerfile

```dockerfile
# Use the official SageMath image (includes CPython, Sage, and toolchain)
FROM sagemath/sagemath:latest

# Create an unprivileged user
ARG USER=sageuser
ARG UID=10001
RUN useradd -m -u ${UID} -s /bin/bash ${USER}

# Workdir
WORKDIR /srv

# System deps for sandboxing and timeouts
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      python3-pip \
      python3-venv \
      ca-certificates \
      tini \
      time \
      util-linux \
    && rm -rf /var/lib/apt/lists/*

# Python deps
COPY requirements.txt ./
RUN pip3 install --no-cache-dir -r requirements.txt

# App source
COPY app ./app
COPY entrypoint.sh ./
RUN chmod +x entrypoint.sh && chown -R ${USER}:${USER} /srv

# Drop privileges
USER ${USER}

# Environment
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    SAGE_NUM_THREADS=1 \
    SAGE_DISABLE_JUPYTER=yes \
    APP_PORT=8000

# Expose API port
EXPOSE 8000

# Use tini as PID 1, then our entrypoint
ENTRYPOINT ["/usr/bin/tini", "--", "/srv/entrypoint.sh"]
```

---

## requirements.txt

```txt
fastapi==0.115.0
uvicorn[standard]==0.30.6
pydantic==2.9.2
python-multipart==0.0.9
```

---

## entrypoint.sh

```bash
#!/usr/bin/env bash
set -euo pipefail

# Optional: pre-warm Sage to speed up first request
sage -c "print('Sage warmup complete')" >/dev/null 2>&1 || true

exec uvicorn app.main:app \
  --host 0.0.0.0 \
  --port "${APP_PORT:-8000}" \
  --no-server-header
```

---

## app/sandbox.py

A tiny helper to run Sage with time/memory limits and artifact collection.

```python
import os
import shutil
import subprocess
import tempfile
from pathlib import Path
from typing import Tuple, List

# Hard resource limits (coarse). Fine-grained cgroup limits should be set at the container level.
DEFAULT_TIMEOUT = int(os.getenv("SAGE_TIMEOUT_SEC", "15"))  # seconds
MAX_STD_BYTES = int(os.getenv("SAGE_MAX_STD_BYTES", str(2 * 1024 * 1024)))  # 2MB

# Whitelist of files we allow returning by pattern
ALLOWED_EXTS = {".png", ".svg", ".pdf", ".txt", ".csv", ".json"}


def run_sage(code: str, timeout: int = DEFAULT_TIMEOUT) -> Tuple[int, str, str, List[Path]]:
    """Run a single Sage command string using `sage -c` in a temp dir.

    Returns (exit_code, stdout, stderr, artifacts)
    """
    workdir = Path(tempfile.mkdtemp(prefix="sageexec_"))

    # Write code to a file to avoid shell quoting issues
    code_file = workdir / "snippet.sage"
    code_file.write_text(code, encoding="utf-8")

    # We'll call: sage -c 'load("snippet.sage")'
    cmd = [
        "bash", "-lc",
        # prlimit adds soft caps; `timeout` kills runaway jobs
        f"ulimit -Sn 1024; "
        f"timeout --signal=KILL {int(timeout)}s "
        f"sage -c 'load(\"{code_file.name}\")'"
    ]

    try:
        proc = subprocess.run(
            cmd,
            cwd=str(workdir),
            capture_output=True,
            text=False,  # capture as bytes to enforce MAX_STD_BYTES safely
            check=False,
        )
        stdout = proc.stdout[:MAX_STD_BYTES].decode("utf-8", errors="replace")
        stderr = proc.stderr[:MAX_STD_BYTES].decode("utf-8", errors="replace")
        rc = proc.returncode
    except subprocess.TimeoutExpired:
        rc, stdout, stderr = 124, "", "Execution timed out"

    # Collect artifacts
    artifacts: List[Path] = []
    for p in workdir.rglob("*"):
        if p.is_file() and p.suffix.lower() in ALLOWED_EXTS and p.name != code_file.name:
            artifacts.append(p)

    return rc, stdout, stderr, artifacts
```

---

## app/main.py

```python
from fastapi import FastAPI, UploadFile, Form
from fastapi import HTTPException
from fastapi.responses import JSONResponse, FileResponse
from pydantic import BaseModel
from typing import List, Optional
from pathlib import Path
import uuid
import os

from .sandbox import run_sage

app = FastAPI(title="Sage Executor", version="0.1.0")


class ExecRequest(BaseModel):
    code: str
    timeout_sec: Optional[int] = None


class ExecResult(BaseModel):
    run_id: str
    exit_code: int
    stdout: str
    stderr: str
    files: List[str]


# Simple in-memory map from run_id to temp dir path of artifacts
RUNS: dict[str, Path] = {}


@app.get("/healthz")
async def healthz():
    return {"ok": True}


@app.post("/v1/execute", response_model=ExecResult)
async def execute(req: ExecRequest):
    if not req.code or len(req.code) > 200_000:
        raise HTTPException(status_code=400, detail="code too large or empty")

    # Execute
    rc, stdout, stderr, artifacts = run_sage(req.code, req.timeout_sec or 15)

    # Persist artifact dir for later download via /v1/files/{run_id}/{name}
    run_id = str(uuid.uuid4())
    if artifacts:
        # All artifacts live in the same parent temp dir
        RUNS[run_id] = artifacts[0].parent
    else:
        # No artifacts, but we still record an empty dir marker
        RUNS[run_id] = Path.cwd()  # harmless placeholder; no files will be found

    files = [a.name for a in artifacts]
    return ExecResult(run_id=run_id, exit_code=rc, stdout=stdout, stderr=stderr, files=files)


@app.get("/v1/files/{run_id}/{filename}")
async def get_file(run_id: str, filename: str):
    base = RUNS.get(run_id)
    if base is None or not isinstance(base, Path):
        raise HTTPException(status_code=404, detail="run_id not found")

    path = base / filename
    if not path.exists():
        raise HTTPException(status_code=404, detail="file not found")

    # Security: only allow whitelisted extensions
    if path.suffix.lower() not in {".png", ".svg", ".pdf", ".txt", ".csv", ".json"}:
        raise HTTPException(status_code=403, detail="forbidden file type")

    return FileResponse(str(path))
```

---

## Example Usage

### Build

```bash
docker build -t sage-executor:latest .
```

### Run (single container)

```bash
docker run --rm -p 8000:8000 \
  --cpus=1.0 --memory=2g --read-only \
  --name sageexec sage-executor:latest
```

> Tip: add a writable tmpfs when using `--read-only`:

```bash
docker run --rm -p 8000:8000 \
  --cpus=1.0 --memory=2g --read-only \
  --tmpfs /tmp:rw,size=512m \
  --name sageexec sage-executor:latest
```

### Test a simple call

```bash
curl -s -X POST http://localhost:8000/v1/execute \
  -H 'Content-Type: application/json' \
  -d '{"code":"print(factor(2^64-1))"}' | jq
```

### Generate a plot and download it

```bash
# 1) Execute code that writes a file
curl -s -X POST http://localhost:8000/v1/execute \
  -H 'Content-Type: application/json' \
  -d '{"code":"p=plot(sin(x),(x,0,10)); p.save(\"sine.png\"); print(\"done\")"}'

# Response includes run_id and files: ["sine.png"]
# 2) Download artifact
curl -O http://localhost:8000/v1/files/<run_id>/sine.png
```

---

## Security & Scaling Notes

* **One container per user** for real multi-tenancy. This code isolates *processes* with timeouts, but file system is shared within the container.
* Add **cgroup limits** via Docker/K8s (CPU, RAM, pids), and consider **seccomp**/**AppArmor** profiles.
* Deny outbound network egress at the container level if you need strict offline execution.
* Implement **idle culling** for containers and a small pool for fast cold-starts.
* For streaming outputs, extend to a **WebSocket** endpoint that tails the process stdout.

---

## Compatibility

* Tested against the moving `sagemath/sagemath:latest` base. For reproducibility, pin a specific tag (e.g., `sagemath/sagemath:10.4`) and mirror it.

---

## Next Steps (I can add on request)

* WebSocket `/stream` with incremental stdout/stderr events.
* Per-user persistent volume mounted at `/home/sageuser/work`.
* Auth (JWT) + rate limiting.
* K8s manifests and a small session manager.

```
```

# Pip Boy

Soldered some component together and it turns on just fine but the 5V falls down to 4.9 and is not enough to power this pi.

Will fix this tomorrow with a new voltage booster.

# Edith Glasses (Meta / OMNI Clones)

Cut out some cardboard and soldered some stuff together to make some edith glasses that can disaply information on a hub near my face.
That way I can optimize my process for planting and engineering things in general.

There is a project called Omni glasses that has open source information there as well but I am still working on my vertical farm...

My cardboard clone is working great! The clear OLED runs kinda hot though... will need to look into some alternatives potentially...

# Finn Tech... No Pi Hawks we have Pi Eagles... In America!!!!

There are several research projects I am interested in involving drones. 

# Momo Bot

Small bipedal robot I made in Tinker CAD using Lewan Soul daisy chained servos... More coming soon.

# Gremlin, Et Tu AI / BruTay AI, Chaos Engineering

I was watching a documentary on Dupont and how the whole city teamed up to try to kill a guy after they poisoned his cows...
The town was vastly improved by the chemical company Dupont... 
they viewed the lawsuit as a threat to their style of living and so they all teamed up to shun and mock the man...
they tried to exile and kill him. Luckily one lawyer saved the day...

This has been similar to my experience with living in the PNW... Amazon moved me out here and devalued me from a senior developer to a a dev II....
Then I did the work of five people, got exceeds expectations, and my boss told me I was gonna get promoted within a year...
Then COVID and a family emergency... take medical leave as required... come back to a PIP...
I ask what do I need to do to get out of the PIP... Oh "you're a DEV II when we feel like you are again..."
Turn in several projects and do more than the other DEV IIs at the time... my boss gets a promotion to manager III and then I get fired by her...
After getting another good performance review...

Then they steal my personal belongings... told me I could return to work in April... then said I had to have a DR note... etc...
HR told me I was eligible for rehire but then a year later when I'm contractor at Whole Foods... My boss finds out he cant hire me because my chinese boss at IMDb had me black listed... and I get let go again...

so after thinking about my experience and the documentary... I pondered what robot would represent the PNW the most...

I'm proud to present BruTay AI... a gremelin type tool that adapts to any system...

E.G. You own a recyclying plant. You want to test a new lock system... it does research and escalates different approaches until it can break in.
You want to test a new vision AI alg on the trash picker... it studies the code and learning data then throws in different stuff at different rates to try to break it or overwhelm it... Its main goal is to produce black swan events...

I have also programmed it to be super friendly!!! It will smile and be super polite while it tries to stab you in the back... if that's what you want... useful for training solders to watch their back. 
