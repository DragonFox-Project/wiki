# Installation
This guide covers installing our ROMs onto Samsung devices powered by Exynos 9611
(Galaxy A51 4G, Galaxy M21, Galaxy M31, Galaxy M31s, Galaxy F41)

> [!Warning]
> * On Samsung, we don't have fastboot, we have only Odin
> * VoLTE, VoWiFi, ViLTE/ViWiFi, etc are BROKEN!
>     * If you own Jio SIM, it will never work on AOSP! <br/>
>     * They're impossible to fix due to the nature of Samsung, using custom implementations of those on OneUI and  being incompatible with AOSP ones. It's currently impossible to make those working.
> * Camera quality is significantly worse, even with Google Camera Go
>     * Making full Google Camera work is currently impossible here, due to missing Camera2 API in Full/Level 3. <br/>
>     * Porting Samsung one is also not possible
> * Do NOT use TWRP for our ROMs, nor don't use multidisabler and/or Disable DM Verity/Force Encryption
>     * Our ROMs are force encrypted for security and better quality of fingerprint. Decryption is deprecated in Android since years ago, and this also may cause instability. Our vendor is in erofs, making it decrypted without proper knowledge is impossible anyways. <br/>
>     * If you use TWRP over provided recovery, DO NOT expect any support from our side.
> * DRM is in L3, this is due to bootloader unlock, and technically unfixable now. 
>     * If you need Netflix etc - use stock ROM with locked bootloader.

**The provided instructions are for our ROMs. These will only work if you follow every section and step precisely.
Do not continue after something fails!**

# Basic requirements 
1. Read through the instructions at least once before actually following them, so as to avoid any problems due to any missed steps!
2. Make sure your computer has `adb`. Setup instructions can be found [here](https://wiki.lineageos.org/adb_fastboot_guide).
3. Enable USB debugging on your device.
4. Boot your device with the stock OS at least once and check every functionality.
5. Our ROMs are provided as-is with no warranty. While we attempt to verify everything works you are installing this at your own risk!

> [!Warning]
> Before following these instructions please ensure that the device is currently using Android 13 (Android 12 on devices without 13) firmware. <br/>
> If the vendor provided multiple updates for that version, e.g. security updates, make sure you are on the latest! <br/>
> If your current installation is newer or older than Android 13 (Android 12 on devices without 13), please upgrade or downgrade to the required version before proceeding (guides can be found on the internet!).

**By continuing reading you are confirming that you've met all of the basic requirements and read the warnings.**

# Checking the correct firmware
Installation on your device requires a specific firmware version to be installed before you continue.

* Firmware refers to a device-specific set of images that are included in, and updated by the stock OS
* Our ROMs builds for this device require a specific version of the stock OS to be installed prior to following the installation guide
* Please ensure that you are checking the Android version, and not the vendor OS version
* Please re-read this section as many times as necessary to fully understand the requirements

> [!Note]
> If you are unsure what firmware version you are currently on, we strongly recommend returning to the corresponding stock OS before following the installation guide!

Failing to install the correct firmware version prior to installation may result in failure to install our ROM, unexpected crashes post-installation, or permanent damage to your device!

# Pre-Install Instructions
> [!Warning]
> The following instructions will unlock the bootloader and wipe all userdata on the device.

1. Connect the device to a Wi-Fi network.
2. Enable Developer Options by pressing the “Build Number” option in the “Settings” app within the “About” menu
    * From within the Developer options menu, enable OEM unlock.
3. Power off the device, and boot it into download mode:
    * With the device powered off, hold **Volume Down** + **Volume Up** and connect USB cable to PC.
    * Now, click the button that the onscreen instructions correlate to “Continue” and/or “Unlock Bootloader”.
4. Your device will reboot, you may now unplug the USB cable from your device.
5. The device will demand you format userdata, please follow the onscreen instructions to do so.
6. Run through Android Setup skipping everything you can, then connect the device to a Wi-Fi network.
7. Re-enable Development settings by clicking the “Build Number” option 10 times, in the “Settings” app within the “About” menu, and verify that “OEM Unlock” is still enabled in the “Developer options” menu.

# Preparing for installation
Samsung devices come with a unique boot mode called “Download mode”, which is very similar to “Fastboot mode” on some devices with unlocked bootloaders.

Enable Developer Options by pressing the “Build Number” option in the “Settings” app within the “About” menu
* From within the Developer options menu, enable OEM unlock

## Installation with Heimdall
Heimdall is a cross-platform, open-source tool for interfacing with Download mode on Samsung devices. The preferred method of installing a custom recovery is through Download Mode – rooting the stock firmware is neither necessary nor required.

1. Download and install the appropriate version of the [Heimdall suite](https://www.androidfilehost.com/?w=files&flid=304516) for your machine’s OS
    * **Windows**: Extract the Heimdall suite zip and take note of the new directory containing `heimdall.exe`. You can verify Heimdall is functioning by opening a Command Prompt or PowerShell in that directory and running `heimdall version`.
        * If you receive an error, install the [Microsoft Visual C++ 2015-2019 Redistributable Package (x86)](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) on your computer.
    * **Linux**: Extract the Heimdall suite zip and take note of the new directory containing `heimdall`. Now copy `heimdall` into a directory in $PATH, a common one on most distros will be /usr/local/bin. For example `cp heimdall /usr/local/bin`. You can verify Heimdall is functioning by opening a Terminal and running `heimdall version`.
    * **macOS**: Mount the Heimdall suite DMG. Now drag **heimdall** down into the `/usr/local/bin` symlink provided in the DMG. You can verify Heimdall is functioning by opening a Terminal and running `heimdall version`.

    > [!Note]
    > These Heimdall suite distributions were built by LineageOS Developers Nolen Johnson (npjohnson) and Jan Altensen (Stricted), as the Heimdall suite executables distributed on the official Heimdall website were outdated and the repo mostly abandoned. Modifications were made to make it build and function on modern OSes.

2. Power off the device, and boot it into download mode:
    * With the device powered off, hold **Volume Down** + **Volume Up** and connect USB cable to PC.
    * Now, click the button that the on screen instructions correlate to “Continue”, and insert the USB cable into the device.

3. **For Windows users only:** install the necessary drivers. A more complete set of instructions can be found in the [Zadig user guide](https://github.com/pbatard/libwdi/wiki/Zadig).
    1. If nothing relevant appears, try uninstalling any Samsung related Windows software, like Samsung Windows drivers and/or Samsung Kies.
    2. Run `zadig.exe` found in your extracted Heimdall directory.
    3. Choose Options » List all devices from the menu.
    4. Select **Samsung USB Composite Device** or **MSM8x60** or **Gadget Serial** or **Device Name** from the drop down menu.
    5. Click **Replace Driver**, then selecting **Install Driver** from the drop down list built into the button.
    6. If you are prompted with a warning that the installer is unable to verify the publisher of the driver, select **Install this driver anyway**. You may receive two more prompts about security. Select the options that accept the warnings and allow you to carry on.
5. On your machine, open a Command Prompt or PowerShell (Windows) window, or Terminal (Linux or macOS) window, and type:
    ```
    heimdall print-pit
    ```
6. If the device reboots that indicates that Heimdall is installed and working properly. If it does not, please refollow these instructions to verify steps weren’t missed, try a different USB cable, and a different USB port.

### Installing a custom recovery using heimdall
1. Download ROM Recovery. Simply download the latest recovery file for your device.
    > [!Important]
    >  Use ROM recovery. Other recoveries may not work for installation or updates. We strongly recommend to use the one mentioned above!
2. Power off the device, and boot it into download mode:
    * With the device powered off, hold **Volume Down** + **Volume Up** and connect USB cable to PC.
    * Now, click the button that the on screen instructions correlate to “Continue”, and insert the USB cable into the device.
3. On your machine, open a Command Prompt or PowerShell (Windows) window, or Terminal (Linux or macOS) window, and type:
    ```
    heimdall flash --RECOVERY recovery.img --no-reboot
    ```
    > [!Note]
    > The file may not be named identically to what stands in this command, and may be in different path, so adjust accordingly. If the file is wrapped in a zip or tar file, extract the file first, because Heimdall is not going to do it for you.
    * If recovery you are flashing needs vbmeta (this might be listed in post, if it's not - you probably still need one), then find a proper (null) vbmeta for your device in Telegram or other sources, and flash it using
        ```
        heimdall flash --VBMETA vbmeta.img --no-reboot
        ```
        > [!Note]
        > The file may not be named identically to what stands in this command, and may be in different path, so adjust accordingly. If the file is wrapped in a zip or tar file, extract the file first, because Heimdall is not going to do it for you.

4. A transfer bar will appear on the device showing the recovery image being flashed.
    > [!Note]
    > The device will continue to display `Downloading... Do not turn off target!!` even after the process is complete.
5. Unplug the USB cable from your device.
6. Manually reboot into recovery, this may require pulling the device’s battery out and putting it back in, or if you have a non-removable battery, press the Volume Down + Power buttons for 8~10 seconds until the screen turns black & release the buttons _immediately_ when it does, then boot to recovery:
    * Reboot and immediately hold **Volume Up** + **Power** while the device is connected to a PC via USB cable.
    > [!Note]
    > Be sure to reboot into recovery immediately after installing the custom recovery. If you don’t the stock ROM will overwrite the custom recovery with the stock recovery, and you’ll need to flash it again.

## Installation with Odin
Odin is a Windows/Linux proprietary Samsung tool for interfacing with Download mode on Samsung devices. The preferred method of installing a custom recovery is through Download Mode – rooting the stock firmware is neither necessary nor required.

### Windows
1. Get [Samsung drivers](https://developer.samsung.com/mobile/file/4d05badf-eaea-4095-af16-3aabfad74760) for Odin and install them
2. Download Odin 3.14.4
    * We're not providing sources for this, find that yourself
3. Unpack it and launch
4. Power off the device, and boot it into download mode:
    * With the device powered off, hold **Volume Down** + **Volume Up** and connect USB cable to PC.
    * Now, click the button that the on screen instructions correlate to “Continue”, and insert the USB cable into the device.
5. If the device shows up in Odin, you can continue. Otherwise, ensure you installed drivers properly.
6. Download ROM Recovery. Simply download the latest recovery file for your device.
    > [!Important]
    > Use ROM recovery. Other recoveries may not work for installation or updates. We strongly recommend to use the one mentioned above!
7. On your machine, rename downloaded recovery to `recovery.img` and using 7-zip or other archiver with tar support, pack it into .tar, so you will have `recovery.tar`
8. Turn off Auto-Restart in options
9. Select your `recovery.tar` in AP in Odin and press Start
10. A transfer bar will appear on the device showing the recovery image being flashed.
    > [!Note]
    > The device will continue to display `Downloading... Do not turn off target!!` even after the process is complete.
11. If recovery you are flashing needs vbmeta (this might be listed in post, if it's not - you probably still need one), then find a proper (null) vbmeta for your device in Telegram or other sources, and flash it, by putting it into AP the same way and flashing without Auto-Reboot
    > [!Note]
    > The file might be just .img, if so, compress it into .tar the same way you did it with recovery.img

12. Unplug the USB cable from your device.
13. Manually reboot into recovery, this may require pulling the device’s battery out and putting it back in, or if you have a non-removable battery, press the Volume Down + Power buttons for 8~10 seconds until the screen turns black & release the buttons _immediately_ when it does, then boot to recovery:
    * Reboot and immediately hold **Volume Up** + **Power** while the device is connected to a PC via USB cable.
    > [!Note]
    > Be sure to reboot into recovery immediately after installing the custom recovery. If you don’t the stock ROM will overwrite the custom recovery with the stock recovery, and you’ll need to flash it again.

### Linux
1. Download [Odin 4](https://xdaforums.com/t/official-samsung-odin-v4-1-2-1-dc05e3ea-for-linux.4453423/)
2. Unpack it
    * Execute `chmod +x odin4`
    * Now copy `odin4` into a directory in $PATH, a common one on most distros will be /usr/local/bin. For example `cp odin4 /usr/local/bin`. You can verify Odin 4 is functioning by opening a Terminal and running `odin4 -v`.
3. Power off the device, and boot it into download mode:
    * With the device powered off, hold **Volume Down** + **Volume Up** and connect USB cable to PC.
    * Now, click the button that the on screen instructions correlate to “Continue”, and insert the USB cable into the device.
4. Execute `odin4 -l`
    * If the device shows up, aka you have USB path from Odin, you can continue. Otherwise, ensure your device is seen by PC 
    > [!Important]
    > You may need to set up your system to detect your device on LINUX host. <br/>
    > create this file: /etc/udev/rules.d/51-android.rules <br/>
    > to add a line to the file:
    > ```
    > SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", MODE="0666", GROUP="plugdev"
    > ```
    >  (http://developer.android.com/tools/device.html) <br/> And you maybe need to unload a module cdc_acm before downloading. (This is only needed for older kernels.)
    > ```
    > $sudo rmmod cdc_acm
    > ```
    > **OR**
    > ```
    > echo "blacklist cdc_acm" > /etc/modprobe.d/cdc_acm-blacklist.conf
    > ```
6. Download ROM Recovery. Simply download the latest recovery file for your device.
    > [!Important]
    > Use ROM recovery. Other recoveries may not work for installation or updates. We strongly recommend to use the one mentioned above!
7. On your machine, rename downloaded recovery to `recovery.img` and using p7zip or other archiver with tar support, like stock `tar -cf`, pack it into .tar, so you will have `recovery.tar`
8. On your machine, Terminal window in dir with recovery.tar, and run:
    ```
    odin4 -a recovery.tar
    ```
    > [!Note]
    > The file may not be named identically to what stands in this command, and may be in different path, so adjust accordingly. If the file is not in dir you are in, adjust path or cd to dir, because Odin4 is not going to do it for you.
9. A transfer bar will appear on the device showing the recovery image being flashed.
    > [!Note]
    > The device will continue to display `Downloading... Do not turn off target!!` even after the process is complete.
10. If recovery you are flashing needs vbmeta (this might be listed in post, if it's not - you probably still need one), then find a proper (null) vbmeta for your device in Telegram or other sources, and flash it using
    ```
    odin4 -a vbmeta.tar
    ```
    > [!Note]
    > The file may not be named identically to what stands in this command, and may be in different path, so adjust accordingly. If the file is not wrapped into tar, using p7zip or other archiver with tar support, like stock `tar -cf`, pack your `vbmeta.img` into .tar, so you will have `vbmeta.tar`, because Odin4 is not going to do it for you.

11. Unplug the USB cable from your device.
12. Manually reboot into recovery, this may require pulling the device’s battery out and putting it back in, or if you have a non-removable battery, press the Volume Down + Power buttons for 8~10 seconds until the screen turns black & release the buttons _immediately_ when it does, then boot to recovery:
    * Reboot and immediately hold **Volume Up** + **Power** while the device is connected to a PC via USB cable.
    > [!Note]
    > Be sure to reboot into recovery immediately after installing the custom recovery. If you don’t the stock ROM will overwrite the custom recovery with the stock recovery, and you’ll need to flash it again.

# Installing ROM from recovery
1. Download the ROM installation package that you would like to install or build the package yourself.
    * LeafOS is shipped in Vanilla and GMS variants
    * LineageOS is Vanilla only
        * (Optionally): If you want to install an application package add-on such as [Google Apps](https://wiki.lineageos.org/gapps.html) (use the `arm64` architecture), please read and follow the instructions on [Google Apps page](https://wiki.lineageos.org/gapps.html)
    * Installing non-DragonFox ROMs in those recoveries also should be possible 
2. If you are not in recovery, reboot into recovery:
    * Reboot and immediately hold **Volume Up** + **Power** while the device is connected to a PC via USB cable.
3. Now tap **Factory Reset**, then **Format data** / **factory reset** and continue with the formatting process. This will remove encryption and delete all files stored in the internal storage, as well as format your cache partition (if you have one).
4. Return to the main menu.
5. Sideload the ROM `.zip` package but **do not reboot** before you read/followed the rest of the instructions!
    * On the device, select “Apply Update”
    * If you use adb, then select “Apply from ADB” to begin sideload.
        * On the host machine, sideload the package using: `adb sideload filename.zip`.
    > [!Note]
    > Normally, adb will report `Total xfer: 1.00x`, but in some cases, even if the process succeeds the output will stop at 47% and report `adb: failed to read command: Success`. In some cases it will report `adb: failed to read command: No error` or `adb: failed to read command: Undefined error: 0` which is also fine.
    * If you use sdcard, copy `.zip` package to sdcard, and select it in recovery, install it.

# Installing Add-Ons
> [!Note]
> If you don’t want to install any add-on (such as Google Apps, but this is not needed anyways if you're using GMS variant), you can skip this whole section!

> [!Note]
> Add-ons aren’t signed with ROM’s official key, and therefore when they are sideloaded, ROM Recovery will present a screen that says Signature verification failed, this is expected, please click Continue.

> [!Warning]
> If you want the Google Apps add-on on your device, you must follow this step before booting into ROM for the first time!

1. Repeat the sideload steps above for all desired packages in sequence.

# All set!
Once you have installed everything successfully, you can now reboot your device into the OS for the first time!
* Click the back arrow in the top left of the screen, then “Reboot system now”.

> [!Note]
> The first boot usually takes no longer than 15 minutes, depending on the device. If it takes longer, you may have missed a step, otherwise feel free to get assistance.

# Get assistance
After you’ve double checked that you followed the steps precisely, didn’t skip any and still have questions or got stuck, feel free to ask in [our telegram](https://t.me/dragonfoxchat) or in GitHub Issues here
