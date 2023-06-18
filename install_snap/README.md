## Description

The install_snap Update Module is able install packages to the device running Mender using
the snap tooling.

Example use-cases:

* managing devices running the [snap](https://snapcraft.io/) app store, specifically Ubuntu

### Specification

|||
| --- | --- |
|Module name| install_snap |
|Supports rollback|no|
|Requires restart|no|
|Artifact generation script|yes|
|Full system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/install_snap/module/install_snap), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/blob/master/install_snap/module-artifact-gen/install_snap-artifact-gen)|

### Install the Update Module

Download the latest version of this Update Module by running:

```
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/install_snap/module/install_snap && chmod +x /usr/share/mender/modules/v3/install_snap
```

### Create artifact

To download `install_snap-artifact-gen`, run the following:

```
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/install_snap/module-artifact-gen/install_snap-artifact-gen && chmod +x install_snap-artifact-gen
```

Generate Mender Artifacts using the following command:

```
ARTIFACT_NAME="my-update-1.0"
DEVICE_TYPE="my-device-type"
./install_snap-artifact-gen \
    --artifact-name ${ARTIFACT_NAME} \
    --device-type ${DEVICE_TYPE} \
    <name of snap> ...
```

This tool depends on the `snap` CLI.

### Maintainer

The author and maintainer of this Update Module is:

- Josef Holzmayr - <josef.holzmayr@northern.tech> - [theyoctojester](https://github.com/theyoctojester)

Always include the original author when suggesting code changes to this update module.
