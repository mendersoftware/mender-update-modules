# Persistent App Update Module

## Description

The Persistent App Update Module installs files to a user-defined directory on the persistent data partition, ensuring deployments survive rootfs A/B updates.

This module uses the `data-partition` software filesystem namespace, which means version tracking in `mender show-provides` persists across system updates. When a rootfs update clears `rootfs-image.*` provides, the `data-partition.*` provides remain intact.

The module supports rollback by maintaining backups on the same persistent partition.

Example use-cases:
* Deploy application files that must persist across OS updates
* Deploy configuration bundles to /data
* Deploy container images, ML models, or other large files to persistent storage

For a detailed tutorial on persistent update modules, visit [Mender Hub](https://hub.mender.io).

### Specification

|||
| --- | --- |
|Module name| persistent-app |
|Supports rollback|yes|
|Requires restart|no|
|Artifact generation script|yes|
|Full system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/persistent-app/module/persistent-app), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/blob/master/persistent-app/module-artifact-gen/persistent-app-artifact-gen)|

### Install the Update Module

Download the latest version of this Update Module by running:

```bash
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/persistent-app/module/persistent-app && chmod +x /usr/share/mender/modules/v3/persistent-app
```

### Create artifact

To download `persistent-app-artifact-gen`, run the following:

```bash
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/persistent-app/module-artifact-gen/persistent-app-artifact-gen && chmod +x persistent-app-artifact-gen
```

Generate Mender Artifacts using the following command:

```bash
ARTIFACT_NAME="myapp-v1.0.0"
DEVICE_TYPE="raspberrypi4"
DEST_DIR="/data/myapp"
SOFTWARE_NAME="myapp"
SOFTWARE_VERSION="1.0.0"
OUTPUT_PATH="myapp-v1.0.0.mender"
./persistent-app-artifact-gen \
    -n ${ARTIFACT_NAME} \
    -t ${DEVICE_TYPE} \
    -d ${DEST_DIR} \
    --software-name ${SOFTWARE_NAME} \
    --software-version ${SOFTWARE_VERSION} \
    -o ${OUTPUT_PATH} \
    ./payload/
```

The artifact generator automatically includes `--software-filesystem data-partition` to ensure version tracking survives rootfs updates.

**Note on directory structure:** The artifact generator flattens all input files into a single directory. If you pass a directory like `./payload/` containing `bin/app` and `etc/config.json`, both files end up directly in the destination directory (e.g., `/data/myapp/app` and `/data/myapp/config.json`), not in subdirectories. This module is intended as a simple example; for complex directory hierarchies, consider using the [dir-overlay](../dir-overlay) module or adapting this module to your needs.

After deploying, verify with:

```bash
mender show-provides | grep data-partition
# Output: data-partition.myapp.version=1.0.0
```

### How it works

1. **ArtifactInstall**: The module backs up any existing content at the destination directory to `<dest_dir>.backup`, then copies the new files.

2. **ArtifactCommit**: On successful deployment, the backup is removed.

3. **ArtifactRollback**: If the deployment fails or is rejected, the backup is restored.

4. **Persistence**: Both the deployed files and the backup reside on the data partition (typically `/data`), which is not affected by rootfs A/B updates.

### Maintainer

The author and maintainer of this Update Module is:

- Josef Holzmayr - <jester@theyoctojester.info> - [TheYoctoJester](https://github.com/TheYoctoJester)

Always include the original author when suggesting code changes to this update module.
