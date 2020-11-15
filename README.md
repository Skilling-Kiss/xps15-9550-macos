中文 README 请看 [README_CN.md](README_CN.md)。

## Configuration

| Model     | XPS15-9550/MacBookPro13,3  | Version        | 10.15.7 19H2        |
| :-------- | :------------------------- | :------------- | :------------------ |
| Processor | Intel Core i5-6300HQ       | Graphics       | HD Graphics 530     |
| Memory    | Micron 2133MHz DDR4 8GB x2 | Storage        | Samsung PM961 512GB |
| Audio     | Realtek ALC298             | WiFi/Bluetooth | Dell Wireless 1830  |
| Display   | Sharp LQ156D1 UHD          | Monitor        | HKC GF40 FHD 144Hz  |

### Not Working

- Discrete GPU
- Thunderbolt
- Bluetooth may not work ([explain](https://github.com/xxxzc/xps15-9570-macos/issues/26))

## Installation

**Please use [the latest release](https://github.com/xxxzc/xps15-9550-macos/releases/latest).** 

- INTEL: Intel Wireless Card
- BRCM: Broadcom Wireless Card/Dell Wireless Card

### Intel Wireless Card

This config supports Intel Wireless Card, but the default `AirportItlwm.kext` is for Catalina, if you are running Big Sur or other versions of macOS, you have to replace the default one from [OpenIntelWireless/itlwm](https://github.com/OpenIntelWireless/itlwm/releases).

### Big Sur

OpenCore in latest release can OTA to/install Big Sur, **but** installing Big Sur on some Samsung SSDs like SM961/PM961 is very likely to fail (I have tried many kinds of configuration but nothing works, my SN550 is fine), do it at your own risk, don't open issue just for installation problem, unless you have a solution.

**For 4k display user**: you need to run `python3 update.py --bigsur` (update.py in this repo) to override edid to force display running at 48Hz, otherwise your display will be blank on Big Sur. Don't worry, this script use `AAPL00,override-no-connect` property in config.plist to override EDID, you can remove it at any time. You can run `python3 update.py --edid restore` to restore this change.

## Post Installation

Try not to use *Clover Configurator* or *OC Configurator* to open config, code editor is a better choice.

If you changed kexts/drivers, you can run `python3 update.py --config` to update these info to config file. If you changed ACPI, you can run `python3 update.py --acpi`.

You may refer to [wmchris's tutorial](https://github.com/wmchris/DellXPS15-9550-OSX) for the installation guide and solutions to some common issues.

But note that please create an issue **in this repository** if you encounter any problem when **using this config** (Please don't disturb others). My writing in English is poooooor:(, but I can read :).

### FHD Display

If your laptop display is 1080p, you have to modify your config.plist:

- OC:  `NVRAM/Add/4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14/UIScale`  -> `AQ==`
- CLOVER: `BootGraphics/UIScale` -> `1`

Or simply run `python3 update.py --display fhd`.

### Silent Boot

Remove `-v` in boot-args to turn off verbose mode(printing boot messages on screen).

```python
python3 update.py --set bootarg--v
```

### Headphone

~~@qeeqez found layout-id 30 is good to drive headphone without PluginFix([Overall Audio State](https://github.com/daliansky/XiaoMi-Pro/issues/96)), and it also works for me.~~ 

After updating to 10.15, headphone will be distorted after a few minutes in battery mode. 

You have to install [ComboJack](https://github.com/hackintosh-stuff/ComboJack/tree/master/ComboJack_Installer) (run install.sh).

### Sleep Wake

Please run following commands:

```shell
sudo pmset -a hibernatemode 0
sudo pmset -a autopoweroff 0
sudo pmset -a standby 0
sudo pmset -a proximitywake 0
```

 or simply run `python3 update.py --fixsleep`.

Please uncheck all options (except `Prevent computer from sleeping...`, which is optional) in the `Energy Saver` panel.

### SN MLB SmUUID

Please use your own SN, MLB and SmUUID, you can copy [smbios.json](./sample_smbios.json) to a new one and change `sn, mlb and smuuid` fields to your own, then run `python3 update.py --smbios xxx.json` to use them, `xxx.json` is your plist file to store those values. 

If you don't have those values, you can run `python3 update.py --smbios gen` to generate them(will saved to both `gen_smbios.json` and config file).

Highly recommend you to use  **Windows system UUID** as SmUUID: run  `wmic csproduct get UUID` in Windows CMD.

### Font Smoothing

If you are using FHD(1080p) display, you may want to enable font smoothing:

```sh
defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO # YES to disable
```

### CLOVER Theme

You can set theme to one of these [themes](https://sourceforge.net/p/cloverefiboot/themes/ci/master/tree/themes/).

```sh
python3 update.py --set theme=xxx # will download if not exist
```

### NTFS Writing

Add `UUID=xxx none ntfs rw,auto,nobrowse` to `/etc/fstab`, **xxx** is the UUID of your NTFS partition. 

If your NTFS partition has Windows installed, you need to run `powercfg -h off`  in powershell in Windows to disable hibernation.

### Tap Delay

- Turn off `Enable dragging` or use `three finger drag` to avoid one-finger tap delay.
- Turn off `Smart zoom` to avoid two-finger tap delay.

See [is-it-possible-to-get-rid-of-the-delay-between-right-clicking-and-seeing-the-context-menu](https://apple.stackexchange.com/a/218181)

## Credits

- [acidanthera](https://github.com/acidanthera) for providing almost all kexts and drivers
- [alexandred](https://github.com/alexandred) for providing VoodooI2C
- [headkaze](https://github.com/headkaze) for providing the very useful Hackintool and OS-X-BrcmPatchRAM
- [daliansky](https://github.com/daliansky) for providing the awesome hotpatch guide [OC-little](https://github.com/daliansky/OC-little/) and the always up-to-date hackintosh solution [XiaoMi-Pro-Hackintosh](https://github.com/daliansky/XiaoMi-Pro-Hackintosh)
- [RehabMan](https://github.com/RehabMan) for providing OS-X-BrcmPatchRAM and [hotpatch](https://github.com/RehabMan/OS-X-Clover-Laptop-Config/tree/master/hotpatch)
- [knnspeed](https://www.tonymacx86.com/threads/guide-dell-xps-15-9560-4k-touch-1tb-ssd-32gb-ram-100-adobergb.224486) for providing well-explained hot patches and USB-C hotplug solution
- [wmchris](https://github.com/wmchris/DellXPS15-9550-OSX/tree/10.15) for providing the installation guide for XPS15