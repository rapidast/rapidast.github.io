---
title: Run from source
description: Run RapiDAST from source code
weight: 3
---


Install dependencies and run RapiDAST directly on a host machine. Unless using the config setting of `general.container.type: podman`, scanners like [ZAP] are expected to be installed on the host system.

**Prerequisites**:

- `python` >= 3.6.8 (3.7 for MacOS/Darwin)
- `podman` >= 3.0.1
  - required when you want to run scanners from their container images, rather than installing them to your host.
- See `requirements.txt` for a list of required python libraries

**Setup**:

Clone the repository.

```sh
$ git clone https://github.com/RedHatProductSecurity/rapidast.git
$ cd rapidast
```

Create a virtual environment.

```sh
$ python3 -m venv venv
$ source venv/bin/activate
```

Install the project requirements.

```sh
(venv) $ pip install -U pip
(venv) $ pip install -r requirements.txt
```

**Run**:

Run RapiDAST script:

```sh
$ ./rapidast.py --config <path/to/config.yml>
```

There are more options.

```sh
usage: rapidast.py [-h] [--log-level {debug,info,warning,error,critical}]
                   [--config CONFIG_FILE] [--no-cleanup]

Runs various DAST scanners against a defined target, as configured by a
configuration file.

options:
  -h, --help            show this help message and exit
  --log-level {debug,info,warning,error,critical}
                        Level of verbosity
  --config CONFIG_FILE  Path to YAML config file
  --no-cleanup          Scanners to not cleanup their environment. (might be
                        useful for debugging purposes).
```


**Note**:

- Example minimum config expects scanners like [ZAP] to be available on the host, and will fail if not found. See [Execution Environments](#choosing-the-execution-environment) section for more info
- Results will be written to the `./results/` directory