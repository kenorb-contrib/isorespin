# `isorespin.sh`

Linuxium's script to respin an Ubuntu, Ubuntu flavour, Linux Mint, neon, elementary, BackBox or Peppermint ISO and optionally add/remove functionality like kernels/packages/files, etc.

In other words, it allows Ubuntu ISO to be respun and customized ("remastered") to create a new ISO.

# History

This script was initially based on the information documented on the following sites:

- <https://help.ubuntu.com/community/LiveCDCustomization> (shared under a Creative Commons Attribution-ShareAlike 3.0 License available at <https://help.ubuntu.com/community/License>)
- <https://wiki.ubuntu.com/KernelTeam/GitKernelBuild> (shared under a Creative Commons Attribution-ShareAlike 3.0 License available at <https://help.ubuntu.com/community/License>)
and then further developed by Linuxium (linuxium@linuxium.com.au).

# Before you start

> It is a given that most people only read the manual page or documentation for a command either when they can't work out how to use it or somethings goes wrong after using it. And it is also much easier to post a question than read the documentation or understand an error message. However try and get through the following quick start guide first as it is designed to help.

# Dependencies

> Although it is expected to run the script on a Linux machine typically running 64-bit Ubuntu or a 64-bit Ubuntu based OS it also works on a Linux virtual machine on Windows (see below for further details). You will need certain packages like `squashfs-tools` and `xorriso` installed (use `sudo apt-get install -y squashfs-tools xorriso` to install them) plus `zenity` if you want to use the GUI. As different Linux distros have different packages installed by default the script will first check and notify you if any other packages are required. You will also need at least 10 GB of free space but this can be on external storage (e.g. a USB) as it can be specified as a location different to where the script is run from. For some options (such as updating the kernel) you will need a working internet connection as the script needs to download software. Also the script needs to be executable which can be achieved using the `chmod` command (`sudo chmod 755 isorespin.sh`). As the script runs some commands (e.g. `mount`/`umount`) which require root access using the `sudo` command you will initially be prompted for your password and must already have `sudo` privileges. Finally some restrictions: you cannot respin an ISO that is EOL (End Of Life); you cannot respin an ISO on an OS that doesn't support `systemd` when using the `--apollo` or the `-b Linuxium` options; you also cannot respin an ISO if the ISO doesn't support 'systemd' when using the `--atom`, `--apollo` or `-b Linuxium` options.

# Usage

> Running the script is really quite simple even though there are quite a lot of features or options making it look rather complex.

- The script's help is displayed using the command: `isorespin.sh -h`
- The version of the script can either be displayed by entering: `isorespin.sh -v`

```
Usage: ./isorespin.sh [ -h | -v | --check | --rolling-list ]
       ./isorespin.sh -i <ISO> [ [ -u | -k <kernel> ] | -r "<repo> ... " | -p "<pkg> ... " | -l "<pkg.deb> ... " | -f "<file> | <directory> ... " | [ -s <size>MB | GB ] | [ -b GRUB | rEFInd | Linuxium ] | ...
       ./isorespin.sh ... -w <directory> | -d "<pkg> ... " | -e "<pkg> ... " | -c "<cmd> ... " | -o "<file> | <directory> ... " | -g "" | "<kernel boot parameter> ... " | ...
       ./isorespin.sh ... --apollo | --atom | ...
       ./isorespin.sh ... --rolling-release | --rolling-release-hwe | --rolling-release-hwe-edge | --rolling-proposed | --rolling-proposed-hwe | --rolling-proposed-hwe-edge | ...
       ./isorespin.sh ... --rolling-testing | --rolling-testing-hwe | --rolling-testing-hwe-edge | --rolling-unstable | --rolling-unstable-hwe | --rolling-unstable-hwe-edge | ...
       ./isorespin.sh ... --upgrade | --key  "<repo> ... " ]
```

> The script can either be run as a local script or it can be installed by copying the script to `/usr/local/bin` where it will be accessible for all users after the script has been made executable using the 'chmod' command (`sudo chmod 755 isorespin.sh`). Depending on the options and their complexity the respinning will take quite a few minutes and the script provides updates on its progress whilst running. The respun ISO is created with a new name derived from the original ISO name and includes indicators of the options used like 'persistence' or the kernel version for example with full details being written to the log file.

> Once the ISO has been respun it can be written to a USB using the standard `dd` command.

# Quick start

1. Clone this repo.
1. Install required dependencies, e.g.

         sudo apt-get install genisoimage dosfstools squashfs-tools xorriso p7zip-full bc curl klibc-utils iproute2 rsync unzip wget findutils

1. Download ISO you want to respin (see: [Ubuntu Releases](http://releases.ubuntu.com/)).
1. Either run the script with GUI, or run in a terminal session, for example:

  1. Add a bootloader using one of the following options:

    - Add a 32-bit bootloader:

            isorespin.sh -i ubuntu-16.04.3-desktop-amd64.iso

    - Respin the ISO suitable for booting on Intel Atom devices

            isorespin.sh -i ubuntu-16.04.3-desktop-amd64.iso --atom

    - Respin the ISO suitable for booting on Intel Apollo Lake devices

            isorespin.sh -i ubuntu-16.04.3-desktop-amd64.iso --apollo

    - Update the ISO with the latest mainline kernel

            isorespin.sh -i ubuntu-16.04.3-desktop-amd64.iso -u

    - Update the ISO with a specific kernel version

            isorespin.sh -i ubuntu-16.04.3-desktop-amd64.iso -k v4.15-rc2

  1. A new ISO prefixed with `linuxium` as a minimum will be created. For example this command creates the ISO:

            isorespin.sh -i ubuntu-16.04.3-desktop-amd64.iso -k v4.15-rc2 --apollo

  1. Insert a USB and make a note of which device it is (use `df`, `blkid` or `lsblk` to confirm).
  1. Unmount any files systems auto mounted from the USB:

            sudo umount /dev/<USB device>*

  1. Write the respun ISO to USB:

            sudo dd if=<respun ISO> of=/dev/<USB device> bs=4M

     e.g.

            sudo dd if=linuxium-apollo-v4.15-rc2-ubuntu-16.04.3-desktop-amd64.iso of=/dev/sdx bs=4M

  1. Boot the USB on your target device e.g. your mini PC.

# Changelog

- Version 1: This work is licensed under a Creative Commons Attribution-ShareAlike 3.0 License.
- Version 2.01.050417: This work is licensed under a Creative Commons Attribution-ShareAlike 4.0 License.
- Version 3.01.120417: This work is licensed under GNU GPL version 3 under one-way compatibility with CC BY-SA 4.0.
- Version 3.02.130417, 4.06.220417 to 4.09.280417, 5.01.020517, 6.01.120517, 6.02.0 to 6.03.3, 7.1.0 to 7.3.5.1, 7.3.6 and 8.0.0 to 8.1.5: This work is licensed under GNU GPL version 3.
- Version 8.1.6: This work is licensed under GNU GPL version 3.
