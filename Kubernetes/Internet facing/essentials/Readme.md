# Essentials PKS deployment map

This deployment is based upon the VMware Essentials tar ball that is now publically available from the my.vmware.com site (Registration required). The deployment requires an Ubuntu host that has the following:

- Internet connectivity (for Docker and Ubuntu packages)
- Network connectivity
- An ssh key uploaded from a machine where parlay will run
- On the host `NOPASSWD` configured for `sudo`
- Ensure Swap is disabled

## Host configuration

### SSH Key upload

The utility `ssh-copy-id` should upload a key that will allow parlay to authenticate with the remote host.

**Usage**

```
ssh-copy-id username@x.x.x.x
```

Once this procedure has completed, test logging in over ssh as usual. 

### No Password sudo

To enable password-less sudo the `/etc/sudoers` file needs modifying (DO NOT DO THIS MANUALLY).

To edit the sudo file use the following command:

```
sudo visudo
```

Then add the following entry to the end of the file, replacing the `username` with the correct entry :

```
username     ALL=(ALL) NOPASSWD:ALL
```

This can be tested by either opening a new session or logging out and back in and then testing that `sudo <CMD>` doesn't require a password.

### Ensure swap is disabled

To disable swap, it can be commented out in the `/etc/fstab` (this requires a reboot). To disable swap for the current session then the following command can be used `swapoff -a`

## Retrieve the Essentials PKS bundle

The bundle is available from (my.vmware.com)[https://my.vmware.com/web/vmware/details?productId=884&downloadGroup=EPKS_1.14], although a vmware subscription is required. 

**Note** If you download the bundle on a macOS machine then it will partially try to decompress the binary `.tar.gz` -> `.tar`. To fix this run the command `gzip <VMware tarball>` to fix this. 

## Retrieve the deployment map

The simplest way of modifying the deployment map is retrieve the raw deployment map or clone the repository as a whole.

## Update the map to point to your Ubuntu host

The example map uses an example address (typically from VMware Fusion), to modify this use the following steps:

- Navigate to the essentials PKS map
- In place `sed` the address to the correct one. 

The below example will replace `172.16.72.131` with `192.168.2.100`
```
sed "s?172.16.72.131?192.168.2.100?g" ./stackedmanager.yaml > newConfig.yaml
```

## Update the map to point to the Essentials PKS bundle

Under most circumstances the bundle should appear under some `$HOME/download` folder, however the task `Upload Essentials PKS` will need to be updated to point to wherever you've downloaded the bundle to. 

## Deploy Essentials PKS

With a configured host and a correctly configured deployment map then we can use plunder to automate the deployment of Essentials PKS.

```
plunder automate --map ./<PATH TO MAP> \
--overrideUser deploy \
--overrideAddress 172.16.72.131 \
--outputFile ./essPKS.log 
```

To use the ui and select (or deselect) specific tasks use the ui subcommand:

```
plunder automate ui --map ./<PATH TO MAP> \
--overrideUser deploy \
--overrideAddress 172.16.72.131 \
--outputFile ./essPKS.log 
```

