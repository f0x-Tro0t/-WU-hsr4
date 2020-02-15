### Beacon Me (200 points)

This is a challenge made by Vulcainreo on the HackSecuReims 2020.

###### Challenge statement:
>Same as Easter, some eggs may contain a surprise.

As well as a zip file called "beacon", which we download directly.
This archive contains the following files :
- decrypt.py
- flag1.txt.enc
- flag2.txt.enc

We suspect that the file will be used to decipher the files, let's take a look at this code.

```Python
def decrypt_file(key, in_filename, out_filename=None, chunksize=24*1024):
    """ Decrypts a file using AES (CBC mode) with the
        given key. Parameters are similar to encrypt_file,
        with one difference: out_filename, if not supplied
        will be in_filename without its last extension
        (i.e. if in_filename is 'aaa.zip.enc' then
        out_filename will be 'aaa.zip')
    """
    if not out_filename:
        out_filename = os.path.splitext(in_filename)[0]

    with open(in_filename, 'rb') as infile:
        origsize = struct.unpack('<Q', infile.read(struct.calcsize('Q')))[0]
        iv = infile.read(16)
        decryptor = AES.new(key, AES.MODE_CBC, iv)

        with open(out_filename, 'wb') as outfile:
            while True:
                chunk = infile.read(chunksize)
                if len(chunk) == 0:
                    break
                outfile.write(decryptor.decrypt(chunk))

            outfile.truncate(origsize)

# Arg1 is the key. The format is : AA1122334455667788 (16 hex)
# Arg2 : the file that you want to decrypt
```

So it's a 16-character AES encryption. Okay, we're talking about beacon here, how about we scan the room to see if any beacons might have been slipped in.
I use the android application "Beacon Scanner", and... Bingo ! 4 beacons.
(I'll put the screenshot of the four beacons in the folder)
The UUIDs are fixed and therefore not interesting a priori, on the other hand minor and major can be set by the user in order to differentiate tags with the same UUID.

Now we have 4 combinations of major and minor:
- 22067,34242
- 50594,25983
- 58229,50214
- 44383,10437

These values are not convenient because they do not allow to make lines of 16 characters, if we pass the values in hexadecimal and we concatenate them, we obtain on the other hand 4 strings of characters of 8 bytes.
Thus, we've got this:
- 563385C2
- C5A2657F
- E375C426
- AD5F28C5

We have 4*4 possibilities, I can't be bothered to write a script for this, let's do it by hand.


```bash
python decrypt.py AD5F28C5E375C426 flag1.txt.enc
cat flag1.txt
>HSR{BeAcOn..........
```

```bash
python decrypt.py 563385C2C5A2657F flag2.txt.enc
cat flag2.txt
>..........SaReFuNnY}
```
Done !
