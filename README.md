# Elementary OS on Lenovo ThinkPad 13 Chromebook

It's a challenge to get Linux to run on Lenovo ThinkPad 13 Chromebook. Elementary OS is not created especially for Chromebooks, but as it's based on latest Ubuntu this works almost fine on Chromebook. There are still few issues on Lenovo ThinkPad 13 Chromebook on using Elementary OS. The biggest challenge is to get audio working, also there is no Chromebook keys.

## Installing Elementary OS on Thinkpad
Lenovo ThinkPad 13 Chromebook is locked, so you cannot install Linux easily on it. But there is a workaround provided by the MrChromebox.tech.

You have to use [ChromeOS Firmware Utility Script](https://mrchromebox.tech/#fwscript). This tool performs two simple tasks: it sets the crossystem boot flag necessary to enable Legacy Boot mode by doing an RW_LEGACY firmware update appropriate for the device. Legacy Boot Mode can be accessed via [CTRL+L] on the Developer Mode boot now. Detailed instructions are provided on  [ChromeOS Firmware Utility Script](https://mrchromebox.tech/#fwscript) page.

To install RW_LEGACY firmware update you have to go into Chrome OS shell and run this command.

```
cd; curl -LO https://mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh
```
You can use this command later too, once you have installed Linux to upgrade firmware or change settings.

Once you have updated the firmware, you can boot your chromebook from Elementary OS live USB device.

Boot your Chromebook into Elementary OS Live and follow instructions to install Elementary OS. Installation procedure is simple and easy.

Update your system after installation.

### Install Ukuu to update kernel.
You need the latest kernel to fix audio, otherwise driver is not loaded. Easiest way for this is to install Ukuu app, which provides nice GUI for this purpose.

```
sudo add-apt-repository ppa:teejee2008/ppa
```
```
sudo apt update && sudo apt install ukuu
```
Reboot your chromebook after kernel update.

## Fixing ThinkPad 13 Chromebook
Once you have installed Elementary OS on your ThinkPad 13 Chromebook and updated it to latest versions, including the latest kernel, most of hardware should work fine.


There are few issues left:

- No Chromebook function keys
- No audio

### Fixing Chromebook keys
Fixing Chromebook keys is simple. You have to replace PC keys mappings and reboot the Chromebook.

Backup current layout of keys

```
sudo cp -n /usr/share/X11/xkb/symbols/pc /usr/share/X11/xkb/symbols/pc.bck
```
Replace PC keys mapping.

```
sudo su
cd /usr/share/X11/xkb/symbols/
wget https://raw.githubusercontent.com/Backpackstudio/chromebook-sentry/master/mods/usr/share/X11/xkb/symbols/pc -O pc
reboot
```

### Fixing audio
Do not use [Gallium OS Skylake Audio Fix](https://github.com/GalliumOS/galliumos-skylake) for ThinkPad 13 Chromebook as this does not work for properly on model. Instead of that, use files provided by [StephanvanSchaik](https://git.codentium.com/StephanvanSchaik/gentoo-chromebook-skylake), but keep in mind, that still some modifications are required.






