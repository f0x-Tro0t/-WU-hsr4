### OSINT Harware (100 points)

This is a challenge made by Vulcainreo on the HackSecuReims 2020. This chall use his DVID board, you can find more information about this project here : https://github.com/Vulcainreo/DVID

###### Challenge statement:
>This black board wants to tell you a secret,maybe a password.
The flag is : HSR{password}

###### Explainations:

We've got :
- The DVID board (with an ATmega328p) and an oled screen
- 3 female-female jumper wires
- 1 male-female jumper wire
- USB to TTL module

First, we can see that we're not going to be able to use the uart as usual because we are lacking in female-female jumper wires. So I'm just trying to power up the dvid with 5v to see if the oled screen shows anything, as follows :

- USB +5v <-----> DVID power 5v,
- USB GND <-----> DVID regulator GND

Bingo, we've got a message, I don't remember exactly, but he was pointing the AIN1 pin.
Now we have to find on the datasheet of the ATmega328p where the pin AIN1 is, as we can see in the attached file, it corresponds to pin 13.
We then look in the documents provided by vulcainreo on his github, we find the gerber type files that allow us to see that pine 13 is attached to the RX of the #EP1-BLE zone.

So we can connect like this:

- USB +5v <-----> DVID power 5v
- USB GND <-----> DVID regulator GND
- USB RX  <-----  DVID BLE RX

Then we open a serial console

```bash
screen /dev/ttyUSB0 9600
```

And we've got the flag !!
