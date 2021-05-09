# uScope toolchain setup

## Supported systems

The uscope toolchain is developed built and tested on Ubuntu Linux, while it should be possible to get it to work on other standard distro (RHEL, Fedora, etc), no testing is performed, and consequently the instructions might be different from these

### Known working versions

- Ubuntu 20.04 (Focal Fossa) LTS
- Ubuntu 21.04 Hirsute Hippo

## Dependencies

Before starting toolchain install and commissioning the following tools should be installed:

### common

- The Xilinx Vivado toolchain, while optional, should be installed for HDL development and debug.
- The following packages from ubuntu repository are needed: libtinfo5, libncurses5, ansible, gawk wget git diffstat unzip texinfo gcc-multilib
build-essential chrpath socat libsdl1.2-dev xterm chrpath

### Ubuntu 21.04

- For Ubuntu 21.04 the package python-is-python2 is needed by yocto

### Ubuntu 20.04

- This version of ubuntu does not carry git-repo in the package repository, and thus it should be installed manually following the 
instruction from the project [homepage](https://gerrit.googlesource.com/git-repo/)
