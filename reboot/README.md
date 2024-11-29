## Description

The reboot Update Module: reboot your device remotely.

Example use-cases:

* something went wrong and you have to reboot your device remotely, and all
access to the device you have is Mender.

### Specification

|||
| --- | --- |
|Module name| reboot |
|Supports rollback|no|
|Requires restart|yes|
|Artifact generation script|yes|
|Full system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/reboot/module/reboot), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/blob/master/reboot/module-artifact-gen/reboot-artifact-gen)|

### Install the Update Module

Download the latest version of this Update Module by running:

```bash
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/reboot/module/reboot && chmod +x /usr/share/mender/modules/v3/reboot
```

### Create artifact

To download `reboot-artifact-gen`, run the following:

```bash
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/reboot/module-artifact-gen/reboot-artifact-gen && chmod +x reboot-artifact-gen
```

Generate Mender Artifacts using the following command:

```bash
ARTIFACT_NAME="my-update-1.0"
DEVICE_TYPE="my-device-type"
./reboot-artifact-gen --artifact-name ${ARTIFACT_NAME} \
                      --device-type ${DEVICE_TYPE}
```

### Maintainer

The author and maintainer of this Update Module is:

- Peter Grzybowski - <peter@northern.tech>

Always include the original author when suggesting code changes to this update module.
