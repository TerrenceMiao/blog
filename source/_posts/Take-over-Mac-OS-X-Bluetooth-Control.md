---
title: Take over Mac OS X Bluetooth Control
date: 2018-06-09 11:45:17
tags:
---

![Bluetooth on Mac OS X](https://support.apple.com/library/content/dam/edam/applecare/images/en_US/osx/el_capitan-system_preferences-bluetooth.png "Bluetooth on Mac OS X")

Quite annoying when Bluetooth on Mac OS X is off, you can't turn it on manually via Systme Preferences interface. Now you can install **blueutil** from <https://github.com/toy/blueutil> to solve this issue.

```bash
igloo:cheatsheets root# blueutil --help
blueutil v2.1.0

Usage:
  blueutil [options]

Without options outputs current state

    -p, --power               output power state as 1 or 0
    -p, --power STATE         set power state
    -d, --discoverable        output discoverable state as 1 or 0
    -d, --discoverable STATE  set discoverable state

    -h, --help                this help
    -v, --version             show version

STATE can be one of: 1, on, 0, off, toggle
```

Firstly, turn off Bluetooth service via command line:

```bash
igloo:cheatsheets root# blueutil -p 0
igloo:cheatsheets root# blueutil -d 0
```

Then turn on Bluetooth service via command line:

```bash
igloo:cheatsheets root# blueutil -d 1
igloo:cheatsheets root# blueutil -p 1
```