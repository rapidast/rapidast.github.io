---
title: Generic plugin
description: Generic plugin for advanced users
weight: 4
---

In addition to the scanners mentioned above, RapiDAST can run any other scanning tools. It is possible to request RapiDAST to run a command and process stdout results, using the `generic` plugin. One use case is to run your own tools or scripts and export the results to Google Cloud Storage.

The following is an example to run a command or a tool in the host where a RapiDAST scan runs:

```yaml
scanners:
  generic:
    results: "*stdout"

    # this config is used when container.type is not 'podman'
    toolDir: scanners/generic/tools
    inline: "echo 'any scan'"
```

(an experimental feature) The following example is to scan a Kubernetes Operator's controller code for a command injection attack:

```yaml
scanners:
  generic:
    results: "*stdout"

    # this config is used when container.type is not 'podman'
    # toolDir: scanners/generic/tools
    inline: "python3 oobtkube.py -d 300 -p <port> -i <ipaddr> -f <cr_example>.yaml"
```

The following is another example to run a [Trivy](https://github.com/aquasecurity/trivy) scan using the container image:

```yaml
scanners:
  generic:
    results: "*stdout"

    container:
      type: "podman"
      parameters:
        image: "docker.io/aquasec/trivy"
        command: "image docker.io/aquasec/trivy"
```

The `results` entry works as follow:

- if it is missing or `*stdout`, the output of the command will be chosen and stored as `stdout-report.txt` in the result directory
- if it is a directory, it will be recursively copied into the result directory
- if it is a file, it will be copied into the result directory

**Notes**:

- `command` can be either a list of string, or a single string which will be split using `shlex.split()` - when using `*.container.type: podman`, the results (if different from stdout) must be present on the host after podman has run, which likely means you will need to use the `container.parameters.volumes` entry to share the results between the container and the host.
- See `config/config-template-generic-scan.yaml` for additional options.