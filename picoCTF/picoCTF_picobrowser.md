# [picobrowser | 200 points | Web Exploitation](https://play.picoctf.org/practice/challenge/9)

## Problem Statement

Description

This website can be rendered only by picobrowser, go and catch the flag!

https://jupiter.challenges.picoctf.org/problem/28921/ or http://jupiter.challenges.picoctf.org:28921

## First impressions

To solve this problem, we start by analyzing the [web page](https://jupiter.challenges.picoctf.org/problem/28921/).

We notice that there is a button with the text "Flag". When we click the button, it tells us that we are not picobrowser.

## The requests

Upon inspecting the web requests, we see that the page sends a GET request to the url `https://jupiter.challenges.picoctf.org/problem/28921/flag`

![Requests Tab](https://github.com/matthew-mw/writeups/assets/107924410/ad53bdf4-842f-4ac1-adc4-86f1e6759f5f)

Inspecting the response, we see that the page essentially tells us that:

```js
document.body.innerHTML = `You're not picobrowser! ${navigator.userAgent}`
```

![Response Preview](https://github.com/matthew-mw/writeups/assets/107924410/bc215dd1-85ae-445d-9019-9b865e73c391)

### Solution

To obtain the flag, we simply send a GET request to the url `https://jupiter.challenges.picoctf.org/problem/28921/flag` with the User-Agent `picobrowser`

## Putting it all together

```python
import requests

url = "https://jupiter.challenges.picoctf.org/problem/28921/flag"

headers = {"User-Agent": "picobrowser"}

page = requests.get(url, headers=headers)
page_text = page.text

flags = []

for line in page_text.splitlines():
    if "picoCTF{" in line and "}" in line:
        flag = line.split("picoCTF{")[1].split("}")[0]
        flags.append(flag)

print("Found {} possible flags".format(len(flags)))

for flag in flags:
    print("Flag: picoCTF{{{}}}".format(flag))
    
```

## The flag

This gives us the flag: picoCTF{p1c0_s3cr3t_ag3nt_84f9c865}
