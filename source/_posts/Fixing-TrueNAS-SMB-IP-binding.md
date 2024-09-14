---
title: Fixing TrueNAS SMB IP binding
date: 2024-09-14 12:37:53
tags:
---

After changing network settings, `TrueNAS` IP address has been updated. If modify SMB configuration, error like:

```
smb_update.bindip.0: IP address [192.168.0.51] is not a configured address for this server
```

thrown.

To reset and clean up already bind IP, login TrueNAS Console and run:

```
root@TrueNAS[~]# midclt call smb.update '{"bindip": []}'
{
    "id": 1,
    "netbiosname": "TRUENAS",
    "netbiosalias": [],
    "workgroup": "IGLOO STUDIO",
    "description": "TrueNAS Server",
    "unixcharset": "UTF-8",
    "loglevel": "MINIMUM",
    "syslog": false,
    "aapl_extensions": true,
    "Localmaster": true,
    "guest": "nobody",
    "filemask"': "",
    "dirmask"': "",
    "smb_options": "",
    "bindip": [],
    "cifs_SID": "S-1-5-21-2487580926-3122677641-100607549",
    "ntImv1_auth": false,
    "enable_smb1": false,
    "admin_group": null,
    "next_rid": 0,
    "multichannel": true,
    "netbiosname_local": "TRUENAS"
}
```

then can modify SMB configuration successfully.

References
----------

- Help with SMB IP binding, _https://www.reddit.com/r/freenas/comments/fovpfz/help_with_smb_ip_binding_
