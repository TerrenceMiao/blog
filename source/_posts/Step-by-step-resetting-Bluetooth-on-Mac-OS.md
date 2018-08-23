---
title: Step by step resetting Bluetooth on Mac OS
date: 2018-08-23 20:30:49
tags:
---

Latest Mac OS High Sierra version 10.13.6.

* Open Terminal and login as user "root":

```console
# rm /Library/Preferences/com.apple.Bluetooth.plist
```

* Reset the Bluetooth Module wiht Option + Shift + click on bluetooth icon in taskbar. Click on Debug -> Reset the Bluetooth Module
* Reset PRAM / NVRAM: Shut down; power on, immediately press Command + Option + P + R until it resets itself a couple times. Official guide <https://support.apple.com/en-us/HT204063>
* Reset SMC: Shut down; hold down Shift + Control + Option while holding the power button for 10 seconds. Official guide <https://support.apple.com/en-us/HT201295>