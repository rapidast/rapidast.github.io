---
title: Getting started with a LLM AI scan
description: Getting started with a LLM AI scan
weight: 5
---


This tutorial shows you how to

1. Create a configuration file for testing a LLM AI model. 
2. Run RapiDAST and get the results.

## LLM

Create a YAML config file(e.g. config.yaml) like the following:

```yaml
config:
  configVersion: 6

application:
  shortName: "garak-test-1.0"

scanners:
  garak:
    model_type: huggingface                         # required, e.g. hugginngface, openai, rest
    model_name: gpt2   
```

Run a scan:

```sh
$ podman run -v ./config.yaml:/opt/rapidast/config/config.yaml:Z quay.io/redhatproductsecurity/rapidast:latest
```

See [here](./run-in-container.md) for more information on running a scan