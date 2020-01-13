# Write ups for forensics winter session.

### chall1.jpeg:
The comment in metadata gives us a string which is a **`base64`** encoded string: **`Njk2ZTYzNzQ2NjdiNTc2ODM0NzQ1ZjM0NzI2NTVmNzkzMDc1MjAzMDVmMzAyMDY5NmU2NzIwMjA2\nMTc0N2Q=`**

which on decoding gives us the flag.
##### Flag: **`inctf{Wh4t_4re_y0u 0_0 ing  at}`**.

### chall2: 

This was an easy one but took a lot of time due to naiveness in **`audio steganography`**, didn't realise there was an option to add a **spectrogram layer** and check in there. You can go to the **`Layer`** in the menu bar or just press **`Shift+G`**. There it is, sitting right there mocking at me for using audacity and all the other tools to crack it while it was **`sonic visualiser`** that did the trick.
##### Flag: **`inctf{did_audacity_work_???}`**


### chall3:

It is a zip file so pretty obvious we should take the help of **`fcrackzip`**. Tried a dictionary attack using the trustworthy **`rockyou.txt`** and voila, we get **`flag.png`**. This cute little puppy is hiding something though. Checking the **`LSB`** shows us that there is something hidden in it. Extracting the **`LSB`** data gives us a lot of data amidst which there exists our flag.
**command:** `fcrackzip -u -v -D -p chall3.zip rockyou.txt`
##### Flag: `inctf{3ach_4nd_3v3ry_s3cre7_inf0rm4t10n_w1ll_b3_kn0wn_by_wir3shark!!!!!_:)}`

### chall4: 

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

### chall5:

Just like we use **`fcrackzip`** for a password protected zip file, pdfcrack is going to help us find the password of this pdf file. As always we'll try a dictionary attack using **`rockyou.txt`** and VOILA! We get the Password which unlocks the pdf file.

##### command: **`fcrackzip chall5.pdf rockyou.txt`**
##### Password: **`852456852456`**
##### Flag: **`inctf{5ddf7d70fcc387ac24660e3fff6129efd0b6e2889cd1339dd1}`**

### chall6: 
This one is a **`steghide`** challenge. This one needs a little but of piping to be done so we can crack the passphrase. A **dictionary** attack using **`John-the-ripper`** and **`rockyou.txt`** and the flag will get written in a separate file.

##### Command: **`steghide extract -sf chall6.jpg | john rockyou.txt;`**
##### Flag: **`inctf{this_is_4n_e4sY_!}`** 

### chall7:

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

### chall8:

This one needs the ability so see each and every colour plane an image can dish out. How do we do that? **`Stegsolve`** is the tool to use. 

##### Command: **`java -jar stegsolve.jar`**
##### Flag: **`inctf{e5c29838a74ab34fa7b9d18ca1a8d07d679c8aa3} `**

### chall9:

This is a barcode and it's pretty simple, **`zbarimg`** gives us the desired result.

##### Command: **`zbarimg chall9.png`**
##### Flag: **`flag{B4r_c0de_scanned}`**

### chall10:

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

### chall11:

This challenge was a little tricky, we had to delete the unwanted bytes that was stopping the recognition of the file. As amateurs, we would correct the number at the offset that coincided with the byte-data error displayed when we see the metadata but it is the no.of bytes, as rightly mentioned and overlooked due to us being naive, that have to be removed so the image can be recognised as a jpeg image.

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
### Missing Heroes:

The GPS coordinates are just a bluff so we go all around the world to find the flag. The numbers in the GPS coordinates were actually ASCII values. This sure was a little tricky...
