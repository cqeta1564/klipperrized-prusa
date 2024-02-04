[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/cqeta1564)
# Základní informace
Tento projekt přeměňuje původní tiskárnu Prusa i3 MK3S+ na tiskárnu se systémem Klipper. Uživatelské rozhraní je Mainsail. Tato modifikace je bezpečná, pokud si nebudete hrát s konfigurací. Tato modifikace také není trvalá. Více informací najdete na stránce [jak se dostat zpět](https://github.com/cqeta1564/klipperrized-prusa/blob/CZ-ReadMe/CZ-README.md#jak-se-vratit-k-puvodnimu-firmwaru).

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
- Síťka Klipper pouze v oblasti tisku Návod k instalaci: https://gist.github.com/ChipCE/95fdbd3c2f3a064397f9610f915f7d02
