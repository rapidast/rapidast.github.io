---
title: Troubleshooting
description: 
weight: 5
---

### Hitting docker.io rate limits

If you are unable to pull/update an image from docker.io due to rate-limit errors, authenticate to your Docker Hub account.

### "Error getting access token" using OAuth2

Possible pitfalls :

- Make sure that the parameters are correct (`client_id`, `token_endpoint`, `rtoken_var_name`) and that the refresh token is provided (via environment variable), and is valid
- Make sure you do not have an environment variable in your current environment that overrides what is set in the `envFile`

### Issues with the ZAP scanner

The best way to start is to look at the ZAP logs, which are stored in `~/.ZAP/zap.log` (within the container where ZAP was running)

Example with podman, considering that the container was not wiped (either `--no-cleanup`, or the container failed):

```sh
[rapidast-ng]$ podman container list --all
969d721cc5a8  docker.io/owasp/zap2docker-stable:latest  /zap/zap.sh -conf...  2 days ago   Exited (1) 2 days ago (unhealthy)              rapidast_zap_vapi_JxgLjx
[rapidast-ng]$ podman unshare
bash-5.2# podman mount rapidast_zap_vapi_JxgLjx
/home/cedric/.local/share/containers/storage/overlay/a5450de782fb7264ff4446d96632e6512e3ff2275fd05329af7ea04106394b42/merged
bash-5.2# cd /home/cedric/.local/share/containers/storage/overlay/a5450de782fb7264ff4446d96632e6512e3ff2275fd05329af7ea04106394b42/merged
bash-5.2# tail home/zap/.ZAP/zap.log

org.zaproxy.zap.extension.openapi.converter.swagger.SwaggerException: Failed to parse swagger defn null
2023-02-17 22:42:55,922 [main ] INFO  CommandLine - Job openapi added 1 URLs
2023-02-17 22:42:55,922 [main ] INFO  CommandLine - Job openapi finished
2023-02-17 22:42:55,923 [main ] INFO  CommandLine - Automation plan failures:
2023-02-17 22:42:55,923 [main ] INFO  CommandLine -     Job openapi target: https://vapi.example.com/api/vapi/v1 error: Failed to parse OpenAPI definition.

org.zaproxy.zap.extension.openapi.converter.swagger.SwaggerException: Failed to parse swagger defn null
2023-02-17 22:42:55,924 [main ] INFO  Control - Automation Framework setting exit status to due to plan errors
2023-02-17 22:43:01,073 [main ] INFO  CommandLineBootstrap - OWASP ZAP 2.12.0 terminated.
```

### ZAP's plugins are missing from the host installation

This happens only when using the host's ZAP (with the `*.container.type: none` option).

If you see a message such as `Missing mandatory plugins. Fixing`, or ZAP fails with an error containing the string `The mandatory add-on was not found:`, this is because ZAP deleted the application's plugin.
See <https://github.com/zaproxy/zaproxy/issues/7703> for additional information.
RapiDAST works around this bug, but with little inconvenients (slower because it has to fix itself and download all the plugins)

- Verify that the host installation directory is missing its plugins.
e.g., in a MacOS installation, `/Applications/ZAP.app/Contents/Java/plugin/` will be mostly empty. In particular, no `callhome*.zap` and `network*.zap` file are present.
- Reinstall ZAP, but *DO NOT RUN IT*, as it would delete the plugins. Verify that the directory contains many plugins.
- `chown` the installation files to root, so that when running ZAP, the application running as the user does not have sufficient permission to delete its own plugins

### ZAP crashing with `java.lang.OutOfMemoryError: Java heap space`

ZAP allows the JVM heap to grow up to a quarter of the RAM. The value can be increased using the `scanners.zap.miscOptions.memMaxHeap` configuration entry

```sh
2023-09-04 08:44:37,782 [main ] INFO  CommandLine - Job openapi started
2023-09-04 08:44:46,985 [main ] INFO  CommandLineBootstrap - OWASP ZAP 2.13.0 terminated.
2023-09-04 08:44:46,985 [main ] ERROR UncaughtExceptionLogger - Exception in thread "main"
java.lang.OutOfMemoryError: Java heap space
        at java.lang.AbstractStringBuilder.<init>(AbstractStringBuilder.java:86) ~[?:?]
        at java.lang.StringBuilder.<init>(StringBuilder.java:116) ~[?:?]
        at com.fasterxml.jackson.core.util.TextBuffer.contentsAsString(TextBuffer.java:487) ~[?:?]
        at com.fasterxml.jackson.core.io.SegmentedStringWriter.getAndClear(SegmentedStringWriter.java:99) ~[?:?]
        at com.fasterxml.jackson.databind.ObjectWriter.writeValueAsString(ObjectWriter.java:1141) ~[?:?]
        at io.swagger.v3.core.util.Json.pretty(Json.java:24) ~[?:?]
        at org.zaproxy.zap.extension.openapi.ExtensionOpenApi.importOpenApiDefinitionV2(ExtensionOpenApi.java:371) ~[?:?]
        at org.zaproxy.zap.extension.openapi.automation.OpenApiJob.runJob(OpenApiJob.java:123) ~[?:?]
        at org.zaproxy.addon.automation.ExtensionAutomation.runPlan(ExtensionAutomation.java:366) ~[?:?]
        at org.zaproxy.addon.automation.ExtensionAutomation.runAutomationFile(ExtensionAutomation.java:507) ~[?:?]
        at org.zaproxy.addon.automation.ExtensionAutomation.execute(ExtensionAutomation.java:621) ~[?:?]
        at org.parosproxy.paros.extension.ExtensionLoader.runCommandLine(ExtensionLoader.java:553) ~[zap-2.13.0.jar:2.13.0]
        at org.parosproxy.paros.control.Control.runCommandLine(Control.java:426) ~[zap-2.13.0.jar:2.13.0]
        at org.zaproxy.zap.CommandLineBootstrap.start(CommandLineBootstrap.java:91) ~[zap-2.13.0.jar:2.13.0]
        at org.zaproxy.zap.ZAP.main(ZAP.java:94) ~[zap-2.13.0.jar:2.13.0]
```

### ZAP crashed while parsing the OpenAPI due to its size

```sh
2024-02-29 19:35:24,526 [main ] INFO  CommandLine - Job openapi started
2024-02-29 19:35:25,576 [main ] WARN  DeserializationUtils - Error snake-parsing yaml content
io.swagger.v3.parser.util.DeserializationUtils$SnakeException: Exception safe-checking yaml content  (maxDepth 2000, maxYamlAliasesForCollections 2147483647)
    at io.swagger.v3.parser.util.DeserializationUtils$CustomSnakeYamlConstructor.getSingleData(DeserializationUtils.java:483) ~[openapi-beta-37.zap:?]
    at org.yaml.snakeyaml.Yaml.loadFromReader(Yaml.java:493) ~[openapi-beta-37.zap:?]
.........

2024-02-29 19:35:25,639 [main ] ERROR DeserializationUtils - Error parsing content
com.fasterxml.jackson.dataformat.yaml.JacksonYAMLParseException: The incoming YAML document exceeds the limit: 3145728 code points.
 at [Source: (StringReader); line: 49813, column: 50]
..........

2024-02-29 19:35:25,702 [main ] WARN  OpenAPIV3Parser - Exception while parsing:
com.fasterxml.jackson.dataformat.yaml.JacksonYAMLParseException: The incoming YAML document exceeds the limit: 3145728 code points.
 at [Source: (StringReader); line: 49813, column: 50]
```

Solutions:

- If you are using a Swagger v2 definition, try converting it to v3 (OpenAPI)
- Set a `maxYamlCodePoints` Java proprety with a big value, which can be passed using environment variables (via the `config.environ.envFile` config entry): `_JAVA_OPTIONS=-DmaxYamlCodePoints=99999999`

### ZAP's Ajax Spider failing

#### Insufficient Resources

Zap's Ajax Spider makes use of a lot of resources, in particular:

- Shared Memory (`/dev/shm`)
- processes

If you see evidence of Firefox crashing, either via in the `zap.log` files stored in `session.tar.gz` file (see below for examples of such evidence), or logged by an external crash report (such as `abrtd` for example).

`zap.log` hints for Firefox crashing:

```sh
2024-07-04 11:21:32,061 [ZAP-AjaxSpiderAuto] WARN  SpiderThread - Failed to start browser firefox-headless
com.google.inject.ProvisionException: Unable to provision, see the following errors:

1) [Guice/ErrorInCustomProvider]: SessionNotCreatedException: Could not start a new session. Response code 500. Message: Failed to decode response from marionette
```

Or the following:

```sh
2024-07-04 12:23:28,027 [ZAP-AjaxSpiderAuto] ERROR UncaughtExceptionLogger - Exception in thread "ZAP-AjaxSpiderAuto"
java.lang.OutOfMemoryError: unable to create native thread: possibly out of memory or process/resource limits reached
```

This issue may also be apparent *outside* of the spider, in particular, the following error being printed on the RapiDAST output is likely an evidence that the maximum number of concurrent thread is currently reached:

```sh
Failed to start thread "Unknown thread" - pthread_create failed (EAGAIN) for attributes: stacksize: 1024k, guardsize: 0k, detached.
```

Solutions:

- Selenium, used to control Firefox, uses shared memory (`/dev/shm/`). When using the RapiDAST image or the ZAP image, the user needs to make sure that sufficient space is available in `/dev/shm/` (in podman, by default, its size is 64MB). A size of 2G is the recommended value by the Selenium community. In podman for example, the option would be `--shm-size=2g`.
- Zap and Firefox can create a huge numbers of threads. Some container engines will default to 2048 concurrent pids, which is not sufficient for the Ajax Spider. Whenever possible, RapiDAST will check if that limit was reached, after the scan is finished, and prints a warning if this happened. In podman, increasing the maximum number of concurrent pids is done via the `--pids-limit=-1` option to prevent any limits.

### Podman errors

#### subuid/subgid are not enabled

If you see one of those errors:

```sh
Error: copying system image from manifest list: writing blob: adding layer with blob "sha256:82aabceedc2fbf89030cbb4ff98215b70d9ae35c780ade6c784d9b447b1109ed": processing tar file(potentially insufficient UIDs or GIDs available in user namespace (requested 0:42 for /etc/gshadow): Check /etc/subuid and /etc/subgid if configured locally and run "podman system migrate": lchown /etc/gshadow: invalid argument): exit status 1
```

 -or-

```sh
Error: parsing id map value "-1000": strconv.ParseUint: parsing "-1000": invalid syntax
```

Podman, in rootless mode (running as a regular user), needs subuid/subgit to be enabled: [rootless mode](https://docs.podman.io/en/latest/markdown/podman.1.html#rootless-mode)

## Caveats

- Currently, RapiDAST does not clean up the temporary data when there is an error. The data may include:
  - a `/tmp/rapidast_*/` directory
  - a podman container which name starts with `rapidast_`

This is to help with debugging the error. Once confirmed, it is necessary to manually remove them.