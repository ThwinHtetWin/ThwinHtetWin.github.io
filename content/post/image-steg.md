---
title: "Image Steg"
date: 2022-05-09T21:58:48+06:30
draft: false
---

# Image Steganography

Created: July 13, 2021 2:29 PM

ကိုယ် ဝှက်ချင်တဲ့ data (message/file) တစ်ခုခုကို တခြား media (mp3, video, images, text) ထဲမှာ ဝှက်တာမျိုးကို steganography လို့ ခေါ်တယ်။

Steg system တစ်ခုဖြစ်လာဖို့ဆိုရင် -

*Undetectablility* : Data hide ထားမှန်း မသိသာရပါဘူး။

*Robustness* : data hide ထားတဲ့ cover file က steg analysis ဒဏ် ဘလောက်ခံနိုင်ရည်ရှိလဲ ဆိုတာပါ။

ဥပမာ - cover image ကို crop, rotate, noise, filter စတာတွေ လုပ်တဲ့အချိန်မှာ hidden data အပေါ် သက်ရောက်မှုကို ပြောတာပါ။

(ဒါ ကျွန်တော် နားလည်ထားတာပါ ၊ မှားရင် လာပြောလို့ရပါတယ်။)

*Payload/Embedding capacity* : cover file ရဲ့ data လက်ခံနိုင်စွမ်း နဲ့ သုံးမဲ့ steg method ရဲ့ အကောင်းဆုံး data hide နိုင်တဲ့ limit ကို ပြောတာပါ။

*Reliability* : hidden data ကို ပြန်ထုတ်တဲ့အခါမှာ data loss မဖြစ်ဘဲ ပြန်ထုတ်နိုင်ရမှာပါ။

ဥပမာ - Hide ခဲ့တဲ့ data က Hello world ဖြစ်နေရင် ပြန် extract လုပ်တဲ့အခါမှာလည်း Hello world ပဲဖြစ်နေရမယ်။

Steg system တစ်ခုမှာ undetectablility, robustness နဲ့ capacity တို့က အကောင်းဆုံးအဆင့်မှာ တစ်ပြိုင်နက် ရှိမနေနိုင်ပါဘူး။

ဥပမာ -  payload capacity များရင် undetectablility နည်းသွားမှာပါ။

Steganography မှာဆိုရင် -

1. Text steganography
2. Image steganography
3. Video steganography
4. Audio steganography
5. Network steganography ဆိုပြီး ရှိတယ်။

ဒီထဲကမှ Image Steganography အကြောင်းကို ပြောပြပါ့မယ်။

## Image Steganography

Image Steganography ဆိုတာက data (string,file) ကို တခြား image file (PNG, JPG, JPEG, BMP) တွေထဲမှာ သာမန်မျက်စိနဲ့ ကြည့်ရုံနဲ့ ဝှက်ထားမှန်းမသိအောင် ဝှက်တာမျိုးပါ။

Image steg method တွေမှာဆိုရင် -

- Spatial Domain
- Transform Domain ကို အသုံးများကြတယ်။

(တခြား algorithm/method တွေလည်း ရှိနိုင်ပါတယ် ၊ လက်လှမ်းမီသလောက်ပဲ ထည့်ရေးထားတာပါ။)

Spatial Domain ထဲမှာ sequential system နဲ့ random/scattered system ဆိုပြီး ဆက်ရှိမယ်။ LSB method ကို အဓိကသုံးကြတယ်။

### LSB method

LSB ဆိုတာ Least Significant Bit ကိုပြောတာပါ။

Image တစ်ခုဟာ Pixel တွေနဲ့ ဖွဲ့စည်းထားတယ်ဆိုတာ သိပြီးသားဖြစ်မှာပါ။

LSB method ရဲ့ သဘောတရားကတော့ hidden data ကို bit ပြောင်းပြီး pixel တစ်ခုချင်းစီရဲ့ LSB မှာ အစာထိုးလိုက်တာပါ။

Sequential system မှာဆိုရင် pixel တွေရဲ့ LSB တစ်ခုချင်းကို ကိုယ်ဝှက်ချင်တဲ့ data ရဲ့ bit တစ်ခုချင်းနဲ့ အစဉ်လိုက် replace လုပ်တယ်။

Scattered/random မှာဆိုရင်တော့ အစဉ်လိုက်မဟုတ်ဘဲ random သွားတယ်ပေါ့။

(တစ်ခါတစ်လေမှာ MSB - Most Significant Bit အနေနဲ့လည်း ရှိလာနိုင်ပါတယ် ၊ သဘောတရားက အတူတူပါပဲ။)

### Transform Domain

Transform Domain မှာဆိုရင် JPEG ကို အသုံးများတယ်။

JPEG file compression process ထဲမှာ secret message ထည့်ပြီး compress လိုက်တယ်လို့ပဲ အကြမ်းဖျင်း မှတ်တယ်။

JPG file compression အကြောင်း ဒီမှာ ဖတ်ပါ။ 
https://www.freecodecamp.org/news/how-jpg-works-a4dbd2316f35/

Transform Domain ထဲမှာဆိုရင် -

- IWT (Integer Wavelet Transform)
- Discrete Curvelet Transform (DCVT)
- DCT (Discrete Cosine Transform)
- DWT (Discrete Wavelet Transform)
- DFT (Discrete Fourier Transform) ဆိုပြီး ဆက်ရှိကြသေးတယ်။

Jsteg, F5 တို့လို model တွေ algorithm တွေမှာဆိုရင် non-zero DCT coefficient ရဲ့ LSB မှာ secret data ကို hide ကြတယ်။

CTF တွေမှာဆိုရင် steganography ကို category သပ်သပ်ဖြစ်စေ Digital forensics အောက်မှာဖြစ်စေ ထည့်ပြီး မေးကြတယ်။

‌အောက်က ပုံ၂ပုံကိုကြည့်ပါ။

![Img1](Image%20Steganography%200b9c65c361054d0cbc9358ab44baf588/incredible-man-meme-template-people-who-know.jpg)

Img1

![Img2](Image%20Steganography%200b9c65c361054d0cbc9358ab44baf588/lsb.png)

Img2

ဒုတိယပုံဟာ သာမာန်မျက်စိနဲ့ ကြည့်မယ်ဆိုရင် data hide ထားမှန်း သိမှာမဟုတ်ပါဘူး။

ပထမပုံနဲ့ အတူတူပဲလို့ ထင်မှာပါ။

ဒါပေမဲ့ LSB method နဲ့ data hide ထားပါတယ်။

ဒုတိယပုံကို [https://stylesuxx.github.io/steganography/](https://stylesuxx.github.io/steganography/) မှာ decode လိုက်မယ်ဆိုရင် secrete message ကို မြင်ရပါလိမ့်မယ်။

LSB method ကို plain text သုံးတာထက် encryption တစ်ခုခုနဲ့ တွဲသုံးတာက ပိုသင့်တော်ပါမယ်။

https://github.com/ThwinHtetWin/HideAndSeek

ဒီ program မှာဆိုရင် secret message ကို symmetric encryption နဲ့ encrypt ပြီးမှ LSB method ကို သုံးထားပါတယ်။

Steg challenge တွေဖြေဖို့အတွက် note ထုတ်ထားတာ ရှိပါတယ်။မပြည့်စုံတာ ရှိနိုင်ပါတယ်။

https://zus.notion.site/ImageSteg-notes-b4feb6d174cd427f9b192f02b4090d6d

*Note : Educational purpose only ပါ။*
