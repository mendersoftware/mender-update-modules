# fwu Update Module

## Description

The fwu Update Module performs A/B updates on platforms using the
[Arm FWU metadata](https://developer.arm.com/documentation/den0118/latest/),
such as the ones with Trusted Firmware-A or the U-Boot FWU Multi Bank Update.

The metadata selects the bank to boot. A bank is a set of images, for example
firmware, boot filesystem and root filesystem. An updated bank is booted in
trial: after a number of failed boots the firmware goes back to the previous
bank.

The module writes the images of an Artifact to the inactive bank and selects
it for the next boot. The updated bank is accepted on commit, and the previous
one is restored on rollback. The devices of each bank are listed in a
configuration file.

### Specification

|||
| --- | --- |
|Module name| fwu |
|Supports rollback|yes|
|Requires restart|yes|
|Artifact generation script|yes|
|Full system updater|yes|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/fwu/module/fwu), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/blob/master/fwu/module-artifact-gen/fwu-artifact-gen)|

### Prerequisites

The module reads and writes the metadata with `fw_printenv` and `fw_setenv`
from [libubootenv](https://github.com/sbabic/libubootenv), which must be a
version supporting the `fwu-mdata` format. The metadata is expected in the
`fwu` namespace of `/etc/fw_env.config`:

```yaml
fwu:
  format : fwu-mdata
  size : 0x1000
  lockfile : /run/fwu-mdata.lock
  devices:
    - path : /dev/disk/by-partlabel/metadata1
    - path : /dev/disk/by-partlabel/metadata2
```

Set `MENDER_FWU_NAMESPACE` in the environment of the Mender client to use a
different namespace.

Both versions of the metadata are supported. Version 1 has no invalid bank
state, so a bank cannot be protected while it is being written or after a
rollback.

### Configuration

The banks are described in `/etc/mender/fwu.conf`, with one line per image:
its name followed by its device in each bank, starting from bank 0.

```
# image   bank 0                            bank 1
fip       /dev/disk/by-partlabel/fip-a      /dev/disk/by-partlabel/fip-b
bootfs    /dev/disk/by-partlabel/bootfs-a   /dev/disk/by-partlabel/bootfs-b
rootfs    /dev/disk/by-partlabel/rootfs-a   /dev/disk/by-partlabel/rootfs-b
```

The booted bank is the one whose device holds the mounted root filesystem.

### Install the Update Module

Download the latest version of this Update Module by running:

```bash
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/fwu/module/fwu && chmod +x /usr/share/mender/modules/v3/fwu
```

### Create artifact

To download `fwu-artifact-gen`, run the following:

```bash
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/fwu/module-artifact-gen/fwu-artifact-gen && chmod +x fwu-artifact-gen
```

Generate Mender Artifacts using the following command:

```bash
ARTIFACT_NAME="release-2"
DEVICE_TYPE="my-device-type"
OUTPUT_PATH="release-2.mender"
./fwu-artifact-gen -n ${ARTIFACT_NAME} -t ${DEVICE_TYPE} -o ${OUTPUT_PATH} \
    fip=fip.bin bootfs=bootfs.ext4 rootfs=rootfs.ext4
```

Each `image=file` argument gives the file to write to an image of
`/etc/mender/fwu.conf`. An Artifact must carry all the images of a bank.

### How it works

1. **Download**: the bank to update is marked invalid and each image is
   streamed to its device in that bank. The update is refused if the booted
   bank is not the active one or has not been accepted yet.

2. **ArtifactInstall**: the updated bank is selected for the next boot, in
   trial.

3. **ArtifactVerifyReboot**: after the reboot, the booted bank must be the
   updated one, otherwise the firmware reverted the update.

4. **ArtifactCommit**: the booted bank is accepted.

5. **ArtifactRollback**: the previous bank becomes active again and the
   updated one is marked invalid.

### Maintainer

The author and maintainer of this Update Module is:

- Dario Binacchi - <dario.binacchi@amarulasolutions.com> - [passgat](https://github.com/passgat)

Always include the original author when suggesting code changes to this update module.
