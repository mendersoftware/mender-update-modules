## Description

The dirty Update Module: modify your device state without installing an artifact

Example use-cases:

* you have a specific action on the device that you want to run multiple times
* you want to avoid re-creating artifacts just for the sake of a new version/name
* you don't want the artifact the show up in the list of installed artifacts

The module will *always* fail the update process, which means it can never be marked
as installed. So it can be attempted any number of times without having to recreate
newly versioned artifacts.

Benefits:
* you can put your desired operation into the `dirty_action` function
* after the (failed) deployment, you can check if your operation
  * finished successfully: the update failed in `ArtifactCommit`
  * resulted in an error: the update failed in `ArfifactInstall`

### Specification

|||
| --- | --- |
|Module name| dirty |
|Supports rollback|no|
|Requires restart|no|
|Artifact generation script|yes|
|Full system updater|no|
|Source code|[Update Module](https://github.com/mendersoftware/mender-update-modules/tree/master/dirty/module/dirty), [Artifact Generator](https://github.com/mendersoftware/mender-update-modules/blob/master/dirty/module-artifact-gen/dirty-artifact-gen)|

### Install the example Update Module

Download the latest version of this Update Module by running:

```
mkdir -p /usr/share/mender/modules/v3 && wget -P /usr/share/mender/modules/v3 https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/dirty/module/dirty && chmod +x /usr/share/mender/modules/v3/dirty
```

The example will add a string to the `/tmp/dirty` file as demonstration of an action.

### Package your "dirty" operation

The example structure provides all necessary states and a `dirty_action` stub function. The operation to
occur should be implemented in the `dirty_action` stub, and return
* `0` upon successful completion
* `!= 0` upon error

### Create artifact

To download `dirty-artifact-gen`, run the following:

```
wget https://raw.githubusercontent.com/mendersoftware/mender-update-modules/master/dirty/module-artifact-gen/dirty-artifact-gen && chmod +x dirty-artifact-gen
```

Generate Mender Artifacts using the following command:

```
ARTIFACT_NAME="my-update-1.0"
DEVICE_TYPE="my-device-type"
./dirty-artifact-gen --artifact-name ${ARTIFACT_NAME} \
                     --device-type ${DEVICE_TYPE}
```

### Maintainer

The author and maintainer of this Update Module is:

- Josef Holzmayr - <josef.holzmayr@northern.tech>

Always include the original author when suggesting code changes to this update module.
