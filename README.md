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
Do not use [Gallium OS Skylake Audio Fix](https://github.com/GalliumOS/galliumos-skylake) for ThinkPad 13 Chromebook as this does not work for properly on this model. Instead of that, use files provided by [StephanvanSchaik](https://git.codentium.com/StephanvanSchaik/gentoo-chromebook-skylake), but keep in mind, that still some modifications are required.

#### 1. Check kernel
Please check your current version of kernel. Older kernel versions might not support audio drivers provided in these instructions.

```
uname -r
```
> 4.20.5-042005-generic

```
lsb_release -a
```

> LSB Version:	core-9.20170808ubuntu1-noarch:security-9.20170808ubuntu1-noarch<br>
> Distributor ID:	elementary<br>
> Description:	elementary OS 5.0 Juno<br>
> Release:	5.0<br>
> Codename:	juno

```
hostnamectl
```
> Static hostname: elementary<br>
> Icon name: computer-desktop<br>
> Chassis: desktop<br>
> Machine ID: e88d7669b6294d3c87a642f6b594e50d<br>
> Boot ID: f68a336a63ac4112bad1ad16199e5262<br>
> Operating System: elementary OS 5.0 Juno<br>
> Kernel: Linux 4.20.5-042005-generic<br>
> Architecture: x86-64

Your system kernel should be newer than 4.17, otherwise you wouldn't get audio. Please follow instruction above how to install Ukuu and update the kernel.

### 2. Install firmware
No firmware, no audio. Invalid firmware, no audio or disrupted audio. There are various versions of dfw_sst.bin available, but some of them wouldn't work or cause sound distortions. The best firmware is provided by [StephanvanSchaik](https://git.codentium.com/StephanvanSchaik/gentoo-chromebook-skylake). 

I have included dfw_sst.bin also (MD5: 5238c7bb0c2af50ffeb109edb4da67f9).

```
sudo su
cd /lib/firmware/
wget https://raw.githubusercontent.com/Backpackstudio/chromebook-sentry/master/mods/lib/firmware/dfw_sst.bin -O dfw_sst.bin
md5sum dfw_sst.bin
```
> 5238c7bb0c2af50ffeb109edb4da67f9 dfw_sst.bin

### 3. Install hardware definitions for ALSA
Once we have installed proper firmware (dfw_sst.bin) we are ready to add hardware definitions for ALSA.

```
sudo su
mkdir -p /usr/share/alsa/ucm/sklnau8825max/
cd /usr/share/alsa/ucm/sklnau8825max/
wget https://raw.githubusercontent.com/Backpackstudio/chromebook-sentry/master/mods/usr/share/alsa/ucm/sklnau8825max/Google-sentry-1.0-sentry.conf -O Google-sentry-1.0-sentry.conf
wget https://raw.githubusercontent.com/Backpackstudio/chromebook-sentry/master/mods/usr/share/alsa/ucm/sklnau8825max/HiFi.conf -O HiFi.conf
wget https://raw.githubusercontent.com/Backpackstudio/chromebook-sentry/master/mods/usr/share/alsa/ucm/sklnau8825max/sklnau8825max.conf -O sklnau8825max.conf
ln -s /usr/share/alsa/ucm/sklnau8825max /usr/share/alsa/ucm/Google-sentry-1.0-sentry
```

### 4. Disable snd-hda-intel
Do use Chromebook audio device we have to disable snd-hda-intel first.

```
sudo su
cd /etc/modprobe.d/
wget https://raw.githubusercontent.com/Backpackstudio/chromebook-sentry/master/mods/etc/modprobe.d/snd-hda-intel.conf -O snd-hda-intel.conf
```

