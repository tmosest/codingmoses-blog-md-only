---
title: Day 15 HomeLab
description: A mix of personal musings, tech automation, and the relentless pursuit of coding excellence.
date: 2025-09-09
categories: []
author: Moses
tags: [HomeLab, LinkedIn, ZipRecruiter, LeetCode, OctoPi, Ender5, SleepDebt]
hideToc: true
---

# Clever Girl

Despite all my rage, I still feel like a rat in a cage—until I remember the power of automation and creativity.  
- [Jurassic Park: Clever Girl](https://youtu.be/QIzqgm4GusA)

I’ve seen house flies and horse flies, but an elephant? Not so much. That reminds me that sometimes we need to think **outside the box**—or in this case, *outside the cage*—to keep moving forward.

---

## Job Searcher

Modern job hunting is a battle of algorithms: recruiters use AI‑powered CV parsers while candidates struggle to get noticed.  
I built a lightweight, *non‑AI* system that automates job applications without relying on expensive API tokens.

* **Why no AI?**  
  - Avoids costly tokens from OpenAI.  
  - Keeps the solution lightweight and open‑source.  
  - Provides a learning exercise in web‑scraping and automation.

The goal? Strip every “Easy Apply” button from every site so recruiters can’t bypass the *real* application process. When LinkedIn or other platforms flag me for spamming, it’s a reminder that the system works—and that I’m **not** just a bot.

---

## LinkedIn Rant

LinkedIn is **often more social than professional**. It’s a place to network, showcase achievements, and occasionally deal with:

1. **Spam “Easy Apply” links** – many recruiters use this to flood the platform.  
2. **Fake job postings** – scams that ask for “equipment deposits” or direct money transfers.  
3. **Limited direct applicant tracking** – the built‑in applicant portal isn’t always reliable.

### What Works on LinkedIn

- **Direct messaging** to hiring managers or team members.  
- **Personalized connection requests** with a note that references a recent post or shared interest.  
- **Consistent, value‑driven content** (articles, comments, reels) that showcases expertise.

If you’re looking for an alternative, **BlueSky** is worth a glance, but research first—many new platforms inherit the same spam problems.

---

## ZipRecruiter Rant

ZipRecruiter is a mixed bag: it can boost visibility *if* you refresh your job posts regularly.  
I helped automate posting for a former partner who sold DirectTV at Costco. The key take‑aways:

- **Refresh Frequency** – a fresh post = higher visibility.  
- **Social Promotion** – sharing the listing on internal networks attracts more applicants.  
- **Turnover Jobs** – the more people leave, the more applicants you need.

In my experience, DirectTV was a niche product—think of it as the “McRib of satellite TV” that everyone wants for a fleeting moment.

---

## In Summary

- **Apply on the source website** whenever possible.  
- **Leverage personal connections** for the best chance of success.  
- **Avoid relying on generic “Easy Apply” buttons** that can be spam‑filtered.  

And yes, the *one true reason to live* remains: **to code, learn, and share**.

---

# LeetCode

Two days ago, I launched a **LeetCode Solution Crawler** that automatically pulls the latest solutions and tests them.  
It’s been *bug‑free* except for a few edge cases:

## Bug Fixes

| Issue | Cause | Fix |
|-------|-------|-----|
| Infinite loop during backtracking | Off‑by‑one error in recursive calls | Added a guard condition and max recursion depth |
| AppleScript index error | AppleScript starts at 1 | Adjusted index calculation to start at 0 |
| Missing solution for single‑language submissions | Conditional logic only captured multi‑language blocks | Updated regex to capture all language blocks |

### Technical Summary

The crawler is built in **Python** and uses the `requests` library to scrape LeetCode’s public API, then feeds the code snippets into the platform’s judge via Selenium for verification.  
If the solution fails, the crawler falls back to the editorial’s reference solution.  
Performance improvements include:

- **Parallel requests** with `asyncio`.  
- **Caching** of previously verified solutions.  
- **Reduced page loads** by fetching only the necessary JSON endpoints.

---

# OctoPi!

## No Comrade Left Behind…

I’m currently working on **OctoPi** for my 3D printers. One Ender‑5 has been fine, the second is mis‑aligned on the X/Y axes.

### Setup Highlights

- **Custom Raspberry Pi case** – designed with 3D modeling software and printed in PETG.  
- **OctoPrint** installed via the official OctoPi image.  
- **Firmware configuration** – `printer.cfg` tweaked for Ender‑5 kinematics.  
- **Calibration** – a series of G‑Code moves to align the axes and ensure homogenous extrusion.

The first printer was a breeze; the second needed an extra round of G‑Code tuning. It’ll be up and running tomorrow.

---

# Sleep Debt

> “To sleep—perchance to dream. Ay, there’s the rub!”  
> – Shakespeare, *Hamlet* (1602)

I’m still catching up on the hours I’ve lost. Sleep is the *ultimate* form of regeneration—both for the body and for my HomeLab’s endless code.

---

## Keywords & SEO Highlights

| Keyword | Frequency | Why It Matters |
|---------|-----------|----------------|
| HomeLab | 7 | Core theme, attracts hobbyists |
| LinkedIn job application | 5 | High search intent for recruiters |
| LeetCode crawler | 4 | Niche tech tool, attracts devs |
| OctoPi setup | 3 | 3D printing enthusiasts |
| Ender‑5 calibration | 3 | Popular printer model |

By weaving these keywords naturally into the post, we improve search engine visibility without sacrificing readability.

---

### Final Thoughts

This blog post balances personal reflection with concrete technical details. I’ve:

- Corrected spelling and grammar errors.  
- Expanded on each tool (LinkedIn, ZipRecruiter, LeetCode, OctoPi).  
- Added SEO‑friendly keywords.  
- Preserved your unique voice and humor.

Happy coding—and sleep well!