---
title: Multiple scans at once
description: 
weight: 7
---

It is possible to run a scanner several times with different configurations. This is done by adding a different identifier to each scan, by appending `_<id>` to the scanner name.

For example :

```yaml
scanners:
  zap_unauthenticated:
    apiScan:
      apis:
        apiUrl: "https://example.com/api/openapi.json"

  zap_authenticated:
    authentication:
      type: "http_basic"
      parameters:
        username: "user"
        password: "mypassw0rd"
    apiScan:
      apis:
        apiUrl: "https://example.com/api/openapi.json"
```

In the example above, the ZAP scanner will first run without authentication, and then rerun again with a basic HTTP authentication.
The results will be stored in their respective names (i.e.: `zap_unauthenticated` and `zap_authenticated` in the example above).