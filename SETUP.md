# uScope toolchain setup

## Supported systems

The uscope toolchain is developed built and tested on Linux Ubuntu 20.04 , while it should be possible to get it to work on other standard distro (RHEL, Fedora, etc), no testing is performed, and consequently the instructions might be different from these

## Dependencies

Before starting toolchain install and commissioning the following tools should be installed:


- The Xilinx Vivado toolchain, while optional, should be installed for HDL development and debug.
- The following packages from ubuntu repository are needed: libtinfo5 libncurses5 ansible gawk wget git diffstat unzip texinfo gcc-multilib
build-essential chrpath socat libsdl1.2-dev xterm chrpath u-boot-tools qemu-user-static npm

- This version of ubuntu does not carry git-repo in the package repository, and thus it should be installed manually following the 
instruction from the project [homepage](https://gerrit.googlesource.com/git-repo/)

- The following collections need to be installed from ansible galaxy 
ansible-galaxy collection install community.crypto

- The yarn package manager is used to build the client web app, it should be installed through npm
    
    sudo npm install --global yarn
