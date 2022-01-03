+++
title = "Showcases"
description = "Showcases"
weight = 1
+++

# Security policy
## Reporting
(button) [EMAIL SECURITY@RUST-LANG.ORG]

Safety is one of the core principles of Rust, and to that end, we would like to ensure that Rust has a secure implementation. Thank you for taking the time to responsibly disclose any issues you find.

All security bugs in the Rust distribution should be reported by email to security@rust-lang.org. This list is delivered to a small security team. Your email will be acknowledged within 24 hours, and you’ll receive a more detailed response to your email within 48 hours indicating the next steps in handling your report. If you would like, you can encrypt your report using our public key. This key is also On MIT’s keyserver and reproduced below.

This email address receives a large amount of spam, so be sure to use a descriptive subject line to avoid having your report be missed. After the initial reply to your report, the security team will endeavor to keep you informed of the progress being made towards a fix and full announcement. As recommended by RFPolicy, these updates will be sent at least every five days. In reality, this is more likely to be every 24-48 hours.

If you have not received a reply to your email within 48 hours, or have not heard from the security team for the past five days, there are a few steps you can take (in order):

- Contact the current security coordinator (Steve Klabnik (public key)) directly.
- Contact the back-up contact (Pietro Albini (public key)) directly.
- Post on the internals forums

Please note that the discussion forums are public areas. When escalating in these venues, please do not discuss your issue. Simply say that you’re trying to get a hold of someone from the security team.

(button) [EMAIL SECURITY@RUST-LANG.ORG]

## Disclosure policy
The Rust project has a 5 step disclosure process.

- The security report is received and is assigned a primary handler. This person will coordinate the fix and release process.
- The problem is confirmed and a list of all affected versions is determined.
- Code is audited to find any potential similar problems.
- Fixes are prepared for all releases which are still under maintenance. These fixes are not committed to the public repository but rather held locally pending the announcement.
- On the embargo date, the Rust security mailing list is sent a copy of the announcement. The changes are pushed to the public repository and new builds are deployed to rust-lang.org. Within 6 hours of the mailing list being notified, a copy of the advisory will be published on the Rust blog.

This process can take some time, especially when coordination is required with maintainers of other projects. Every effort will be made to handle the bug in as timely a manner as possible, however it’s important that we follow the release process above to ensure that the disclosure is handled in a consistent manner.

## Receiving security updates
The best way to receive all the security announcements is to subscribe to the Rust security announcements mailing list (alternatively by sending an email to rustlang-security-announcements+subscribe@googlegroups.com). The mailing list is very low traffic, and it receives the public notifications the moment the embargo is lifted.

We will announce vulnerabilities 72 hours before the embargo is lifted to distros@openwall, so that Linux distributions can update their packages.

### Plaintext PGP key
```
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1

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
u05l
=SPB7
-----END PGP PUBLIC KEY BLOCK-----
```