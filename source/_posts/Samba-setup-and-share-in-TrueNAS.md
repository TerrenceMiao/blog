---
title: Samba setup and share in TrueNAS
date: 2024-08-07 22:19:00
tags:
---

`TrueNAS` installed and a few steps to setup to let users access shared `Samba` directories.

- Enable `SMB` Samba service in `TrueNAS`

![TrueNAS - Enable SMB service](/img/TrueNAS%20-%20Enable%20SMB%20service.png "TrueNAS - Enable SMB service")

![TrueNAS - SMB Settings](/img/TrueNAS%20-%20SMB%20Settings.png "TrueNAS - SMB Settings")

- Create an user account with home directory and all access permisssions

![TrueNAS - Create user](/img/TrueNAS%20-%20Create%20user.png "TrueNAS - Create user")

- Share `TrueNAS` pool created

![TrueNAS - Sharing](/img/TrueNAS%20-%20Sharing.png "TrueNAS - Sharing")

Then can access shared directory from `TrunNAS` in both Windows and Mac:

![TrueNAS - Access](/img/TrueNAS%20-%20Access.png "TrueNAS - Access")

Make sure there is NO a lock icon on the folder (can unlock the folder in MacOS Finder)
