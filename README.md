# DIY WiFi-Pineapple
Homemade WiFi Pineapple for under $50

![AR300M](https://raw.githubusercontent.com/0x29aNull/WiFi-Pineapple/refs/heads/main/GLINETAR300.jpg)

Since Hak5 came out with their WiFi Pineapple I've always wanted one but never really wanted to pay the price. I am however a DIY'er and when I learned that you can create one that could fit in the palm of your hand I knew I had to make one.

## The router
Of course, the main part of this build is the router which is the GL iNet AR300M-16 "Shadow" which can be found on Amazon for around $30. [Click here](https://a.co/d/4ouoOg0) to go to the Amazon listing. The AR300M has a MIPS 24kc CPU which is the same CPU as the WiFi Pineapple.

## The firmware
I tried compiling the "Sweet Pineapple Builder" by Samy Youni but that did not seem to work for me. I found that xchwarze on github had a cloning method that worked much better. I've put the compiled binary in this git [Click Here](https://github.com/0x29aNull/WiFi-Pineapple/raw/main/gl-ar300m-universal-sysupgrade.bin) to download the WiFi Pineapple Tetra firmware.   
NOTE: This firmware is ONLY for the GL iNet AR300M16 - It will soft brick the AR300 and potentially the AR300M.

## The install
This is a fairly easy and straight forward setup. Plug in and power up your AR300M-16 and wait for it to boot. After a few minutes look for the "GL-INET-300M" (or similar) network and connect. Open a web browser and navigate to http://192.168.8.1 to setup the router. The router's setup will ask you to enter a new root password but this password is just temporary. After you get to the main page of the router open your terminal of choice and SSH into the router.

```ssh root@192.168.8.1```

On the first connect you will be asked if you're sure you want to connect, Press "Y" and press enter. Once you've verified that you can connect and login, exit the SSH session by typing ```exit```. Navigate to the directory where you've downloaded the firmware and use scp to upload the firmware to the router

```scp gl-ar300m-universal-sysupgrade.bin root@192.168.8.1:/tmp/```

Enter your root password and wait for the firmware upload to complete. After the file has been successfully been copied to the router use SSH to connect to the router once again and give the command to start the upgrade.

```
ssh root@192.168.8.1
sysupgrade -n -F /tmp/gl-ar300m-universal-sysupgrade.bin
```

The router will now kick you off and start the upgrade process. You will notice that the LEDs will flash for a few minutes. Once you see that the first 2 LEDs are solid green look for an open "Pineapple" network. Mine was "Pineapple_E800" your may be different.

## The setup
Depending on your OS you may be able to navigate to the WiFi Pineapple management page without making changes to your network adapters. Open a browser and navigate to http://172.16.42.1:1471 to start the setup and configuration process. Should you get a "Not Found" error or anything other than the WiFi Pineapple logo, follow these instructions.

### Windows
1. Open the Control Panel and click on "Network and Sharing Center"
2. In the left pane click "Change Adapter Settings"
3. Find your WiFi network adapter, right click on it, and select "Properties"
4. Click "Internet Protocol Version 4 (TCP/IPv4)" then click the "Properties" button
5. Select "Use the following IP Address" and enter the following information  
  a. IP Address: 177.16.42.69  
  b. Subnet Mask: 255.255.0.0  
  c. Default Gateway: 172.16.42.1  
7. Click "OK"
8. Press the refresh button in your browser to load the management page.

### macOS
1. Open Settings
2. Click on Network Settings
3. Click on your interface
4. Select the "Details" button
5. Go to the TCP/IP tab
6. Select the Configure IPv4 dropdown menu & select "Manually"
7. IP Address: 172.16.42.69
8. Netmask: 255.255.0.0
9. Router/Gateway: 172.16.42.1

### Linux
Since there are many different Linux variants which have many different ways of configuring your network interfaces I will post instructions for my
favorite distribution, [Void Linux](https://www.voidlinux.org). Please lookup instructions for your Linux distro.

1. get the interface name ```ip link show``` (we will use the interface name "wlan0" for these instructions)
2. type the following commands..
3. ```sudo ip addr add 172.16.42.69/24 dev wlan0```
4. ```sudo ip link set wlan0 up```
5. ```sudo ip route add default via 172.16.42.1```
6. Verify the settings with ```ip addr show && ip route show```

The page will ask you to hold the side button for 2 or more seconds to enable management via WiFi. At this point I would suggest that you get a usb hub, a minimum 16gb USB drive and an additional USB WiFi Dongle and plug both the drive and the WiFi Dongle in to the hub and the hub into the USB port on the side of the router. 

## The USB Drive
The router doesn't have much room for anything beyond the bare WiFi Pineapple firmware so we need a USB drive to act as the SD Card that a normal WiFi Pineapple would have. While a FAT32 formatted USB Drive would work, I suggest formatting it in the Linux native format of EXT4. To do this in Windows you will need a tool like MiniTool by Partitionwizard [Click Here](https://www.partitionwizard.com/free-partition-manager.html) to download. 

### Linux
1. Open terminal
2. Find the disk by typing ```lsblk``` look for the drive with the same amount of storage, it is usually ```/dev/sda```
3. Partition the drive ```cfdisk /dev/sdaX```
4. [Click Here](https://www.geeksforgeeks.org/cfdisk-command-in-linux-with-examples/) for instructions on how to use CFDisk.
5. Save your change in CFDisk and exit
6. Create the file system on the drive ```mkfs.ext4 /dev/sdaX``` 

## The WiFi Dongle
I had a spare Ralink (MediaTek) MT7601 WiFi dongle laying around [Click Here](https://a.co/d/cBMD0uD) for a 2 pack (these are the antenna-less version but any Ralink dongle should work) and all in order for it to work I needed to first identify the chipset and install the kernel mod so that it is supported.
```lsusb```
```opkg install kmod-mt7601u```


## The USB Hub
Since the router only has 1 USB port you will need a 2 port hub for the WiFi dongle and USB Drive. I found a cheap $5 four port hub at a five below store. I used a hand saw to cut the additional 2 ports off and it still worked just fine after, This may or may not be the case for whatever hub you choose.

## The bricked router
There is the possibility of bricking your router if you use a firmware for a different device or a firmware that is corrupted/incomplete. There is a fix for this! First, [Click Here](https://dl.gl-inet.com/router/ar300m/) to download the latest firmware for your device. Unplug your router from power and plug an ethernet cable in to either port on the router and plug the other end of the ethernet cable in to your PC (Make sure there are no other ethernet cables plugged in to the router). Following the directions above on setting your network interface settings to access the WiFi Pineapple this time we want to select the ethernet port instead of the WiFi and set the IPv4 settings to the following..

1. IP Address: 192.168.1.2
2. Netmask: 255.255.255.0
3. Gateway: 192.168.1.1

Once all of that is in place hold down the side button on the router and plug the power into the router. Wait for the red LED to blink 5 times. Wait 1 second after the 5th blink and release the button. Now open a browser and navigate to http://192.168.1.1 you should see a UBoot Firmware update page. Move the switch on the side of the router to the right position then select "Enable switch" and press accept then select your firmware and start the restore.
