# Write ups for forensics winter session.

### chall1.jpeg:
The comment in metadata gives us a string which is a **`base64`** encoded string: **`Njk2ZTYzNzQ2NjdiNTc2ODM0NzQ1ZjM0NzI2NTVmNzkzMDc1MjAzMDVmMzAyMDY5NmU2NzIwMjA2\nMTc0N2Q=`**

which on decoding gives us the flag.
We can use `ipython` to decode the `base64` encoded string which first gets converted into hex and then to string.
```
x="Njk2ZTYzNzQ2NjdiNTc2ODM0NzQ1ZjM0NzI2NTVmNzkzMDc1MjAzMDVmMzAyMDY5NmU2NzIwMjA2\nMTc0N2Q="

x.decode("base64")

y="696e6374667b576834745f3472655f79307520305f3020696e67202061747d"

y.decode("hex")
```


##### Flag: **`inctf{Wh4t_4re_y0u 0_0 ing  at}`**.

### chall2.wav: 

This was an easy one but took a lot of time due to naiveness in **`audio steganography`**, didn't realise there was an option to add a **spectrogram layer** and check in there. You can go to the **`Layer`** in the menu bar or just press **`Shift+G`**. There it is, sitting right there mocking at me for using audacity and all the other tools to crack it while it was **`sonic visualiser`** that did the trick.

##### Flag: **`inctf{did_audacity_work_???}`**


### chall3.zip:

It is a zip file so pretty obvious we should take the help of **`fcrackzip`**. Tried a dictionary attack using the trustworthy **`rockyou.txt`** and voila, we get **`flag.png`**. This cute little puppy is hiding something though. Checking the **`LSB`** shows us that there is something hidden in it. Extracting the **`LSB`** data gives us a lot of data amidst which there exists our flag.

**command:** `fcrackzip -u -v -D -p chall3.zip rockyou.txt`

##### Flag: `inctf{3ach_4nd_3v3ry_s3cre7_inf0rm4t10n_w1ll_b3_kn0wn_by_wir3shark!!!!!_:)}`

### chall4.png: 

**`feh`** gives out a lot of information in terms of what chunk of an image is corrupted.
The magic number is tampered with:

```
99 50 6E 47 CD
```
to 
```
89 50 4E 47 CD
```
The **`IHDR`** **`IDAT`** **`IEND`** chunks are corrupt as well.

Change the **`IHDR`** chunk from:

```
49 48 64 52
```

to 

```
49 48 44 52
```
**`IDAT`** chunk from:
```
49 44 61 54
```
to 
```
49 44 41 54
```

and the **`IEND`** chunk from:

```
49 45 6E 44
```
to 
```
49 45 4E 44
```
and then open the image.

##### Flag: **`inctf{correcting_all_chunks_gives_the_image}`**

### chall5.pdf:

Just like we use **`fcrackzip`** for a password protected zip file, pdfcrack is going to help us find the password of this pdf file. As always we'll try a dictionary attack using **`rockyou.txt`** and VOILA! We get the Password which unlocks the pdf file.

##### command: **`fcrackzip chall5.pdf rockyou.txt`**

##### Password: **`852456852456`**

##### Flag: **`inctf{5ddf7d70fcc387ac24660e3fff6129efd0b6e2889cd1339dd1}`**

### chall6.jpg: 

This one is a **`steghide`** challenge. This one needs a little but of piping to be done so we can crack the passphrase. A **dictionary** attack using **`John-the-ripper`** and **`rockyou.txt`** and the flag will get written in a separate file.

##### Command: **`steghide extract -sf chall6.jpg | john rockyou.txt;`**

##### Flag: **`inctf{this_is_4n_e4sY_!}`** 

### chall7.txt:

This was a little surprising at first but when we take a look at common steganography tools over at **https://teambi0s.gitlab.io/bi0s-wiki/forensics/Tools/** we find an interesting tool called **`stegsnow`** which basically helps us find out hidden data in a .txt file. When we read the .txt file, we see:

***`
The hand that mocked them, and the heart that fed:
And on the pedestal these words appear:
My name is password:Ozymandias, king of kings:
Look on my works, ye Mighty, and despair!
Nothing beside remains. Round the decay
`***

**password:** `Ozymandias` is given. So when we are prompted the password, we type in Ozymandias when we run the following command.

##### Command: **`sudo stegsnow -C -p Ozymandias chall7.txt`**

##### Password: **`Ozymandias`**

##### Flag: **`inctf{snow_snow_snow_stegsnowwwww....}`**

### chall8.png:

This one needs the ability so see each and every colour plane an image can dish out. How do we do that? **`Stegsolve`** is the tool to use. 

##### Command: **`java -jar stegsolve.jar`**

##### Flag: **`inctf{e5c29838a74ab34fa7b9d18ca1a8d07d679c8aa3} `**

### chall9.png:

This is a barcode and it's pretty simple, **`zbarimg`** gives us the desired result.

##### Command: **`zbarimg chall9.png`**
##### Flag: **`flag{B4r_c0de_scanned}`**

### chall10.png:

This is an **`LSB`** challenge. We'll need **`zsteg`** for this along with some tweaking the code to retrieve the flag.
When we check the image for **`LSB`** data, we see there is random text in maybe Gibberish or Latin(Can't really tell the difference) next to it is a location which looks something like this:
```
imagedata           .. text: "LPS$&;JOq"
b1,bgr,lsb,xy       .. text: "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Mattis pellentesque id nibh tortor id aliquet lectus proin. Sodales ut etiam sit amet nisl purus. Consequat mauris nunc cong"
b3,r,lsb,xy         .. text: "\"tAwc`Eb"
b4,r,msb,xy         .. text: "W4C'W $a"
b4,g,msb,xy         .. file: ALAN game data
b4,b,msb,xy         .. text: "N7=\"pw[g"
b4,rgb,msb,xy       .. text: "sA27&4WR"
b4,bgr,msb,xy       .. text: "0q3B67$R"
```
and if we observe carefully, the location next the random text, it looks like the **`LSB`** data is stored at that address and all we have to do is extract it.

##### Command: **`zsteg -E b1,bgr,lsb,xy chall10.png | strings | cat > flag.txt`**
##### Flag: **`inctf{y0u_g0t_th3_fl4g_d1d_y0u_le4rn_any7hing_?}`**

### chall11.jpg:
This is a challenge based on the hex values and byte values. Metadata gives us a little information

```
File Name                       : chall11.jpg
Directory                       : .
File Size                       : 217 kB
File Modification Date/Time     : 2019:12:14 13:30:02+05:30
File Access Date/Time           : 2019:12:14 13:30:18+05:30
File Inode Change Date/Time     : 2020:01:13 18:18:28+05:30
File Permissions                : rw-rw-r--
Warning                         : Processing TIFF-like data after unknown 30-byte header
Exif Byte Order                 : Little-endian (Intel, II)
X Resolution                    : 96
Y Resolution                    : 96
Resolution Unit                 : inches
Software                        : GIMP 2.10.12
Modify Date                     : 2019:10:21 18:23:09
Image Width                     : 256
Image Height                    : 167
Bits Per Sample                 : 8 8 8
Compression                     : JPEG (old-style)
Photometric Interpretation      : YCbCr
Samples Per Pixel               : 3
Thumbnail Offset                : 262
Thumbnail Length                : 4119
Image Size                      : 256x167
Megapixels                      : 0.043

```
if we carefully observe the warning, it gives us information that there is unknown data from the beginning to byte 30 which is causing problems for the OS to recognise the file as a **`.jpg`** image.
All we have to do is remove the data from the beginning till byte 30.

When we execute the above process and delete 31 bytes, we end up with something like:
```
ExifTool Version Number         : 11.65
File Name                       : chall11.jpg
Directory                       : .
File Size                       : 217 kB
File Modification Date/Time     : 2020:01:13 20:39:28+05:30
File Access Date/Time           : 2020:01:13 20:38:46+05:30
File Inode Change Date/Time     : 2020:01:13 20:39:28+05:30
File Permissions                : rw-rw-r--
Warning                         : Processing JPEG-like data after unknown 231-byte header
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Image Width                     : 256
Image Height                    : 167
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 256x167
Megapixels                      : 0.043

```
This gives us information that **`.jpg`** data is starting from byte 232 so we have to delete those many bytes from the beginning. When we do this, we get somewthing like this:
```
ExifTool Version Number         : 11.65
File Name                       : chall11.jpg
Directory                       : .
File Size                       : 217 kB
File Modification Date/Time     : 2020:01:13 20:42:40+05:30
File Access Date/Time           : 2020:01:13 20:42:32+05:30
File Inode Change Date/Time     : 2020:01:13 20:42:40+05:30
File Permissions                : rw-rw-r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Image Width                     : 256
Image Height                    : 167
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 256x167
Megapixels                      : 0.043

```
This shows out image is now recognised as **`.jpg`**
To be sure that the image is recognised and readable by the OS, use **`feh`** 
##### Flag: **`inctf{C0rr3ct3d_th3_JPG_f1l3}`**
### MissingHeroes.zip:
This is somewhat easy. We had to dig into it's metadata, we find **`GPS`** information and see what it shows:
```
GPS Version ID                  : 2.3.0.0
GPS Latitude                    : 105 deg 0' 0.00"
GPS Longitude                   : 105 deg 0' 0.00"
XMP Toolkit                     : Image::ExifTool 10.80

```
We can see that there is a coordinate. Instinctively, we would search the coordinates but hold on,**`Lattitude`** shows 105 but they range from -90 to +90. Let's see the next image:
```
GPS Version ID                  : 2.3.0.0
GPS Latitude                    : 110 deg 0' 0.00"
GPS Longitude                   : 110 deg 0' 0.00"

```
Yes, my doubt was correct, this is not coordinate, this is similar to **`ASCII`** value.
```
Dec  Char                           Dec  Char     Dec  Char     Dec  Char
---------                           ---------     ---------     ----------
  0  NUL (null)                      32  SPACE     64  @         96  `
  1  SOH (start of heading)          33  !         65  A         97  a
  2  STX (start of text)             34  "         66  B         98  b
  3  ETX (end of text)               35  #         67  C         99  c
  4  EOT (end of transmission)       36  $         68  D        100  d
  5  ENQ (enquiry)                   37  %         69  E        101  e
  6  ACK (acknowledge)               38  &         70  F        102  f
  7  BEL (bell)                      39  '         71  G        103  g
  8  BS  (backspace)                 40  (         72  H        104  h
  9  TAB (horizontal tab)            41  )         73  I        105  i
 10  LF  (NL line feed, new line)    42  *         74  J        106  j
 11  VT  (vertical tab)              43  +         75  K        107  k
 12  FF  (NP form feed, new page)    44  ,         76  L        108  l
 13  CR  (carriage return)           45  -         77  M        109  m
 14  SO  (shift out)                 46  .         78  N        110  n
 15  SI  (shift in)                  47  /         79  O        111  o
 16  DLE (data link escape)          48  0         80  P        112  p
 17  DC1 (device control 1)          49  1         81  Q        113  q
 18  DC2 (device control 2)          50  2         82  R        114  r
 19  DC3 (device control 3)          51  3         83  S        115  s
 20  DC4 (device control 4)          52  4         84  T        116  t
 21  NAK (negative acknowledge)      53  5         85  U        117  u
 22  SYN (synchronous idle)          54  6         86  V        118  v
 23  ETB (end of trans. block)       55  7         87  W        119  w
 24  CAN (cancel)                    56  8         88  X        120  x
 25  EM  (end of medium)             57  9         89  Y        121  y
 26  SUB (substitute)                58  :         90  Z        122  z
 27  ESC (escape)                    59  ;         91  [        123  {
 28  FS  (file separator)            60  <         92  \        124  |
 29  GS  (group separator)           61  =         93  ]        125  }
 30  RS  (record separator)          62  >         94  ^        126  ~
 31  US  (unit separator)            63  ?         95  _        127  DEL

```
referring to the above table and matching the values we get `105 = i` and `110 = n` so if we continue checking the **metadata**, we will get the flag.


##### Flag: inctf{d3f4a51}
