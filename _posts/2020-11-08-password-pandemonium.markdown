---
layout: post
title:  "Sunshine CTF - Password Pandemonium"
---
## Password Pandemonium was a challenge in the web category worth 100 points
> This webpage (https://pandemonium.web.2020.sunshinectf.org) takes us to a ficticious travel booking site for Oceanic Airlines. It appears that we must first create an account. On first try you are told that you need to supply a username and password. Each time you attempt to supply a password that fits with the requirement, a new requirement is mentioned in the error messages (this happens about 10 times). To finish the challenge you must supply a password that fits all of the requirements.

## Testing different passwords
To test certain passwords to see if they passed the filter, I used a python script to send post requests with the data that would be supplied by the HTML form. This way I can see my password I am supplying in plain text and not forget if I tried a certain password before. I also used the beautifulsoup library to clean the html responses that were sent back into clean plain text. The python code is listed below. 

```python 
import requests

from bs4 import BeautifulSoup

url = 'https://pandemonium.web.2020.sunshinectf.org/sign_up'
myobj = {'username': 'hackerman', 'password': 'testpassword'}

x = requests.post(url, data = myobj)

#print the response text (the content of the requested file):

html = x.text
soup = BeautifulSoup(html, features="html.parser")

text = soup.get_text()

# break into lines and remove leading and trailing space on each
lines = (line.strip() for line in text.splitlines())
# break multi-headlines into a line each
chunks = (phrase.strip() for line in lines for phrase in line.split("  "))
# drop blank lines
text = '\n'.join(chunk for chunk in chunks if chunk)

print(text)
```

## Requirements for valid password
* Password has to be not too short
* Password had to be not too long
* Password must include more than two special characters
* Password must include a prime amount of numbers
* Password must have equal amount of uppercase and lowercase characters
* Password must include an emoji
* Password's MD5 hash must start with a number
* Password must be valid JavaScript that evaluates to True
