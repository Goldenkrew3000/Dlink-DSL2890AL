# Dlink-DSL2890AL
## A port of the FreshTomato Open Source Firmware to the Dlink DSL-2890AL (In a specific configuration)

## Note: You should have previous experience compiling software for/modifying routers. I am NOT going into too much depth here.

WARNING: There is a very real possibility that you WILL brick this terrible router installing this. I don't expect anyone to use this except for me because this is already an obscure router, and I am using it in an even more obscure way, but regardless, I AM NOT RESPONSIBLE. What works for me might not work for you. <br>

The patches in the ```Patches``` folder is based off of freshtomato-arm commit ```0118aba```. <br>
And no, this cannot be pushed upstream back to the freshtomato-arm project, because this router has no practical way of differentiating itself from the Dlink DIR868L (Same board identifiers and everything), but has a fairly different I/O structure. <br>

# Building
Step 1 - Install the dependencies and clone the ```freshtomato-arm``` repo at https://bitbucket.com/pedro311/freshtomato-arm <br>
Step 2 - Patch the files with the patches in the ```Patches``` directory. <br>
Step 3 - Go to the ```src-rt-6.x.4708``` directory. <br>
Step 4 - Build for this router with ```make dsl2890al```. NOTE: DO NOT use ```-j```, it WILL fail. It automatically handles this for you. <br>
Step 5 - The TRX image is in the ```image``` directory. NOTE: It will be named for the DIR868. Ignore this, I never figured out how to change the output name. <br>

# Installing
## Items Required
- 3.3v USB TTL Converter
- Ethernet Cable

Step 1 - Connect the USB TTL Converter to the Router board. <br>
On my board, the UART pins were already soldered, and the layout is (PIN, PIN, PIN, NO PIN, PIN). The pinout of that connector is (TX, GND, 3V3, NO PIN, RX). So the bord TX pin goes to your USB TTL RX. The baudrate is 115200 bps. Make sure you can see the Broadcom CFE bootloader when you power on the modem. <br>

Step 2 - Get into Broadcom CFE. <br>
Hold the CTRL + C keys until you drop into a shell that looks like ```CFE>```. <br>

Step 3 - Start a TFTP server, setup the Ethernet IP, and flash the TRX image to the board. <br>
Set your computer's ethernet settings to ```192.168.1.2``` with a gateway of ```192.168.1.1```. <br>
Then you can plug an ethernet cable from your computer directly into any port on the router. <br>
After this, use ```tftp-now``` to start a TFTP server in the same directory as the TRX file with ```sudo tftp-now serve```. <br>
Finally, on the router in the broadcom CFE environment, type ```flash -noheader 192.168.1.2:<file>.trx nflash1.trx```. <br>
After this has finished, reboot the router. It will restart a couple times to set NVRAM values, but FreshTomato is installed. <br>

The WAN port on this router is technically on 24-pin internal header, but I have by default remapped it to the LAN port closest to the USB 2 port. <br>

# Speeds
This is an old router, but the speeds are still relatively okay. <br>
Data going between the WAN and General VLAN is around 250-300mbit/s, but data travelling across the same VLAN gets pinned to a gigabit. <br>
