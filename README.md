# OpenWRT-automated-nightly-firmware-builds
### Requirements and Recommendations

This command requires auc, wget, findutils-xargs and all their dependencies. To install from shell:

`opkg update`

`opkg install auc wget findutils-xargs`

Persistent storage (attached, network, cloud) is recommended and may be required for devices with low memory. How to setup persistent mount points is beyond the scope of this guide.

## AUC firmware binary automated download command on OpenWRT

This command executes a dry-run of AUC and automatically downloads the built firmware binary to the specified path

`auc -n -y 2>&1 >/dev/null | grep -Eo 'https://sysupgrade.openwrt.org/[^ ]+' | xargs -I{} wget -q {} -O /path/to/file.name`

Prior to executing this command, change "/path/to/file.name" to the location the firmware binary is to be downloaded to. 

## Nightly automation with crontab

The above command can be run from the shell at any time or can be put in the crontab to run automatically at a specified interval. This cron syntax will run it once per day each night at 23:59:00

`59 23 * * * auc -n -y 2>&1 >/dev/null | grep -Eo 'https://sysupgrade.openwrt.org/[^ ]+' | xargs -I{} wget -q {} -O /path/to/file.name`

Prior to adding to crontab, change "/path/to/file.name" to the location the firmware binary is to be downloaded to. If this line is added to the crontab as is, the firmware will be overwritten each night and each build only stored for 24 hours.

## Automated versioning by date

To prevent nightly overwriting of the downloaded firmware binaries, the date command can be used in the destination path to create unique file name each day:

`59 23 * * * auc -n -y 2>&1 >/dev/null | grep -Eo 'https://sysupgrade.openwrt.org/[^ ]+' | xargs -I{} wget -q {} -O /path/AUC-firmware-$(date '+%Y-%m-%d)`
