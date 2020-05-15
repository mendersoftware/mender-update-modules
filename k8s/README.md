## Description

The k8s Update Module is able to apply Kubernetes manifesto files (yaml) to the device running Mender.

Example use-cases:

* Edge or IoT devices running [k3s](https://k3s.io/)

### Specification

|||
| --- | --- |
|Module name| k8s |
|Supports rollback|no|
|Requires restart|no|
|Artifact generation script|yes|
|Full system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/k8s/module/k8s), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/blob/master/k8s/module-artifact-gen/k8s-artifact-gen)|

### Install the Update Module

Download the latest version of this Update Module by running:

```
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/k8s/module/k8s && chmod +x /usr/share/mender/modules/v3/k8s
```

This Update Module depends on the `kubectl` CLI.

### Create artifact

To download `k8s-artifact-gen`, run the following:

```
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/k8s/module-artifact-gen/k8s-artifact-gen && chmod +x k8s-artifact-gen
```

Generate Mender Artifacts using the following command:

```
ARTIFACT_NAME="my-update-1.0"
DEVICE_TYPE="my-device-type"
./k8s-artifact-gen --artifact-name ${ARTIFACT_NAME} \
                   --device-type ${DEVICE_TYPE} \
                  <path to yaml file> ...
```

This tool depends on the `kubectl` CLI.

### Maintainer

The author and maintainer of this Update Module is:

- Fabio Tranchitella - <fabio.tranchitella@northern.tech> - [tranchitella](https://github.com/tranchitella)

Always include the original author when suggesting code changes to this update module.
