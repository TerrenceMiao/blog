---
title: Running a Mac app from Unidentified Developer on MacOS Sequoia
date: 2024-09-19 10:16:23
tags:
---

Latest `MacOS Sequoia` has made another security enforcement. To open and run a Mac application, e.g. `VLC` nightly build at _https://nightlies.videolan.org/_, **Unidentified Developer** , the following steps can take.

- Enable "Allow Applications from Anywhere"

```
base ~
sudo spctl --master-disable
Password:
Globally disabling the assessment system needs to be confirmed in System Settings.
```

Go to **System Settings** -> **Privacy & Security**, select "Allow Applications from Anywhere":

![MacOS - Allow Applications from Anywhere](/img/MacOS%20-%20Allow%20Applications%20from%20Anywhere.png "MacOS - Allow Applications from Anywhere")

```
base ~
sudo spctl --master-enable
Password:
This operation is no longer supported. Please see the man page for more information.
```

- Disable MacOS Quarantine on the application

In this case application `VLC`:

```
base ~
xattr -dr com.apple.quarantine /Applications/VLC.app
```
