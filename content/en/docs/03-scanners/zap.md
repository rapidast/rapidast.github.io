---
title: ZAP
description: ZAP - web scanner
weight: 1
---

ZAP (Zed Attack Proxy) is an open-source DAST tool. It can be used for scanning web applications and API.

See <https://www.zaproxy.org/> for more information.

##### Methodology

ZAP needs to be pointed to a list of endpoints to the tested application. Those can be:

- A regular HTML page
- A REST endpoint
- A GraphQL interface

The GraphQL interface can be provided to RapiDAST via the `graphql` configuration entry. It requires the URL of the GraphQL interface and the GraphQL schema(if available), in order to be scanned. Additional options are available. See the `config-template-zap-long.yaml` configuration template file for a list of options.

The other endpoints can be provided via several methods, discussed in the chapters below.

###### an OpenAPI schema

This is the prefered method, to be used whenever possible.
RapiDAST accepts OpenAPI v2(formerly known as Swagger) and v3 schemas. These schemas will describe a list of endpoints, and for each of them, a list of parameters accepted by the application.

###### Build the endpoint list using a spider/crawler

In this method, RapiDAST is given a Web entrypoint. The crawler will download that page, extract a list of URLs and recursively crawl all of them. The entire list of URLs found is then provided to the scanner.

There are two crawlers available:

- Basic spider: the list of URLs will be searched in the HTML tags (e.g.: `<a>`, `<img>`, etc.)
- Ajax spider: this crawler will run a real browser (by default: firefox headless), allowing the dynamic execution of Javascripts from each page found. This method will find URLs generated dynamically.

See the `spider` and `spiderAjax` configuration entries in the `config-template-zap-long.yaml` configuration template file for a list of options available.

###### A list of endpoints

A file containing a list of URLs corresponding to endpoints and their parameters.

Example of file:

```
https://example.com/api/v3/groupA/functionA?parameter1=abc&parameter2=123
https://example.com/api/v3/groupB/functionB?parameter1=def&parameter2=456
```

Only GET requests will be scanned.

##### ZAP scanner specific options

Below are some configuration options that are worth noting, when running a RapiDAST scan with the ZAP scanner.

- (`*.container.type: podman` only) Inject the ZAP container in an existing Pod:

It is possible to gather both RapiDAST and the tested application into the same podman Pod and run a scan against the application. This might help CI/CD automation & clean-up.
In order to do that, the user must create the Pod prior to running RapiDAST, and indicate its name in the RapiDAST configuration: `scanners.zap.container.parameters.podName`.
However, it is currently necessary to map the host user to UID 1000 / GID 1000 manually during the creation of the Pod using the `--userns=keep-id:uid=1000,gid=1000` option
Example: `podman pod create --userns=keep-id:uid=1000,gid=1000 myApp_Pod`

- (when running scans on the desktop with the `*.container.type: none` configuration only) Enable ZAP's Graphical UI:

This is useful for debugging.  Set `scanners.zap.miscOptions.enableUI: True` (default: False).  Then, the ZAP desktop will run with GUI on your host and show the progress of scanning.

- Enable add-on updates:

Set `scanners.zap.miscOptions.updateAddons: True` (default: False). ZAP will first update its addons and then run the scan.

- Install additional addons:

Set `scanners.zap.miscOptions.additionalAddons: "comma,separated,list,of,addons"` (default: []). Prior to running a scan, ZAP will install a given list of addons. The list can be provided either as a YAML list, or a string of the addons, separated by a comma.

- Force maximum heap size for the JVM:

Set `scanners.zap.miscOptions.memMaxHeap` (default: ¼ of the RAM), similarly to Java's `-Xmx` option.

Example:

```yaml
scanners:
    zap:
        container:
            parameters:
                podName: "myApp_Pod"
        miscOptions:
            enableUI: True
            updateAddons: False
            memMaxHeap: "6144m"
```

- To use ZAP's '-config' option:

Set `scanners.zap.miscOptions.overrideConfigs` with the same value as you would run with ZAP's '-config' option. It allows RapiDAST to run additional '-config' options when it invokes the ZAP cli command. This can be useful to set a value for Path parameters of the OpenAPI specification. The following example will allow RapiDAST to send the 'default' value to the `{namespace}` parameter in your OpenAPI file.

Example:

```yaml
scanners:
  zap:
    overrideConfigs:
      - formhandler.fields.field(0).fieldId=namespace
      - formhandler.fields.field(0).value=default
```
