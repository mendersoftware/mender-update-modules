## Description

The DFU Update Module is able to update peripheral devices connected to the device running Mender.

Example use-cases:
* Deploy firmware updates to peripheral devices using the USB Device Firmware Update (DFU) protocol

For a more detailed tutorial on how to use this Update Module please visit [Mender Hub](https://hub.mender.io/t/device-firmware-update-dfu)

### Specification

|||
| --- | --- |
|Module name| dfu |
|Supports rollback|no|
|Requires restart|no|
|Artifact generation script|yes|
|Full system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/dfu/module/dfu), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/blob/master/dfu/module-artifact-gen/dfu-artifact-gen)|

### Install the Update Module

Download the latest version of this Update Module by running:

```
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/dfu/module/dfu && chmod +x /usr/share/mender/modules/v3/dfu
```

### Create artifact

To download `dfu-artifact-gen`, run the following:

```
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/dfu/module-artifact-gen/dfu-artifact-gen && chmod +x dfu-artifact-gen
```

Generate Mender Artifacts using the following command:

```
ARTIFACT_NAME="my-update-1.0"
DEVICE_TYPE="my-device-type"
./dfu-artifact-gen --artifact-name ${ARTIFACT_NAME} \
                   --device-type ${DEVICE_TYPE} \
                   --firmware-file <path to firmware file>
```

### Maintainer

The author and maintainer of this Update Module is:

- Mirza Krak - <mirza.krak@northern.tech> - [mirzak](https://github.com/mirzak)

Always include the original author when suggesting code changes to this update module.
