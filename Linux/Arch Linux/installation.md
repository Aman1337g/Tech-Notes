# Arch Linux Full Installation Guide

## Connecting to Internet

`ip addr show` : to show info about internet connections

### To add wifi device

`iwctl` : will open a prompt [iwd]#
`device list` : to list wifi devices
`station <adapter_name : eg -wlan0> scan` : to scan the local area for wifi
`station wlan0 get-networks` : after scanning to list out wifi devices
`station wlan0 connect "network-name"` , then enter the passwork : to connect

After connecting to wifi network , exit the iwd prompt using **Ctrl+d**

then check if you are connected to internet : `ip addr show` (to show if you have an ip)
to verify if you are connected to internet : `ping -c 5 8.8.8.8`

## Setting up hard-disk (drive) for Arch Installation (Only follow one of the methods below)

### Method1 - Setting up disk (non-uefi)

`fdisk -l` : listing out available disk drives
then,
`fdisk /dev/<disk_name>` , then prompt will open : Command (m for help): 
`p` : to view disk information
`o` : to create a new partition table
`n` : to create new partition then enter, enter, enter, enter to choose default options

Now, this is going to give us just one partition eg - /dev/sda1
`t` : to set the type of partition it is
`8e` : to set the partition type to LVM
`a` : to make the partition active (automatically selects partition1)
`w` : to finalise the changes

NOW, you will be prompted out of **fdisk**

You can view the new partition created using : `fdisk -l`

### Configuring LVM

`pvcreate --dataalignment 1m /dev/sda1` : to create a physical volume
`vgcreate volgroup0 /dev/sda1` : to create a volume group in LVM
`lvcreate -L 30GB volgroup0 -n lv_root` : to create logical volume
`lvcreate -l 100%FREE volgroup0 -n lv_home` : space to store our personal files
`modprobe dm_mod` : to load our new volume to memory
`vgscan` : scan the system for volume group
`vgchange -ay` : to activate the volume group
`mkfs.ext4 /dev/volgroup0/lv_root`: to format our logical volumes
`mount /dev/volgroup0/lv_root /mnt` : to mount the logical volumes
`mount /dev/volgroup0/lv_home` : mounting our second volume group

Mount logical volumes at /mnt/home
mkdir /mnt/home
mount /dev/volgroup0/lv_home /mnt/home
mkdir /mnt/etc
genfstab -U -p /mnt >> /mnt/etc/fstab : to save the partition layout
cat /mnt/etc/fstab : to check the file

Method2 : Setting up Disk (with uefi)

Firsly enable UEFI if your device support in the BIOS before going further

fdisk -l : to select the disk drive we want to work with (let's assume it to be - /dev/sda)
fdisk /dev/sda
p : to print all the partitions we have on the disk
g : to create a new gpt partition table
n : to create new partition, enter(for partition no.), enter(first sector), type +500M(megabytes) for efi partition
enter - Partition Created !!

t : type of partition
1 : for EFI System
n : to create another partition, enter, enter, enter(to take up all the available free space)
enter, Parition2 Created !!

t : type of partition
enter (default2), 30 (LVM partition type)
p : to display disk information
now we have to partitions - EFI System and Linux LVM
w : for finalising changes

Formatting partitions
mkfs.fat -F32 /dev/sda1

Setting up LVM
pvcreate --dataalignment 1m /dev/sda2 : to create a physical volume
vgcreate volgroup0 /dev/sda2 : to create a volume group in LVM
lvcreate -L 30GB volgroup0 -n lv_root : to create logical volume
lvcreate -l 100%FREE volgroup0 -n lv_home : space to store our personal files
modprobe dm_mod : to load our new volume to memory
vgscan : scan the system for volume group
vgchange -ay : to activate the volume group
mkfs.ext4 /dev/volgroup0/lv_root: to format our logical volumes
mount /dev/volgroup0/lv_root /mnt : to mount the logical volumes
mount /dev/volgroup0/lv_home : mounting our second volume group

Mount logical volumes at /mnt/home
mkdir /mnt/home
mount /dev/volgroup0/lv_home /mnt/home
mkdir /mnt/etc
genfstab -U -p /mnt >> /mnt/etc/fstab : to save the partition layout
cat /mnt/etc/fstab : to check the file

Method3 - Setting up Disk (uefi with encryption)
Enable UEFI in BIOS in order for UEFI methods to work

fdisk -l : to list out disks (to look out for disk identifier eg - /dev/sda)
fdisk /dev/sda
p : to list out all the partitions you currently have
g : to create a new gpt partition layout
n : to create new partition, enter, enter, +500M for efi
t : type of partition
1 : enter, Changed type of partition 'Linux filesystem' to 'EFI System'
n : creating second partition, enter, enter, +500 for another partition
n : for final partition(LVM), enter, enter, enter
t : type of partition, enter(default partition no.3), 30(LVM partition type), enter
p : Three partitions created (EFI System, Linux filesystem, and Linux LVM) 
w : for finalising changes

Formatting partitions
mkfs.fat -F32 /dev/sda1 (for first partition)
mkfs.fat -F32 /dev/sda2 (for second partition)
we will not format the third partition as we are going to use it for LVM

Let's encrypt the third partition
cryptsetup luksFormat /dev/sda3
type 'yes'
enter passphrase : ****
verify : ****
(we can ignore the warning if it comes)
Now partition will be encrypted

Now to work with it, we will have to un-encrypt it
cryptsetup open --type luks /dev/sda3 lvm
enter passphrase : ****
Now it will be unlocked

Setting up LVM
pvcreate --dataalignment 1m /dev/mapper/lvm : to create a physical volume
vgcreate volgroup0 /dev/mapper/lvm : to create a volume group in LVM
lvcreate -L 30GB volgroup0 -n lv_root : to create logical volume
lvcreate -l 100%FREE volgroup0 -n lv_home : space to store our personal files
modprobe dm_mod : to load our new volume to memory
vgscan : scan the system for volume group
vgchange -ay : to activate the volume group
mkfs.ext4 /dev/volgroup0/lv_root: to format our logical volumes
mount /dev/volgroup0/lv_root /mnt : to mount the logical volumes
mkdir /mnt/boot : creating a boot directory
mount /dev/sda2 /mnt/boot : mounting second partition at /mnt/boot
mkfs.ext4 /dev/volgroup0/lv_home : formatting the logical volume that will be used for /home folder

Mount logical volumes at /mnt/home
mkdir /mnt/home
mount /dev/volgroup0/lv_home /mnt/home
mkdir /mnt/etc : fstab is the file that system reads at bootup
genfstab -U -p /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab : will show three volumes : / , /boot , /home

Section4 - Installing Arch Linux
Now, at this point you should have your disk prepared and ready to go for Arch Linux

Installing all base packages for Arch Linux
pacstrap -i /mnt base : base is a package group that contains a bunch of packages
press 'Y' for installing packages

Configuring our installation
arch-chroot /mnt : will give us access to arch linux installation

Installing linux kernel
pacman -S linux linux-headers linux-lts linux-lts-headers : here we are installing both rolling release and stable(lts) release kernels
you will be able to select which kernel to use at boot time
enter : Enter a number (default=1):
Proceed with installation? [Y/n] : enter
Now we will have both linux and linux-lts ready to go!!

Finalising the remainder of installation
pacman -S nano : installing nano editor
pacman -S base-devel openssh : base-devel is a package group that will install development related packages and openssh will give us the ability to manage our installation remotely
press enter, enter, and enter (it will now install all the dependcies needed and those two packages)
if you have installed openssh, then you may consider running : systemctl enable sshd
so that openssh runs at system startup so you don't have to start it manually to access remote works
packman -S networkmanager wpa_supplicant wireless_tools netctl : installing packages required for networking
pacman -S dialog : gives ability to use something like wifi menu to connect to wifi over commandline in situation where our GUI isn't working 
systemctl enable NetworkManager

Adding lvm support (Needed regardless of the method you used in setting up your hard-disk)
pacman -S lvm2
nano /etc/mkinitcpio.conf : In order to ensure our boot process supports our configurations 
Scroll over first uncommented line starting with HOOKS : include encrypt if you have used encrpytion otherwise not write it
HOOKS=(base udev autodetect modconf block encrypt lvm2 filesystems keyboard fsck) 
Now save that file : Ctrl+s Ctrl+x

For those effects to take place
mkinitcpio -p linux
mkinitcpio -p linux-lts (as we have installed both linux and linux-lts kernel)
nano /etc/locale.gen : uncomment according to your location eg - en_US.UTF-8 UTF-8
save the file Ctrl+s Ctrl+x
locale-gen
passwd : to set a password for root user account (make sure it's a really good one)
useradd -m -g users -G wheel <your_user> : to create a standard user
passwd <your_user>
pacman -S sudo : checking if sudo package is installed on system OR which sudo (if installed we will get output otherwise no-output)
EDITOR=nano visudo : associating wheel group with sudo
uncomment line containing %wheel ALL=(ALL) ALL
press Ctrl+o, enter to save file, Ctrl+x to exit the file

Installing GRUB - to make system bootable and independable from bootable media

If you have gone with the non-uefi methods, packages to install
pacman -S grub dosfstools os-prober mtools

If gone along with one of the two uefi methods, packages to install
pacman -S grub efibootmgr dosfstools os-prober mtools

If you have used one of the two uefi methods for setting up your hard disk for arch installation, you need to create the following directory
mkdir /boot/EFI
mount /dev/sda1 /boot/EFI

Installing grub

For non-uefi
grub-install --target=i386-pc --recheck /dev/sda

For those of you that chose to utilize EFI for your installation
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck

check if there is a locale directory in /boot/grub
ls -l /boot/grub
if not present create one,
mkdir /boot/grub/locale (if exists no need to write)
cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo

For those who did decided to go with encryption
nano /etc/default/grub : informing grub to unlock disk at bootup, uncomment the line GRUB_ENAABLE_CRYPTODISK=y
and set GRUB_CMDLINE_LINUX_DEFAULT="cryptodevice=/dev/sda3:volgroup0:allow-discards loglevel=3 quiet"

grub mkconfig -o /boot/grub/grub.cfg : to generate grub configuration file
exit : to exit the chroot environment

umount -a : to unmount everything
reboot 

Post-install Tweaks

su : to log in as root (enter root password)
cd /root
dd if=/dev/zero of=/swapfile bs=1M count=2048 status=progress : creating 2GB size swap file at root of the file-system 
chmod 600 /swapfile : to change permission of swapfile
mkswap /swapfile : making it true swapfile
cp /etc/fstab /etc/fstab.bak: creating a backup fstab file in case we make a mistake
echo '/swapfile none swap sw 0 0' | tee -a /etc/fstab : mounting swapfile
cat /etc/fstab : to check if the swapfile line was added
free -m : to check for swap size
mount -a : to test the /etc/fstab file
swapon -a : to enable swap
free -a : to see the swap size
timedatectl list-timezones : to see the timezones available, find one that is closest to your location (write it down somewhere)
timedatectl set-timezone <noted_down_location> : setting timezone
systemctl enable systemd-timesyncd : enabling service for clock synchronisation
hostnamectl set-hostname <your-hostname> : setting hostname for our system i.e. - user@hostname

nano /etc/hosts : edit the /etc/hosts file
127.0.0.1 localhost
127.0.1.1 <your_hostname>
save file then

hostnamectl : to view host information
pacman -S intel-ucode: to install microcode for your computer (for amd it is amd-ucode)

Installing a GUI environment or Desktop environment: For that we need to have Xorg/Wayland installed and a video driver

pacman -S xorg-server
pacman -S mesa : if you have amd/intel gpu otherwise , pacman -S nvidia : for nvidia gpu (normal linux kernel) if you installed linux-lts kernet , pacman -S nvidia-lts according to kernel or kernels you installed

If you are installing driver inside a virtualbox either of mesa/nvidia will work
packages to install if you are using a vm-box
pacman -S virtualbox-guest-utils xf86-video-vmware
systemctl enable vboxservice : to ensure virtualbox extensions are working properly on system startup

Setting up a desktop environment

1) gnome : pacman -S gnome 
   then, [optional] to get more control over gnome env. : pacman -S gnome-tweaks
systemctl enable gdm : enabling display manager
then, reboot : after rebooting if everything goes well, you will see a login screen
then , go to settings>region & language : then select your preferred language after that it will prompt for restart, restart your device then

2) plasma : pacman -S plasma-meta kde-applications
   systemctl enable sddm : enabling display manager 

3) xfce : pacman -S xfce4 xfce4-goodies
   pacman -S lightdm lightdm-gtk-greeter : installing display manager
   systemctl enable lightdm
   then, reboot

4) matte : pacman -S mate mate-extra
   pacman -S lightdm lightdm-gtk-greeter
   systemctl enable lightdm
   reboot


