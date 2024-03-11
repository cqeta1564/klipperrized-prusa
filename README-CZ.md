[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/cqeta1564)
# Základní informace
Tento projekt přeměňuje původní tiskárnu Prusa i3 MK3S+ na tiskárnu se systémem Klipper. Uživatelské rozhraní je Mainsail. Tato modifikace je bezpečná, pokud si nebudete hrát s konfigurací. Tato modifikace také není trvalá. Více informací najdete na stránce [jak se dostat zpět](https://github.com/cqeta1564/klipperrized-prusa/blob/main/README-CZ.md#jak-se-vrátit-k-původnímu-firmwaru).

Co touto úpravou tiskárny získáte. Rychlejší výtisky, lepší kvalitu a tišší provoz.

# Tutorial

## Potřebný hardware
- Nejlepší možností je použít Raspberry Pi Zero (nejlépe 2W), ale pokud dáváte přednost Raspberry Pi 2/3/4/5, lze také použít s trochou více práce a několika dalšími dráty.
- Pokud používáte Raspberry Pi Zero, budete potřebovat [samčí hlavičky](https://amzn.to/3XND883), abyste jej mohli připojit přímo k desce Einsy.
- Micro-USB na USB-B (kabel k tiskárně) nebo adaptér micro-USB, pokud již máte kabel USB-B.

## Pre-Check
- Zjistěte hodnotu posunu Z z aktuálního firmwaru (Menu -> Kalibrace -> Posun Z).

## Instalace systému Mainsail do počítače Raspberry Pi
1. Stáhněte si [Pi Imager](https://downloads.raspberrypi.org/imager/imager_latest.exe), nainstalujte jej a otevřete.

2. 
	a. Vyberte správnou desku, kterou používáte

	b. Vyberte "Zvolit OS" > "Jiný OS pro konkrétní účely" > "3D tisk" > "Mainsail OS" > vyberte 32bitovou verzi.

	 c. Vyberte "Choose Storage" > vyberte kartu SD, kterou chcete flashovat.

	d. Vyberte "Další" > "Upravit nastavení".

	e. V části "Obecné" zaškrtněte políčko "Nastavit uživatelské jméno a heslo" a vyplňte je.

	f. V části "Obecné" zaškrtněte políčko "Konfigurace bezdrátové sítě LAN", pokud plánujete používat wifi, a vyplňte jej.

	g. V části "Obecné" zaškrtněte políčko "Nastavení místního jazyka" a vyplňte jej.

	h. V části "Služby" zaškrtněte políčko "Povolit SSH" a vyberte možnost "Použít ověřování heslem".

	i. Klikněte na "Uložit" > "Ano".

3. Uživatelé Pi Zero připájejte hlavičky na Pi podle [oficiálního návodu Prusa](https://help.prusa3d.com/en/article/raspberry-pi-zero-w-preparation-and-installation_2180), ale ještě je nezapojujte do desky Einsy.

4. Vypněte tiskárnu a připojte počítač Pi k desce Einsy podle [oficiálního návodu Prusa](https://help.prusa3d.com/en/article/raspberry-pi-zero-w-preparation-and-installation_2180) nebo jej napájejte pomocí micro USB.

5. Zapněte tiskárnu a přejděte na adresu "yourHostName".local/ nebo IP adresu vašeho počítače Pi.

6. Přejděte na kartu "Stroj" a přejděte dolů na položku "Správce aktualizací". Pokud se zobrazí spousta červených tlačítek s nápisem "invalid" (neplatné) nebo "error" (chyba), dejte desce 5 minut a poté ji restartujte pomocí tlačítka napájení v pravém horním rohu rozhraní programu Mainsail.

7. Přejděte zpět do správce aktualizací, a pokud jsou tlačítka stále červená, počkejte asi 5 minut. Nakonec se vyplní. V dolní části bude tlačítko s nápisem "Aktualizovat všechny součásti" Kliknutím na něj aktualizujete všechny balíčky na nejnovější verzi.

8. Znovu restartujte počítač Pi

9. Na kartě "Machine" nahrajte soubor "printer.cfg" z mého GitHubu.

## Programování Einsy a nastavení komunikace mezi Rpi a Einsy
1. Vypněte počítač Pi v hlavní plachtě. Pokud je počítač Pi připojen k tiskárně, vypněte i ji a vyjměte počítač Pi. Připojte zdroj napájení USB k portu micro-USB na nejvzdálenějším okraji desky označeném "PWR IN". Zapojte kabel micro-USB na USB-b do prostředního portu micro-USB označeného "USB" a druhý konec připojte k základní desce Einsy. Zapněte tiskárnu, pokud ještě není zapnutá.

2. Flashování softwaru Klipper na zařízení Einsy

a. Stáhněte si [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) a připojte se k počítači Pi. Zadejte následující příkazy  

   ```yml
   cd ~/klipper/
   make menuconfig   
   ```
   
Zkompilujte firmware pro "AVR" "atmega2560". Chcete-li připojit Pi Zero přes sériové rozhraní, vyberte možnost "Enable extra low-level configuration options" (Povolit další nízkoúrovňové konfigurační možnosti) a vyberte **UART1** (sériové rozhraní RasPi) nebo **UART0**, pokud se plánujete připojit přes USB.
   Stiskněte tlačítko "q" a ukončete

   ```yml
   make
   ```

b. najděte port pro kabel USB

   ```yml
   ls /dev/serial/by-id/*
   ```

Putty vrátí něco jako "/dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0" nebo "/dev/serial/by-id/usb-Prusa_Research__prusa3d.com__Original_Prusa_i3_MK3_CZPX4820X004XK35533-if00".
	
Zkopírujte tento řádek a nahraďte jím odpovídající řádek v níže uvedeném kódu. Poté spusťte tyto příkazy v programu Putty
	
   ```yml
   sudo service klipper stop
   make flash FLASH_DEVICE=/dev/serial/by-id/usb-1a86_USB2.0-Serial-if00-port0
   sudo service klipper start
   ```

3. Restartujte tiskárnu vypnutím a opětovným zapnutím napájení.
   
4. Připraveno k tisku


## Jak používat profily PrusaSlicer
Můžete si stáhnout balíček profile.ini, ale počítejte s tím, že nebude aktuální s nejnovější konfigurací PrusaSliceru. Můžete si také nakonfigurovat vlastní profil podle tohoto návodu níže:

Použijte profil Original Prusa MK3.5 Input Shaper s vypnutým "Nastavení tisku" > "Pokročilé" > "Přizpůsobení obloukem" a nastavte "G-code rozlišení" na 0.006. V "Nastavení tiskárny" > "Obecné" nastavte "Druh G-code" na "Klipper" a "Náhledy G-codu" na "64x64/PNG, 400x300/PNG" a vypněte "Podpora binárního G-code". V části "Vlastní G-code" nastavte "Začátek G-code" na tuto hodnotu: 
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

A "Konec G-code" na tuto:
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

A "G-code před změnou vrstvy" na toto:
```yml
;BEFORE_LAYER_CHANGE
G92 E0.0
;[layer_z]
M201 X{interpolate_table(extruded_weight_total, (0,4000), (1400,2500), (10000,2500))} Y{interpolate_table(extruded_weight_total, (0,4000), (1400,2500), (10000,2500))}
```

To je vše pro rychlý profil mk3.5 nebo můžete použít jen pomalý a starý profil pro Original Prusa i3 MK3S & MK3S+ s těmito úpravami:

V "Nastavení tiskárny" > "Obecné" nastavte "Druh G-code" na "Klipper" a "Náhledy G-codu" na "64x64/PNG, 400x300/PNG". V části "Vlastní G-code" nastavte "Začátek G-code" na tuto hodnotu: 
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


## Nepovinné věci, které je dobré mít

### Klipper mesh pouze v oblasti tisku
Stačí si stáhnout můj konfigurační balíček PrusaSlicer a použít dodaný profil tiskárny, abyste mohli tuto funkci používat. Konfigurace Klipperu je již s touto funkcí nastavena.


### Překalibrování input shaperu
Další informace se chystají. Mezitím je zde oficiální průvodce klipperem: [Překalibrování input shaperu](https://www.klipper3d.org/Resonance_Compensation.html) právě pro vaši tiskárnu pomocí [ADXl345](https://amzn.to/3XMmTZa)


## Jak se vrátit k původnímu firmwaru
Stačí přeflashovat Einsy původním firmwarem pomocí PrusaSlicer, stejně jako při aktualizaci. O tiskové statistiky nepřijdete, protože jsou uloženy v paměti, kterou Klipper nepoužívá.

## Zdroje
- Výukový kurz: https://github.com/Fail-Fast-V0/klipper-prusa-mk3s/
- Soubor, ze kterého vychází moje konfigurace: https://github.com/tillin9/klipper/blob/patch-2/config/printer-prusa-mk3s-2021.cfg
- Další dobrá konfigurace: https://lab4450.com/latest-projects/revive-your-prusa-mk3s-with-klipper-2-5-compile-and-flash-klipper/
- Síťka Klipper pouze v oblasti tisku Návod k instalaci: https://gist.github.com/ChipCE/95fdbd3c2f3a064397f9610f915f7d02
