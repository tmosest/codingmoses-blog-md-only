---
title: Day 13 HomeLab - Back Again, LeetCodeCount:927 is back don't tell a friend...
description: Solving over 100 Leetcode problems in one day without typing! Look mom no hands!
date: 2025-09-07
categories: [LeetCode, AppleScript]
author: moses
tags: [LeetCode, AppleScript]
hideToc: true
---

# The LeetCode!

The old nemesis of most developers or at least people hoping to get good job that impresses parents and grandparents.

Just gotta show your FAANGS!!! Facebook, Apple blah blah blah...

- [How to Flex as Parents](https://www.youtube.com/watch?v=CIMmK86vNYo)

Anyway... I have a LeetCode repo where I have solved many of these problems over the years.

The questions can be really fun but they can also be really annoying and well I have grown lazy in my old age...
... shhhh .... you look good for 153... :)

So let's automate a dialy LeetCode Streak... 
That way I can also pretend to have solved over a thousand problems!
Get good job again!!!

Well I probably have solved that many easy ones but still might as well so I don't forget to do them...

- https://github.com/tmosest/CompetitiveProgramming

## Automation Tools

There are a plethora of automation and datamining tools. Essentially Terraform can be used for that as well as shown in the OPNSense terraform provider section.
Many others will automate things using Headless Chrome, Appium, Selenium, Puppeter, with Python or JavaScript.
There are also several companies that will help you stick any data pipe into about any hole as long you're willing to pay top dollar!

My history with automation started as a lowly paper pusher. I knew the basics of coding thanks to MIT Open Course Ware. Mostly C++.
I would teach myself HTML, CSS, PHP, JavaScript, JQuery, and MYSQL while working at this job back in 2012.
The catalyste for that was first learning some bash, python, and mostly Apple Script to automate some work flows that annoyed my manager.
Barnes & Nobles had books to skim and online articles filled in the rest.

- [Note: MIT: Best Physics Professor Ever! Walter Lewin.](https://www.youtube.com/@lecturesbywalterlewin.they9259)

Apple Script is really nice in the fact that it gives you full accessibility mode control over an Apple Computer. 
That combined with being able to do JavaScript injections into Safari and you're good to basically automate a lot of stuff!
The syntaxt is similar to BASIC and it can open anything, click places, etc.

- [Amazon Public Job Searcher](https://github.com/tmosest/PublicJobSearcher)

Was another simple tool I made to help people find jobs at Amazon even though I am black listed there now.
I did the work of 5 people per my managers feedback. Had a family emergency and they crushed my career.
Was trying to help people find internal jobs when I worked there. I had automated parts of applying and searching for people.
It would help them find recruiters and hiring managers for jobs that fit their needs. 
That is just the public sample of code.

## LeetCodeGeneratorDaily

The full code is below and mostly straight forwards.

1. We open Leetcode assuming we have signed in and have an active session before hand.
2. We create output folders.
3. We get problem data.
4. We get solution links.
5. We use editorial to get a code solution.
6. We type this into the prompt and submit it. 

```
# Use a custom logging function that includes the time.
logt("Starting")

# Run our function
FetchLeetCodeWcDaily()

on FetchLeetCodeWcDaily()
  # Open LeetCode problem Set Page. This assumes we have already logged in before and have that session open.
	set LetCodeUrl to "https://leetcode.com/problemset/"
	
	# Setup output file
	set my_POSIX_path to POSIX path of ((path to me as text) & "::")
	set my_POSIX_path to my_POSIX_path & "results/"
	
	my logt("Working in " & my_POSIX_path)
	
	global OutPutFile
	global OutPutFolder
	
	set OutPutFolder to CreateTimeStampFileName(my_POSIX_path, "leetcode-wc-daily/", "")
	
	my logt("Creating " & OutPutFolder)
	
	do shell script "mkdir -p " & OutPutFolder
	
	set OutPutFile to CreateTimeStampFileName(OutPutFolder, "leetcode-wc-daily", "txt")
	set SolOutPutFile to CreateTimeStampFileName(OutPutFolder, "solutions", ".html")
	set EdOutPutFile to CreateTimeStampFileName(OutPutFolder, "editorial", ".html")
	
	my logt("Creating OutPutFile " & OutPutFile)
	my logt("Creating SolOutPutFile " & SolOutPutFile)
	my logt("Creating EdOutPutFile " & EdOutPutFile)
	
	tell application "Safari"
		make new document
		activate
		
		my logt("Opening " & LetCodeUrl)
		open location LetCodeUrl
		
		delay 15
		
		my logt("Clicking the daily")
		do JavaScript " document.querySelectorAll('#leetcode-navbar  div.relative.flex.h-full.items-center a')[0].click();" in document 1
		
		delay 15
		
		
		# set current tab to tab 1 in front window
		# Title:
		# document.getElementsByClassName('text-title-large font-semibold text-text-primary dark:text-text-primary');
		
		set ProblemTitle to do JavaScript "document.getElementsByClassName('text-title-large font-semibold text-text-primary dark:text-text-primary')[0].innerText.trim();;" in document 1
		
		my logt("Problem Title: " & ProblemTitle)
		
		my WriteToFile(OutPutFile, ProblemTitle & ";=;=")
		
		# Difficulty:
		# document.getElementsByClassName('relative inline-flex items-center justify-center text-caption px-2 py-1 gap-1 rounded-full bg-fill-secondary text-difficulty-easy dark:text-difficulty-easy');
		
		set ProblemDifficulty to do JavaScript "document.getElementsByClassName('relative inline-flex items-center justify-center text-caption px-2 py-1 gap-1 rounded-full bg-fill-secondary')[0].innerText.trim();" in document 1
		
		my logt("Problem Difficulty: " & ProblemDifficulty)
		
		my WriteToFile(OutPutFile, ProblemDifficulty & ";=;=")
		
		# Solution Method Name etc:
		# document.getElementsByClassName('monaco-scrollable-element editor-scrollable vs-dark mac')[0].innerText.replace('class','').replace('Solution', '').replaceAll('}', '').trim().replaceAll('{', '').trim();
		
		set ProblemMethod to do JavaScript "document.getElementsByClassName('monaco-scrollable-element editor-scrollable vs-dark mac')[0].innerText.replace('/\\/\\*[\\s\\S]*?\\*\\/|(?<=[^:])\\/\\/.*|^\\/\\/.*/g', '').replaceAll('*/', '').replaceAll('*', '').replace('class','').replace('Solution', '').replaceAll('}', '').trim().replaceAll('{', '').trim()" in document 1
		
		my logt("Problem Method " & ProblemMethod)
		
		my WriteToFile(OutPutFile, ProblemMethod & ";=;=")
		
		
		# Tags:
		# document.getElementsByClassName('no-underline hover:text-current relative inline-flex items-center justify-center text-caption px-2 py-1 gap-1 rounded-full bg-fill-secondary text-text-secondary')
		
		# Problem Description:
		# $('div[data-track-load]')
		
		# set ProblemDesc to do JavaScript "$('div[data-track-load]').innerHTML" in document 1
		
		# TODO click Java code
		
		set ProblemDesc to do JavaScript "document.getElementsByClassName('elfjS')[0].innerText.trim();" in document 1
		
		my logt("Problem Description " & ProblemDesc)
		
		my WriteToFile(OutPutFile, ProblemDesc & ";=;=")
		
		set ProblemUrl to do JavaScript "window.location.href" in document 1
		
		my logt("Problem URL " & ProblemUrl)
		
		my WriteToFile(OutPutFile, ProblemUrl & ";=;=")
		
		set SolutionsUrl to do JavaScript "window.location.href.replace('description', 'solutions')" in document 1
		open location SolutionsUrl
		
		delay 15
		
		# Click the Java Button
		do JavaScript "var buttons = document.getElementsByClassName('lc-md:px-2 lc-md:py-[3px] inline-flex cursor-pointer items-center gap-1.5 whitespace-nowrap rounded-full px-3 py-[6px] text-xs bg-lc-fill-02 dark:bg-dark-lc-fill-02 text-lc-text-secondary dark:text-dark-lc-text-secondary'); for (var i = 0; i < buttons.length; i++) { var btn = buttons[i]; console.log(btn.innerText); if (btn.innerText == 'Java') {btn.click();} }" in document 1
		
		delay 2
		
		# Get the Links to some of the solutions
		set SolutionLinks to do JavaScript "var result = ''; var solutions = document.getElementsByClassName('no-underline hover:text-blue-s dark:hover:text-dark-blue-s truncate w-full'); for (var i = 0; i < solutions.length; i++) { result += solutions[i].href + ';;'; }; result;" in document 1
		
		my logt("Solutions URLs " & SolutionLinks)
		
		my WriteToFile(OutPutFile, SolutionLinks & ";=;=")
		
		# Get First Link
		# document.getElementsByClassName('no-underline hover:text-blue-s dark:hover:text-dark-blue-s truncate w-full')[0].href
		# set FirstSolLink to do JavaScript "document.getElementsByClassName('no-underline hover:text-blue-s dark:hover:text-dark-blue-s truncate w-full')[0].href;" in document 1
		
		# open location FirstSolLink
		
		# delay 15
		
		# Get the Solution
		# document.getElementsByClassName('h-full w-full overflow-hidden !min-w-[440px]')[0].innerText
		
		# End this problem		
		
		my WriteToFile(OutPutFile, "=;=;=;")
		
		# Examples:
		# document.getElementsByClassName('example-block');
		
		set EditorialUrl to do JavaScript "window.location.href.replace('solutions', 'editorial')" in document 1
		my logt("EditorialUrl " & EditorialUrl)
		
		open location EditorialUrl
		delay 15
		
		# document.getElementsByClassName('flexlayout__tab')[1].innerHTML
		
		set EditorialHtml to do JavaScript "document.getElementsByClassName('flexlayout__tab')[1].innerHTML" in document 1
		
		my logt("EditorialHtml " & EditorialHtml)
		
		my WriteToFile(EdOutPutFile, EditorialHtml)
		
		
		# Get the solution code link with 
		# var iframe = document.getElementsByTagName('iframe')[0]; iframe.src
		
		set SolutionFrameUrl to do JavaScript "var iframe = document.getElementsByTagName('iframe')[0]; iframe.src" in document 1
		
		my logt("SolutionFrameUrl " & SolutionFrameUrl)
		
		open location SolutionFrameUrl
		delay 15
		
		# Click Java
		
		do JavaScript "var btns = document.getElementsByClassName('btn'); for (var i = 0; i < btns.length; i++) { var btn = btns[i]; if(btn.innerText.trim() == 'Java') btn.click() };" in document 1
		
		set FrameHtml to do JavaScript "document.body.innerHTML" in document 1
		
		my logt("FrameHtml " & FrameHtml)
		
		my WriteToFile(SolOutPutFile, FrameHtml)
		
		# Get Code
		
		# set Code to do JavaScript "var lines = document.querySelectorAll('.CodeMirror-line'); var data = []; for (var i = 0; i < lines.length; i++) { var line = lines[i]; data.push(line.innerText); }; data;" in document 1
		
		set Code to do JavaScript "var lines = document.querySelectorAll('.CodeMirror-line'); var data = ''; for (var i = 0; i < lines.length; i++) { var line = lines[i].innerText.trim(); if(line == '' || line == '\\u200b') continue; data += (line) + '\\n'; }; data;" in document 1
		
		my logt("Code " & Code)
		
		my logt("EditorialUrl " & EditorialUrl)
		
		open location EditorialUrl
		delay 15
		
		set ProbUrl to do JavaScript "window.location.href.replace('editorial', 'description')" in document 1
		my logt("ProbUrl " & ProbUrl)
		
		open location ProbUrl
		delay 15
		
		tell application "System Events"
			
			tell process "Safari"
				-- This example attempts to click a button with a specific name.
				-- Replace "My Button" with the actual name or accessibility description of the button.
				# click text area "Editor content;Press Alt+F1 for Accessibility Options."
				# click (first UI element where its accessibility description is "Editor content;Press Alt+F1 for Accessibility Options.")
				# set focused of text area 1 to true -- Ensure the text area has focus
				my logt("typing")
				tell application "Safari"
					do JavaScript "document.querySelectorAll('[aria-roledescription=\"editor\"]')[0].focus(); " in document 1
					
					# do JavaScript "var targetElement = document.querySelectorAll('[aria-roledescription=\"editor\"]')[0];" in document 1
					
					# do JavaScript "const commandAEvent = new KeyboardEvent('keydown', {\\n    key: 'a',\\n    code: 'KeyA',\\n    keyCode: 65, // ASCII value for 'A'\n    which: 65,\\n    metaKey: true, // For Command key on macOS\n    ctrlKey: false, // For Ctrl key on Windows/Linux (if needed)\n    altKey: false,\\n    shiftKey: false,\n    bubbles: true, // Event bubbles up the DOM tree\\n    cancelable: true // Event can be canceled\n});');" in document 1
					
					# do JavaScript "targetElement.dispatchEvent(commandAEvent);" in document 1
					
					# do JavaScript "const backspaceEvent = new KeyboardEvent('keydown', {\\n    key: 'Backspace',\\n    code: 'Backspace',\\n    keyCode: 8, // Deprecated, but included for broader compatibility\\n    which: 8,   // Deprecated, but included for broader compatibility\\n    bubbles: true,\\n    cancelable: true\\n  });" in document 1
					
					# do JavaScript "targetElement.dispatchEvent(backspaceEvent);" in document 1
					
					# do JavaScript "const textToSimulate = '" & Code & "';" in document 1
					
					# do JavaScript "for (let i = 0; i < textToSimulate.length; i++) {\\n        const char = textToSimulate[i];\\n        const event = new KeyboardEvent(\"keydown\", {\\n            key: char,\\n            code: `Key${char.toUpperCase()}`, // Adjust code for specific keys\\n            bubbles: true, // Allow event to bubble up the DOM\\n            cancelable: true // Allow default action to be prevented\\n        });\n\n        // Dispatch the event\n        targetElement.dispatchEvent(event);\\n    }" in document 1
				end tell
				delay 2
				keystroke "a" using command down
				# keystroke backspace
				delay 2
				set the clipboard to Code
				delay 1
				keystroke "v" using command down
				# keystroke Code
				# repeat with C in Code
				#	keystroke C
				#	delay 5
				#	keystroke return
				#	delay 3
				#end repeat
				delay 10
			end tell
		end tell
		
		do JavaScript "document.querySelectorAll('[data-e2e-locator=\"console-submit-button\"]')[0].click(); " in document 1
		
		# close (every tab of every window)
		tell front window
			set firstTab to item 1 of its tabs
			repeat with i from (count of its tabs) to 2 by -1
				close (item i of its tabs)
			end repeat
			set current tab to firstTab
		end tell
	end tell
	
end FetchLeetCodeWcDaily

# --- Function Definitions ---
on logt(messsage)
	log "  --- " & messsage & " " & ((current date) as string) & " --- "
end logt


# Function to read from files
# https://developer.apple.com/library/archive/documentation/LanguagesUtilities/Conceptual/MacAutomationScriptingGuide/ReadandWriteFiles.html
on readFile(theFile)
	-- Convert the file to a string
	set theFile to theFile as string
	
	-- Read the file and return its contents
	return read file theFile
end readFile

# Funtion to write data to a file
on WriteToFile(FileName, Content)
	try
		# do shell script "touch " & FileName with administrator privileges
	end try
	# display dialog FileName
	set myFile to open for access FileName with write permission
	write Content to myFile starting at eof
	close access myFile
end WriteToFile

# Simple function to replace text
on replace_chars(this_text, search_string, replacement_string)
	set AppleScript's text item delimiters to the search_string
	set the item_list to every text item of this_text
	set AppleScript's text item delimiters to the replacement_string
	set this_text to the item_list as string
	set AppleScript's text item delimiters to ""
	return this_text
end replace_chars

# Function to help create a timestamp named file
on CreateTimeStampFileName(FilePath, FileName, FileExtension)
	return FilePath & FileName & "-" & replace_chars(replace_chars(replace_chars(((current date) as string), " ", "_"), ",", "_"), ":", "_") & "." & FileExtension
end CreateTimeStampFileName
```

## Dedicated Hardware

We move this over to my poor Old Yeller of a MAC book...
Even thought the old bat cant see well enough to run docker or x-code she will do the trick.
At least she can find old life is a data mining machine since she couldn't be a build server.

```
origin of dirt and song. We are made
by touch, not terror for tat, 

but one humble pulse in a numb 
abyss. Bet, god breathes this air.
```

Raucous Prayer -  Patrick Rosal

Oh yeah and setup a cron job so I don't have to worry about this lol...

and then get carried away more than I should with solving or not solving leetcode problems...
