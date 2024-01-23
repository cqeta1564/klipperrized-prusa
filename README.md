[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/cqeta1564)
# Basic info
This project transforms original prusa i3 MK3S+ to printer running klipper. The user interface is mainsail. This mod is safe unless you play with the config. This mod is also not permanent. More info at **how to get back**.

What you will get from doing this modification to your printer. Faster prints, better quality and quieter operations.

# Tutorial

## Hardware needed
- The best option is to use a Raspberry Pi Zero (2W preferred) but if you prefer Raspberry Pi 2/3/4 can also be used with a bit more work and a few more wires
- If using Raspberry Pi Zero you'll need [male headers](https://amzn.to/3XND883) so can mount directly to einsy board
- Micro-usb to usb-b (printer cable) or a micro-usb adapter if you already have a usb-b cable

## Pre-Check
- Get Z offset value from your current firmware (Menu -> Calibration -> Z-offset)

## Installing Mainsail on your Raspberry Pi
1. Download [Pi Imager](https://downloads.raspberrypi.org/imager/imager_latest.exe), install it and open it
2. 
   a. Select the right board that you are using
   
   b. Select "Choose OS" > "Other specific-purpose OS" > "3D Printing" > "Mainsail OS" > select the 32 bit version
   
   c. Select "Choose Storage" > select the sd card that you want to flash
   
   d. Select "Next" > "Edit Settings"

   e. In "General" tick "Set username and password" and fill it out

   f. In "General" tick "Configure wireless LAN" if you plan on using wifi and fill it out

   g. In "General" tick "Set locale settings" and fill it out

   h. In "Services" tick "Enable SSH" and choose "Use pasword authentication"

   i. Click "Save" > "Yes"

4. For Pi Zero users, solder the headers onto the Pi per [Prusa's official instructions](https://help.prusa3d.com/en/article/raspberry-pi-zero-w-preparation-and-installation_2180) but do not plug it into the einsy board yet

5. Power off your printer and plug in your Pi to the einsy board per [Prusa's official instructions](https://help.prusa3d.com/en/article/raspberry-pi-zero-w-preparation-and-installation_2180) or power it with a micro USB

7. Power on your printer and navigate to "yourHostName".local/ or ip address of your Pi

8. Navigate to the "Machine" tab and scroll down the "Update Manager" if you see a lot of red buttons that say "invalid" or "error" then give the board 5 minutes and then reboot the board using the power button in the top right of the Mainsail interface

9. Navigate back to the update manager and if the buttons are still red, then wait about 5 minutes. They will eventually populate. There will be a button at the bottom that says "Update All Components" Click this to update all packages to the latest

10. Reboot the Pi again

11. In the "Machine" tab, upload "printer.cfg" from my GitHub

## Flashing Einsy and Setting up Communications between Rpi and Einsy
1. Power down your Pi in mainsail. If your Pi is plugged into your printer power that down too and remove the Pi. Connect the a USB power source to the micro-usb port on the furthest edge of the board labeled "PWR IN". Plug your micro-usb to usb-b cable in the center micro-usb port labeled "USB" and then plug the other end into your Einsy mainboard. Turn your printer on if it isn't already. 

2. To flash klipper onto your einsy

   a. Download [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and connect to your Pi. Enter the following commands  

   ```yml
   cd ~/klipper/
   make menuconfig   
   ```
   
   Compile the firmware for "AVR" "atmega2560". To connect your Pi Zero over serial, select "Enable extra low-level configuration options" and select **UART1** (the RasPi serial) or **UART0** when you plan to connect via the USB.
   Press "q" and exit

   ```yml
   make
   ```

   b. find the port for the usb cable

   ```yml
   ls /dev/serial/by-id/*
   ```

   Putty will return something like "/dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0" or "/dev/serial/by-id/usb-Prusa_Research__prusa3d.com__Original_Prusa_i3_MK3_CZPX4820X004XK35533-if00"
	
   Copy this line and replace the corresponding line in the code below. Then run those commands in Putty
	
   ```yml
   sudo service klipper stop
   make flash FLASH_DEVICE=/dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0
   sudo service klipper start
   ```

3. Restart the printer by turning the power supply off and on againg
   
4. Print

## Optional things that are nice to have
- [Klipper mesh on print area only install guide](https://gist.github.com/ChipCE/95fdbd3c2f3a064397f9610f915f7d02)
- [Recalibrating input shaper](https://www.klipper3d.org/Resonance_Compensation.html) just for your printer using [adxl345](https://amzn.to/3XMmTZa) 


## How to get back to original firmware
Just reflash the Einsy with original firmware using PrusaSlicer just like you would when upgrading it. You will not loose print statistic because they are stored in memory that is not used by klipper.

## Sources
- Tutorial: https://github.com/Fail-Fast-V0/klipper-prusa-mk3s/
- The file from which my configuration is based on: https://github.com/tillin9/klipper/blob/patch-2/config/printer-prusa-mk3s-2021.cfg
