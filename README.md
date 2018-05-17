# hats-linux (Remember to check the latest version of VBoxGuestAddition on the website)
http://download.virtualbox.org/virtualbox

## For installing VBox Guest Addition if using VM
 yum -y install dkms wget gcc kernel-devel-$(uname -r)
 wget  -nc --tries=0 --continue --server-response --timeout=0 --retry-connrefused "http://download.virtualbox.org/virtualbox/5.2.12/VBoxGuestAdditions_5.2.12.iso" -nv -O "VBoxGuestAdditions_5.2.12.iso"
 mkdir /media/VBoxGuestAdditions
 mount -o loop,ro VBoxGuestAdditions_5.2.12.iso /media/VBoxGuestAdditions
 sh /media/VBoxGuestAdditions/VBoxLinuxAdditions.run
 rm -f VBoxGuestAdditions_5.2.12.iso
 umount /media/VBoxGuestAdditions
 rmdir /media/VBoxGuestAdditions
