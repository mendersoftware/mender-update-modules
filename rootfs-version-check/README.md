## Description

The rootfs-version-check Update Module implements a full image update with additional checks to protect against replay attacks.

This is functionally equivalent to the built-in full image update with an extra check to ensure the artifact name follows a specific
format and that installing "older" images is rejected.  For this reference implementaton, we simply use a numeric identifier and ensure
that it is larger than the version installed.  For actual device fleet use, you may need to customize this based on your artifact
naming scheme.

Example use-cases:
* Deploy root filesystem updates and ensure only newer artifacts are installed

For a more detailed tutorial on how to use this Update Module please visit [Mender Hub](https://hub.mender.io/t/rootfs-version-check/)

### Specification

|||
| --- | --- |
|Module name| rootfs-version-check |
|Supports rollback|yes|
|Requires restart|yes|
|Artifact generation script|no|
|Full system updater|yes|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/rootfs-version-check/module/rootfs-version-check)|

### Install the Update Module

Download the latest version of this Update Module by running:

```bash
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/rootfs-version-check/module/rootfs-version-check && chmod +x /usr/share/mender/modules/v3/rootfs-version-check
```

### Create artifact

Generate Mender Artifacts using the following command:

```bash
ARTIFACT_NAME="my-update-1.0"
DEVICE_TYPE="my-device-type"
OUTPUT_PATH="my-update-1.0.mender"
IMAGE="rootfs.ext4"
mender-artifact write module-image -T rootfs-version-check -n ${ARTIFACT_NAME} -t ${DEVICE_TYPE} -o ${OUTPUT_PATH} -f ${IMAGE}
```

### Maintainer

The author and maintainer of this Update Module is:

- Drew Moseley - <drew.moseley@northern.tech> - [drewmoseley](https://github.com/drewmoseley)

Always include the original author when suggesting code changes to this update module.
