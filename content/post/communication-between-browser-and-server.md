---
title: "Communication between Server and Browser"
date: 2022-05-09T20:43:49+06:30
draft: false
---

# How HTTP/HTTPS works

Created: July 13, 2021 2:30 PM

## HTTP/HTTPS

Hypertext Transfer Protocol ( HTTP ) ဆိုတာ World Wide Web ပေါ်က data တွေနဲ့ Browser ကို ဆက်သွယ်ပေးတဲ့ အခြေခံ Application Protocol တစ်ခုဖြစ်တယ််။

Hypertext Transfer Protocol Secure (HTTPS) ကျတော့ SSL/TLS protocol ကို သုံးပြီး encrypted လုပ်ထားတယ်။

---

## HTTP message

Client (browser) နဲ့ server ကြားမှာ data တွေကို HTTP message တွေနဲ့ Transfer လုပ်ကြတယ်။

HTTP message မှာ *request* နဲ့ *response* ဆိုပြီး ၂မျိုးရှိတယ်။

- Action တစ်ခုခုလုပ်ချင်လို့ server ဆီကို client (browser) ဘက်ကနေ sent လိုက်တဲ့ HTTP message တစ်ခုကို HTTP request လို့ ခေါ်တယ်။

```
GET / HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947"
Cache-Control: max-age=0

<body>
.
.
.
</body>
```

- Client က ပို့လိုက်တဲ့ HTTP request ကို Server က ပြန် Response လုပ်တဲ့ Message ကိုတော့ HTTP response လို့ ခေါ်တယ်။

```
HTTP/1.1 304 Not Modified
Accept-Ranges: bytes
Age: 59485
Cache-Control: max-age=604800
Date: Tue, 13 Jul 2021 08:32:58 GMT
Etag: "3147526947"
Expires: Tue, 20 Jul 2021 08:32:58 GMT
Last-Modified: Thu, 17 Oct 2019 07:18:26 GMT
Server: ECS (oxr/830E)
Vary: Accept-Encoding
X-Cache: HIT
Connection: close

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>A simple webpage</title>
</head>
<body>
  <h1>Simple webpage</h1>
  <p>Hello, world!</p>
</body>
</html>
```

HTTP message တစ်ခုမှာ start line, HTTP headers, HTTP body ဆိုပြီး ၃ ပိုင်းပါတယ်။

### Start line

- HTTP request ရဲ့ start line မှာ HTTP method, ကိုယ်ယူမဲ့ resource ရှိတဲ့ path, Protocol Version ဆိုပြီး ၃ ခု ပါတယ်။

- HTTP response ရဲ့ start line မှာဆိုရင်တော့ Protocol Version, HTTP status code, HTTP status message ဆိုပြီး ၃ခု ပါတယ်။

> start line က single line ပါ။
> 

### HTTP headers

- HTTP header တွေကို Response header, General header, Representation header ဆိုပြီး အကြမ်းအားဖြင့် ဆက်ခွဲလို့ရတယ်။

- Response header ထဲမှာ Browser က ဘာလိုချင်တယ်/ဘာကိုလက်ခံချင်တယ် ဆိုတဲ့ အချက်အလက်တွေပါတယ်။
    
    (ဥပမာ-  *Accept-Language : en-US* ထည့်ပြီး request လိုက်ခြင်းအားဖြင့် ဒီ client (browser) က server response ပြန်ပေးမဲ့ data ကို English လို နားလည်နိုင်ကြောင်း/ပြန်လက်ခံမယ်ဆိုတဲ့ အကြောင်း server ကို ပြောလိုက်တာပါ။)
    

- General header ထဲမှာတော့ ဒီ HTTP message တစ်စောင်ရဲ့ သာမန် data တွေ ပါတယ်။
    
    (ဥပမာ- *Connection*, *Cache-Control*) 
    

### HTTP Body

- HTTP message body ထဲမှာပါတဲ့ data တွေရဲ့ အချက်အလက်တွေက Representation header ထဲမှာ သွားပါတယ်။
    
    (ဥပမာ- *Content-Type : text/html; charset=UTF-8* လို့ ပါခဲ့ရင် HTTP body ထဲမှာ ပါတဲ့ data က text data ဖြစ်ကြောင်း ပြောတာပါ။)
    

> HTTP method တွေကို [https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) မှာ ဖတ်ပါ။
> 

> HTTP status code တွေအကြောင်းကို [https://developer.mozilla.org/en-US/docs/Web/HTTP/Status](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) မှာ ဖတ်ပါ။
> 

---

Client နဲ့ server ကြား တကယ်ဆက်သွယ်တဲ့ပုံစံကတော့ - 

1. ပထမဆုံး client ဘက်ကနေ TCP connection တစ်ခုကို request, response အတွက် စဖွင့်တယ်။
2. HTTP request message တစ်ခုကို server ဆီကို ပို့လိုက်တယ်။
3. Server ဘက်ကနေ HTTP response message တစ်ခုကို ပြန်ပို့ပေးတယ်။
4. Client က server ပို့လိုက်တဲ့ response ကို ပြန်ယူပြီး connection တစ်ခု ပြီးဆုံးသွားတယ်။

HTTP message တွေ Transfer လုပ်ပုံကို Browser တွေမှာ ကိုယ်တိုင်မြင်နိုင်ပါတယ်။

Firefox မှာဆိုရင် Right click on the page → select "Inspect Element" → Network tab မှာ မြင်ရပါမယ်။

Chrome မှာဆိုရင် Right click on the page → select "Inspect Element" → Network tab သွားပြီး page ကို reload → Headers မှာ မြင်ရပါမယ်။

Burp Suite, ZAP တို့လို Proxy tool တွေသုံးပြီးတော့လည်း ကြည့်လို့ရပါတယ်။

Web development အတွက် စလေ့လာနေချိန်မှာ ကိုယ့် browser နဲ့ server ကြား ဘလိုအလုပ်လုပ်သွားလဲဆိုတာလည်း သိထားသင့်ပါတယ်။

Web application security ဘက် သွားမဲ့သူတွေကတော့ မဖြစ်မနေ သိထားရမှာပါ။
