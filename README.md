![OpenCore logo](https://github.com/acidanthera/OpenCorePkg/raw/master/Docs/Logos/OpenCore_with_text_Small.png)

# Surface-Laptop-3-OpenCore
macOS on the Microsoft Surface Laptop 3 thanks to [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg).
  
## Software Specifications
| Software         | Version                            |
| ---------------- | ---------------------------------- |
| Target OS        | Apple macOS 15 Sequoia, 14 Sonoma and 13 Ventura |
| OpenCore         | [MOD-OC v1.0.3](https://github.com/wjz304/OpenCore_NO_ACPI_Build/releases/download/1.0.3_20b758b/OpenCore-Mod-1.0.3-RELEASE.zip) |
| SMBIOS           | MacBookPro16,2 |
| SSD format       | APFS file system, GPT partition table |

## Abstract
With its nice display, large and smooth trackpad, comfortable keyboard and its quite decent speakers, the `Surface Laptop 3` series is an excellent Hackintosh laptop. Moreover, the M.2 2230 SSD can be swapped easily for a larger one.

Apart from ACPI S3 Sleep which is broken, everything on the `Intel 13.5-Inch and 15-Inch Surface Laptop 3` is working perfectly like on a real Mac. ACPI S4 Hibernate works great, though, and resuming from Hibernation takes around ten to fifteen seconds. The advantage Hibernate has over Sleep is that the device doesn't draw any power while in a hibernated state.

The battery runtime is around five hours.

> [!TIP]
> I recommend installing `macOS 13 Ventura` rather than the newer `macOS 14 Sonoma` or `macOS 15 Sequoia`. The builtin Intel Wireless chip works almost perfectly with Apple's iServices and Continuity features on Ventura while those features are partially broken at the moment on newer versions of macOS.

> [!IMPORTANT]
> For macOS to be able to boot on the Surface Laptop 3, the `Secure Boot` option _**must be disabled**_ [in the UEFI](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/README.md#uefi-settings). The boot screen will then display a large red bar with a padlock symbol at the top of the display when Secure Boot is disabled.

> [!IMPORTANT]
> The keyboard and trackpad are now working in the installer as well as in the installed OS, but the trackpad will be lagging/skipping every few seconds. Furthermore, the keyboard and trackpad will be unresponsive after resuming from hibernation. To fix those issues, you **MUST** downgrade the firmware of your `Surface Laptop 3`. To do so, [follow these instructions](https://github.com/jlempen/Surface-Laptop-3-OpenCore?tab=readme-ov-file#downgrading-the-uefi-firmware).
>
> Then, you **MUST** also replace `BigSurface.kext` and its dependencies with `BigSurfaceSLB3.kext` and its dependencies in your `config.plist` file by following [these instructions](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/README.md#replacing-bigsurfacekext-with-bigsurfaceslb3kext).

> [!CAUTION]
> ~~At the moment, it is not possible to have Windows installed at the same time as macOS, as the SL3 will immediately update to the latest firmware again when you reboot into Windows, even if you turn off your WiFi or disable automatic Windows Updates. I haven't found a way to prevent this behaviour yet. Any kind of Linux distribution is fine, though.~~
> 
> As of 6 April 2025, [@pnxl](https://github.com/pnxl) added instructions on how to run Windows on Surface Laptop 3, without Windows Update updating the firmware to the latest firmware version upon reboot. The instructions can be found [here](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/README.md#running-windows-without-updating-the-firmware).

## Disclaimer
This repository is neither a howto nor an installation manual. Using these files requires at least basic knowledge of [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg), ACPI, UEFI and the art of hackintoshing in general. I recommend reading the excellent [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide), as well as all its linked resources. For those who wish to improve their hackintoshing knowledge, [5T33Z0's OC-Little-Translated](https://github.com/5T33Z0/OC-Little-Translated) repository is the most comprehensive resource I've found on the subject.

## Recommendations
I recommend completely erasing the device's SSD by creating a new GPT partition table before attempting to install macOS, as it makes the installation process much easier. You may use any Linux live ISO with a partitioning tool such as `GParted` or `KPartition` to erase the SSD.

Please be aware that all `PlatformInfo` and `SMBIOS` information was removed from the OpenCore `config.plist` file. Users will therefore need to generate their own `PlatformInfo` with [CorpNewt's GenSMBIOS tool](https://github.com/corpnewt/GenSMBIOS) before attempting to boot a Surface Laptop 3 with this repository's EFI folder.

`BigSurfaceSLB3.kext` is required to enable the trackpad, the keyboard, the touchscreen, the battery status, the power and volume buttons and the ambient light sensor. Updating this `kext` with the official one from [Xiashangning's BigSurface repo](https://github.com/Xiashangning/BigSurface) will most certainly break something! `BigSurfaceSLB3.kext` is a modified version of `BigSurface` v6.5 which fixes the skipping/lagging trackpad and hibernate mode 25.

An UEFI firmware released by Micro$oft in August 2023 broke the Surface Laptop 3's trackpad in macOS in several ways. Downgrading the UEFI firmware to the last known working version `13.101.140.0` is required to fix the trackpad and hibernate mode 25 on macOS. See the detailed instructions below to easily downgrade the UEFI firmware. This needs to be done before installing any version of macOS or you won't have a working trackpad and/or keyboard during installation.

`AirportItlwm-Ventura.kext`, `AirportItlwm-Sonoma140.kext` and `AirportItlwm-Sonoma144.kext` from the [OpenIntelWireless repo](https://github.com/OpenIntelWireless/itlwm) are required to enable the Wifi chip. This EFI will dynamically load the appropriate kext for macOS Ventura or Sonoma depending on the running kernel. No need to manually replace the kext file when updating your version of macOS. As the Intel Wifi chip does not yet work with the `AirportItlwm.kext` in macOS Sequoia, you'll need to use the `Itlwm.kext` and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) to connect to a Wifi network. You'll find the latest stable `HeliPort.dmg` in the [Tools folder](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/Tools/HeliPort.dmg) of this repo. This EFI will dynamically load the `Itlwm.kext` instead of `AirportItlwm.kext` when you boot into macOS Sequoia.

This repository uses the unofficial OpenCore_NO_ACPI_Build fork of OpenCore by [btwise](https://gitee.com/btwise/OpenCore_NO_ACPI), wich is not endorsed by Acidanthera (the dev team behind OpenCore). The main (and only) difference between this fork and the official OpenCore version is that it allows to prevent ACPI injection (e.g. patches, tables, boot parameters) into other OSes besides macOS.

Windows and Linux should be detected automagically by the OpenCore boot loader even when installed after macOS.

<details>
  <summary>Computer Specifications</summary>
  
## Computer Specifications
| Device           | Hardware                           |
| ---------------- | ---------------------------------- |
| CPU              | Intel Core i7-1065G7 or Intel Core i5-1035G7 |
| iGPU             | Intel Iris Plus Graphics |
| Audio            | Realtek ALC274 |
| RAM              | 8 or 16 GB RAM |
| Wifi + Bluetooth | Wifi6 AX200, Bluetooth 5.0 |
| Storage          | Kioxia/Toshiba/SK Hynix PCIe NVMe 2230 SSD |
| USB Type-C 3.1 Gen 1 | Supports Power Delivery and DisplayPort |
| Camera | 720p |
| IR camera | Intel(R) AVStream Camera 2500, ISP Interface |
| Keyboard / Trackpad | |
| Display | 13.50 inch 3:2, 2256 x 1504 pixel or 15 inch 3:2, 2496 x 1664 pixel 201 PPI |
| Touchscreen | 10-point capacitive |
| Battery | |
| Ambient light sensor | |
</details>

<details>
  <summary>What works</summary>
  
## What works
- [x] CPU power management
- [x] CPU SpeedStep
- [x] iGPU with full acceleration
- [x] SSD drive
- [x] USB-C port
- [x] USB-A port
- [x] WLAN
- [x] Bluetooth
- [x] 720p camera
- [x] Internal speakers, microphone and Combojack
- [x] Power, volume up and volume down buttons
- [x] Keyboard with working brightness, volume and mute keys, working caps lock light
- [x] Trackpad with native multi-touch gestures
- [x] Touchscreen
- [x] Surface Pen
- [x] Ambient light sensor
- [x] Battery percentage and cycle count
- [x] Hibernation (hibernatemode 25) - the device successfully wakes up from hibernation mode
- [x] USB Type-C to HDMI
- [x] USB Type-C to USB3 & USB2
- [x] USB Type-C Power Delivery
</details>

<details>
  <summary>What needs some more work</summary>
  
## What needs some more work
- [ ] Sleep (hibernatemode 3) - the device only turns off the display without sleeping
- [ ] On macOS Sequoia, the user needs to close and open the lid again to wake up the display after hibernation
</details>

<details>
  <summary>What will probably never work</summary>
  
## What will probably never work
- [ ] IR camera (Windows Hello)
</details>

<details>
  <summary>UEFI Settings</summary>
  
## UEFI Settings
To enter the UEFI Settings, power on your Surface Laptop 3 and hold the `Volume Up Button` as soon as the Surface Logo is displayed on the screen.

The `Secure Boot` setting ***must be disabled to boot macOS***. I also recommend moving `USB Storage` to the top of the boot configuration list, which makes booting from an USB stick much easier.

| Security | |
| -------- | ----- |
| Secure Boot | Disabled |

| Boot configuration | |
| -------- | ----- |
| USB Storage | Move the item to the top of the list |
</details>

<details>
  <summary>Downgrading the UEFI firmware</summary>

## Downgrading the UEFI firmware
In order to fix the skipping/lagging trackpad in macOS and make the trackpad and keyboard work after hibernation, you must downgrade your UEFI firmware to the last known working version `13.101.140.0`.

1. Boot with a Linux Live USB stick, preferably a Debian, Arch or Fedora based distribution (I use the Arch-based Manjaro).
2. Download and unzip the compressed firmware archive [SurfaceLaptop3_FW_13.101.140.0.zip](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/UEFI%20Firmware/SurfaceLaptop3_FW_13.101.140.0.zip) from this repository.
3. Add the line `OnlyTrusted=false` to the `/etc/fwupd/daemon.conf` config file. On some Linux distros such as Arch, endeavourOS and Manjaro, the config file to change is `/etc/fwupd/fwupd.conf`:
```
sudo nano /etc/fwupd/daemon.conf
```
or
```
sudo nano /etc/fwupd/fwupd.conf
```
4. Open a terminal and navigate to the folder where you extracted the firmware files.
5. Connect your Surface device to a power supply.
6. Copy the following lines and paste them into the terminal:
```
for f in *; do 
  sudo fwupdmgr install --allow-older --allow-reinstall --no-reboot-check "$f"
done
```
7. Close the terminal and reboot into Linux once more.

For some firmware files, the `fwupdmgr` tool may complain that it is unable to find a matching device. This is normal, as not all Surface Laptop 3 models use the exact same hardware, thus the compressed firmware archive contains all the required files for all models.

The Surface Laptop 3 will reboot and downgrade all UEFI firmwares at once, which takes around 5 to 10 minutes. You'll see progress bars with different colours depending on which type of firmware is being flashed.
Once the process is done, your laptop will restart a few times and seem to hang on the Surface logo for 20 or 30 seconds each time, this is normal. Then it will restart for good to your OpenCore picker.

Now restart while holding the F4/Volume Up key to check the firmware version in the UEFI. In the Firmware section, `System UEFI` should now show `13.101.140`.

Reboot and you're done.

~~If you are using Windows on the laptop, you'll have to find a way to prevent Windows Update from automatically updating the firmware to the latest firmware version again on the next reboot into Windows! I haven't found a way to prevent this yet. Any kind of Linux distribution is fine, though.~~

As of 6 April 2025, [@pnxl](https://github.com/pnxl) added instructions on how to run Windows on Surface Laptop 3, without Windows Update updating the firmware to the latest firmware version upon reboot. The instructions can be found [here](https://github.com/jlempen/Surface-Laptop-3-OpenCore/blob/main/README.md#running-windows-without-updating-the-firmware).
</details>

<details>
  <summary>Running Windows without updating the firmware</summary>
  
## Running Windows without updating the firmware
Since Windows 7 (and Server 2008), Microsoft has added a way for system administrators to prevent the installation of device drivers for specific devices via a hardware ID block list. We can take advantage of this to prevent any firmware updates to the Surface UEFI firmware.

First, you'll have to install Windows with the Surface recovery (as the Windows installer DOES NOT include the drivers to communicate with the Surface Aggregator Module which handles HID devices, oddly enough)

Once you have Windows up and running, you'll want to install the last known working version of the drivers. Download [SurfaceLaptop3_Win10_19041_22.011.9779.0.msi](https://drive.google.com/file/d/1UfafCWYVMuY5J_2tNFkMs9-fSAdw-RYQ/view?usp=sharing) from this Google Drive mirror. If you haven't downgraded your firmware, this will do that for you too.

After complete, follow the instructions to block device driver installation for the Surface UEFI firmware.

1. Open the Registry Editor, and navigate to `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows`
2. Make a new key, named `DeviceInstall`
3. Under that key, make another new key named `Restrictions`
4. Under the `Restrictions` key, make a DWORD Value named `DenyDeviceIDs` and `DenyDeviceIDsRetroactive`. Set both values to 1.
5. Create a new key under `Restrictions`, named `DenyDeviceIDs`.
6. You should end up with something like this.
![Registry Editor showing DWORD values under Restrictions key](https://github.com/user-attachments/assets/038f12c7-022b-47c9-93fe-1b01a3d44bcd)

We're not done just yet - let's continue.
1. Open Device Manager, and locate, then expand the Firmware section.
2. You'll see a bunch of different firmware modules for the components on your Surface.
3. Right-click on one, and click on Properties.
4. Click on the details tab, and select Hardware IDs from the dropdown.
5. Right-click the top-most Hardware ID, and paste it in a text editor, temporarily.

Repeat for all the remaining firmware modules. You should end up with something like this.
![All Hardware IDs listed in a text document](https://github.com/user-attachments/assets/02877452-26b4-4c8a-a01c-d8e0f82c81a6)

We're still not done! Hang in there.
1. Go back to the Registry Editor, and go to the `DenyDeviceIDs` key we made.
2. Create a new String Value, and name it `1`.
3. Set the value to the first Hardware ID you copied.

Rinse and repeat for the rest of the Hardware IDs you copied. Increment the names of the string values as you go. (Name 1 for the first, 2 for the second, and so on)

> Pro tip!
> If you need to make lots of String Values, you'll find that it gets cumbersome to do (especially on a trackpad). You can use the following keyboard sequence to create String Values much faster: [Alt], [E], [N], [S], [number].

In the end, you'll have something similar to this.
![Registry Editor showing all Hardware IDs added to DenyDevicesIDs key](https://github.com/user-attachments/assets/76259a01-6a53-4416-b249-14190c45fe92)

Restart your computer to apply the changes.
</details>

<details>
  <summary>Replacing BigSurface.kext with BigSurfaceSLB3.kext</summary>
  
## Replacing BigSurface.kext with BigSurfaceSLB3.kext
Additionally, to fix the skipping/lagging trackpad in macOS and make the trackpad and keyboard work after hibernation, you also have to replace the official `BigSurface.kext` with the `BigSurfaceSLB3.kext`. 

In the `Kernel` -> `Add` section of your `config.plist` file, disable or delete the following kexts:
```
BigSurface.kext/Contents/PlugIns/VoodooGPIO.kext
BigSurface.kext/Contents/PlugIns/VoodooSerial.kext
BigSurface.kext/Contents/PlugIns/VoodooInput.kext
BigSurface.kext
BigSurface.kext/Contents/PlugIns/BigSurfaceHIDDriver.kext
```
Then enable the following kexts:
```
BigSurfaceSLB3.kext/Contents/PlugIns/VoodooGPIO.kext
BigSurfaceSLB3.kext/Contents/PlugIns/VoodooSerial.kext
BigSurfaceSLB3.kext/Contents/PlugIns/VoodooInput.kext
BigSurfaceSLB3.kext
BigSurfaceSLB3.kext/Contents/PlugIns/BigSurfaceHIDDriver.kext
```
Save your `config.plist` file and reboot. Your trackpad should now be buttery smooth before and after resuming from hibernation. The keyboard will now also work after resuming from hibernation.
</details>

<details>
  <summary>Disabling Sleep and enabling Hibernate</summary>
  
## Disabling Sleep and enabling Hibernate
As we still haven't found a solution for the Sleep/Wake issues on the Surface Laptop 3, disable Sleep altogether and use Hibernate for now. Open the `Terminal` and enter the following commands, then reboot for the changes to take effect:
```
sudo pmset restoredefaults
sudo pmset -a hibernatemode 25
```
If for whatever reason Hibernate is not working on your system, you should reset the `Power Management` settings and rebuild the `sleepimage` file. To do so, open the `Terminal` and enter the following commands, then reboot for the changes to take effect:
```
sudo rm /Library/Preferences/com.apple.PowerManagement*
sudo rm /var/vm/sleepimage
sudo pmset hibernatefile /var/vm/sleepimage
```
Once you are back in macOS, disable Sleep and enable Hibernate again, then reboot:
```
sudo pmset restoredefaults
sudo pmset -a hibernatemode 25
```

It's also a good idea to reset the NVRAM before rebooting into macOS. To do so, press the space bar in the OpenCore picker and use the arrow keys to select `Reset NVRAM`.

Keep in mind that once the Surface Laptop 3 hibernates, you need to let it hibernate for a couple of minutes before waking it up. Failing to do so will disrupt hibernation and the device will hang. You'll have to turn it off forcefully by pressing on the power button for 10 seconds. Sometimes you even need to press the power button for up to 20 seconds to restart the laptop.
</details>

<details>
  <summary>Fix broken Bluetooth on Wake from Hibernation</summary>
  
## Fix broken Bluetooth on Wake from Hibernation
After the device wakes up from Hibernation, Bluetooth may be broken / unable to connect.

A very simple fix for this issue is to [download and install Bluesnooze](https://github.com/odlp/bluesnooze). Launch the app, enable `Launch at login` and you're done!
</details>

<details>
  <summary>Enabling native HiDPI display settings in macOS</summary>
  
## Enabling native HiDPI display settings in macOS
On the installed macOS system, the default display resolution is less than ideal. To enable native HiDPI settings in the Display Preferences of macOS, download and run the [one-key-hidpi](https://github.com/jlempen/one-key-hidpi) script and select the option `(7) Manual input resolution`, then copy and paste the resolutions below into the terminal, press Enter and reboot for the changes to take effect. This will give you five sane preset resolutions for your Surface Laptop 3.

For the 13.5-Inch Surface Laptop 3:
```
2254x1504 1920x1280 1600x1066 1344x896 1280x854 1128x752
```

For the 15-Inch Surface Laptop 3:
```
2496x1664 2250x1500 2048x1366 1920x1280 1600x1066 1344x896 1280x854 1248x832
```

You may also download and install [BetterDisplay](https://github.com/waydabber/BetterDisplay) to change and manage the display resolutions on the Surface Laptop 3.
</details>

<details>
  <summary>Installing the IPTSDaemon to enable the touchscreen</summary>
  
## Installing the IPTSDaemon to enable the touchscreen
The [IPTSDaemon](https://github.com/Xiashangning/IPTSDaemon) is a tool made by the author of BigSurface [Xiashangning](https://github.com/Xiashangning). It enables the touchscreen on Surface devices running macOS.

1. Download the [IPTSDaemon](https://github.com/jlempen/Surface-Book-3-OpenCore/blob/main/Tools/IPTSDaemon.zip)
2. Unzip the downloaded file
3. Open a `Terminal` and navigate to the `IPTSDaemon` folder:
```
cd /Downloads/IPTSDaemon/IPTSDaemon
```
4. Run the `install_daemon.sh` file:
```
sudo bash install_daemon.sh
```
5. Enter your password to install the daemon

Now you'll see a nasty popup window:

![Nasty popup window](https://github.com/user-attachments/assets/eacbfe79-04a4-4bd8-b851-ba83cd55e9b6)

This is actually macOS's way of telling you that Apple considers that the software is from an untrusted source because it is unsigned. But it's actually very easy to tell it to open the file anyway.

Click on the "Show in Finder" button of this popup window, then right-click on the `libinih.0.dylib` file and select the first option, "Open". Nothing will happen, but the annoying popup window will not show anymore for this file. Now repeat the same procedure for the `libfmt.9.dylib` file and you're done.

Perhaps you'll need to repeat this a few times, as the popup window appearing for one file will block the "Open" popup window for the other file and vice versa. Basically, once there's no warning popup appearing anymore, both files were registered and started.

You may now verify that the multitouch gestures are working on your touchscreen by playing around with the standard macOS multitouch gestures you're used to on your trackpad, but the same gestures now work on the touchscreen as well :-)

These instructions are confirmed working on SL3 and SB3 running macOS Ventura and Sonoma. On macOS Sequoia, the procedure is pretty much similar, but there won't be a "Show in Finder" button in the popup window. To open the dylib files, you'll have to go to the `System Settings` -> `Privacy and Security` -> `Security` section and open the files from there.
</details>

<details>
  <summary>Fixing broken Apple Messages and FaceTime</summary>
  
## Fixing broken Apple Messages and FaceTime
To fix issues with Apple Messages and FaceTime related to the [Intel Wireless driver](https://github.com/OpenIntelWireless/itlwm) on macOS Sonoma, disable all `AirportItlwm-***.kext` entries under `Kernel -> Add` in your `config.plist` file and use the [itlwm_v2.3.0_stable.kext.zip](https://github.com/OpenIntelWireless/itlwm/releases/download/v2.3.0/itlwm_v2.3.0_stable.kext.zip) and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases/download/v1.5.0/HeliPort.dmg) instead.
The latest version 2.3.0 of itlwm.kext is already included in the Kext folder and `config.plist` file.

In addition to the above, to enable `itlwm.kext` under macOS Ventura and macOS Sonoma, you need to delete any text (i.e. `24.0.0` and `24.99.99` respectively) in the `MinKernel` and `MaxKernel` fields under `Kernel -> Add -> itlwm.kext` in your `config.plist` file.
</details>

<details>
  <summary>Related repositories</summary>
  
## Related repositories
* https://github.com/jc-bao/surface-laptop3-ventura
* https://github.com/Xiashangning/BigSurface
* https://github.com/Xiashangning/IPTSDaemon
</details>
