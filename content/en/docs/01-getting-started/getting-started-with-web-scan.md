---
title: Getting started with a web scan
description: Getting started with a web scan
weight: 4
---


This tutorial shows you how to

1. Create a configuration file for testing the web application.
2. Run RapiDAST and get the results.
    - First run with passive scanning only, which can save time at the initial scanning phase. There are various situations that can cause an issue, not only from scanning set up but also from your application or test environment. Active scanning takes a long time in general.
    - Once passive Scanning has run successfully, run another scan with active scanning enabled in the configuration file.


## Web (OpenAPI based scanning)

Create a YAML config file(e.g. config.yaml) like the following:

```yaml
config:
  configVersion: 6

application:
  shortName: "example-1.0"
  url: "https://example.com" # root URL of the application

scanners:
  zap:
    apiScan:
      apis:
        apiUrl: "https://example.com/api/v1/swagger.json" # URL to application openAPI spec
```

Run a scan:

```sh
$ podman run -v ./config.yaml:/opt/rapidast/config/config.yaml:Z quay.io/redhatproductsecurity/rapidast:latest
```

See [here](./run-in-container.md) for more information on running a scan