## Description

The SWU Update Module allows deploying an SWUpdate-based artifact to the device

Example use-cases:
* extend an existing platform using SWUpdate with OTA

### Specification

|||
| --- | --- |
|Module name| swu |
|Supports rollback| no |
|Requires restart| configurable |
|Artifact generation script| yes |
|Full system updater| no |
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/swu/module/swu)|

### Install the Update Module

Download the latest version of this Update Module by running:

```bash
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/swu/module/swu && chmod +x /usr/share/mender/modules/v3/swu
```

### Create artifact

To download `swu-artifact-gen`, run the following:

```bash
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/install_snap/module-artifact-gen/install_snap-artifact-gen && chmod +x swu-artifact-gen
```

Generate Mender Artifacts using the following command:

```bash
ARTIFACT_NAME="my-update-1.0"
DEVICE_TYPE="my-device-type"
./swu-artifact-gen \
    --artifact-name ${ARTIFACT_NAME} \
    --device-type ${DEVICE_TYPE} \
    <name of swu file(s)> ...
```

### Optional reboot

The SWU Update Module supports rebooting after artifact installation. If this is required for the packaged SWU file(s), add the `--reboot` flag to the `swu-artifact-gen` invocation.

### Possible future improvements

- rollback support
- automatic determination of metadata from SWU file(s)
- enforced ordering of multiple SWUs
- streaming support

### Maintainer

The author and maintainer of this Update Module is:

- Josef Holzmayr - <josef.holzmayr@northern.tech> - [theyoctojester](https://github.com/theyoctojester)

Always include the original author when suggesting code changes to this update module.
