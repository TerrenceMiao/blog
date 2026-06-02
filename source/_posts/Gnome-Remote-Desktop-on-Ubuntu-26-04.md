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

$ sudo chmod 600 /var/lib/gnome-remote-desktop/rdp-tls.key
$ sudo chmod 644 /var/lib/gnome-remote-desktop/rdp-tls.crt
```

Register self-signed certificate with the system daemon:

```
$ sudo grdctl --system rdp set-tls-key /var/lib/gnome-remote-desktop/rdp-tls.key
$ sudo grdctl --system rdp set-tls-cert /var/lib/gnome-remote-desktop/rdp-tls.crt

$ sudo grdctl --system rdp set-credentials $(whoami) rdp-user-password

$ sudo grdctl --system rdp enable
$ sudo systemctl restart gnome-remote-desktop.service
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

> **NOTE:**  According to A.I., GNOME Remote Desktop **--system** mode uses Server Redirection — after you authenticate at the system level, it redirects the RDP client to your actual user session on a different internal port. Microsoft Remote Desktop on Mac sometimes rejects this redirection for self-signed certificates because the certificate on the redirected session doesn't match what it expects. And use user-level approach instead.

However, choose a different Remote Desktop Client like `Royal TSX` _https://royalapps.com/ts/mac/features_, works fine.


References
----------

- xRDP – Easy install xRDP on Ubuntu 22.04,24.04,25.10,26.04 (Script Version 1.6)- Maintenance Release _https://c-nergy.be/blog/?p=20369_