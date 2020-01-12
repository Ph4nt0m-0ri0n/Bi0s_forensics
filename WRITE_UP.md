# Write ups for forensics winter session.

#### chall1.jpeg:
The comment in metadata gives us a string which is a base64 string:**`Njk2ZTYzNzQ2NjdiNTc2ODM0NzQ1ZjM0NzI2NTVmNzkzMDc1MjAzMDVmMzAyMDY5NmU2NzIwMjA2\nMTc0N2Q=`**

which on decoding gives us the flag:  **`inctf{Wh4t_4re_y0u 0_0 ing  at}`**.

#### chall2: 

This was an easy one but took a lot of time due to naiveness in audio steganography, didn't realise there was an option to add a spectrogram layer and check. There it is, sitting right there mocking at me for using audacity and all the other tools to crack it while it was sonic visualiser that did the trick.
#### chall3:

It is a zip file so pretty obvious we should take the help of fcrackzip. Tried a dictionary attack using the trustworthy rockyou.txt and voila, we get flag.png. This cute little puppy is hiding something though. Checking the lsb shows us that there is something hidden in it. Extracting the lsb data gives us a lot of data amidst which there exists our flag.
#### chall4: 

feh gives out a lot of information in terms of what chunk of an image is corrupted. An edit to the magic number header of the png file from 99 to 89 and correcting the PNG, IHDR, IDAT and IEND chunk makes it possible to view the image and indeed, correcting the chunks gives us the image.
#### chall5:

Just like we use fcrackzip for a password protected zip file, pdfcrack is going to help us find the password of this pdf file. As always we'll try a dictionary attack and again, voila! We get the password which unlocks the pdf file.
#### chall6: 

This was a little frustrating but actually easy when the solution was found. The metadata and the image structure doesn't give away much, neither does the image colour planes give us anything. So a little try using steghide and there is a little hit but we don't know the passphrase. Maybe john can help us with a little help of rockyou.txt. Eureka! We got the passphrase and the the hidden content gets written in another file which we can access and get the flag. Thanks john! :)
#### chall7:

This was a little surprising but doing a bit of blind search, I stumbled upon stegsnow. Well, that did work in the end.
#### chall8:

This one needs the ability so see each and every colour plane an image can dish out. How do we do that? Stegsolve is the tool to use. 
#### chall9:

This is a barcode and it's pretty simple, zbarimg gives us the desired result.
#### chall10:

This was quiet a complex one. Struggled for a while until i stumbled upon zsteg and voila, it is a lsb type challenge. Extract the data in using zsteg and then pipe it with strings and then copy the output to a file. Search for the flag in that file. There, you have it. Now you've learnt something new. :)
#### chall11:

This challenge was a little tricky, we had to delete the unwanted bytes that was stopping the recognition of the file. As amateurs, we would correct the number at the offset that coincided with the byte-data error displayed when we see the metadata but it is the no.of bytes, as rightly mentioned and overlooked due to us being naive, that have to be removed so the image can be recognised as a jpeg image.
#### Missing Heroes:

The GPS coordinates are just a bluff so we go all around the world to find the flag. The numbers in the GPS coordinates were actually ASCII values. This sure was a little tricky...
