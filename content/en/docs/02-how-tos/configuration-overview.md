---
title: Configuration Overview
description: 
weight: 1
---

The configuration file is presented as YAML, and contains several main entries:

- `config` : contains `configVersion` which tells RapiDAST how to consume the config file
- `application` : contains data relative to the application being scanned : name, etc.
- `general` : contains data that will be used by all the scanners, such as proxy configuration, etc.
  - Each scanner can override an entry from `general` by creating an entry with the same name
- `scanners` : list of scanners, and their configuration

[Enviornment variables](./configuration-using-envvar/) can be used for configuration, e.g. for secrets.

## Templates

See templates in the [config](https://github.com/RedHatProductSecurity/rapidast/tree/development/config) directory for rapidast configuration examples.

- `config-template-zap-tiny.yaml` : describes a bare minimum configuration, without authentication options.
- `config-template-zap-simple.yaml` : describes a generic/minimal use of the ZAP scanner (i.e.: the minimum set of option to get a ZAP scan from RapiDAST)
- `config-template-zap-mac.yaml` : describes a minimal use of the ZAP scanner on a Apple Mac environment
- `config-template-zap-long.yaml` : describes a more extensive use of ZAP (all configuration options are presented)
- `config-template-multi-scan.yaml` : describes how to combine multiple scanners in a single configuration
- `config-template-generic-scan.yaml` : describes the use of the generic scanner
- `config-template-garak-fast.yaml` : describes the use of the Garak LLM AI scanner for a fast scan
- `config-template-garak-baseline.yaml` : describes the use of the Garak LLM AI scanner for a baseline scan
- `config-template-garak-tool.yaml` : describes the use of the Garak LLM AI scanner for a full scan

See [here](https://github.com/RedHatProductSecurity/rapidast/tree/development/examples) for examples on how to run RapiDAST in various CI/CD pipelines.

## Basic example
Example bare minimum config file, without any Authentication options, and passive scanning only:

```yaml
config:
  configVersion: 5

application:
  shortName: "example-1.0"
  url: "https://example.com" # root URL of the application

scanners:
  zap:
    apiScan:
      apis:
        apiUrl: "https://example.com/api/v1/swagger.json" # URL to application openAPI spec
```