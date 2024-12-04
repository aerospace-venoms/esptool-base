# README

This project is a Poetry shell I use for running the esptool utility. If you're using it on Linux, all you need to do is have Poetry setup and run `Poetry Shell`, then skip all that and proceed to 'flashing micropython section.

If you're running this in Windows 11/WSL2 like I am, there are some additional setup steps below to passthrough your device to the WSL2 guest Linux. Ignore these if you're on pure linux.

## Setup Instructions
I followed this guide: https://hackmd.io/@aeefs2Y8TMms-cjTDX4cfw/r1fqAa_Da


### One-time Setup:

Install usbipd on your windows host: https://github.com/dorssel/usbipd-win/releases/tag/v4.0.0

On your WSL guest, `sudo apt install util-linux-extra` to get the hwclock utility
`sudo hwclock -s` to sync the hwclocks between windows/wsl. Required probably every time the windows host sleeps.
`sudo apt install linux-tools-generic hwdata usb-utils
sudo update-alternatives --install /usr/local/bin/usbip usbip /usr/lib/linux-tools/*-generic/usbip 20`

Plug the device into your windows host. 
In an Admin windows powershell, run `usbipd list` and find your device. Likely called something like "serial JTAG device" or similar. Note down the BUSID.
Bind the device with `usbipd bind --busid=2-4` or similar, depending on your busid

### Every-time setup

You will need to do these steps every time you plug/unplug your ESP device to set it up again:
From your Admin windows powershell, `usbipd attach --wsl --busid=2-4` or similar, depending on your busid from above.

In WSL, run `lsusb` to verify that your device got passed through. You should also be able to see it in /dev/ttyACM* or /dev/ttyUSB* or similar. 

## Flashing Micropython:
I've already included a micropython image on this repo, in the root dir. You can program it to your device with:

`esptool.py --chip esp32c6 --port /dev/ttyACM0 erase_flash`
`esptool.py --chip esp32c6 --port /dev/ttyACM0 --baud 460800 write_flash -z 0x0 esp32c6-20241025-v1.24.0.bin`

If you want a fresher or different version of micropython, you can get hem from micropython.org/download/esp32c6/. 
