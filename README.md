[![noswpatv3](http://zoobab.wdfiles.com/local--files/start/noupcv3.jpg)](https://ffii.org/donate-now-to-save-europe-from-software-patents-says-ffii/)
[![noswpatv3](http://zoobab.wdfiles.com/local--files/start/noupcv3.jpg)](https://ffii.org/donate-now-to-save-europe-from-software-patents-says-ffii/)
About
=====

This is a copy of CH341PAR_LINUX.ZIP published by WCH:

http://www.wch.cn/download/CH341PAR_LINUX_ZIP.html

This is a parallel port driver (EPP, MEM) for ch341 chip.

Screenshots
===========

Everybody love screenshots.

GPIO tab:

![CH341 GPIO tab](https://raw.githubusercontent.com/zoobab/ch341-parport/master/ch341-gpio.png)

MEM tab:

![CH341 MEM tab](https://raw.githubusercontent.com/zoobab/ch341-parport/master/ch341-parport-mem.png)

How to toggle the D5 GPIO
=========================

First of all, you have to connect the 2 pins SDA+GND together, for me I did it with a jumper on my Electrodragon board (http://www.zoobab.com/ch341-usb-spi-i2c-uart-isp-dongle#toc5). You should see the board in EPP/I2C mode with the USB VID:PID 1a86:5512, with lsusb output:

```
root@zoobab-ThinkPad-X200:~/ch341-parport/CH341PAR_LINUX/demo# lsusb 
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 008 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 007 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 006 Device 003: ID 093a:2510 Pixart Imaging, Inc. Optical Mouse
Bus 006 Device 023: ID 1a86:5512 QinHeng Electronics CH341 in EPP/MEM/I2C mode, EPP/I2C adapter
```

You should then load the kernel module:
```
root@zoobab-ThinkPad-X200:~/ch341-parport/CH341PAR_LINUX/driver# insmod ./ch34x_pis.ko
root@zoobab-ThinkPad-X200:~/ch341-parport/CH341PAR_LINUX/driver# dmesg
[...]
[ 5711.969691] /home/zoobab/ch341-parport/CH341PAR_LINUX/driver/ch34x_pis.c 1119: CH34x_pis-0 now disconnected
[ 5713.322777] /home/zoobab/ch341-parport/CH341PAR_LINUX/driver/ch34x_pis.c 1037: Found a bulk in endpoint
[ 5713.322787] /home/zoobab/ch341-parport/CH341PAR_LINUX/driver/ch34x_pis.c 1046: Found a bulk out endpoint
[ 5713.322791] /home/zoobab/ch341-parport/CH341PAR_LINUX/driver/ch34x_pis.c 1053: Found a interrupt in endpoint
[ 5713.322963] /home/zoobab/ch341-parport/CH341PAR_LINUX/driver/ch34x_pis.c 1069: Ch34x_pis device now attached to ch34x_pis-0
[ 5713.323049] usbcore: registered new interface driver ch34x_pis
```

You should see a new device in /dev called /dev/ch34x_pis0:
```
root@zoobab-ThinkPad-X200:~/ch341-parport/CH341PAR_LINUX/driver# ls -l /dev/ch34x_pis*
lrwxrwxrwx 1 root root     10 apr 13 20:18 /dev/ch34x_pis -> ch34x_pis0
crw------- 1 root root 180, 0 apr 13 21:47 /dev/ch34x_pis0
```
Note that I had to make a symlink from /dev/ch34x_pis to /dev/ch34x_pis0 to get the CLI demo app working.

You can set the pin D5 HIGH with 0x60, and LOW with 0x50:
```
root@zoobab-ThinkPad-X200:~/ch341-parport/CH341PAR_LINUX/demo# ./DEMO
Open sucessful
drv_version : WCH CH34x Driver Version V0.1
VendorId : 0x0030
This is main menu listed
-->1: EEPROM TEST
-->2: EPP TEST
-->3: MEM TEST
-->4: SPI FLASH TEST( SST25VF512 )
-->5: SETOUTPUT TEST
-->6: SET_D5_D0 TEST
Please enter your selection:
6
You choose 6 
input the direction data(Hex):
0x3F
input the control data(Hex):
0x60
ByteBuffer:ab a0 7f 20 Write.Lenth:4 

enter 'q' to exit or 'b' to come back
b
This is main menu listed
-->1: EEPROM TEST
-->2: EPP TEST
-->3: MEM TEST
-->4: SPI FLASH TEST( SST25VF512 )
-->5: SETOUTPUT TEST
-->6: SET_D5_D0 TEST
Please enter your selection:
6
You choose 6 
input the direction data(Hex):
0x3F
input the control data(Hex):
0x50
ByteBuffer:ab 90 7f 20 Write.Lenth:4 

enter 'q' to exit or 'b' to come back
```

Now I have to find the other values for the other pins D0-D7.

* D4: HIGH=0x10 LOW=0x20
* D3: HIGH=0x   LOW=0x
* D2: HIGH=0x   LOW=0x
* D1: HIGH=0x   LOW=0x
* D0: HIGH=0x   LOW=0x

Todo
====

* compile it on different kernel versions
** works on Ubuntu 16.04
* find how to toggle D0-D7 pins in GPIO mode
* sniff USB packets and make a libusb tool
