---
title: M365 user and Open Authentication (OATH)
date: 2026-10-17 11:49:13
tags:
---

Prerequisites
-------------

A Microsoft M365 user, work / school account, with M365 Copilot, premium or not.

Setting up a **customised** Authentication, without using the **Microsoft Authenticator**:

- Login work / school Microsoft account and land on **Security info** _https://mysignins.microsoft.com/security-info_

- Add a sign-in method

![Add a sign-in method](/img/M365%20-%20Add%20a%20sign-in%20method.png "Add a sign-in method")

- Install Microsoft Authenticator

Choose 'Set up a different authentication app'.

![Install Microsoft Authenticator](/img/M365%20-%20Authenticator.png "Install Microsoft Authenticator")

-  Set up your account in app

Click next.

![Set up your account in app](/img/M365%20-%20Set%20up%20your%20account%20in%20app.png "Set up your account in app")

- Scan the QR code

Select 'Can't scan the QR code?'.

![Scan the QR code](/img/M365%20-%20Scan%20the%20QR%20code.png "Scan the QR code")

- Enter the following into Authenticator

Copy the Secret key, which is a **base32** encoded.

![Enter the following into Authenticator](/img/M365%20-%20Secret%20key.png "Enter the following into Authenticator")

Generate a 6-digit code from the secret key to finish enrollment, with `oathtool`.

```
$ oathtool --totp -b "THE_BASE32_SECRET"
732501
```

Then paste the **seed / secret key** into the password manager, and enter that generated 6-digit code.


Concepts
--------

- `oathtool` is a command-line utility used to generate and validate **Open Authentication (OATH)** one-time passwords.
- **TOTP** (Time-based One Time Password): Generates time variant codes (default 30-second steps, supports SHA1, SHA256, and SHA512).
- **HOTP** (HMAC-based One Time Password): Generates event-based counters by default.


References
----------

- m365-copilot-proxy _https://github.com/cramt/m365-copilot-proxy_
