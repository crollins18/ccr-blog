---
layout: post
title:  "Imaginary CTF - Build-A-Website"
---

## Build-A-Website was a challenge in the web category worth 100 points
> This website has a form that takes input and renders it as an html webpage. The backend is handled by a Python library called Flask. We are provided the source code to the web app. The source code in its entirety can be viewed [here](https://raw.githubusercontent.com/crollins18/ccr-ctfwriteups/main/file-dumps/imaginary2021/build-a-website/app.py). After examining the way the input is processed and the comments inside the source code we determine that our attack vector will be Cross Site Scripting (known as XSS). More specifically this challenge deals with a type of XSS attack called Server Side Template Injections (known as SSTIs) that allows us to execute arbitrary Python code such as reading a flag file on the system. We are told in the challenge description (and on Discord) that we are looking for a flag file called `flag.txt`.

## Thought Process
On first glance I noticed that the blacklist (terms not allowed to be submitted) contained html tags such as `iframe`, `script`, `document`, etc. This lead me to believe that I needed to go with a JavaScript XSS attack to steal something like a session cookie. The only problem was that for this type of attack to work there needed to be a cookie the site hands out and a person (besides ourselves) needs to visit the page. I had neither of these components so I was back at square one.

***NOTE: Even if there was a cookie to steal, the returned html page had a (hardcoded) Content Security Policy (CSP) in place that would not allow any outside sources (such as JavaScript) to be processed***

`<meta http-equiv="Content-Security-Policy" content="default-src 'none'">`

I noticed that the only place direct input was handled (through the backend) was in the return statement of `site()` method. See below.

`return render_template_string(csp + content)`

There was a comment earlier in the code stating `i dont remember how to return a string in flask so here goes nothing`. This inspired me to investigate how rendering a template could be potentially malicious. After some research I came across articles talking about Server Side Template Injections through the Jinja templating language that Flask takes advantage of. To test this theory that any text sent could be interpreted as template information I sent this simple payload. 

![](https://i.imgur.com/38L3FEv.png)

The webpage returned printed `64` indicating that this string input was correctly injected and not treated as a literal. Instead it was treated as though it was part of template placed by an administrator.

I knew that the objective from this point on was to find a way to read a system file. Instead of accessing typical libraries to read a file from the operating system, we have to be a little more strategic (since we can't import external libraries other than what is already specified in `app.py`). Various common payloads to read a remote file exist for Jinja (see [here](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md#jinja2---read-remote-file)). They involve navigating up through superclasses and subclasses to get to a desired class (like the File class) that can read out a flag. The problem with these common approaches is that our blacklist has terms `las`, `bas`, and `bal`. These are all substrings of `class`, `subclass`, and `global` keyword used in payloads. After much head banging about how to evade this type of blacklist (and still have out injection evaluate) I found a lead.

## Solution

This payload `{{request|attr('application')|attr('\x5f\x5f\x67\x6c\x6f\x62\x61\x6c\x73\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('cat flag.txt')|attr('read')()}}` takes advantage of hexidecimal letter representations to get around the blacklist while still telling Python that we want to call execute the shell command `cat flag.txt`.

`ictf{:rooYay:_:rooPOG:_:rooHappy:_:rooooooooooooooooooooooooooo:}`
