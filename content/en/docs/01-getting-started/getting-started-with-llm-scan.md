---
title: Getting started with a LLM AI scan
description: Getting started with a LLM AI scan
weight: 5
---


This tutorial shows you how to

1. Create a configuration file for testing a LLM AI model. 
2. Run RapiDAST and get the results.

## LLM

Create a YAML config file like the following:

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

See [here](./examples/) for more examples on how to run RapiDAST in various CI/CD pipelines. 