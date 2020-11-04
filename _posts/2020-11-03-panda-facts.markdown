---
layout: post
title:  "Panda Facts"
---
## panda-facts was a challenge in the web category worth 412 points
#### Special thanks to Aidan who helped guided me through this challenge.
> This webpage (https://panda-facts.2020.redpwnc.tf) takes user input for a username to see panda facts. The username supplied is then placed
> inside a token and is not sanitized to check for injections or malicious code segments. This allows us
> to manipulate the `member` field in the token therefore allowing us to see the flag.

First, I wanted to see where exactly this html form was sending the provided username. After inspecting element and opening the associated `script.js` file, it is apparent that this html form data was being sent via a POST request to `/api/login` upon submission. See below.
```js
const form = document.getElementById('enter');
form.addEventListener('submit', async (event) => {
   event.preventDefault();
   const username = document.getElementById('username').value;
   const res = await fetch('/api/login', {
        method: 'POST', // or 'PUT'
        headers: {
             'Content-Type': 'application/json',
        },
            body: JSON.stringify({username}),
        });
        const json = await res.json(); 
        document.cookie = `token=${json.token}`;
        window.location = '/pandas/';
```


In addition, I also observed that after this information was sent, a token was created and the user is redirected to `/pandas`. This information will be expanded on later.

I next wanted to inspect the provided [index.js](index.js) source code and how it was related to the above information. Looking at the `generateToken(username)` method, it appears that the token is prepared with the format `{"integrity":"${INTEGRITY}","member":0,"username":"${username}"}`before being encrypted. It is important to note that the `$username` variable from the form that is supplied has not been sanitized and is therefore suceptiable to a JSON injection. 

At this point I knew what the attack vector would be, but was not sure what exactly need to be manipulated to view the flag. So I decided to look closer at what happens when `/api/flag` is called. A code snippet is supplied below. 

```js
app.get('/api/flag', async (req, res) => {
    if (!req.cookies.token || typeof req.cookies.token !== 'string') {
        res.json({success: false, error: 'Invalid token'});
        res.end();
        return;
    }

    const result = await decodeToken(req.cookies.token);
    if (!result) {
        res.json({success: false, error: 'Invalid token'});
        res.end();
        return;
    }

    if (!result.member) {
        res.json({success: false, error: 'You are not a member'});
        res.end();
        return;
    }

    res.json({success: true, flag: process.env.FLAG});
});
```
In order to receive the flag, we need to have a valid token (which we can mimic with an injection) and we need to be a member. This field could easily be manipulated so at this point I was ready to provide my payload. When checking the token `decodeToken(token)` is called and therefore is interpreted as plain text, so no type of cryptography is involved.

### Payload
#### Input into html form: `me", "member": 1, "h":"k`
#### Concatenated token: `{"integrity":"12370cc0f387730fb3f273e4d46a94e5","member":0,"username":"me","member":1,"h":"k"}`

Since JSON when parsing only looks at the last usage of a value, it now believes we are in fact a member.
Like mentioned earlier we are forwarded to `/pandas` where there is a html button that when clicked calls `/api/flag` and the flag is displayed.

### Flag
`flag{1_c4nt_f1nd_4_g00d_p4nd4_pun}`
