fedora-coreos-proxmox
===

## About
This is a fork from https://git.geco-it.net/GECO-IT-PUBLIC/fedora-coreos-proxmox.git, it has the modified as below:
- Fixed: the problem of `geco-motd` and `qemu-ga` in the setup of latest FCOS, according to this [post](https://forum.proxmox.com/threads/howto-wrapper-script-to-use-fedora-coreos-ignition-with-proxmox-cloud-init-system-for-docker-workloads.86494/post-463507)
- Feature: additional custom config of Template VM base on the modifies of [Doc-Tiebeau/proxmox-flatcar](https://github.com/Doc-Tiebeau/proxmox-flatcar)
- Feature: allow adding custom packages repo

## How To

1.  Be sure to install `git` on your PVE server first:

    ```shell
    apt update
    apt install git
    ```

2.  Clone this repository on your Proxmox server:

    ```shell
    git clone https://github.com/jimlee2002/fedora-coreos-proxmox.git
    ```

3.  Get into the directory `fedora-coreos-proxmox`

    ```shell
    cd fedora-coreos-proxmox
    ```

4.  Modify `template_deploy.conf` to custom your VM parameter as below:

    ```
    # template vm vars
    TEMPLATE_NAME="TMPLT-fcos" # Template VM name append with <flactar_version> in Proxmox GUI
    TEMPLATE_RECREATE="false" # Fore recreate template ?
    # Note: If you want only update hook script and Template_Ignition file, you can keep it as false, these files are always overwritten
    TEMPLATE_VMID="900" # VMID of Template VM
    TEMPLATE_VMSTORAGE="local-lvm" # Target storage for template VM
    SNIPPET_STORAGE="local" # Target storage for snippets files
    VMDISK_OPTIONS=",discard=on"

    TEMPLATE_MEMORY="4096" # Amount of RAM for the template VM in MB
    TEMPLATE_CPU_TYPE="host" # Emulated CPU type
    TEMPLATE_CPU_CORE="4" # The number of cores for template VM
    # 0-False, 1-True
    TEMPLATE_AUTOSTART="1" # Whether the VM will be automatic restart after crash
    TEMPLATE_ONBOOT="0" # Whether the VM will be started during system bootup.


    TEMPLATE_IGNITION="fcos-base-tmplt.yaml"

    # fcos image version
    STREAMS=stable # The stream you decide to use
    VERSION=36.20220618.3.1 # You need to bump it to latest version manually
    PLATEFORM=qemu
    BASEURL=https://builds.coreos.fedoraproject.org
    ```

5.  Add your custom packages repo in `hook-fcos.sh` as below:

    ```shell
    (...)

        echo -n "Fedora CoreOS: Adding custom packages repos..."
        pkgs_repo=(
            # Put the URL of custom packages repo here
            "https://pkgs.tailscale.com/stable/fedora/tailscale.repo"
            "https://download.docker.com/linux/fedora/docker-ce.repo"
        )
        
    (...)
    ```

6.  Run the scripts to generate the template VM:

    ```shell
    ./vmsetup.sh
    ```

7.  Check the template VM just generated in Proxmox Web GUI and clone a VM base on it.


8.  BEFORE first boot: update Cloud-Init config of your new VM in Proxmox Web GUI.
    > Without specifying, the default username is `admin`

9.  Wait for multiple reboot the enjoy.


## Credits

- [GECO-IT-PUBLIC/fedora-coreos-proxmox](https://git.geco-it.net/GECO-IT-PUBLIC/fedora-coreos-proxmox.git)
- [Doc-Tiebeau/proxmox-flatcar](https://github.com/Doc-Tiebeau/proxmox-flatcar)
- [Proxmox VE - Fedora CoreOS : Un mariage presque parfait / An almost perfect Union [Geco-iT Wiki]](https://wiki.geco-it.net/public:pve_fcos)
- [[TUTORIAL] - HOWTO : Wrapper Script to Use Fedora CoreOS Ignition with Proxmox cloud-init system for Docker workloads | Proxmox Support Forum](https://forum.proxmox.com/threads/howto-wrapper-script-to-use-fedora-coreos-ignition-with-proxmox-cloud-init-system-for-docker-workloads.86494)