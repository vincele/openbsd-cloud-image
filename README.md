# Qcow2 image builder for OpenBSD

This script generates Qcow2 images of OpenBSD with [cloud-init](https://cloud-init.io/) pre-installed.
The images are ready-to-use for your favorite cloud provider.

## Pre-requisites:

* `python3`
* `sudo`
* `curl`
* `signify` (Debian: `signify-openbsd` and `signify-openbsd-keys`)
* `qemu-system-x86_64`

## Usage

* Clone the git repository
* Run: `./build_openbsd_qcow2.sh -b`
* Done

See `./build_openbsd_qcow2.sh -h` for more information.

## Tips

* Build a standard image: `./build_openbsd_qcow2.sh -r 7.5 --image-file openbsd.qcow2 -b`
* Build a customized image (small disk size, custom disklabel, disabled sets): `./build_openbsd_qcow2.sh -r 7.5 --image-file openbsd-min.qcow2 --size 2 --disklabel custom/disklabel.cloud --sets "-game*.tgz -x*.tgz" --allow_root_ssh no -b`

## How does it work

- Downloads and checks the official OpenBSD installer files
- Starts a local HTTP server (python3 stdlib's http.server module)
- Creates the installation target virtual QCOW2 disk file
- Launches a qemu KVM virtual machine with local TFTP server
- VM boots from the network, downloading a PXE bootloader via TFTP
- The PXE bootloader downloads its configuration file via TFTP
- The PXE bootloader downloads the OpenBSD ramdisk kernel via TFTP
- The ramdisk kernel is started in automatic installation mode
- The automated installer uses a tailored config file specifying the
  disklabel and a site.tgz set, among other config options
- At the end of the OpenBSD installation, the install.site script
  installs the cloud-init package from its upstream github repository
- The VM automatically shuts down
- The resulting image is compressed to save space
