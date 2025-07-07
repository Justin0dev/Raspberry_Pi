
HUGEPAGES is diabled by default in the Raspberry Pi Linux kernel.   To enable HUGEPAGES the kernel must be recompiled.  

## Install dependencies
`sudo apt update`
`sudo apt install -y git build-essential bc bison kmod cpio flex libncurses5-dev libelf-dev libssl-dev`

## Clone the Raspberry Pi Linux Kernel Repository
`git clone --depth=1 https://github.com/raspberrypi/linux`
`cd linux`

## Backup `.config` file before customizing
`cp .config .config.backup`  
The `.config` file is not present until the following command is ran which generates a fresh basic `.config` file.  

## Configure Kernel
`KERNEL=kernel_2712`
`make bcm2712_defconfig` 

## Enable hugepage support in the kernel configuration - One line at a time
```bash
sed -i 's/# CONFIG_HUGETLBFS.*/CONFIG_HUGETLBFS=y/' .config
sed -i 's/# CONFIG_TRANSPARENT_HUGEPAGE.*/CONFIG_TRANSPARENT_HUGEPAGE=y/' .config
sed -i 's/# CONFIG_HUGETLB_PAGE.*/CONFIG_HUGETLB_PAGE=y/' .config 
sed -i 's/# CONFIG_ARCH_HAS_HUGEPD.*/CONFIG_ARCH_HAS_HUGEPD=y/' .config
sed -i 's/# CONFIG_HUGETLB_PAGE_SIZE_VARIABLE.*/CONFIG_HUGETLB_PAGE_SIZE_VARIABLE=y/' .config
sed -i 's/# CONFIG_ARCH_WANT_GENERAL_HUGETLB.*/CONFIG_ARCH_WANT_GENERAL_HUGETLB=y/' .config
sed -i 's/# CONFIG_SYSFS.*/CONFIG_SYSFS=y/' .config
sed -i 's/# CONFIG_CHECKPOINT_RESTORE.*/CONFIG_CHECKPOINT_RESTORE=y/' .config
sed -i 's/# CONFIG_HIGH_RES_TIMERS.*/CONFIG_HIGH_RES_TIMERS=y/' .config
```

## Build the kernel

`make -j4 Image.gz modules dtbs`

## Install the kernel
```bash
sudo make modules_install
sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/firmware/
sudo cp arch/arm64/boot/dts/overlays/*.dtb* /boot/firmware/overlays/
sudo cp arch/arm64/boot/dts/overlays/README /boot/firmware/overlays/
sudo cp arch/arm64/boot/Image.gz /boot/firmware/$KERNEL.img
```
`sudo cp arch/arm64/boot/Image.gz /boot/firmware/kernel8hp.img`

Edit the `config.txt` file and specify the new kernel:  
`sudo nano /boot/firmware/config.txt`  
Add the following line and comment out and other `kernel=` lines if present.
`kernel=kernel8hp.img`

## Reboot and kernel will be installed and active

Check current HUGEPAGES:  
Display HUGEPAGE information
`grep -i huge /proc/meminfo`
Displays the number of HUGEPAGES currently allocated
`grep HugePages_Total /proc/meminfo`  




