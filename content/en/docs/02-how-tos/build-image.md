---
title: Build a RapiDAST image
description: 
weight: 9
---

If you want to build your own RapiDAST image, run the following command.

`$ podman build . -f containerize/Containerfile -t <image-tag>`

Disclaimer: This tool is not intended to be run as a long-running service. Instead, it is designed to be run for a short period of time while a scan is being invoked and executed in a separate test environment. If this tool is used solely for the scanning purposes, vulnerabilities that may be indicated to exist in the image will not have a chance to be exploited. The user assumes all risks and liability associated with its use.