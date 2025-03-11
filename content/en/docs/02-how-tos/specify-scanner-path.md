---
title: Specifiying scanner path
description: 
weight: 8
---

When running RapiDAST scans from source on your localhost, specify the path to the scanning tool if it is not included in your system's PATH.

For macOS, the default path to ZAP is typically `/Applications/ZAP.app/Contents/Java/zap.sh`.

Example:

```yaml
scanners:
  zap:
    container:
      parameters:
        executable: "/Applications/ZAP.app/Contents/Java/zap.sh"
```

