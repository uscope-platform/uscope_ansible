# uScope Platform

## Supported systems

The uscope toolchain is developed built and tested on Linux Ubuntu 20.04 , while it should be possible to get it to work on other standard distro (RHEL, Fedora, etc), no testing is performed, and consequently the instructions might be different from these.

While virtual machine usage is possible, the generation  of the operating system through yocto is resource intensive, and thus it greatly benefits from native installation,Virtualization, while possible, is not advised as it significantly slows down the process.

Hardware wise, core count is the most important characteristic, at this time (May 2021) A high core count AMD processor ( 8 core 16 threads or above) is recomended, a desktop class processor should also be preferred to mobile processors as the larger power budget leads to much better performance. System memory (RAM) should be proportional to the number of cores on the system (At least 2 GB per real core). To complete a build succesfully a large amount of disk space is needed at least 150-200 GB), since speed is not crucual traditional Hard drives can be used.

## Dependencies

The following dependencies are needed to work with the toolchain

### Xilinx Vivado (optional)

If HDL level debugging or development is required the installation of the  Xilinx Vivado toolchain is recomended

### System packages

To support the uScope workflow the several packages are needed on a ubuntu 20.04 system that can be installed through the following command:

```sh
sudo apt-get install -y libtinfo5 libncurses5 ansible gawk wget git diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev xterm chrpath u-boot-tools qemu-user-static npm
```

### Additional software

The git-repo tool is also needed, however this version of ubuntu does not carry it in the package repository, and thus it should be installed manually following the 
instruction from the project [homepage](https://gerrit.googlesource.com/git-repo/)

The yarn package manager is used to build the client web app, it should be installed through npm with the following command

```sh
    sudo npm install --global yarn
```

### Ansible collections

The Ansible configuration tool is used to automate many steps in the build process, and needs installation of few additional collections, through the following command

```sh
ansible-galaxy collection install community.crypto
```

### Docker multi-arch

To for docker multi-arch support to work properly additional setup is required, this the following command must be run

```sh
docker run --privileged --rm tonistiigi/binfmt --install all
```

## uScope Stack generation

The Full commissioning process is roughly split into three separate phases

1. Build system setup

2. OS compilation

3. uScope stack build and deployment


### Access to repositories

The since the build infrastructure pulls code directly from the private upstream repositories ssh access to github must be set up on the machine, and garanted to the repository by an administrator. To allow the build systems to use the ssh key safely it should be added to the system ssh agent, that needs to be running in the current terminal.

To launch the ssh agent use the following commands after launching the terminal.

```sh
eval $(ssh-agent)
ssh-add
```

### Build system setup

In this first step the yocto build system is prepared on the host machine through an ansible playbook. By default the target toolchain is placed in the /software/yocto directory, that needs to be created with the correct permissions, this can be changed by overriding the ***toolchain_target_dir*** variable in yocto_setup.yml

To run this step the following command should be issued:

```sh
ansible-playbook yocto_setup.yml
```

### OS Compilation

Once the host build system is set up the OS compilation can start.

The process is completely automated, and can take, depending on the hardware configuration up to few hours. It should also be noted that the build system is smart enough to resume from where it left off in case of an interruption, thus the process can be stopped and restarted.

These two commands should be issued in succession in the same terminal window, from the target directory to start compilation.

```sh
 source setupsdk
 bitbake uscope
```

### uScope stack build and deployment

The last step in the commissioning process involves the creation of an OS image, that is then written to an sd card and the online commissioning of the uscope platform. An ansible playbook is used 
here as well to automate the process and can be run with the following command (zynq 700)

```sh
ansible-playbook commissioning.yml --ask-vault-pass --extra-vars '@passwd.yml'
```

or the following one for zynq ultrascale+

```sh
ansible-playbook commissioning_kria.yml --ask-vault-pass --extra-vars '@passwd.yml'
```
Several variables can be overridden to control the process:

- ***yocto_image_dir***: This variable informs the tool of the previous step outputs location, its value should be the same as ***toolchain_target_dir***

- ***image_user***: This variable should be overridden with the current user username

- ***image_file***: Full path (including filename) of the generated OS Image it is by default placed in the /software directory the user should make sure to have the correct permissions

- ***mac_address***: Ethernet MAC address used by the OS (This value should only be modified if multiple platforms need to be used in the same network)

The process is composed of three steps, image generation, fs resizing and containers build/deploy. The first step needs to be run only after OS compilation, or if a mac address change is necessary. To run these steps the answer 'yes' should be given when prompted.

To wrie the image to the SD card (when prompted) a third party program should be used [Etcher](https://www.balena.io/etcher/) is recomended.

For speed the generated image is only as large as necessary, to take advantage of the full size of the SD card the filesystem needs to be grown to completely fill the free space aftwe SD card flashing. This step needs to be run only the first time, and consequently is skipped by default and can be run by responding 'yes' when prompted.

## Usage

To start the uscope stack, the following command should be issued on the desired platform terminal.

```sh
docker-compose up
```

Both SSH

```sh
    ssh root@uscope_0.local
```

or

```sh
    picocom -b115200 /dev/ttyUSB0
```

The interface can then be accessed through the interface at the address [https://uscope_0.local](https://uscope_0.local). For technical reasons a per platform custom CA (the file uscope_root.crt), that is generated during commissioning, needs to be installed on each client machine, this can be done on the google chrome in the security section of the settings panel.
