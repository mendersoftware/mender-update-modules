## Description

The IPK Update Module allows opkg-based packages to be installed on a device

Example use-cases:
* Deploy any ipk package built by Yocto.

For a more detailed tutorial on how to use this Update Module please visit [Mender Hub](https://hub.mender.io/t/ipk-packages)

### Specification

|||
| --- | --- |
|Module name| ipk |
|Supports rollback|no|
|Requires restart|no|
|Artifact generation script|no|
|Full system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/ipk/module/ipk)|

### Install the Update Module

Download the latest version of this Update Module by running:

    mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/ipk/module/ipk && chmod +x /usr/share/mender/modules/v3/ipk

### Create artifact

Generate Mender Artifacts using the following command:

    ARTIFACT_NAME="my-update-1.0"
    DEVICE_TYPE="my-device-type"
    OUTPUT_PATH="my-update-1.0.mender"
    PACKAGES="my-package-1.ipk my-package-2.ipk"
    mender-artifact write module-image -T ipk -n ${ARTIFACT_NAME} -t ${DEVICE_TYPE} -o ${OUTPUT_PATH} -f $(echo "$PACKAGES" | sed -e 's/ / -f /g')

### Maintainer

The author and maintainer of this Update Module is:

- Drew Moseley - <drew.moseley@northern.tech> - [drewmoseley](https://github.com/drewmoseley)

Always include the original author when suggesting code changes to this update module.
