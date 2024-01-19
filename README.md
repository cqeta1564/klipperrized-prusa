[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/cqeta1564)
# Tutorial
Tutorial can be found [here](https://github.com/Fail-Fast-V0/klipper-prusa-mk3s).
# Config based on
The file from which my configuration is based can be found [here](https://github.com/tillin9/klipper/blob/patch-2/config/printer-prusa-mk3s-2021.cfg).
# Install my config 
### EN
Do not copy any files as stated in the instructions. Instead, copy the printer.cfg file from my github. The configuration from the instructions heats up the engines a lot, which is not what you want. My config is safe because I use it too, but I still recommend going through and reading the file. The file needs no editing if you plan to use Raspberry Pi Zero (2) W (H). If you want to use the usb port on the motherboard to communicate, you will need to change the communication address in the file. You can also edit the configuration, but be careful and make sure you know what you are doing. 

Download the config.ini file and import it into PrusaSlicer. It is a configuration modified for Klipper needs. You can use other profiles, but stick to the modified settings from this profile.

If you plan to use the Raspberry Pi Zero W (H), keep in mind that you will need to overclock it as it does not have enough power for the "G2" and "G3" calculations.

```
sudo nano /boot/config.txt

arm_freq=1085
#arm_freq=1095 with heatsink
gpu_freq=530
#gpu_freq=550 with heatsink.
over_voltage=2
core_freq=515
sdram_freq=533
over_voltage_sdram=1

Ctrl + x , Y, enter
```

Perform a PID calibration before the first print:

```
PID_CALIBRATE HEATER=extruder TARGET=215

SAVE_CONFIG

PID_CALIBRATE HEATER=heater_bed TARGET=60

SAVE_CONFIG
```

I recommend checking out these things as well:
  1) Sanity check and PID Tuning: https://www.klipper3d.org/Config_checks.html
  2) Pressure Advance: https://www.klipper3d.org/Pressure_Advance.html
  3) Skew Correction: https://www.klipper3d.org/skew_correction.html
  4) Resonance Compensation: https://www.klipper3d.org/Resonance_Compensation.html

Happy printing!


### CZ
Nekopírujte žádné soubory, jak je uvedeno v pokynech. Místo toho zkopírujte soubor printer.cfg z mého githubu. Konfigurace z návodu motory hodně zahřívá, což není to, co chcete. Moje konfigurace je bezpečná, protože ji také používám, ale přesto doporučuji soubor projít a přečíst. Soubor není třeba nijak upravovat, pokud plánujete používat Raspberry Pi Zero (2) W (H). Pokud chcete ke komunikaci používat usb port na základní desce, budete muset v souboru změnit komunikační adresu. Konfiguraci můžete také upravit, ale buďte opatrní a ujistěte se, že víte, co děláte. 

Stáhněte si soubor config.ini a importujte jej do PrusaSliceru. Je to konfigurace upravená pro potřeby Klipperu. Můžete použít i jiné profily, ale držte se upraveného nastavení z tohoto profilu.

Pokud plánujete použít Raspberry Pi Zero W (H), mějte na paměti, že jej budete muset přetaktovat, protože nemá dostatečný výkon pro výpočet "G2" a "G3".

```
sudo nano /boot/config.txt

arm_freq=1085
#arm_freq=1095 with heatsink
gpu_freq=530
#gpu_freq=550 with heatsink.
over_voltage=2
core_freq=515
sdram_freq=533
over_voltage_sdram=1

Ctrl + x , Y, enter
```

Před prvním tiskem proveďte PID kalibraci:

```
PID_CALIBRATE HEATER=extruder TARGET=215

SAVE_CONFIG

PID_CALIBRATE HEATER=heater_bed TARGET=60

SAVE_CONFIG
```

Doporučuji se kouknout i na tyto věci:
  1) Sanity check and PID Tuning: https://www.klipper3d.org/Config_checks.html
  2) Pressure Advance: https://www.klipper3d.org/Pressure_Advance.html
  3) Skew Correction: https://www.klipper3d.org/skew_correction.html
  4) Resonance Compensation: https://www.klipper3d.org/Resonance_Compensation.html

Tisku zdar!

# ToDo
- [ ] Recalibrate homing speed and sensitivity
- [ ] Calibrate PA for PETG
- [ ] Finish config for PrusaSlicer
