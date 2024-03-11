[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/cqeta1564)
# Basic info
This project transforms the original Prusa i3 MK3S+ into a printer running Klipper. The user interface is Mainsail. This mod is safe unless you play with the config. This mod is also not permanent. More information on [how to get back](https://github.com/cqeta1564/klipperrized-prusa/blob/main/README.md#how-to-get-back-to-the-original-firmware).

What you will get from doing this modification to your printer. Faster prints, better quality, and quieter operations.

# Tutorial

## Hardware needed
- The best option is to use a Raspberry Pi Zero (2W preferred), but if you prefer Raspberry Pi 2/3/4/5 can also be used with a bit more work and a few more wires
- If using Raspberry Pi Zero, you'll need [male headers](https://amzn.to/3XND883) so it can mount directly to the Einsy board
- Micro-USB to USB-B (printer cable) or a micro-USB adapter if you already have a USB-B cable

## Pre-Check
- Get the Z offset value from your current firmware (Menu -> Calibration -> Z-offset)

## Installing Mainsail on your Raspberry Pi
1. Download [Pi Imager](https://downloads.raspberrypi.org/imager/imager_latest.exe), install it, and open it

2. 
	a. Select the right board that you are using

	b. Select "Choose OS" > "Other specific-purpose OS" > "3D Printing" > "Mainsail OS" > select the 32-bit version

 	c. Select "Choose Storage" > select the SD card that you want to flash

	d. Select "Next" > "Edit Settings"

	e. In "General" tick "Set username and password" and fill it out

	f. In "General" tick "Configure wireless LAN" if you plan on using wifi and fill it out

	g. In "General" tick "Set locale settings" and fill it out

	h. In "Services" tick "Enable SSH" and choose "Use password authentication"

	i. Click "Save" > "Yes"

3. For Pi Zero users, solder the headers onto the Pi per [Prusa's official instructions](https://help.prusa3d.com/en/article/raspberry-pi-zero-w-preparation-and-installation_2180), but do not plug it into the Einsy board yet

4. Power off your printer and plug in your Pi to the Einsy board per [Prusa's official instructions](https://help.prusa3d.com/en/article/raspberry-pi-zero-w-preparation-and-installation_2180), or power it with a micro USB

5. Power on your printer and navigate to "yourHostName".local/ or IP address of your Pi

6. Navigate to the "Machine" tab and scroll down to "Update Manager". If you see a lot of red buttons that say "invalid" or "error", then give the board 5 minutes and then reboot the board using the power button in the top right corner of the Mainsail interface

7. Navigate back to the update manager, and if the buttons are still red, then wait about 5 minutes. They will eventually populate. There will be a button at the bottom that says "Update All Components" Click this to update all packages to the latest

8. Reboot the Pi again

9. In the "Machine" tab, upload "printer.cfg" from my GitHub

## Flashing Einsy and Setting up Communications between Rpi and Einsy
1. Power down your Pi in Mainsail. If your Pi is plugged into your printer, power that down too and remove the Pi. Connect the USB power source to the micro-USB port on the furthest edge of the board labelled "PWR IN". Plug your micro-USB to USB-b cable in the centre micro-USB port labelled "USB", and then plug the other end into your Einsy mainboard. Turn your printer on if it isn't already.

2. To flash Klipper onto your Einsy

a. Download [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and connect to your Pi. Enter the following commands  

   ```yml
   cd ~/klipper/
   make menuconfig   
   ```
   
Compile the firmware for "AVR" "atmega2560". To connect your Pi Zero over serial, select "Enable extra low-level configuration options" and select **UART1** (the RasPi serial) or **UART0** when you plan to connect via the USB.
   Press "q" and exit

   ```yml
   make
   ```

b. find the port for the USB cable

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
   
4. Ready to print

## How to use PrusaSlicer profiles
You can download profile.ini bundle but be aware that it wont be up to date with newest PrusaSlicer configs. You can also configure your own profile by this tutorial below:

Use profile for Original Prusa MK3.5 Input Shaper with "Print Settings" > "Advanced"  > "Arc fitting" turned off and set "G-code resolution to 0.006. In "Printer Settings" > "General" set "G-code flavor" to "Klipper" and "G-code thumbnail" to "64x64/PNG, 400x300/PNG" and turn off "Supports binary G-code". In "Custom G-code" set "Start G-code" to this: 
```yml
G90 ; use absolute coordinates
M83 ; extruder relative mode

M140 S[first_layer_bed_temperature] ; set bed temp
M104 S170 ; set extruder temp for bed leveling
M109 S170 ; wait for temp
M190 S[first_layer_bed_temperature] ; wait for bed temp

G28 ; home all

BED_MESH_CALIBRATE AREA_START={first_layer_print_min[0]},{first_layer_print_min[1]} AREA_END={first_layer_print_max[0]},{first_layer_print_max[1]}

; prepare for purge
M104 S{first_layer_temperature[0]}
G0 X0 Y-4 Z15 F4800 ; move away and ready for the purge
M109 S{first_layer_temperature[0]}

; Extrude purge line

G92 E0 ; reset extruder position
G0 E7 X15 Z0.2 F500 ; purge
G0 X25 E4 F500 ; purge
G0 X35 E4 F650 ; purge
G0 X45 E4 F800 ; purge
G0 X{45 + 3} Z0.05 F8000 ; wipe, move close to the bed
G0 X{45 + 3 * 2} Z0.2 F8000 ; wipe, move quickly away from the bed

G92 E0
M221 S100 ; reset flow to 100%
```

And "End G-code" to this:
```yml
{if layer_z < max_print_height}G1 Z{z_offset+min(layer_z+1, max_print_height)} F720 ; Move print head up{endif}
M104 S0 ; turn off temperature
M140 S0 ; turn off heatbed
M107 ; turn off fan
G1 X241 Y201 F3600 ; park
{if layer_z < max_print_height}G1 Z{z_offset+min(layer_z+23, max_print_height)} F300 ; Move print head up{endif}
G4 ; wait
M572 S0 ; reset PA
M84 X Y E ; disable motors
; max_layer_z = [max_layer_z]
```

And "Before layer change G-code" to this:
```yml
;BEFORE_LAYER_CHANGE
G92 E0.0
;[layer_z]
M201 X{interpolate_table(extruded_weight_total, (0,4000), (1400,2500), (10000,2500))} Y{interpolate_table(extruded_weight_total, (0,4000), (1400,2500), (10000,2500))}
```


That is all for the fast mk3.5 profile or you can just use slow and old profile for Original Prusa i3 MK3S & MK3S+ with these modifications:

In "Printer Settings" > "General" set "G-code flavor" to "Klipper" and "G-code thumbnail" to "64x64/PNG, 400x300/PNG". In "Custom G-code" set "Start G-code" to this: 

```yml
G90 ; use absolute coordinates
M83 ; extruder relative mode
M140 S[first_layer_bed_temperature] ; set bed temp
M190 S[first_layer_bed_temperature] ; wait for bed temp
G28 W ; home all without mesh bed level
BED_MESH_CALIBRATE AREA_START={first_layer_print_min[0]},{first_layer_print_min[1]} AREA_END={first_layer_print_max[0]},{first_layer_print_max[1]}
{if filament_settings_id[initial_tool]=~/.*Prusament PA11.*/}
G1 Z0.3 F720
G1 Y-3 F1000 ; go outside print area
M104 S[first_layer_temperature] ; set extruder temp
M109 S[first_layer_temperature] ; wait for extruder temp
G92 E0
G1 X60 E9 F1000 ; intro line
G1 X100 E9 F1000 ; intro line
{else}
G1 Z0.2 F720
G1 Y-3 F1000 ; go outside print area
M104 S[first_layer_temperature] ; set extruder temp
M109 S[first_layer_temperature] ; wait for extruder temp
G92 E0
G1 X60 E9 F1000 ; intro line
G1 X100 E12.5 F1000 ; intro line
{endif}
G92 E0
M221 S{if layer_height<0.075}100{else}95{endif}
```


## Optional things that are nice to have 

### Klipper mesh on the print area only
Just download my PrusaSlicer configuration package and use the provided printer profile to use this feature. The Klipper configuration is already set up with this feature.


### Recalibrating input shaper
More info is comming. In the meantime, here is oficial klipper guide: [Recalibrating input shaper](https://www.klipper3d.org/Resonance_Compensation.html) just for your printer using [ADXl345](https://amzn.to/3XMmTZa)


## How to get back to the original firmware
Just reflash the Einsy with the original firmware using PrusaSlicer, just like you would when upgrading it. You will not lose print statistics because they are stored in memory that is not used by Klipper.

## Sources
- Tutorial: https://github.com/Fail-Fast-V0/klipper-prusa-mk3s/
- The file from which my configuration is based on: https://github.com/tillin9/klipper/blob/patch-2/config/printer-prusa-mk3s-2021.cfg
- Another good configuration: https://lab4450.com/latest-projects/revive-your-prusa-mk3s-with-klipper-2-5-compile-and-flash-klipper/
- Klipper mesh on the print area only install guide: https://gist.github.com/ChipCE/95fdbd3c2f3a064397f9610f915f7d02 
