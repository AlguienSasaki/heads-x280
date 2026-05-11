# heads-x280

I'm using this repo to only have the necessary x280 files for the heads port.
The final files will be uploaded to [This repo](https://github.com/NobodyNo0ne/heads-port-x280)

Initialy I started compiling and testing the final rom in the T480.

## Aca le pones la imagen papeto y note olvides de quitarle la metadata pe.

With the understanding of how the compilation works, 
I started working in the x280 port, and I used the T480 files as a base 
(it worked when I did  the libreboot port.)

A lot of the hard work was already done, since "guessing" the file hashes was
something I did for the x280 libreboot port, and I noticed that the required
t480 blobs end up being the same as the ones used in libreboot (that is easily
checkable by pasing both libreboot's and heads' blobs by the sha256sum program)
so, I already had something to put inside the blobs/xx80/x280_download_clean_deguard_me_pad_tb.sh

But the only problem I got was the x280_ifd.bin file, that appearently is incorrect 
(which surprised by tbh because it didn't caused any problems with libreboot, but now I have to change it in the libreboot port.)

well, the problem is in the [x280_initial_ifd_file_error.txt]()
I tried fixing it by changing the [next layout file]() and using the "fix_cbfs_ifd" 
parameter, but neither of them worked, since I didn't want to realocate space for the
ifd file I have because that could break the linuxboot payload, I tried getting some 
AI assistance to shrink the x280_ifd.bin file, and yeah, it helped me to get a final 
image built, but it did't boot, making some noises and instantly turning off 
(what a suprise.)

I used the following code to get the image that would compile but wouldn't boot:

```bash
python3 -c '
with open("ifd_16", "r+b") as f:
    # Ir al offset de las regiones (FRBA) que está en 0x40
    f.seek(0x40)
    
    # Escribir los registros de región exactos del T480:
    # FLREG0 (Descriptor): 0000 0000 -> bytes: 00 00 00 00
    f.write(b"\x00\x00\x00\x00")
    # FLREG1 (BIOS): 00114000 - 00ffffff -> bytes: 14 01 ff 0f
    f.write(b"\x14\x01\xff\x0f")
    # FLREG2 (Intel ME): 00003000 - 00113fff -> bytes: 03 00 13 01
    f.write(b"\x03\x00\x13\x01")
    # FLREG3 (GbE): 00001000 - 00002fff -> bytes: 01 00 02 00
    f.write(b"\x01\x00\x02\x00")
'

ifdtool -d -p sklkbl ifd_16
ifdtool -p sklkbl -u ifd_16
mv ifd_16.new blobs/xx80/x280_ifd.bin
```

Honestly I don't totally get what the python part does but it compile and does't boot.

I will put everything that didn't get into the heads file structure in the [mess]() folderfor the future.

I won't be working in testing for a while. My SOIC 8 clip died while doing the testing and I just barely made it to flash a libreboot rom into it. I'll ask a teacher if he has one, in the case the answer is yes nothing will change a lot, but I can't promise a lot.

