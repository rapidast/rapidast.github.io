---
title: Garak
description: Garak - LLM AI scanner
weight: 2
---


Garak is an LLM AI scanner developed by NVIDIA. See https://github.com/NVIDIA/garak for more information.

The following is an example to launch a scan:
```yaml
scanners:
  garak:
    parameters:
      plugins:
        model_type: huggingface
        model_name: gpt2
```