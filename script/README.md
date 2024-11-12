### Description

The Script Update Module allows to execute any general purpose script or binary on the target device. A Mender Artifact containing one or more executables (usually scripts) is sent to the device, where the Update Module will execute these during the `ArtifactInstall` state.

If multiple scripts are provided, they will be executed in alphabetical order on the device.

Example use-cases:

* Restart application into diagnostic mode
* Run diagnostics script
* Execute any other generic command

### Specification

|||
| --- | --- |
|Module name|script|
|Supports rollback|no|
|Requires restart|no|
|Artifact generation script|no|
|Full operating system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/script/module/script)|
|Maintainer|Community|

## Prepare the device
This section describes how to setup your target device, i.e. the device to be updated. This will also be referred to as the device environment.

All commands outlined in this section should be run in the device environment.

### Prerequisites

This update module has the following prerequisites for the device environment:

* [Install the Mender client](https://docs.mender.io/client-installation/install-with-debian-package), version 2.0 or later
* Ensure the device has a Bash Unix shell
     * How to install this depends on which OS you are running

### Install the Update Module

Download the latest version of this Update Module by running:

```bash
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/script/module/script
```

## Prepare the development environment on your workstation

This section describes how to set up your development environment on your workstation.

All commands outlined in this section should be run in the development environment.

### Prerequisites

This Update Modules has the following prerequisites for the development environment:

* [Install mender-artifact](https://docs.mender.io/downloads), version 3.1.0 or later

### Create Mender Artifacts
The Artifact can be generated using `mender-artifact` tool.

Create example content to deploy:

```bash
cat > my-script-1.sh << EOF
#!/bin/bash
echo "$(basename "$0") was executed on device!" > /tmp/my-script-1.txt
EOF

cat > my-script-2.sh << EOF
#!/bin/bash
echo "$(basename "$0") was executed on device!" > /tmp/my-script-2.txt
EOF
```

Now generate a Mender Artifact using the following command:

```bash
ARTIFACT_NAME="my-update-1.0"
DEVICE_TYPE="my-device-type"
OUTPUT_PATH="my-update-1.0.mender"
SHELL_SCRIPTS="my-script-1.sh my-script-2.sh"
mender-artifact write module-image -T script -n ${ARTIFACT_NAME} -t ${DEVICE_TYPE} -o ${OUTPUT_PATH} -f $(echo "$SHELL_SCRIPTS" | sed -e 's/ / -f /g')
```

* `ARTIFACT_NAME`  - The name of the Mender Artifact
* `DEVICE_TYPE`  - The compatible device type of this Mender Artifact
* `OUTPUT_PATH`  - The path where to place the output Mender Artifact. This should always have a  `.mender`  suffix
* `SHELL_SCRIPTS` - The path to the script that you wish to deploy and execute on your device. **NOTE** that the extension does not matter.

You can either deploy this Artifact in managed mode with the Mender server (upload it under Releases in the server UI) or by using the Mender client only in [Standalone deployments](https://docs.mender.io/artifact-creation/standalone-deployment).

### Artifact technical details

The Mender Artifact used by this Update Module has a payload with as many files as scripts are contained in the Artifact.

The Mender Artifact contents will look like:

```bash
Updates:
  - Type: script
    Provides:
      rootfs-image.script.version: my-update-1.0
    Depends: {}
    Clears Provides: [rootfs-image.script.*]
    Metadata: {}
    Files:
      - checksum: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
        modified: 2024-11-07 13:16:32 +0100 CET
        name: my-script-1.sh
        size: 140
      - checksum: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
        modified: 2024-11-07 13:16:32 +0100 CET
        name: my-script-2.sh
        size: 139
```
