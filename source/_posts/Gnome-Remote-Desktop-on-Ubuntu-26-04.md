---
title: Gnome Remote Desktop on Ubuntu 26.04
date: 2026-06-02 12:05:43
tags:
---

Latest `Ubuntu 26.04 LTS` has dropped the support of **X11 Server** and **xRDP** and moved to the adoption of **Wayland Display Server**.  Old approach with **xRDP** doesn't work anymore. The solution / fix is the new **Gnome Remote Desktop**.

![Gnome Remote Desktop](/img/Gnome%20Remote%20Desktop.png "Gnome Remote Desktop")

Generate Remote Desktop Server self-signed certificate:

```
$ sudo openssl req -newkey rsa:2048 -nodes -keyout /var/lib/gnome-remote-desktop/rdp-tls.key -x509 -days 3650 -out /var/lib/gnome-remote-desktop/rdp-tls.crt -subj "/CN=$(hostname)"

$ sudo chown gnome-remote-desktop:gnome-remote-desktop /var/lib/gnome-remote-desktop/rdp-tls.key
$ sudo chown gnome-remote-desktop:gnome-remote-desktop /var/lib/gnome-remote-desktop/rdp-tls.crt
```

Register self-signed certificate with the system daemon:

```
$ sudo grdctl --system rdp set-tls-key /var/lib/gnome-remote-desktop/rdp-tls.key
$ sudo grdctl --system rdp set-tls-cert /var/lib/gnome-remote-desktop/rdp-tls.crt

$ sudo grdctl --system rdp set-credentials $(whoami) rdp-user-password

$ sudo grdctl --system rdp enable
$ sudo systemctl restart gnome-remote-desktop.service
```

After Gnome Remote Desktop restarted, in the log:

```
$ sudo journalctl -u gnome-remote-desktop.service -f
...
Jun 06 22:21:17 Aorus systemd[1]: Starting gnome-remote-desktop.service - GNOME Remote Desktop...
Jun 06 22:21:17 Aorus gnome-remote-desktop-daemon[24618]: Init TPM credentials failed because Failed to initialize transmission interface context: tcti:IO failure, using GKeyFile as fallback
Jun 06 22:21:17 Aorus systemd[1]: Started gnome-remote-desktop.service - GNOME Remote Desktop.
Jun 06 22:21:17 Aorus gnome-remote-desktop-daemon[24618]: RDP server started

```

Show Remote Desktop Server status:

```
$ sudo grdctl --system status --show-credentials
Overall:
        Unit status: active
RDP:
        Status: enabled
        Port: 3389
        Authentication methods: credentials
        TLS certificate: /var/lib/gnome-remote-desktop/rdp-tls.crt
        TLS fingerprint: 71:f1:20:64:fe:e3:58:97:95:8d
        TLS key: /var/lib/gnome-remote-desktop/rdp-tls.key
        Kerberos keytab: (null)
        Username: terrence
        Password: rdp-user-password
```

When connect from `Windows App (Microsoft Remote Desktop)`, error thrown in log:

```
$ sudo journalctl -u gnome-remote-desktop.service -f
...
Jun 02 12:40:22 NucBox gnome-remote-desktop-daemon[58071]: [RDP] Sending server redirection
Jun 02 12:40:22 NucBox gnome-remote-desktop-daemon[58071]: [12:40:22:884] [58071:0004752e] [ERROR][com.freerdp.core.peer] - [rdp_set_error_info]: ERRINFO_LOGOFF_BY_USER [0x0001000C]
Jun 02 12:40:22 NucBox gnome-remote-desktop-daemon[58071]: [RDP] Network or intentional disconnect, stopping session
```

However, choose a different Remote Desktop Client like `Royal TSX` _https://royalapps.com/ts/mac/features_, works fine.

> **NOTE:** This is a well-documented, well-known incompatibility between Windows App on macOS and GNOME Remote Desktop's "server redirection" handshake — not something specific to 26.04, and there's a widely confirmed fix.

What's actually happening: in "Remote Login" (system) mode, GNOME Remote Desktop doesn't hand you straight into a session. It first accepts the connection, then sends an RDP Server Redirection PDU telling the client "reconnect, here's the real session token" (that's **[RDP] Sending server redirection** line — this part is normal and means auth succeeded). Windows App on Mac has a bug/quirk where it doesn't follow that redirection properly by default, so it just logs off immediately — hence **ERRINFO_LOGOFF_BY_USER** right after.  

1. Export the connection as an .rdp file

2. Edit the file to force redirection handling - Find the line `use redirection server name:i:0` and change the `0` to a `1`. This is the specific setting Windows App needs to correctly follow GNOME's server-redirection handshake instead of dropping the session.

3. Delete old entry and import the edited .rdp file into Windows App


References
----------

- xRDP – Easy install xRDP on Ubuntu 22.04,24.04,25.10,26.04 (Script Version 1.6)- Maintenance Release _https://c-nergy.be/blog/?p=20369_