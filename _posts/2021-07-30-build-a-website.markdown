---
layout: post
title:  "Imaginary CTF - Build-A-Website"
---

## Build-A-Website was a challenge in the web category worth 100 points
> This website has a form that takes input and renders it as an html webpage. The backend is handled by a Python library called Flask. We are provided the source code to the web app. The source code in its entirety can be viewed [here](https://raw.githubusercontent.com/crollins18/ccr-ctfwriteups/main/file-dumps/imaginary2021/build-a-website/app.py). After examining the way the input is processed and the comments inside the source code we determine that our attack vector will be Cross Site Scripting (known as XSS). More specifically this challenge deals with a type of XSS attack called Server Side Template Injections (known as SSTIs) that allows us to execute arbitrary Python code such as reading a flag file on the system.

