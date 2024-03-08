# Linux-on-RISC-V
Composing disk and container images for Linux on RISC-V
Implementaion
4.1) Disk Image:
Prerequisites: Fedora Operating System , Visionfive Starfive 2 board.
4.1.1) Building Toolchain:
1. Step 1: git clone https://github.com/riscv/riscv-gnu-toolchain
2. Step 2: sudo yum install autoconf automake python3 libmpc-devel mpfr-devel
gmp-devel gawk bison flex texinfo patchutils gcc gcc-c++ zlib-devel expat-devel
(above command must be in a single line or use ‘\’ )
3. Step 3: ./configure --prefix=/path to the destination/riscv --with-arch=rv64gc 
4. Step 4: make linux
5. Step 5: goto terminal vi .bashrc add following line anywhere in the file
 export PATH="/path to directory where installed/bin:$PATH"
Description:
Step 1: Clone the RISC-V GNU Toolchain Repository. Use the git clone command
to clone the RISC-V GNU Toolchain repository from GitHub. This repository
contains the source code for the RISC-V GCC compiler and related tools.
Example: git clone https://github.com/riscv/riscv-gnu-toolchain
Step 2: Install Required Dependencies. Use the package manager (in this case, yum)
to install the required dependencies for building the toolchain. The dependencies
include autoconf, automake, python3, libmpc-devel, mpfr-devel, gmp-devel,
C-DAC ACTS (Pune)/PG-DESD/ P a g 
e | 23
 Composing Disk and Container Image for Linux on RISC-V
gawk, bison, flex, texinfo, patchutils, gcc, gcc-c++, zlib-devel, and expat-devel.
Example: sudo yum install autoconf automake python3 libmpc-devel mpfr-devel
gmp-devel gawk bison flex texinfo patchutils gcc gcc-c++ zlib-devel expat-devel
Step 3: Configure the Toolchain. Run the configure script in the RISC-V GNU
Toolchain directory to configure the build settings. Specify the installation prefix
using the --prefix option (e.g., /opt/riscv) to specify the path where the toolchain
will be installed. Use the --with-arch option to specify the RISC-V architecture
and ABI (Application Binary Interface) to target. Here, rv64gc indicates a 64-bit
RISC-V architecture with the gc (IMAFD) extension.
Example: ./configure --prefix=/opt/riscv --with-arch=rv64gc
Step 4: Build the Toolchain . Use the make command to build the RISC-V GNU
Toolchain for Linux. The make command will compile the GCC compiler, binutils, and
other tools necessary for building RISC-V binaries.
Example: make linux
After completing these steps, you should have a compiled RISC-V GNU Toolchain
installed in the specified prefix directory (/opt/riscv in this case). This toolchain can be
used to compile and build RISC-V binaries for the specified architecture and ABI.
4.1.2) U-boot
1. git clone https://github.com/starfive-tech/u-boot.git
2. cd u-boot
git checkout -b JH7110_VisionFive2_devel origin/JH7110_VisionFive2_devel
git pull
3. make <Configuration_File> ARCH=riscv CROSS_COMPILE=riscv64-linux-gnuC-DAC ACTS (Pune)/PG-DESD/ P a g 
e | 24
 Composing Disk and Container Image for Linux on RISC-V
 Configuration_File: For VisionFive 2, the file is
starfive_visionfive2_defconfig.
4. make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu5. ls
Output: There will be these 3 files generated after compilation inside the u-boot directory:
 u-boot.bin
 arch/riscv/dts/starfive_visionfive2.dtb
 spl/u-boot-spl.bin
Clone the U-Boot Repository: Use git clone to clone the U-Boot repository from the 
specified URL.
Navigate to the U-Boot Directory: Use cd to change the current directory to the U-Boot 
directory.
Checkout to a Specific Branch: Use git checkout -b to create and switch to a new 
branch based on the specified branch in the repository.
Pull the Latest Changes: Use git pull to fetch and apply the latest changes from the 
remote repository to the current branch.
Compile U-Boot with Specific Configuration: Use make with the specified 
configuration file (starfive_visionfive2_defconfig for VisionFive 2) to compile U-Boot 
for the RISC-V architecture using the specified cross-compiler (riscv64-linux-gnu-).
Compile U-Boot without Configuration: Use make without a configuration file to 
compile U-Boot for the RISC-V architecture using the specified cross-compiler.
List the Generated Files: Use ls to list the files generated after compilation in the UBoot directory, which should include u-boot.bin, arch/riscv/dts/starfive_visionfive2.dtb, 
and spl/u-boot-spl.bin.
4.1.3) OpenSBI Compilation and Integration
1. git clone https://github.com/starfive-tech/opensbi.git
2. cd opensbi
3. make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- PLATFORM=generic
FW_PAYLOAD_PATH={U-BOOT_PATH}/u-boot.bin FW_FDT_PATH={UBOOT_PATH}/arch/riscv/dts/starfive_visionfive2.dtb 
FW_TEXT_START=0x40000000
After compilation, the file fw_payload.bin will be generated in the directory 
opensbi/build/platform/generic/firmware and the size is larger than 2M.
C-DAC ACTS (Pune)/PG-DESD/ P a g 
e | 25
 Composing Disk and Container Image for Linux on RISC-V
 4. git clone https://github.com/starfive-tech/Tools
 5. cd Tools
 6. git checkout master
 7. git pull
 8. cd spl_tool/
 9. make
 10. ./spl_tool -c -f {U-BOOT_PATH}/spl/u-boot-spl.bin
You will see a new file named u-boot-spl.bin.normal.out generated under {UBOOT_PATH}/spl. 
 11. cd Tools/uboot_its
 12. cp {OPENSBI_PATH}/build/platform/generic/firmware/fw_payload.bin ./
 13. {U-BOOT_PATH}/tools/mkimage -f visionfive2-uboot-fit-image.its -A riscv -O 
u-boot -T firmware visionfive2_fw_payload.img
You will see a new file named visionfive2_fw_payload.img generated.
4.1.4 ) Linux Kernel with Container Support
1. git clone https://github.com/starfive-tech/linux.git
2. cd linux
git checkout -b JH7110_VisionFive2_devel origin/JH7110_VisionFive2_devel
git pull
3. make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnustarfive_visisionfive2_defconfig 
(this is a single instruction and space is there between gnu- and starfive)
4. make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- menuconfig
5. Do the following configurations in the menuconfig menu
 General setup --->
 [*] POSIX Message Queues
 BPF subsystem --->
 [*] Enable bpf() system call (<span style="color:green;">Optional</span>)
 [*] Control Group support --->
 [*] Memory controller 
 [*] Swap controller (<span style="color:green;">Optional</span>)
 [*] Swap controller enabled by default (<span style="color:green;">Optional</span>)
 [*] IO controller (<span style="color:green;">Optional</span>)
 [*] CPU controller --->
 [*] Group scheduling for SCHED_OTHER (<span style="color:green;">Optional</span>)
 [*] CPU bandwidth provisioning for FAIR_GROUP_SCHED (<span style="color:green;">Optional</span>)
 [*] Group scheduling for SCHED_RR/FIFO (<span style="color:green;">Optional</span>)
 [*] PIDs controller (<span style="color:green;">Optional</span>)
 [*] Freezer controller
 [*] HugeTLB controller (<span style="color:green;">Optional</span>)
 [*] Cpuset controller
 [*] Include legacy /proc/<pid>/cpuset file (<span style="color:green;">Optional</span>)
 [*] Device controller
 [*] Simple CPU accounting controller
 [*] Perf controller (<span style="color:green;">Optional</span>)
 [*] Support for eBPF programs attached to cgroups (<span style="color:green;">Optional</span>)
 [*] Namespaces support
C-DAC ACTS (Pune)/PG-DESD/ P a g 
e | 26
 Composing Disk and Container Image for Linux on RISC-V
 [*] UTS namespace
 [*] IPC namespace
 [*] User namespace (<span style="color:green;">Optional</span>)
 [*] PID Namespaces
 [*] Network namespace
General architecture-dependent options --->
 [*] Enable seccomp to safely execute untrusted bytecode (<span style="color:green;">Optional</span>)
[*] Enable the block layer --->
 [*] Block layer bio throttling support (<span style="color:green;">Optional</span>)
[*] Networking support --->
 Networking options --->
 [*] Network packet filtering framework (Netfilter) --->
 [*] Advanced netfilter configuration
 [*] Bridged IP/ARP packets filtering
 Core Netfilter Configuration --->
 [*] Netfilter connection tracking support
 [*] Network Address Translation support 
 [*] MASQUERADE target support
 [*] Netfilter Xtables support
 [*] "addrtype" address type match support
 [*] "conntrack" connection tracking match support
 [*] "ipvs" match support (<span style="color:green;">Optional</span>)
 [*] "mark" match support 
 [*] IP virtual server support ---> (<span style="color:green;">Optional</span>)
 [*] TCP load balancing support (<span style="color:green;">Optional</span>)
 [*] UDP load balancing support (<span style="color:green;">Optional</span>)
 [*] round-robin scheduling (<span style="color:green;">Optional</span>)
 [*] Netfilter connection tracking (<span style="color:green;">Optional</span>) 
 IP: Netfilter Configuration --->
 [*] IP tables support
 [*] Packet filtering
 [*] iptables NAT support
 [*] MASQUERADE target support
 [*] REDIRECT target support (<span style="color:green;">Optional</span>)
 [*] 802.1d Ethernet Bridging
 [*] VLAN filtering
 [*] QoS and/or fair queueing ---> (<span style="color:green;">Optional</span>)
 [*] Control Group Classifier (<span style="color:green;">Optional</span>)
 [*] L3 Master device support
 [*] Network priority cgroup (<span style="color:green;">Optional</span>)
Device Drivers --->
 [*] Multiple devices driver support (RAID and LVM) --->
 [*] Device mapper support (<span style="color:green;">Optional</span>)
 [*] Thin provisioning target (<span style="color:green;">Optional</span>)
 [*] Network device support --->
 [*] Network core drive support
 [*] Dummy net driver support
 [*] MAC-VLAN net driver support
 [*] IP-VLAN support
 [*] Virtual eXtensible Local Area Network (VXLAN)
 [*] Virtual ethernet pair device
 Character devices --->
 -*- Enable TTY
 -*- Unix98 PTY support
 [*] Support multiple instances of devpts (option appears if you are using systemd)
File systems --->
 [*] Btrfs filesystem support (<span style="color:green;">Optional</span>)
 [*] Btrfs POSIX Access Control Lists (<span style="color:green;">Optional</span>)
 [*] Overlay filesystem support 
 Pseudo filesystems --->
 [*] HugeTLB file system support (<span style="color:green;">Optional</span>)
Security options --->
 [*] Enable access key retention support
6. make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- -j$(nproc)
7. Till this point in arch/riscv/boot directory Image.gz and dts will be generated
C-DAC ACTS (Pune)/PG-DESD/ P a g 
e | 27
 Composing Disk and Container Image for Linux on RISC-V
vmlinuz has to be generated to have a compressed image of the kernel.
8. Make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnuINSTALL_PATH=/path to a directory zinstall –j$(nproc) 
This will generate the vmlinuz ,config file and system map file which have to copied to
boot partition. 
4.2 Container Image 
1) Boot the Visionfive2 board or qemu emulated riscv board with fedora image 
having the container support.
2) sudo yum install buildah
3) sudo yum install podman
4) Vi install.sh
#! /bin/bash
echo "Start of Script" 
scratchcontainer=$(buildah from scratch)
scratchmnt=$(buildah mount $scratchcontainer)
dnf install --installroot $scratchmnt -y --releasever 38 @buildsys-build --setopt install_weak_deps=false
buildah rename $scratchcontainer fedora38-riscv64g
buildah commit fedora38-riscv64g fedora38-riscv64g-image
buildah unmount fedora38-riscv64g
 5) podman run -it fedora38-riscv64g-image bash
Description:
 Install Buildah and Podman:Use sudo yum install buildah podman to install 
Buildah and Podman on your system. Buildah is used for building OCI (Open 
Container Initiative) container images, while Podman is used for running 
containers.
 Create the Install Script: Create a new script file (e.g., install.sh) and add the 
provided script content. This script will create a minimal Fedora container image 
with the necessary packages and configurations.
 Make the Script Executable: Use chmod +x install.sh to make the script 
executable.
 Run the Install Script:Run the script using ./install.sh to execute the commands 
inside the script.
 The script will: 
a) Create a new container from scratch using Buildah. Mount the container's 
filesystem to a directory for modifications.
b) Use dnf (Fedora's package manager) to install the necessary packages and 
dependencies into the container's filesystem.
c) Rename the container to fedora38-riscv64g.
d) Commit the container to an image named fedora38-riscv64g-image.
C-DAC ACTS (Pune)/PG-DESD/ P a g 
e | 28
 Composing Disk and Container Image for Linux on RISC-V
e) Unmount the container's filesystem.
f) Run a Container with the Fedora Image:
 After the image is created, run a container with the newly created Fedora image 
using podman run -it fedora38-riscv64g-image bash.
 This command starts a new container from the fedora38-riscv64g-image image 
and opens a bash shell inside the container, allowing interaction with the 
containerized environment. Explore the Container Environment: Inside the 
container, you can explore the Fedora environment, install additional packages, 
and run applications.
By following these steps, you can create a custom Fedora container image with container 
support and run it on your VisionFive2 board or a QEMU emulated RISC-V board. This 
allows you to test and develop applications in a containerized environment on your 
RISC-V platform.
