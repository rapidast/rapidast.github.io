---
title: Nessus
description: Nessus for vulnerability scanning across various targets (requiring a user's own license)
weight: 3
---

Nessus is a vulnerability scanner developed by Tenable, Inc. It helps organizations identify and address security vulnerabilities across various systems, devices, and applications.

The following is an example to launch a scan:

```yaml
scanners:
  nessus:
    server:
      url: https://nessus-example.com/ # URL of Nessus instance
      username: foo # OR username_from_var: NESSUS_USER
      password: bar # OR password_from_var: NESSUS_PASSWORD
    scan:
      name: test-scan # name of new scan to create
      folder: test-folder # name of folder in to contain scan
      policy: "py-test" # policy used for scan
      # timeout: 600 # timeout in seconds to complete scan
      targets:
      - 127.0.0.1
```