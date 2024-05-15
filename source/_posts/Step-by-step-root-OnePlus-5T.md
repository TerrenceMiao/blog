---
title: Step by step root OnePlus 5T
date: 2024-04-26 21:29:42
tags:
---

**OnePlus 5T**, first announced in Nov 2017. 7 years later, has been upgraded to Android 10.0.1, still robust and fast.

**NOTE**: Before you take on this brave journey, make sure **backup** all important files on the phone at first!

- First, unlock the bootloader, _https://www.lifewire.com/how-to-unlock-bootloader-android-phone-4689186_

- Enable **Developer Options** in **Settings** -> **About phone** -> **Build number**

![About phone](/img/OnePlus%205T%20-%20About%20phone.jpg "About phone")

- In **Settings** -> **System** -> **Developer options**, enable **Advanced reboot**, **OEM unlocking**, **USB Debugging**

![Developer options](/img/OnePlus%205T%20-%20Developer%20options.jpg "Developer options")

- Download and install Android SDK Platform Tools on Windows _https://developer.android.com/tools/releases/platform-tools_

- Run commands:

```
$ adb devices
List of devices attached
9b26c76 device

$ adb reboot bootloader
```

- Wait for phone to reboot till phone in the **Bootloader** mode, then run:

```
$ fastboot flashing unlock
```

- ON the phone will ask to confirm "UNLOCK THE BOOTLOADER". After UNLOCK, your phone **WILL BE RESET**, like a factory hard reset. **ALL APPS AND DATA ARE GONE**. Android system will be reinstalled.

- Go to OnePlus Smartphone Software Update site and download the latest version of OnePlus 5T update on Windows, _https://oneplus.net/in/support/softwareupdate_

- Unzip OnePlus 5T update on Widnows

- On the phone **Settings**, search for **USB Preferences**, select USE USB FOR **File transfer**

![USB Preferences](/img/OnePlus%205T%20-%20USB%20Preferences.jpg "USB Preferences")

- On Windows, in File Explorer, copy **OnePlus5TOxygen_43_OTA_069_all_2010292144_76910d123e3940e5/boot.img** file to _**ONEPLUS A5010 -> Internal shared storage -> Download**_ directory on the phone

- On the phone, download and install latest version Magisk, _https://github.com/topjohnwu/Magisk_

- Run Magisk, select Magisk Install, _https://topjohnwu.github.io/Magisk/install.html_

- Select and patch **boot.img** file under _**/Download**_ directory

![Magisk](/img/OnePlus%205T%20-%20Magisk.jpg "Magisk")

![Magisk select and patch](/img/OnePlus%205T%20-%20Magisk%20select%20and%20patch.jpg "Magisk select and patch")

![Magisk patch boot.img](/img/OnePlus%205T%20-%20Magisk%20patch%20boot.jpg "Magisk patch boot.img")

- A patched file **magisk_patched-27000_nplRf** successfully generated. On Windows, in File Explorer, copy it to local directory

- On Windows, run:

```
$ fastboot flash boot magisk_patched-27000_nplRf.img
```

**NOTE**: Always patch boot image on the **SAME** device where you run Magisk.

- Reboot the phone, and install `Root Checker` to verify Root access, _https://play.google.com/store/search?q=root%20checker&c=apps&hl=en&gl=US_

Now OnePlus 5T has been officially **ROOTED**!

**NOTE**: NO need to install **TWRP** (Team Win Recovery Project), _https://twrp.me_, a customised recovery application for Android devices on OnePlus 5T.
