### Description

The Banner Update Module manages the system's message of the day (MOTD) displayed when users log in via SSH or console. It uses metadata-driven updates to change the banner text without requiring any file payload, supports rollback functionality, and operates without requiring system reboots.

### Specification

|||
| --- | --- |
|Module name|banner|
|Supports rollback|yes|
|Requires restart|no|
|Artifact generation script|yes|
|Full operating system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/banner/module/banner), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/tree/master/banner/module-artifact-gen/banner-artifact-gen)|
|Maintainer|Community|

## Prepare the device

This section describes how to setup your target device, i.e. the device to be updated. This will also be referred to as the device environment.

All commands outlined in this section should be run in the device environment.

### Prerequisites

This update module has the following prerequisites for the device environment:

* [Install the Mender client](https://docs.mender.io/client-installation/install-with-debian-package), version 2.0 or later
* A recent version of the JSON parser `jq` needs to be installed on the device
* A POSIX-compatible shell

### Install the Update Module

Download the latest version of this Update Module by running:

```bash
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/banner/module/banner
```

## Prepare the development environment on your workstation

This section describes how to set up your development environment on your workstation.

All commands outlined in this section should be run in the development environment.

### Prerequisites

This Update Module has the following prerequisites for the development environment:

* [Install mender-artifact](https://docs.mender.io/downloads), version 3.1.0 or later
* A recent version of the JSON parser `jq`

### Artifact creation

For convenience, an Artifact generator tool `banner-artifact-gen` is provided along the module. This tool will generate Mender Artifacts in the same format that the Update Module expects them.

Download `banner-artifact-gen`, by running the following command:

```bash
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/banner/module-artifact-gen/banner-artifact-gen
```

Make it executable:

```bash
chmod +x banner-artifact-gen
```

Now generate a Mender Artifact using the following command:

```bash
ARTIFACT_NAME="my-banner-update-1.0"
DEVICE_TYPE="my-device-type"
OUTPUT_PATH="my-banner-update-1.0.mender"
BANNER_TEXT="Welcome to Production!"
./banner-artifact-gen -n ${ARTIFACT_NAME} -t ${DEVICE_TYPE} -o ${OUTPUT_PATH} -b "${BANNER_TEXT}"
```

* `ARTIFACT_NAME` - The name of the Mender Artifact
* `DEVICE_TYPE` - The compatible device type of this Mender Artifact
* `OUTPUT_PATH` - The path where to place the output Mender Artifact. This should always have a `.mender` suffix
* `BANNER_TEXT` - The banner text to display as the message of the day

You can either deploy this Artifact in managed mode with the Mender server (upload it under Releases in the server UI) or by using the Mender client only in [Standalone deployments](https://docs.mender.io/artifact-creation/standalone-deployment).

### Artifact technical details

The Mender Artifact used by this Update Module has no payload files. Instead it uses the `Metadata` field to store the banner text, which will be written to `/etc/motd` by the device. This meta-data is composed by a single `banner_text` JSON key with the text to be displayed.

As an example, the artifact created as described above can be inspected with `mender-artifact read my-banner-update-1.0.mender`, which yield the following information:

```bash
Updates:
  - Type: banner
    Provides:
      rootfs-image.banner.version: my-banner-update-1.0
    Depends: {}
    Clears Provides: [rootfs-image.banner.*]
    Metadata:
      {
        "banner_text": "Welcome to Production!"
      }
    Files: []
```
