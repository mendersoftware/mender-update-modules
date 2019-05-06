## Description

The Directory Overlay Update Module installs a user defined file tree structure into a given destination directory in the target.

Before the deploy into the destination folder on the device, the Update Module will take a backup copy of the current contents, allowing restore of it using the rollback mechanism of the Mender client if something goes wrong. The Update Module will also delete the current installed content that was previously installed using the same module, this means that each deployment is self contained and there is no residues left on the system from the previous deployment.

Example use-cases:
* Deploy root filesystem overlays

For a more detailed tutorial on how to use this Update Module please visit [Mender Hub](https://hub.mender.io/t/directory-overlay/)

### Specification

|||
| --- | --- |
|Module name| dir-overlay |
|Supports rollback|yes|
|Requires restart|no|
|Artifact generation script|yes|
|Full system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/dir-overlay/module), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/blob/master/dir-overlay/module-artifact-gen/dir-overlay-artifact-gen)|

### Install the Update Module

Download the latest version of this Update Module by running:

    mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/dir-overlay/module/dir-overlay && chmod +x /usr/share/mender/modules/v3/dir-overlay

### Create artifact

To download dir-overlay-artifact-gen, run the following:

    wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/dir-overlay/module-artifact-gen/dir-overlay-artifact-gen

Make it executable by running,

    chmod +x dir-overlay-artifact-gen

Generate Mender Artifacts using the following command:

    ARTIFACT_NAME="my-update-1.0"
    DEVICE_TYPE="my-device-type"
    OUTPUT_PATH="my-update-1.0.mender"
    DEST_DIR="/"
    OVERLAY_TREE="rootfs_overlay"
    ./dir-overlay-artifact-gen -n ${ARTIFACT_NAME} -t ${DEVICE_TYPE} -d ${DEST_DIR} -o ${OUTPUT_PATH} ${OVERLAY_TREE}

### Maintainer

The author and maintainer of this Update Module is:

- Mirza Krak - <mirza.krak@northern.tech> - [mirzak](https://github.com/mirzak)

Always include the original author when suggesting code changes to this update module.
