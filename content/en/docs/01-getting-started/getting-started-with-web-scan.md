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

```yaml
config:
  configVersion: 6

application:
  shortName: "jechoi-garak-test"
  url: "localhost:1313"

scanners:
  zap:
    spider:
      url: ""
```


See [here](./examples/) for more examples on how to run RapiDAST in various CI/CD pipelines. 