---
layout: post
title:  "Sunshine CTF - Password Pandemonium"
---
## Password Pandemonium was a challenge in the web category worth 100 points
> This webpage (https://pandemonium.web.2020.sunshinectf.org) takes us to a ficticious travel booking site for Oceanic Airlines. It appears that we must first create an account. On first try you are told that you need to supply a username and password. Each time you attempt to supply a password that fits with the requirement, a new requirement is mentioned in the error messages (this happens 8 times). To finish the challenge you must supply a password that fits all of the requirements.

### Testing different passwords
To test certain passwords to see if they passed the filter, I used a python script to send post requests with the data that would be supplied by the HTML form. This way I can see my password I am supplying in plain text and not forget if I tried a certain password before. I also used the beautifulsoup library to clean the html responses that were sent back into clean plain text. The python code is listed below. 

```python 
import requests

from bs4 import BeautifulSoup

url = 'https://pandemonium.web.2020.sunshinectf.org/sign_up'
myobj = {'username': 'hackerman', 'password': 'testpassword'}

x = requests.post(url, data = myobj)

html = x.text
soup = BeautifulSoup(html, features="html.parser")

text = soup.get_text()

# break into lines and remove leading and trailing space on each
lines = (line.strip() for line in text.splitlines())
# break multi-headlines into a line each
chunks = (phrase.strip() for line in lines for phrase in line.split("  "))
# drop blank lines
text = '\n'.join(chunk for chunk in chunks if chunk)

#print the response text

print(text)
```

### Requirements for valid password
After testing password after password....these are all the requirements that are necessary to submit a valid password
* Password has to be not too short (arbitrary length)
  * ex. `pass`
* Password had to be not too long (arbitrary legnth)
  * ex. `yoooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo`
* Password must include more than two special characters
  * ex. `testpassword!@#'
* Password must include a prime amount of numbers
  * ex. `testpassword!@#123`
* Password must have equal amount of uppercase and lowercase characters
  * ex. `testpassword!@#123TESTPASSWORD`
  
#### The more tricky ones...
* Password must include an emoji
* Password's MD5 hash must start with a number
* Password must be valid JavaScript that evaluates to True

### Including an Emoji
At first I thought that an html encoding of emojis was how I needed to submit emojis, however after repeatedly submitting different hex representations of typical emojis my number count and (uppercase/lowercase) letter count was getting out of wack. I realized this later on, but it was a bug DUHH moment, but I could just copy and paste an emoji that is already rendered from Google and place in into the python script. And like magic (not really!) the site now recognized the emoji requirement was fulfilled and now asked me to consider the next requirement.
* ex. `⚾testpassword!@#123TESTPASSWORD`

### MD5 hash that starts with a number
MD5 hashes involve lots of complex maths and well all I know is that after tooling around with a different emojis at the beginning of the password strings that the MD5 hash started with a number. I used (CyberChef)[https://gchq.github.io/CyberChef/] to play around with potential MD5 hash outputs.
* ex. `😊testpassword!@#123TESTPASSWORD`
* MD5 hash: `34f17320401021beea774eaafebdaac9`

### Creating a password that is valid Javascript that evaluates true

### Payload

### Flag

*It is important to note that there are a whole range of different passwords that could have fulfilled the requirements and the examples listed here are just potential solutions you can use to try on the site yourself*
