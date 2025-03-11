---
title: Run in container
description: Run RapiDAST in docker container
weight: 2
---


Run the pre-built [rapidast container image](https://quay.io/repository/redhatproductsecurity/rapidast), which includes scanners like [ZAP]. Not compatible with config files using `general.container.type` set to `podman`.

**Prerequisites**:

- `docker` / `podman` (>= v3.0.1)

**Run**:

```sh
$ podman run -v ./config.yaml:/opt/rapidast/config/config.yaml:Z quay.io/redhatproductsecurity/rapidast:latest
```

**Note**:

- Sample config is very minimal and has no [Authentication](#authentication) enabled
- The `:Z` option is only necessary on RHEL/CentOS/Fedora systems with SELinux enabled
- To retrieve scan results, add a volume mount like `-v ./results/:/opt/rapidast/results/:Z`. The permissions of the `./results/` directory may need to be modified first with a command like `chmod o+w ./results/` to be writeable by the rapidast user in the container.
- RapiDAST images are currently built on the linux/amd64 architecture only. To run a scan on MacOS(arm64), use `--platform=linux/amd64`.
