# Custom Kernel with USB/IP and vhci-hcd support

Compile the kernel with custom .config

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
RHEL: Configure config with `make menuconfig`

```
# Example for RHEL:
sudo yum install elfutils-libelf-devel kernel-devel -y
cd /usr/src/kernels/3.10.0-957.27.2.el7.x86_64
sudo cp -r . ~/kernel
cd ~/kernel
sudo chown -R $USER:$USER .
sudo make menuconfig
```

Install the kernel

```

sudo apt-get install ./linux-image-4.15.18_20190621.custom_amd64.deb
```


Reboot the VM:

```
sudo reboot now
```
