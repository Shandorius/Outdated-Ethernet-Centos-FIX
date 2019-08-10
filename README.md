# FIX for Centos installations on old Hardware

> Maily relates to Ethernet-driver issues. From there everything will be easier.

> For anyone with the same problem, here is what I did (this is the only way I found to work on an offline machine since you need all dependencies)
> This is sort of a copy of my centos forum post, because this way it would be easier to obtain in the future

[Forum post (Click here)](https://www.centos.org/forums/viewtopic.php?f=50&t=70311)

## How to approach the problem:

1. Use the lspci command to get the information on the hardware you have (I exported it to a file and place this on a usb so I could view it on a different pc)

1. Install the exact same Centos version on a different computer on a virtual machine

1. Connect this VM to the internet and add the ELRepo repository

   ```sh
   $ sudo rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
   $ sudo rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
   ```

1. Install the yum plugin downloadonly

   ```sh
   $ yum install -y yum-plugin-downloadonly
   ```

1. Now find the kmod package required for your network card (This can most often be found by googling "youradaptername centos 7 kmod")

1. Download it to a directory instead of installing it

   ```sh
   $ yum install -y --downloadonly --downloaddir=/root/package/ kmod-you-need-here
   ```

1. Move the directory to a USB device

   ```sh
   $ mkdir -p media/USB #creating USB directory to mount usb to

   # Now connect a usb device (it will show sdb/sda/sdc replace the one you have in the next line)
   $ mount -t vfat /dev/sdb1 /media/USB
   $ mv /root/package /media/USB/package
   $ umount /media/USB
   ```

1. Insert the USB into the device and do as explained in code 5

   ```sh
   $ mkdir -p media/USB #creating USB directory to mount usb to

   # Now connect the usb device (it will show sdb/sda/sdc replace the one you have in the next line)
   $ mount -t vfat /dev/sdb1 /media/USB
   $ mv /media/USB/package /package
   $ sed -i 's/enabled=1/enabled=0/' '/etc/yum/pluginconf.d/fastestmirror.conf'
   $ cd /package/
   $ yum install -y --disablerepo=\* *	# Will install the kmod and dependencies
   $ sed -i 's/enabled=0/enabled=1/' '/etc/yum/pluginconf.d/fastestmirror.conf'
   $ reboot # A new boot kernel will show up, boot into this one (This is the default setting)
   ```

Done!
