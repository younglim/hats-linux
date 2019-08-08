# Custom Kernel with USB/IP and vhci-hcd support

## Ubuntu: Compile the kernel with custom .config

```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt install bc build-essential libncurses-dev libssl-dev libelf-dev kernel-package -y
rm -rf custom-kernel
mkdir -p custom-kernel
cp "$(ls /usr/src/linux-source-*.tar.bz2 | tail -1)" custom-kernel
cd custom-kernel
tar -xvf "$(ls linux-source-*.tar.bz2 | tail -1)"
wget https://raw.githubusercontent.com/younglim/hats-linux/master/kernel/.config
cd */
DATE=`date +%Y%m%d`
fakeroot make-kpkg --initrd --revision=$DATE.custom kernel_image
cd ..
```


Install the kernel

```

sudo apt-get install ./linux-image-4.15.18_20190621.custom_amd64.deb
```


Reboot the VM:

```
sudo reboot now
```

## RHEL: Configure config with `make menuconfig`

### Install dependencies
```
# Example for RHEL:
yum groupinstall "Development Tools"
yum install ncurses-devel
yum install unifdef
yum install bc
```

### Download and install kernel source
```
rpm -ivh kernel-3.10.0-1062.el7.src.rpm
tar -xvf /root/rpmbuild/SOURCES/linux-3.10.0-1062.el7.tar.xz
cd /root/rpmbuild/SOURCES/linux-3.10.0-1062.el7
cp /boot/config-3.10.0-957.27.2.el7.x86_64 .config
```

###  Customise the Kernel
```
make menuconfig

# Device Drivers -->  USB Support -->
# <*>   USB/IP support, 
# <*>     VHCI hcd -->
# (15)      Number of ports per USB/IP virtual host controller                      │ │  
# (32)      Number of USB/IP virtual host controllers  
# <Save> --> .config

```

### Patch the issue of building rpm
`vi ./scripts/package/Makefile`

Add and remove the following lines
```
 # Remove hyphens since they have special meaning in RPM filenames
 KERNELPATH := kernel-$(subst -,_,$(KERNELRELEASE))
 # Include only those top-level files that are needed by make, plus the GPL copy
TAR_CONTENT := $(KBUILD_ALLDIRS) kernel.spec .config .scmversion Makefile \
-                Kbuild Kconfig COPYING $(wildcard localversion*)
+                Makefile.qlock Kbuild Kconfig COPYING $(wildcard localversion*)
TAR_CONTENT := $(addprefix $(KERNELPATH)/,$(TAR_CONTENT))
 MKSPEC     := $(srctree)/scripts/package/mkspec
```

### Build RPM package
`make rpm-pkg`

### Install RPM package
```
cd /root/rpmbuild/RPMS/x86_64
yum remove kernel-firmware
yum install ./kernel-3.10.0-2.x86_64.rpm  ./kernel-headers-3.10.0-2.x86_64.rpm
```
