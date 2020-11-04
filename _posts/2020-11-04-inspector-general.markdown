---
layout: post
title:  "RedPwnCTF - Inspector General"
---
## inspector-general was a challenge in the web category worth 112 points.

> Overall this is a pretty beginner level challenge. The main idea in this challege is to understand that
> information can be obfiscated inside html source code that is not necessary
> translated to the front-end visual experiance.

We're told that the website https://redpwn.net has a flag somewhere we are supposed to find. 
Naturally, I figured that the flag would not obvious and placed directly in plain text on any of the pages.
I still did my due diligence and used the keyboard shortcut `CTRL + F` to search all of the subpages for the string 
matching the given flag format of `flag{`. No success.

After some thought I remembered that the name of this challege does infact mention inspecting, leading me to think 
the browser's inspect element tool would need to be employed. I was able to view the main page source code this way and it is
also included below.
```html
<!DOCTYPE html>
<html lang="en-us">
<head>
	<meta name="generator" content="Hugo 0.72.0" />
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<meta name="redpwnctf2020" content="flag{1nspector_g3n3ral_at_w0rk}">
	<title>Home | redpwn</title>
	<link rel="stylesheet" href="/css/style.css" />
	<link rel="stylesheet" href="/css/fonts.css" />
	<link rel="stylesheet" href="/css/theme-override.css">
	<header>
		<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/styles/atom-one-light.min.css">
		<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/highlight.min.js"></script>
		<script>
		hljs.initHighlightingOnLoad();
		</script>
		<nav>
			<ul>
				<li class="pull-left current"> <a href="/">~/home</a> </li>
				<li class="pull-left "> <a href="/writeups/">~/writeups</a> </li>
				<li class="pull-left "> <a href="/ctfs/">~/ctfs</a> </li>
				<li class="pull-left "> <a href="/categories/">~/categories</a> </li>
				<li class="pull-right"> <a href="/index.xml">~/atom_feed</a> </li>
			</ul>
		</nav>
	</header>
</head>
<body>
	<br/>
	<div class="main">
		<p><img src="/logo.png" alt="logo"></p>
		<p><a href="https://github.com/redpwn">GitHub</a></p>
		<p><a href="/writeups/">Writeups</a></p>
		<p><a href="https://ctftime.org/team/59759">CTFtime</a></p>
		<p><a href="https://ctf.redpwn.net">RedpwnCTF</a></p>
		</main>
		<footer>
			<script>
			(function() {
				function center_el(tagName) {
					var tags = document.getElementsByTagName(tagName),
						i, tag;
					for(i = 0; i < tags.length; i++) {
						tag = tags[i];
						var parent = tag.parentElement;
						if(parent.childNodes.length === 1) {
							if(parent.nodeName === 'A') {
								parent = parent.parentElement;
								if(parent.childNodes.length != 1) continue;
							}
							if(parent.nodeName === 'P') parent.style.textAlign = 'center';
						}
					}
				}
				var tagNames = ['img', 'embed', 'object'];
				for(var i = 0; i < tagNames.length; i++) {
					center_el(tagNames[i]);
				}
			})();
			</script>
		</footer>
</body>
</html>
```
And just like earlier I used the find key shortcut provided in the browser to search for the string `flag{` and eureka there it was.

Flag: `flag{1nspector_g3n3ral_at_w0rk}`

As you can see the flag is essentially hidden with this meta tag nested inside the head of the html document. 
Meta tags as the name implies are designed to store meta data which is essential information about the html document that the browser can use in anything from rendering pages to assistive technologies for the hearing visually impaired. Some common examples are listed below. This was directly taken from https://www.w3schools.com/tags/tag_meta.asp. In this case the flag was hidden here and is of no particular use to the browser.

### Examples of common `<meta>` tag uses
```html
<head>
  <meta charset="UTF-8">
  <meta name="description" content="Free Web tutorials">
  <meta name="keywords" content="HTML, CSS, JavaScript">
  <meta name="author" content="John Doe">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
```
