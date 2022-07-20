---
title: "Steg Note"
date: 2022-05-09T21:33:44+06:30
draft: false
---

# ImageSteg notes

Created: July 23, 2021 12:44 PM

## Initial Analysis

file format verification : `file`

view metadata : `exiftool`, `exiv2`

extract some data : `strings`, `cat`, `bgrep`

More detail about image : `stegoveritas` 

---

## LSB Check

 PNG image : `zsteg`

JPG/JPEG image : `jsteg` 

`zsteg` sometimes reveal hidden image.

Syntax : `zsteg -E <method> hidden_image.png > reveal_img.png`

Ref : [https://szymanski.ninja/en/ctfwriteups/2021/shellctf/hidden-inside-2/](https://en.wikipedia.org/wiki/List_of_file_signatures)

---

## File Carving

Tools : `binwalk`, `dd`, `foremost`, `steghide`

Sometimes a file seems bigger than actual file size because it conceals another file to host file to avoid detect.

`binwalk` can show in decimal and hexadecimal value of location to extract.

Use `-e` flag to extract automatically.

Syntax : `binwalk -e file.png`

`dd` is used to extract file from host file.

Example,

`dd if=example.jpg bs=1 skip=1972141 of=foo.zip`

skip must be in decimal values(can get from binwalk.) 

If one of image files is suspicious, then use steghide's extract feature.

Syntax : `steghide extract -sf sus.png`

---

## File Header Analysis(Magic Bytes)

Tools : any hex editor( `ghex`, `xxd` , 010editor, `hexedit` )

check magic bytes(file signature) if we can't open on default viewer.

Ref : [https://en.wikipedia.org/wiki/List_of_file_signatures](https://en.wikipedia.org/wiki/List_of_file_signatures)

---

## PNG Corrupted File

Tools : `pngcheck`, any hex editor, `PCRT`

By using `pngcheck`, we can follow the corrupted bytes and edit the wrong ones to make it valid.

 

Critical chunks :

- PNG - A PNG image always starts with those 4 bytes.
- IHDR - Image Header which is a very first chunk.
- IDAT - Image Data Chunk.
- IEND - trailer.Mark as the end of PNG datastream.

### CRC error in IHDR chunk

IHDR chunk error can fix with `pngcheck` tool. 

(computed - REAL BYTES, expected - ERROR BYTES) 

Read dimensionless challenge again. 

---

## Bruteforce

Tools : `zip2john`

`zip2john` can be used to crack the password of zip file which are embedded in the image.(sometimes combo with `binwalk`)

Usage : 

1. `zip2john challenge.zip > hash.txt`
2. `john -- wordlist=/usr/share/wordlists/rockyou.txt --format=zip hash.txt` or `john hash.txt`

---

## Texts Hidden in Images

Tool : `stegsolve`, GIMP or Photoshop software

if the challenge gives two pics, check stegsolve's image combiner.

---

## Audio Steganography

Tools : audacity, sonic-visualizer, `stegpy` 

check waveform and spectrogram to find some information or flag.

---

## Steganographic nature of white-space

Tools : `stegsnow` 

If challenge text file seems bigger than written text and noticed big spaces, then try `stegsnow`.

---

Combine 2 Images with numpy and PIL

```python
import numpy as np
from PIL import Image
# Open images
im1 = Image.open("scrambled1.png")
im2 = Image.open("scrambled2.png")
# Make into Numpy arrays
im1np = np.array(im1)
im2np = np.array(im2)
# Add images
result = im2np + im1np
# Convert back to PIL image and save
Image.fromarray(result).save('result.png')
```

Ref : [https://github.com/HHousen/PicoCTF-2021/tree/master/Cryptography/Pixelated](https://github.com/HHousen/PicoCTF-2021/tree/master/Cryptography/Pixelated)

Online BMP file viewer

[https://www.photopea.com/](https://www.photopea.com/)
