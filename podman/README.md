### Description

The Podman Update Module handles the Podman container images that shall be running on the target device. A deployment with this module will stop all currently running Podman containers on the device and start new containers based on the list of Podman images provided in the Mender Artifact.

In case of any unforeseen error during the process, the module will trigger the rollback mechanism of the Mender client to restore the previously running Podman containers.

### Specification
|||
| --- | --- |
|Module name|podman|
|Supports rollback|yes|
|Requires restart|no|
|Artifact generation script|yes|
|Full operating system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/podman/module/podman), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/tree/master/podman/module-artifact-gen/podman-artifact-gen)|
|Maintainer|Community|

## Prepare the device

This section describes how to setup your target device, i.e. the device to be updated. This will also be referred to as the device environment.

All commands outlined in this section should be run in the device environment.

### Prerequisites

This update module has the following prerequisites for the device environment:

* [Install Podman](https://podman.io/getting-started/installation)
* A recent version of the JSON parser `jq` must be installed on the device.
* The device must have a Bash Unix shell available.

How to install these depends on which OS you are running.

### Install the Update Module

Download the latest version of this Update Module by running:

```bash
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/podman/module/podman
```

## Prepare the development environment on your workstation

This section describes how to set up your development environment on your workstation.

All commands outlined in this section should be run in the development environment.

### Prerequisites

This Update Module has the following prerequisites for the development environment:

* [Install mender-artifact ](https://docs.mender.io/downloads), version 3.1.0 or later

### Artifact creation

For convenience, an Artifact generator tool `podman-artifact-gen` is provided along the module. This tool will generate Mender Artifacts in the same format that the Update Module expects them.

Download `podman-artifact-gen`, by running the following command:

```bash
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/podman/module-artifact-gen/podman-artifact-gen
```

Make it executable:

```bash
chmod +x podman-artifact-gen
```

Now generate a Mender Artifact using the following command:

```bash
ARTIFACT_NAME="my-container-update-1.0"
DEVICE_TYPE="my-device-type"
OUTPUT_PATH=my-container-update-1.0.mender
PODMAN_IMAGES="podman-image-1 podman-image2"
./podman-artifact-gen -n ${ARTIFACT_NAME} -t ${DEVICE_TYPE} -o ${OUTPUT_PATH} ${PODMAN_IMAGES}
```

* `ARTIFACT_NAME`  - The name of the Mender Artifact
* `DEVICE_TYPE`  - The compatible device type of this Mender Artifact
* `OUTPUT_PATH`  - The path where to place the output Mender Artifact. This should always have a  `.mender`  suffix
* `PODMAN_IMAGES` - The list of Podman images that we want the target to run. Each item can be any valid name for Podman to pull images from (tags or digests). For example debian, debian:jessie, debian:latest, debian:sha256@…, etc

Note that the actual image id that will be added in the Artifact is the digest (sha256 hash) of the image, regardless of the tag used to pull it in. This will ensure that the device will pull the exact same version of each image than the generation tool used when preparing the Artifact.

You can either deploy this Artifact in managed mode with the Mender server (upload it under Releases in the server UI) or by using the Mender client only in [Standalone deployments](https://docs.mender.io/artifact-creation/standalone-deployment).

#### Artifact technical details

The Mender Artifact used by this Update Module has no payload files. Instead it uses the `Metadata` field to list the Podman images, which will be downloaded by the device. This meta-data is composed by a single `containers` JSON key with the array of images digests to be installed in the update.

As an example, the following update will install two specific versions of Podman images debian and ubuntu:

```bash
Updates:
  - Type: podman
    Provides:
      rootfs-image.podman.version: my-container-update-1.0
    Depends: {}
    Clears Provides: [rootfs-image.podman.*]
    Metadata:
      {
        "containers": [
          "debian@sha256:e11072c1614c08bf88b543fcfe09d75a0426d90896408e926454e88078274fcb",
          "ubuntu@sha256:99c35190e22d294cdace2783ac55effc69d32896daaa265f0bbedbcde4fbe3e5"
        ],
        "run_args": ""
      }
    Files: []
```
