# Arch-Linux Installation

## The Easiest Way to Install Arch Linux in 20 Minutes or NOT(Dual Boot with Windows 11)

Installing Arch Linux used to be a daunting task, but not anymore! In this guide, you’ll learn how to install Arch Linux easy and manually ,without using the official ArchInstall script which i personally find confusing especially when it comes to formatting and partition drives. This method works whether you want to install Arch on a dedicated drive or dual-boot alongside Windows 11 and gives you more flexibility of you machine configuration ,something that arch users take pride in.

🎯 Goal: Dual-boot Arch Linux with Windows 11 using separate drives (same-drive setup covered via external video link in the original tutorial).

🧰 What You'll Need

A dedicated or empty drive for Arch Linux

USB drive (8GB or higher)

Internet connection (Ethernet or Wi-Fi)

Access to BIOS settings

A Windows PC or laptop (Windows 11 in this case)

## Step 1: Download Arch Linux & Create Bootable USB

Go to the Arch Linux official site and download the ISO image.

Use a tool like:

Rufus (Recommended)

Ventoy

Balena Etcher

Burn the ISO to your USB drive using your chosen tool.

## ⚙️ Step 2: Boot from USB and Access BIOS

Restart your PC and enter the BIOS/UEFI using keys like F2, F10, ESC, or DEL (varies by manufacturer).

Enable USB Boot and set USB as the primary boot device.

Disable Secure Boot (important).

Save and exit BIOS.

Your system ashould now boot into the Arch Linux Live session (CLI-based).

## Step 3: Connect to the Internet

Ethernet: Use ping archlinux.org to test.

Wi-Fi:

```sh
iwctl
device list
device wlan0 show
station wlan0 get-networks
station wlan0 connect "SSID_NAME"
```

Enter the Wi-Fi password. Exit iwctl, and verify connectivity:

```sh
ping google.com
```

Interrupt with CTRL + C once confirmed.

## Step 4: Format the Target Drive

List drives:

```sh
fdisk -l
```

Initially i had 98G of free space and i had to split it it three partitions for the EFI paartition where grub will reside,Root Partition for the linux Operating system and a swap partition

```sh
mkfs.fat -F32 /dev/sda5 
mkfs.ext4  /dev/sda6
mkswap  /dev/sda7
```

/dev/sda5 295317504 296341503 1024000 500M EFI System /dev/sda6 296341504 485085183 188743680 90G Linux filesystem /dev/sda7 485085184 500117503 15032320 7.2G Linux swap

## Step 5: Mount the Partitions

```sh
mount / /dev/sda6 /mnt
mount / /dev/sda5 /mnt/boot
swapon /dev/sda7
lsblk # to verify everything
```

## Step 6: Install the most essential packages on your root partition

```sh
pacstrap -i /mnt base base-devel linux linux-firmware git sudo htop vim nano network-manager-applet grub chromium konsole efibootmgr i3 gcc make xorg gdm firefox 
```

```sh
genfstab -U /mnt 
```

```sh
genfstab -U /mnt >> /mnt/etc/fstab
```

## Step 7: Chroot into you new system

```sh
arch-chroot /mnt
passwd
```

```sh
useradd -m -g users -G wheel,storage,power,video,audio -s /bin/bash username
```

```sh
passswd username
EDITOR=nano visudo 

```

## Step 8: Configure GRUB

```sh
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=ARCHLINUX
grub-mkconfig -o /boot/grub/grub.cfg
systemctl enable NetworkManager gdm
```
