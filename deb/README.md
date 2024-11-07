### Description

The Deb Update Module updates software on the device using the native local package manager.

A Mender Artifact containing one or more software packages is sent to the device, where the Update Module will call the package manager to install them in alphabetical order.

### Specification

|||
| --- | --- |
|Module name|deb|
|Supports rollback|no|
|Requires restart|no|
|Artifact generation script|no|
|Full operating system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/deb/module/deb)|
|Maintainer|Community|

## Prepare the device

This section describes how to setup your target device, i.e. the device to be updated. This will also be referred to as the device environment.

All commands outlined in this section should be run in the device environment.

### Prerequisites

This update module has the following prerequisites for the device environment:
* [Install the Mender client](https://docs.mender.io/client-installation/install-with-debian-package), version 2.0 or later

### Install the Update Module

Download the latest version of this Update Module by running:

```bash
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/deb/module/deb
```

## Prepare the development environment on your workstation

This section describes how to set up your development environment on your workstation.

All commands outlined in this section should be run in the development environment.

### Prerequisites

This Update Modules has the following prerequisites for the development environment:
* [Install mender-artifact ](https://docs.mender.io/downloads), version 3.1.0 or later

### Create Mender Artifacts

The Artifact can be generated using the `mender-artifact` tool.

Now generate a Mender Artifact using the following command:

```bash
ARTIFACT_NAME="my-update-1.0"
DEVICE_TYPE="my-device-type"
OUTPUT_PATH="my-update-1.0.mender"
PACKAGES="my-package-1.deb my-package-2.deb"
mender-artifact write module-image -T deb -n ${ARTIFACT_NAME} -t ${DEVICE_TYPE} -o ${OUTPUT_PATH} -f $(echo "$PACKAGES" | sed -e 's/ / -f /g')
```

* `ARTIFACT_NAME`  - The name of the Mender Artifact
* `DEVICE_TYPE`  - The compatible device type of this Mender Artifact
* `OUTPUT_PATH`  - The path where to place the output Mender Artifact. This should always have a  `.mender`  suffix
* `PACKAGES` - List of all .deb packages to be contained in the Artifact.

You can either deploy this Artifact in managed mode with the Mender server (upload it under Releases in the server UI) or by using the Mender client only in [Standalone deployments](https://docs.mender.io/artifact-creation/standalone-deployment).


### Artifact technical details

The Mender Artifact used by this Update Module has a payload with as many files as software packages we are contained in the Artifact.

The Mender Artifact contents will look like:

```bash
Updates:
  - Type: deb
    Provides:
      rootfs-image.deb.version: my-update-1.0
    Depends: {}
    Clears Provides: [rootfs-image.deb.*]
    Metadata: {}
    Files:
      - checksum: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
        modified: 2024-11-07 12:00:03 +0100 CET
        name: my-package-1.deb
        size: 0
      - checksum: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
        modified: 2024-11-07 12:00:03 +0100 CET
        name: my-package-2.deb
        size: 0
```
