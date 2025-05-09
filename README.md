# THE ULTIMATE ARCH LINUX INSTALLATION & CONFIGURATION GUIDE

<div align="center">
  <img src="https://archlinux.org/static/logos/archlinux-logo-dark-90dpi.ebdee92a15b3.png" width="450" alt="Arch Linux Logo">
  <br><br>
  <h3><em>The definitive, professional-grade, comprehensive guide to installing and configuring Arch Linux</em></h3>
  <p><strong>From bare metal to fully customized desktop environment</strong></p>
</div>

---

## 📑 MASTER TABLE OF CONTENTS

### PART I: INSTALLATION
- [Introduction & Prerequisites](#-introduction--prerequisites)
- [Pre-Installation Setup](#-pre-installation-setup)
- [Live Environment Preparation](#-live-environment-preparation)
- [Network Configuration](#-network-configuration)
- [Disk Preparation](#-disk-preparation)
- [Base System Installation](#-base-system-installation)
- [System Configuration](#-system-configuration)
- [Bootloader Installation](#-bootloader-installation)
- [Final Installation Steps](#-final-installation-steps)

### PART II: DESKTOP ENVIRONMENT SETUP
- [Basic Desktop Setup](#-basic-desktop-setup)
- [Full Desktop Environments](#-full-desktop-environments)
- [Window Managers](#-window-managers)
  - [i3 Window Manager Setup](#i3-window-manager-setup)
  - [bspwm Window Manager Setup](#bspwm-window-manager-setup)
  - [dwm Window Manager Setup](#dwm-window-manager-setup)
- [Graphical Environment Essentials](#-graphical-environment-essentials)

### PART III: SYSTEM CUSTOMIZATION
- [System Optimization](#-system-optimization)
- [Essential Software](#-essential-software)
- [AUR Helper Setup](#-aur-helper-setup)
- [Advanced Customization](#-advanced-customization)

### PART IV: REFERENCE & TROUBLESHOOTING
- [Post-Installation Reference](#-post-installation-reference)
- [Comprehensive Troubleshooting](#-comprehensive-troubleshooting)
- [Security Hardening](#-security-hardening)
- [Maintenance Tasks](#-maintenance-tasks)

---

# PART I: INSTALLATION

## 🔍 Introduction & Prerequisites

### About This Guide

This comprehensive guide covers the complete process of installing and configuring Arch Linux with a professional approach, prioritizing:

- **Clarity**: Precise, unambiguous instructions
- **Efficiency**: Optimized procedures without unnecessary steps
- **Best Practices**: Industry-standard approaches for reliability
- **Completeness**: Coverage of everything from installation to advanced customization

### System Requirements

- **Processor**: 64-bit (x86_64) processor
- **RAM**: 2GB minimum (4GB+ recommended)
- **Storage**: 20GB minimum (50GB+ recommended)
- **Internet Connection**: Required during installation
- **USB Drive**: 4GB+ for installation media

### Prerequisites Knowledge

While this guide is comprehensive, basic familiarity with these concepts is helpful:
- Command-line interface navigation
- Basic understanding of partitioning and filesystems
- Awareness of your hardware specifications (CPU, GPU, etc.)

### Installation Overview

The Arch Linux installation process consists of these major phases:

1. **Preparation**: Creating bootable media and configuring BIOS/UEFI
2. **Live Environment**: Booting into the installer and preparing the environment
3. **Disk Setup**: Partitioning and formatting storage
4. **Base Installation**: Installing the core system
5. **System Configuration**: Setting up the new installation
6. **Bootloader**: Installing and configuring the bootloader
7. **Post-Installation**: Installing desktop environments and applications

Let's begin with a professional, systematic approach to installation.

---

## 📥 Pre-Installation Setup

### 1. Download the Installation Image

1. Visit the official [Arch Linux download page](https://archlinux.org/download/)
2. Download the latest ISO file (`archlinux-yyyy.mm.dd-x86_64.iso`)
3. Verify the ISO integrity (highly recommended for professionals):

   **On Linux/macOS:**
   ```bash
   # Download the PGP signature
   curl -O https://archlinux.org/iso/latest/archlinux-yyyy.mm.dd-x86_64.iso.sig
   
   # Verify the signature
   gpg --keyserver-options auto-key-retrieve --verify archlinux-yyyy.mm.dd-x86_64.iso.sig
   ```

   **On Windows:**
   ```powershell
   # Calculate SHA-256 checksum
   Get-FileHash -Algorithm SHA256 archlinux-yyyy.mm.dd-x86_64.iso
   # Compare the output hash with the one listed on the download page
   ```

### 2. Create Bootable USB Installation Media

#### Option A: Using Linux

```bash
# Replace sdX with your USB device (BE ABSOLUTELY CERTAIN!)
sudo dd bs=4M if=/path/to/archlinux-yyyy.mm.dd-x86_64.iso of=/dev/sdX status=progress oflag=sync
```

To identify your USB device:
```bash
lsblk
# Look for your USB device size and name (e.g., sdb, sdc)
```

#### Option B: Using Windows

1. **With Rufus:**
   - Download [Rufus](https://rufus.ie/)
   - Launch Rufus
   - Select your USB drive
   - Click "SELECT" and choose the Arch Linux ISO
   - Use the default settings
   - Click "START" and select "Write in ISO Image mode" when prompted

2. **With Etcher:**
   - Download [Etcher](https://www.balena.io/etcher/)
   - Launch Etcher
   - Click "Flash from file" and select the Arch Linux ISO
   - Select your USB drive
   - Click "Flash!"

#### Option C: Using macOS

```bash
# Convert the ISO file to IMG format
hdiutil convert -format UDRW -o archlinux.img archlinux-yyyy.mm.dd-x86_64.iso

# Identify your USB device
diskutil list

# Replace N with your disk number
diskutil unmountDisk /dev/diskN

# Write the image (replace N with your disk number)
sudo dd if=archlinux.img.dmg of=/dev/rdiskN bs=1m
```

### 3. Configure BIOS/UEFI Settings

1. Restart your computer
2. Enter BIOS/UEFI settings:
   - Common keys: F2, F10, F12, Del, or Esc (check manufacturer documentation)
   - On Windows: Hold Shift while clicking Restart → Troubleshoot → Advanced Options → UEFI Firmware Settings

3. Configure these critical settings:
   - **Disable Secure Boot**: Find under "Boot," "Security," or "Authentication" tab
   - **Enable UEFI Boot Mode**: Recommended for modern installations (disable CSM/Legacy if available)
   - **Set Boot Priority**: Move USB drive to the top of the boot order
   - **Disable Fast Boot**: If available (improves compatibility)
   - **Virtualization**: Enable CPU virtualization (VT-x, AMD-V, SVM) if you plan to use virtual machines

4. Save changes and exit (usually F10 → Yes)

---

## 💻 Live Environment Preparation

### 1. Boot From Installation Media

1. Insert the USB drive into your computer
2. Restart or power on your computer
3. If needed, press the boot menu key (typically F12, F11, or Esc) to select the USB drive
4. At the Arch Linux boot menu, select "Arch Linux install medium" and press Enter
5. Wait for the system to boot to the command prompt (should end with `root@archiso ~ #`)

### 2. Verify Boot Mode

Confirm if you're booted in UEFI mode (recommended for modern systems):

```bash
ls /sys/firmware/efi/efivars
```

**Results interpretation:**
- If the command shows files without errors: You're in UEFI mode ✓
- If you get "No such file or directory" error: You're in BIOS/Legacy mode

### 3. Set Console Preferences

#### Keyboard Layout Configuration

The default is US (QWERTY) keyboard layout. To change it:

```bash
# List all available keyboard layouts
localectl list-keymaps

# Filter the list for a specific country (e.g., German)
localectl list-keymaps | grep -i de

# Load your preferred layout
loadkeys de-latin1  # Example: German layout
```

Common keyboard layouts:
- US: `us`
- UK: `uk`
- German: `de-latin1`
- French: `fr`
- Spanish: `es`
- Italian: `it`
- Russian: `ru`

#### Console Font Configuration (Optional)

If the console text is too small:

```bash
# List available fonts
ls /usr/share/kbd/consolefonts/ | grep -E "16|18"

# Set a larger font (example: Terminus 16pt bold)
setfont ter-v16b
```

---

## 📡 Network Configuration

### 1. Critical: Enable Network Services

**ESSENTIAL FIRST STEP** - This is often missed in other guides:

```bash
# Enable the DHCP client daemon - CRITICAL STEP
systemctl enable --now dhcpcd.service
```

### 2. Establish Internet Connection

#### Option A: Wired Connection

Wired connections should work automatically after enabling dhcpcd. Verify with:

```bash
# Check if your interface has an IP address
ip addr show

# If needed, bring up the interface manually (replace enp0s3 with your interface name)
ip link set enp0s3 up

# Request IP via DHCP if needed
dhclient enp0s3
```

#### Option B: Wireless Connection

For WiFi connections:

```bash
# Enable and start the wireless daemon
systemctl enable --now iwd.service

# Launch the interactive wireless tool
iwctl

# Inside the iwctl prompt, enter these commands one by one:
device list                          # Find your wireless device name (e.g., wlan0)
station wlan0 scan                   # Scan for networks (replace wlan0 with your device)
station wlan0 get-networks           # List available networks
station wlan0 connect "NetworkName"  # Connect to your network (will prompt for password)
exit                                 # Exit the tool

# Verify interface status
ip addr show
```

### 3. Verify Internet Connectivity

```bash
# Try to ping a reliable server
ping -c 4 archlinux.org
```

If ping fails, troubleshoot using these steps:

1. Check if DHCP is running:
   ```bash
   systemctl status dhcpcd
   ```

2. Verify that your interface has an IP address:
   ```bash
   ip addr
   ```

3. Test DNS resolution:
   ```bash
   ping -c 4 8.8.8.8  # If this works but archlinux.org doesn't, you have DNS issues
   ```

4. For DNS issues, create a temporary resolv.conf:
   ```bash
   echo "nameserver 8.8.8.8" > /etc/resolv.conf
   ```

### 4. Update System Clock

Synchronize the system clock:

```bash
# Enable time synchronization
timedatectl set-ntp true

# Verify status
timedatectl status  # Should show "System clock synchronized: yes"
```

### 5. Optimize Package Mirrors

Configure the fastest mirrors for better download speeds:

```bash
# Install reflector
pacman -Sy reflector

# Backup the original mirrorlist
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup

# Update mirrorlist with fastest mirrors
# Replace 'US' with your country code or remove for worldwide mirrors
reflector --country US --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist

# Verify the updated mirrorlist
head -n 5 /etc/pacman.d/mirrorlist
```

---

## 💾 Disk Preparation

### 1. Identify Storage Devices

First, identify all storage devices in your system:

```bash
# List all block devices
lsblk

# Get detailed disk information
fdisk -l | grep "Disk /dev/"
```

Carefully note your target disk (e.g., `/dev/sda`, `/dev/nvme0n1`).

> ⚠️ **CRITICAL WARNING**: Make ABSOLUTELY CERTAIN you've identified the correct disk. The next steps will PERMANENTLY DESTROY all existing data on the selected disk!

### 2. Wipe Existing Partitions

This step is **ESSENTIAL** when installing on a disk with existing data:

#### Option A: GUI-Based Partition Removal (Easier for Beginners)

```bash
# Install cfdisk
pacman -S cfdisk

# Start cfdisk with your disk
cfdisk /dev/sdX  # Replace X with your disk identifier
```

In cfdisk:
1. Select each partition one by one
2. Choose "Delete" for each partition
3. Continue until all partitions are deleted
4. Select "Write" to apply changes (type "yes" to confirm)
5. Select "Quit" to exit

#### Option B: Command-Line Partition Removal (For Professionals)

```bash
# Start fdisk for your disk
fdisk /dev/sdX  # Replace X with your disk identifier
```

In fdisk, follow these commands:
```
p           # Print the current partition table
d           # Delete partition
            # If prompted for partition number, enter it
            # Repeat 'd' for each partition until all are deleted
p           # Verify all partitions are deleted
w           # Write changes to disk
```

#### Option C: Complete Disk Wiping (Most Thorough)

To completely erase all partitions and data (recommended for security):

```bash
# Create a new empty GPT partition table (erases EVERYTHING)
sgdisk --zap-all /dev/sdX  # Replace X with your disk identifier

# Verify the disk is empty
lsblk /dev/sdX
```

> 📊 **Visualization: Before & After Disk Wiping**
> ```
> Before:                        After:
> ┌─────────────────────┐       ┌─────────────────────┐
> │ /dev/sdX1 (Windows) │       │                     │
> ├─────────────────────┤       │                     │
> │ /dev/sdX2 (NTFS)    │  →    │      Empty Disk     │
> ├─────────────────────┤       │                     │
> │ /dev/sdX3 (Linux)   │       │                     │
> └─────────────────────┘       └─────────────────────┘
> ```

### 3. Create New Partition Scheme

Now create a clean, professional partition layout:

#### Option A: Using fdisk (Command-Line Interface)

```bash
# Start fdisk
fdisk /dev/sdX  # Replace X with your disk identifier
```

In fdisk, enter these commands sequentially:

```
g           # Create a new empty GPT partition table
n           # New partition (EFI System Partition)
1           # Partition number
<Enter>     # First sector (default)
+512M       # Last sector (512MB size)
t           # Change partition type
1           # Select the partition
1           # EFI System partition type

n           # New partition (Root Partition)
2           # Partition number
<Enter>     # First sector (default)
<Enter>     # Last sector (default - use remaining space)

p           # Print partition table to verify
w           # Write changes to disk
```

#### Option B: Using gdisk (Modern GPT Tool)

```bash
# Start gdisk
gdisk /dev/sdX  # Replace X with your disk identifier
```

In gdisk, enter these commands sequentially:

```
o           # Create a new empty GPT partition table
y           # Confirm deletion

n           # New partition (EFI System Partition)
1           # Partition number
<Enter>     # First sector (default)
+512M       # Last sector (512MB size)
ef00        # EFI System partition type code

n           # New partition (Root Partition)
2           # Partition number
<Enter>     # First sector (default)
<Enter>     # Last sector (default - use remaining space)
8300        # Linux filesystem type code

p           # Print partition table to verify
w           # Write changes to disk
y           # Confirm writing
```

> 📊 **Visualization: Final Partition Layout**
> ```
> ┌─────────────────────────────────────────────────┐
> │ /dev/sdX1: 512MB - EFI System Partition (FAT32) │
> ├─────────────────────────────────────────────────┤
> │                                                 │
> │ /dev/sdX2: Remaining Space - Root (ext4)        │
> │             (Contains entire system)            │
> │                                                 │
> └─────────────────────────────────────────────────┘
> ```

### 4. Format the Partitions

Format each partition with the appropriate filesystem:

```bash
# Format EFI System Partition with FAT32
mkfs.fat -F32 /dev/sdX1

# Format Root Partition with ext4
mkfs.ext4 /dev/sdX2
```

> 📝 **Note for NVMe Drives**: If you're using an NVMe drive, the partition naming convention is different. Use `/dev/nvme0n1p1` and `/dev/nvme0n1p2` instead of `/dev/sdX1` and `/dev/sdX2`.

### 5. Mount the Filesystems

Mount the partitions for installation:

```bash
# Mount the root partition
mount /dev/sdX2 /mnt

# Create directory for EFI partition
mkdir -p /mnt/boot/efi

# Mount the EFI partition
mount /dev/sdX1 /mnt/boot/efi

# Verify mounts
lsblk -f
```

The output should show both partitions mounted at their respective mount points.

---

## 📦 Base System Installation

### 1. Update Package Databases

Ensure package databases are up to date:

```bash
pacman -Sy
```

### 2. Install Essential Packages

Install the base system and essential components:

```bash
# Install base system, kernel, firmware, and development tools
pacstrap /mnt base linux linux-firmware base-devel

# Install text editors (at least one is recommended)
pacstrap /mnt vim nano

# Install man pages and documentation
pacstrap /mnt man-db man-pages texinfo

# Install CPU microcode updates (choose ONE based on your CPU)
pacstrap /mnt intel-ucode   # For Intel CPUs
# OR
pacstrap /mnt amd-ucode     # For AMD CPUs

# Install networking tools
pacstrap /mnt networkmanager dhcpcd iwd
```

> 💼 **Professional Note**: This step will take several minutes (5-20) depending on your internet speed and system performance. This is a good time to review the next steps.

### 3. Generate File System Table (fstab)

Generate the fstab file, which defines how partitions are mounted at boot:

```bash
# Generate fstab using UUID identifiers (recommended)
genfstab -U /mnt >> /mnt/etc/fstab

# Review the generated fstab file for correctness
cat /mnt/etc/fstab
```

The output should show entries for both your root and EFI partitions with their UUIDs, filesystem types, and mount options.

### 4. Enter the New System (chroot)

Change root into the newly installed system:

```bash
arch-chroot /mnt
```

Your command prompt should change, indicating you're now working inside your new Arch Linux installation. All commands from this point forward will affect your installed system, not the live environment.

---

## ⚙️ System Configuration

### 1. Set Time Zone

Configure the system's time zone:

```bash
# List available regions
ls /usr/share/zoneinfo/

# List cities in your region (example: America)
ls /usr/share/zoneinfo/America/

# Set your timezone (example: New York)
ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime

# Generate /etc/adjtime (hardware clock settings)
hwclock --systohc
```

### 2. Configure Localization

Set up language and locale settings:

```bash
# Install necessary packages (if not already installed)
pacman -S glibc

# Edit locale.gen to uncomment needed locales
# For most users, uncomment en_US.UTF-8 UTF-8
sed -i 's/#en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen

# If you need additional locales, uncomment them with sed or a text editor
# Example for German locale:
# sed -i 's/#de_DE.UTF-8 UTF-8/de_DE.UTF-8 UTF-8/' /etc/locale.gen

# Generate the locales
locale-gen

# Set the default system language
echo "LANG=en_US.UTF-8" > /etc/locale.conf

# Set keyboard layout for console (replace "us" if needed)
echo "KEYMAP=us" > /etc/vconsole.conf
```

### 3. Configure Network Settings

Set up basic networking for your new system:

```bash
# Set your hostname (replace "archsys" with your preferred name)
echo "archsys" > /etc/hostname

# Configure hosts file with proper mappings
cat > /etc/hosts << EOF
127.0.0.1   localhost
::1         localhost
127.0.1.1   archsys.localdomain   archsys
EOF
```

### 4. Set Root Password

Set a strong password for the root account:

```bash
passwd
```

You'll be prompted to enter and confirm your password. Use a strong, unique password for security.

### 5. Create Initial RAM Disk

Generate the initial ramdisk environment:

```bash
# Regenerate initramfs
mkinitcpio -P
```

This process will take a moment and will output a message for each kernel image it creates.

---

## 🔄 Bootloader Installation

### 1. Install Bootloader Packages

```bash
# Install GRUB and EFI boot manager
pacman -S grub efibootmgr

# Install additional useful utilities
pacman -S os-prober dosfstools mtools
```

### 2. Install GRUB Bootloader

#### For UEFI Systems (Most Modern Hardware)

```bash
# Install GRUB to the EFI partition
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ARCH
```

#### For Legacy BIOS Systems (Older Hardware)

```bash
# Install GRUB to the MBR
grub-install --target=i386-pc /dev/sdX  # Replace X with your disk identifier, NOT partition number
```

### 3. Configure GRUB

Enable OS detection for dual-boot setups (optional):

```bash
# Enable OS prober to detect other operating systems
echo "GRUB_DISABLE_OS_PROBER=false" >> /etc/default/grub
```

Generate the GRUB configuration file:

```bash
# Generate grub.cfg
grub-mkconfig -o /boot/grub/grub.cfg
```

The command should output several lines indicating that it found the Linux kernel and initramfs images.

---

## 🔧 Final Installation Steps

### 1. Install Essential System Utilities

```bash
# Install basic network tools
pacman -S networkmanager net-tools wireless_tools

# Install filesystem utilities
pacman -S e2fsprogs ntfs-3g

# Install utility programs
pacman -S sudo git wget curl zip unzip p7zip
```

### 2. Enable Critical Services

Enable essential services to start at boot:

```bash
# Enable NetworkManager
systemctl enable NetworkManager

# Enable DHCP client
systemctl enable dhcpcd

# Enable SSH server (optional)
pacman -S openssh
systemctl enable sshd
```

### 3. Create User Account

Create a regular user account with administrative privileges:

```bash
# Create a new user with home directory (-m)
# Add to wheel group for admin privileges (-G wheel)
# Set the default shell (-s, e.g., /bin/bash)
useradd -m -G wheel -s /bin/bash username  # Replace "username" with your desired username

# Set password for the new user
passwd username
```

### 4. Configure sudo Access

Grant sudo privileges to members of the wheel group:

```bash
# Edit the sudoers file
EDITOR=nano visudo
```

Find and uncomment this line by removing the `#`:
```
%wheel ALL=(ALL) ALL
```

Save and exit the editor:
- In nano: Ctrl+O, Enter, Ctrl+X
- In vim: Escape, :wq, Enter

### 5. Final Steps

Exit the chroot environment and reboot into your new system:

```bash
# Exit chroot
exit

# Unmount all partitions
umount -R /mnt

# Reboot
reboot
```

Remove the installation media during reboot. Your system should now boot into the new Arch Linux installation.

Log in with your username and password to continue with desktop environment setup.

---

# PART II: DESKTOP ENVIRONMENT SETUP

## 🖥️ Basic Desktop Setup

After installation and first boot, log in with your user account at the console. Now you can set up a graphical environment.

### 1. Install Graphics Drivers

First, identify your graphics hardware:

```bash
lspci | grep -E "VGA|3D|Display"
```

Based on your hardware, install the appropriate drivers:

#### Intel GPU

```bash
sudo pacman -S xf86-video-intel mesa vulkan-intel intel-media-driver libva-intel-driver
```

#### NVIDIA GPU (Proprietary Drivers)

```bash
sudo pacman -S nvidia nvidia-utils nvidia-settings
```

#### NVIDIA GPU (Open Source)

```bash
sudo pacman -S nouveau mesa
```

#### AMD GPU

```bash
sudo pacman -S xf86-video-amdgpu mesa vulkan-radeon libva-mesa-driver
```

### 2. Install X Window System

The X Window System is required for most desktop environments and window managers:

```bash
# Install X.Org Server and essential packages
sudo pacman -S xorg xorg-server xorg-xinit xorg-apps
```

### 3. Install Display Manager

A display manager provides the graphical login screen:

```bash
# Install LightDM (lightweight display manager)
sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings

# Enable LightDM service
sudo systemctl enable lightdm
```

Alternative display managers:
- SDDM (for KDE): `sudo pacman -S sddm`
- GDM (for GNOME): `sudo pacman -S gdm`

---

## 🖼️ Full Desktop Environments

You can choose ONE of these full desktop environments:

### GNOME (Modern, Feature-rich)

```bash
# Install GNOME
sudo pacman -S gnome gnome-extra gnome-tweaks

# Enable GDM (GNOME Display Manager)
sudo systemctl enable gdm

# Reboot to start GNOME
sudo reboot
```

### KDE Plasma (Modern, Highly Customizable)

```bash
# Install KDE Plasma
sudo pacman -S plasma kde-applications

# Install SDDM display manager
sudo pacman -S sddm

# Enable SDDM
sudo systemctl enable sddm

# Reboot to start KDE
sudo reboot
```

### Xfce (Lightweight, Traditional)

```bash
# Install Xfce
sudo pacman -S xfce4 xfce4-goodies

# Enable LightDM (if not already enabled)
sudo systemctl enable lightdm

# Reboot to start Xfce
sudo reboot
```

### MATE (Traditional, Based on GNOME 2)

```bash
# Install MATE
sudo pacman -S mate mate-extra

# Enable LightDM (if not already enabled)
sudo systemctl enable lightdm

# Reboot to start MATE
sudo reboot
```

### Cinnamon (Modern Features with Traditional Desktop)

```bash
# Install Cinnamon
sudo pacman -S cinnamon nemo-fileroller

# Enable LightDM (if not already enabled)
sudo systemctl enable lightdm

# Reboot to start Cinnamon
sudo reboot
```

---

## 🪟 Window Managers

Window managers provide a more minimal and customizable approach compared to full desktop environments. They require more manual configuration but offer greater control and typically use fewer system resources.

### i3 Window Manager Setup

#### 1. Install i3 and Essential Components

```bash
# Install i3 window manager with gaps and supporting tools
sudo pacman -S i3-gaps i3status i3blocks dmenu picom feh alacritty rofi

# Install additional utilities
sudo pacman -S nitrogen lxappearance thunar xfce4-terminal scrot dunst
```

#### 2. Configure i3

```bash
# Create necessary directories
mkdir -p ~/.config/i3

# Copy the default config file
cp /etc/i3/config ~/.config/i3/
```

Edit the i3 configuration file:

```bash
nano ~/.config/i3/config
```

Add or modify these key sections in the config file:

```
# i3 config file

# Set mod key (Mod1=Alt, Mod4=Super/Windows)
set $mod Mod4

# Font for window titles
font pango:DejaVu Sans Mono 10

# Use Mouse+$mod to drag floating windows
floating_modifier $mod

# Start a terminal
bindsym $mod+Return exec alacritty

# Kill focused window
bindsym $mod+Shift+q kill

# Start dmenu (a program launcher)
bindsym $mod+d exec dmenu_run
# Alternatively, use Rofi
bindsym $mod+space exec rofi -show drun

# Change focus
bindsym $mod+j focus left
bindsym $mod+k focus down
bindsym $mod+l focus up
bindsym $mod+semicolon focus right

# Alternatively, use the cursor keys
bindsym $mod+Left focus left
bindsym $mod+Down focus down
bindsym $mod+Up focus up
bindsym $mod+Right focus right

# Move focused window
bindsym $mod+Shift+j move left
bindsym $mod+Shift+k move down
bindsym $mod+Shift+l move up
bindsym $mod+Shift+semicolon move right

# Alternatively, use the cursor keys
bindsym $mod+Shift+Left move left
bindsym $mod+Shift+Down move down
bindsym $mod+Shift+Up move up
bindsym $mod+Shift+Right move right

# Split in horizontal orientation
bindsym $mod+h split h

# Split in vertical orientation
bindsym $mod+v split v

# Enter fullscreen mode for the focused container
bindsym $mod+f fullscreen toggle

# Change container layout (stacked, tabbed, toggle split)
bindsym $mod+s layout stacking
bindsym $mod+w layout tabbed
bindsym $mod+e layout toggle split

# Toggle tiling / floating
bindsym $mod+Shift+space floating toggle

# Change focus between tiling / floating windows
bindsym $mod+space focus mode_toggle

# Focus the parent container
bindsym $mod+a focus parent

# Define names for default workspaces
set $ws1 "1"
set $ws2 "2"
set $ws3 "3"
set $ws4 "4"
set $ws5 "5"
set $ws6 "6"
set $ws7 "7"
set $ws8 "8"
set $ws9 "9"
set $ws10 "10"

# Switch to workspace
bindsym $mod+1 workspace number $ws1
bindsym $mod+2 workspace number $ws2
bindsym $mod+3 workspace number $ws3
bindsym $mod+4 workspace number $ws4
bindsym $mod+5 workspace number $ws5
bindsym $mod+6 workspace number $ws6
bindsym $mod+7 workspace number $ws7
bindsym $mod+8 workspace number $ws8
bindsym $mod+9 workspace number $ws9
bindsym $mod+0 workspace number $ws10

# Move focused container to workspace
bindsym $mod+Shift+1 move container to workspace number $ws1
bindsym $mod+Shift+2 move container to workspace number $ws2
bindsym $mod+Shift+3 move container to workspace number $ws3
bindsym $mod+Shift+4 move container to workspace number $ws4
bindsym $mod+Shift+5 move container to workspace number $ws5
bindsym $mod+Shift+6 move container to workspace number $ws6
bindsym $mod+Shift+7 move container to workspace number $ws7
bindsym $mod+Shift+8 move container to workspace number $ws8
bindsym $mod+Shift+9 move container to workspace number $ws9
bindsym $mod+Shift+0 move container to workspace number $ws10

# Restart i3 inplace
bindsym $mod+Shift+r restart

# Exit i3
bindsym $mod+Shift+e exec "i3-nagbar -t warning -m 'Exit i3?' -B 'Yes' 'i3-msg exit'"

# Resize window mode
mode "resize" {
        bindsym j resize shrink width 10 px or 10 ppt
        bindsym k resize grow height 10 px or 10 ppt
        bindsym l resize shrink height 10 px or 10 ppt
        bindsym semicolon resize grow width 10 px or 10 ppt

        bindsym Left resize shrink width 10 px or 10 ppt
        bindsym Down resize grow height 10 px or 10 ppt
        bindsym Up resize shrink height 10 px or 10 ppt
        bindsym Right resize grow width 10 px or 10 ppt

        bindsym Return mode "default"
        bindsym Escape mode "default"
        bindsym $mod+r mode "default"
}

bindsym $mod+r mode "resize"

# Gaps configuration
gaps inner 10
gaps outer 5
smart_gaps on

# Window border settings
default_border pixel 2
default_floating_border pixel 2
hide_edge_borders smart

# Autostart applications
exec_always --no-startup-id picom -b
exec_always --no-startup-id nitrogen --restore
exec_always --no-startup-id $HOME/.config/polybar/launch.sh

# Screenshot
bindsym Print exec --no-startup-id scrot 'Screenshot_%Y-%m-%d-%H-%M-%S.png' -e 'mv $f ~/Pictures/'

# Volume controls
bindsym XF86AudioRaiseVolume exec --no-startup-id pactl set-sink-volume @DEFAULT_SINK@ +5%
bindsym XF86AudioLowerVolume exec --no-startup-id pactl set-sink-volume @DEFAULT_SINK@ -5%
bindsym XF86AudioMute exec --no-startup-id pactl set-sink-mute @DEFAULT_SINK@ toggle
```

#### 3. Create i3 Status Bar Configuration

Create and configure the i3status configuration:

```bash
mkdir -p ~/.config/i3status
nano ~/.config/i3status/config
```

Add this content to the file:

```
general {
        colors = true
        interval = 5
        color_good = "#81a1c1"
        color_degraded = "#fafe7c"
        color_bad = "#ff7770"
}

order += "wireless _first_"
order += "ethernet _first_"
order += "battery all"
order += "disk /"
order += "cpu_usage"
order += "memory"
order += "tztime local"

wireless _first_ {
        format_up = "W: (%quality at %essid) %ip"
        format_down = "W: down"
}

ethernet _first_ {
        format_up = "E: %ip"
        format_down = "E: down"
}

battery all {
        format = "%status %percentage %remaining"
        status_chr = "⚡"
        status_bat = "🔋"
        status_full = "☻"
        low_threshold = 15
}

disk "/" {
        format = "Disk: %avail"
}

cpu_usage {
        format = "CPU: %usage"
}

memory {
        format = "MEM: %used / %available"
        threshold_degraded = "1G"
        format_degraded = "MEMORY < %available"
}

tztime local {
        format = "%Y-%m-%d %H:%M:%S"
}
```

#### 4. Set Up Compositor (Picom) for Visual Effects

Create a picom configuration file:

```bash
mkdir -p ~/.config/picom
nano ~/.config/picom/picom.conf
```

Add this content:

```
# Shadow
shadow = true;
shadow-radius = 7;
shadow-offset-x = -7;
shadow-offset-y = -7;
shadow-opacity = 0.7;
shadow-exclude = [
    "name = 'Notification'",
    "class_g = 'Conky'",
    "class_g ?= 'Notify-osd'",
    "class_g = 'Cairo-clock'",
    "_GTK_FRAME_EXTENTS@:c"
];

# Fading
fading = true;
fade-delta = 4;
fade-in-step = 0.03;
fade-out-step = 0.03;

# Transparency / Opacity
inactive-opacity = 0.95;
frame-opacity = 0.9;
inactive-opacity-override = false;
focus-exclude = [ "class_g = 'Cairo-clock'" ];

# Background-Blurring
blur-method = "dual_kawase";
blur-strength = 6;
blur-background = true;
blur-background-frame = true;
blur-kern = "3x3box";
blur-background-exclude = [
    "window_type = 'dock'",
    "window_type = 'desktop'",
    "_GTK_FRAME_EXTENTS@:c"
];

# General Settings
backend = "glx";
vsync = true;
mark-wmwin-focused = true;
mark-ovredir-focused = true;
detect-rounded-corners = true;
detect-client-opacity = true;
use-damage = true;
log-level = "warn";
```

#### 5. Configure Rofi Application Launcher

Create a Rofi configuration:

```bash
mkdir -p ~/.config/rofi
nano ~/.config/rofi/config.rasi
```

Add this content:

```
configuration {
    modi: "window,run,drun";
    width: 50;
    lines: 15;
    columns: 1;
    font: "DejaVu Sans Mono 12";
    bw: 1;
    location: 0;
    padding: 5;
    yoffset: 0;
    xoffset: 0;
    show-icons: true;
    terminal: "alacritty";
    ssh-client: "ssh";
    ssh-command: "{terminal} -e {ssh-client} {host} [-p {port}]";
    run-command: "{cmd}";
    run-list-command: "";
    run-shell-command: "{terminal} -e {cmd}";
    drun-icon-theme: "Papirus";
    window-match-fields: "all";
    window-command: "wmctrl -i -R {window}";
    window-format: "{w}    {c}   {t}";
    combi-modi: "window,run,drun";
    sorting-method: "normal";
    case-sensitive: false;
}

* {
    background-color: #282c34;
    border-color: #61afef;
    text-color: #dfdfdf;
    spacing: 0;
}

inputbar {
    border: 0 0 1px 0;
    children: [prompt,entry];
}

prompt {
    padding: 16px;
    border: 0 1px 0 0;
}

entry {
    padding: 16px;
}

listview {
    cycle: false;
    margin: 0 0 -1px 0;
    scrollbar: false;
}

element {
    border: 0 0 1px 0;
    padding: 16px;
}

element selected {
    background-color: #3e4452;
}
```

#### 6. Create i3 Autostart Script

Create a script to launch applications at startup:

```bash
mkdir -p ~/.config/autostart
nano ~/.config/autostart/i3-autostart.sh
```

Add this content:

```bash
#!/bin/bash

# Start compositor
picom --config ~/.config/picom/picom.conf -b &

# Set wallpaper
nitrogen --restore &

# Start notification daemon
dunst &

# Start network manager applet
nm-applet &

# Start volume control applet
volumeicon &

# Start clipboard manager
clipit &
```

Make the script executable:

```bash
chmod +x ~/.config/autostart/i3-autostart.sh
```

#### 7. Set Up .xinitrc for Starting i3

Create or edit your .xinitrc file:

```bash
nano ~/.xinitrc
```

Add this content:

```bash
#!/bin/sh

# Start i3 window manager
exec i3
```

#### 8. Launch i3

You can now start i3 in two ways:

**Option A**: If using a display manager like LightDM, select "i3" from the session menu at the login screen.

**Option B**: If starting from console, use:

```bash
startx
```

### bspwm Window Manager Setup

bspwm is a tiling window manager that represents windows as the leaves of a binary tree.

#### 1. Install bspwm and Essential Components

```bash
# Install bspwm, sxhkd (key handler), and supporting utilities
sudo pacman -S bspwm sxhkd dmenu picom feh alacritty rofi

# Install additional utilities
sudo pacman -S nitrogen lxappearance thunar xfce4-terminal scrot dunst polybar
```

#### 2. Configure bspwm

Create the configuration directories and copy default configs:

```bash
# Create config directories
mkdir -p ~/.config/{bspwm,sxhkd}

# Copy default config files
cp /usr/share/doc/bspwm/examples/bspwmrc ~/.config/bspwm/
cp /usr/share/doc/bspwm/examples/sxhkdrc ~/.config/sxhkd/

# Make bspwmrc executable
chmod +x ~/.config/bspwm/bspwmrc
```

Edit the bspwm configuration:

```bash
nano ~/.config/bspwm/bspwmrc
```

Replace the content with:

```bash
#!/bin/sh

# Start sxhkd
pgrep -x sxhkd > /dev/null || sxhkd &

# Set wallpaper
nitrogen --restore &

# Start compositor
picom &

# Start polybar
$HOME/.config/polybar/launch.sh &

# Start network manager applet
nm-applet &

# Define workspaces
bspc monitor -d I II III IV V VI VII VIII IX X

# Configure bspwm
bspc config border_width         2
bspc config window_gap          10
bspc config split_ratio          0.52
bspc config borderless_monocle   true
bspc config gapless_monocle      true

# Window rules
bspc rule -a Gimp desktop='^8' state=floating follow=on
bspc rule -a Firefox desktop='^2'
bspc rule -a Thunar desktop='^3' follow=on
bspc rule -a Spotify desktop='^9'
bspc rule -a mpv state=floating
bspc rule -a Kupfer.py focus=on
bspc rule -a Screenkey manage=off

# Color settings
bspc config normal_border_color "#44475a"
bspc config active_border_color "#bd93f9"
bspc config focused_border_color "#ff79c6"
bspc config presel_feedback_color "#6272a4"

# Cursor settings
xsetroot -cursor_name left_ptr
```

#### 3. Configure sxhkd (Simple X Hotkey Daemon)

Edit the key bindings configuration:

```bash
nano ~/.config/sxhkd/sxhkdrc
```

Replace the content with:

```
# Terminal
super + Return
    alacritty

# Program launcher
super + @space
    rofi -show drun

# Alternate program launcher
super + d
    dmenu_run

# Reload sxhkd config
super + Escape
    pkill -USR1 -x sxhkd

# Quit/restart bspwm
super + alt + {q,r}
    bspc {quit,wm -r}

# Close and kill
super + {_,shift + }w
    bspc node -{c,k}

# Alternate between the tiled and monocle layout
super + m
    bspc desktop -l next

# Send the newest marked node to the newest preselected node
super + y
    bspc node newest.marked.local -n newest.!automatic.local

# Swap the current node and the biggest window
super + g
    bspc node -s biggest.window

# State/flags
super + {t,shift + t,s,f}
    bspc node -t {tiled,pseudo_tiled,floating,fullscreen}

# Set the node flags
super + ctrl + {m,x,y,z}
    bspc node -g {marked,locked,sticky,private}

# Focus/swap
super + {_,shift + }{h,j,k,l}
    bspc node -{f,s} {west,south,north,east}

# Focus/swap with arrow keys
super + {_,shift + }{Left,Down,Up,Right}
    bspc node -{f,s} {west,south,north,east}

# Focus the node for the given path jump
super + {p,b,comma,period}
    bspc node -f @{parent,brother,first,second}

# Focus the next/previous window in the current desktop
super + {_,shift + }c
    bspc node -f {next,prev}.local.!hidden.window

# Focus the next/previous desktop in the current monitor
super + bracket{left,right}
    bspc desktop -f {prev,next}.local

# Focus the last node/desktop
super + {grave,Tab}
    bspc {node,desktop} -f last

# Focus the older or newer node in the focus history
super + {o,i}
    bspc wm -h off; \
    bspc node {older,newer} -f; \
    bspc wm -h on

# Focus or send to the given desktop
super + {_,shift + }{1-9,0}
    bspc {desktop -f,node -d} '^{1-9,10}'

# Preselect the direction
super + ctrl + {h,j,k,l}
    bspc node -p {west,south,north,east}

# Preselect the ratio
super + ctrl + {1-9}
    bspc node -o 0.{1-9}

# Cancel the preselection for the focused node
super + ctrl + space
    bspc node -p cancel

# Cancel the preselection for the focused desktop
super + ctrl + shift + space
    bspc query -N -d | xargs -I id -n 1 bspc node id -p cancel

# Expand a window by moving one of its side outward
super + alt + {h,j,k,l}
    bspc node -z {left -20 0,bottom 0 20,top 0 -20,right 20 0}

# Contract a window by moving one of its side inward
super + alt + shift + {h,j,k,l}
    bspc node -z {right -20 0,top 0 20,bottom 0 -20,left 20 0}

# Move a floating window
super + {Left,Down,Up,Right}
    bspc node -v {-20 0,0 20,0 -20,20 0}

# Volume control
XF86AudioRaiseVolume
    pactl set-sink-volume @DEFAULT_SINK@ +5%

XF86AudioLowerVolume
    pactl set-sink-volume @DEFAULT_SINK@ -5%

XF86AudioMute
    pactl set-sink-mute @DEFAULT_SINK@ toggle

# Brightness control
XF86MonBrightnessUp
    xbacklight -inc 10

XF86MonBrightnessDown
    xbacklight -dec 10

# Screenshot
Print
    scrot 'Screenshot_%Y-%m-%d-%H-%M-%S.png' -e 'mv $f ~/Pictures/'
```

#### 4. Configure Polybar

Install Polybar if not already installed:

```bash
sudo pacman -S polybar
```

Create the Polybar configuration:

```bash
mkdir -p ~/.config/polybar
nano ~/.config/polybar/config.ini
```

Add this content:

```ini
[colors]
background = #282a36
background-alt = #44475a
foreground = #f8f8f2
primary = #bd93f9
secondary = #ff79c6
alert = #ff5555
disabled = #6272a4

[bar/main]
width = 100%
height = 27
radius = 0
fixed-center = true

background = ${colors.background}
foreground = ${colors.foreground}

line-size = 3
line-color = ${colors.primary}

border-size = 0
border-color = #00000000

padding-left = 0
padding-right = 1

module-margin = 1

font-0 = "Noto Sans:size=10;2"
font-1 = "Font Awesome 5 Free:style=Regular:size=10;2"
font-2 = "Font Awesome 5 Free:style=Solid:size=10;2"
font-3 = "Font Awesome 5 Brands:size=10;2"

modules-left = bspwm xwindow
modules-center = date
modules-right = pulseaudio memory cpu wlan battery powermenu

cursor-click = pointer
cursor-scroll = ns-resize

enable-ipc = true

[module/bspwm]
type = internal/bspwm
pin-workspaces = true
inline-mode = false
enable-click = true
enable-scroll = true
reverse-scroll = true

ws-icon-0 = I;1
ws-icon-1 = II;2
ws-icon-2 = III;3
ws-icon-3 = IV;4
ws-icon-4 = V;5
ws-icon-5 = VI;6
ws-icon-6 = VII;7
ws-icon-7 = VIII;8
ws-icon-8 = IX;9
ws-icon-9 = X;10

format = <label-state> <label-mode>

label-focused = %name%
label-focused-background = ${colors.background-alt}
label-focused-underline= ${colors.primary}
label-focused-padding = 2

label-occupied = %name%
label-occupied-padding = 2

label-urgent = %name%!
label-urgent-background = ${colors.alert}
label-urgent-padding = 2

label-empty = %name%
label-empty-foreground = ${colors.disabled}
label-empty-padding = 2

[module/xwindow]
type = internal/xwindow
label = %title:0:60:...%

[module/pulseaudio]
type = internal/pulseaudio

format-volume-prefix = "VOL "
format-volume-prefix-foreground = ${colors.primary}
format-volume = <label-volume>

label-volume = %percentage%%

label-muted = muted
label-muted-foreground = ${colors.disabled}

[module/memory]
type = internal/memory
interval = 2
format-prefix = "RAM "
format-prefix-foreground = ${colors.primary}
label = %percentage_used:2%%

[module/cpu]
type = internal/cpu
interval = 2
format-prefix = "CPU "
format-prefix-foreground = ${colors.primary}
label = %percentage:2%%

[network-base]
type = internal/network
interval = 5
format-connected = <label-connected>
format-disconnected = <label-disconnected>
label-disconnected = %{F#707880}disconnected%{F-}

[module/wlan]
inherit = network-base
interface-type = wireless
label-connected = %{F#bd93f9}NET%{F-} %essid%

[module/date]
type = internal/date
interval = 1

date = %H:%M
date-alt = %Y-%m-%d %H:%M:%S

label = %date%
label-foreground = ${colors.primary}

[module/battery]
type = internal/battery
battery = BAT0
adapter = ADP1
full-at = 98

format-charging = <label-charging>
format-charging-prefix = "BAT+ "
format-charging-prefix-foreground = ${colors.primary}

format-discharging = <label-discharging>
format-discharging-prefix = "BAT- "
format-discharging-prefix-foreground = ${colors.primary}

format-full-prefix = "BAT "
format-full-prefix-foreground = ${colors.primary}

[module/powermenu]
type = custom/menu

expand-right = true

format-spacing = 1

label-open = 
label-open-foreground = ${colors.secondary}
label-close = cancel
label-close-foreground = ${colors.secondary}
label-separator = |
label-separator-foreground = ${colors.foreground}

menu-0-0 = reboot
menu-0-0-exec = reboot
menu-0-1 = power off
menu-0-1-exec = poweroff

[settings]
screenchange-reload = true
pseudo-transparency = true
```

Create a launch script for Polybar:

```bash
nano ~/.config/polybar/launch.sh
```

Add this content:

```bash
#!/bin/bash

# Terminate already running bar instances
killall -q polybar

# Wait until the processes have been shut down
while pgrep -u $UID -x polybar >/dev/null; do sleep 1; done

# Launch Polybar
polybar main &

echo "Polybar launched..."
```

Make the script executable:

```bash
chmod +x ~/.config/polybar/launch.sh
```

#### 5. Set Up .xinitrc for Starting bspwm

Create or edit your .xinitrc file:

```bash
nano ~/.xinitrc
```

Add this content:

```bash
#!/bin/sh

# Start bspwm
exec bspwm
```

#### 6. Launch bspwm

You can now start bspwm in two ways:

**Option A**: If using a display manager like LightDM, select "bspwm" from the session menu at the login screen.

**Option B**: If starting from console, use:

```bash
startx
```

### dwm Window Manager Setup

dwm is a minimalist and highly efficient tiling window manager. Unlike other window managers, it requires compilation from source.

#### 1. Install Prerequisites

```bash
# Install development tools and dependencies
sudo pacman -S base-devel libx11 libxinerama libxft freetype2 fontconfig
```

#### 2. Download dwm Source Code

```bash
# Create a directory for source code
mkdir -p ~/builds
cd ~/builds

# Clone dwm repository
git clone https://git.suckless.org/dwm
```

#### 3. Configure dwm

```bash
# Enter the dwm directory
cd dwm
```

Create a custom configuration file:

```bash
cp config.def.h config.h
nano config.h
```

Modify the configuration according to your preferences. Here's an example with some common customizations:

```c
/* appearance */
static const unsigned int borderpx  = 2;        /* border pixel of windows */
static const unsigned int snap      = 32;       /* snap pixel */
static const int showbar            = 1;        /* 0 means no bar */
static const int topbar             = 1;        /* 0 means bottom bar */
static const char *fonts[]          = { "DejaVu Sans Mono:size=10" };
static const char dmenufont[]       = "DejaVu Sans Mono:size=10";
static const char col_gray1[]       = "#222222";
static const char col_gray2[]       = "#444444";
static const char col_gray3[]       = "#bbbbbb";
static const char col_gray4[]       = "#eeeeee";
static const char col_cyan[]        = "#005577";
static const char *colors[][3]      = {
	/*               fg         bg         border   */
	[SchemeNorm] = { col_gray3, col_gray1, col_gray2 },
	[SchemeSel]  = { col_gray4, col_cyan,  col_cyan  },
};

/* tagging */
static const char *tags[] = { "1", "2", "3", "4", "5", "6", "7", "8", "9" };

/* layout(s) */
static const float mfact     = 0.55; /* factor of master area size [0.05..0.95] */
static const int nmaster     = 1;    /* number of clients in master area */
static const int resizehints = 1;    /* 1 means respect size hints in tiled resizals */
static const int lockfullscreen = 1; /* 1 will force focus on the fullscreen window */

static const Layout layouts[] = {
	/* symbol     arrange function */
	{ "[]=",      tile },    /* first entry is default */
	{ "><>",      NULL },    /* no layout function means floating behavior */
	{ "[M]",      monocle },
};

/* key definitions */
#define MODKEY Mod4Mask  /* Super key (Windows key) */
#define TAGKEYS(KEY,TAG) \
	{ MODKEY,                       KEY,      view,           {.ui = 1 << TAG} }, \
	{ MODKEY|ControlMask,           KEY,      toggleview,     {.ui = 1 << TAG} }, \
	{ MODKEY|ShiftMask,             KEY,      tag,            {.ui = 1 << TAG} }, \
	{ MODKEY|ControlMask|ShiftMask, KEY,      toggletag,      {.ui = 1 << TAG} },

/* helper for spawning shell commands in the pre dwm-5.0 fashion */
#define SHCMD(cmd) { .v = (const char*[]){ "/bin/sh", "-c", cmd, NULL } }

/* commands */
static char dmenumon[2] = "0"; /* component of dmenucmd, manipulated in spawn() */
static const char *dmenucmd[] = { "dmenu_run", "-m", dmenumon, "-fn", dmenufont, "-nb", col_gray1, "-nf", col_gray3, "-sb", col_cyan, "-sf", col_gray4, NULL };
static const char *termcmd[]  = { "alacritty", NULL };
static const char *browsercmd[]  = { "firefox", NULL };
static const char *filecmd[]  = { "thunar", NULL };

static const Key keys[] = {
	/* modifier                     key        function        argument */
	{ MODKEY,                       XK_p,      spawn,          {.v = dmenucmd } },
	{ MODKEY,                       XK_Return, spawn,          {.v = termcmd } },
	{ MODKEY,                       XK_b,      togglebar,      {0} },
	{ MODKEY,                       XK_j,      focusstack,     {.i = +1 } },
	{ MODKEY,                       XK_k,      focusstack,     {.i = -1 } },
	{ MODKEY,                       XK_i,      incnmaster,     {.i = +1 } },
	{ MODKEY,                       XK_d,      incnmaster,     {.i = -1 } },
	{ MODKEY,                       XK_h,      setmfact,       {.f = -0.05} },
	{ MODKEY,                       XK_l,      setmfact,       {.f = +0.05} },
	{ MODKEY|ShiftMask,             XK_Return, zoom,           {0} },
	{ MODKEY,                       XK_Tab,    view,           {0} },
	{ MODKEY|ShiftMask,             XK_c,      killclient,     {0} },
	{ MODKEY,                       XK_t,      setlayout,      {.v = &layouts[0]} },
	{ MODKEY,                       XK_f,      setlayout,      {.v = &layouts[1]} },
	{ MODKEY,                       XK_m,      setlayout,      {.v = &layouts[2]} },
	{ MODKEY,                       XK_space,  setlayout,      {0} },
	{ MODKEY|ShiftMask,             XK_space,  togglefloating, {0} },
	{ MODKEY,                       XK_0,      view,           {.ui = ~0 } },
	{ MODKEY|ShiftMask,             XK_0,      tag,            {.ui = ~0 } },
	{ MODKEY,                       XK_comma,  focusmon,       {.i = -1 } },
	{ MODKEY,                       XK_period, focusmon,       {.i = +1 } },
	{ MODKEY|ShiftMask,             XK_comma,  tagmon,         {.i = -1 } },
	{ MODKEY|ShiftMask,             XK_period, tagmon,         {.i = +1 } },
	{ MODKEY,                       XK_w,      spawn,          {.v = browsercmd } },
	{ MODKEY,                       XK_e,      spawn,          {.v = filecmd } },
	TAGKEYS(                        XK_1,                      0)
	TAGKEYS(                        XK_2,                      1)
	TAGKEYS(                        XK_3,                      2)
	TAGKEYS(                        XK_4,                      3)
	TAGKEYS(                        XK_5,                      4)
	TAGKEYS(                        XK_6,                      5)
	TAGKEYS(                        XK_7,                      6)
	TAGKEYS(                        XK_8,                      7)
	TAGKEYS(                        XK_9,                      8)
	{ MODKEY|ShiftMask,             XK_q,      quit,           {0} },
};

/* button definitions */
/* click can be ClkTagBar, ClkLtSymbol, ClkStatusText, ClkWinTitle, ClkClientWin, or ClkRootWin */
static const Button buttons[] = {
	/* click                event mask      button          function        argument */
	{ ClkLtSymbol,          0,              Button1,        setlayout,      {0} },
	{ ClkLtSymbol,          0,              Button3,        setlayout,      {.v = &layouts[2]} },
	{ ClkWinTitle,          0,              Button2,        zoom,           {0} },
	{ ClkStatusText,        0,              Button2,        spawn,          {.v = termcmd } },
	{ ClkClientWin,         MODKEY,         Button1,        movemouse,      {0} },
	{ ClkClientWin,         MODKEY,         Button2,        togglefloating, {0} },
	{ ClkClientWin,         MODKEY,         Button3,        resizemouse,    {0} },
	{ ClkTagBar,            0,              Button1,        view,           {0} },
	{ ClkTagBar,            0,              Button3,        toggleview,     {0} },
	{ ClkTagBar,            MODKEY,         Button1,        tag,            {0} },
	{ ClkTagBar,            MODKEY,         Button3,        toggletag,      {0} },
};
```

#### 4. Compile and Install dwm

```bash
# Compile dwm
make

# Install dwm
sudo make clean install
```

#### 5. Create a Custom Status Bar Script

```bash
nano ~/.local/bin/dwm-status
```

Add this content:

```bash
#!/bin/sh

# Function to get battery status
get_battery() {
    local capacity=$(cat /sys/class/power_supply/BAT0/capacity 2>/dev/null)
    local status=$(cat /sys/class/power_supply/BAT0/status 2>/dev/null)
    
    if [ -n "$capacity" ]; then
        if [ "$status" = "Charging" ]; then
            echo "BAT: ${capacity}%+"
        else
            echo "BAT: ${capacity}%"
        fi
    else
        echo ""
    fi
}

# Function to get CPU usage
get_cpu() {
    top -bn1 | grep "Cpu(s)" | sed "s/.*, *\([0-9.]*\)%* id.*/\1/" | awk '{print "CPU: " 100 - $1 "%"}'
}

# Function to get memory usage
get_memory() {
    free -h | awk '/^Mem/ {print "MEM: " $3 "/" $2}'
}

# Function to get date and time
get_datetime() {
    date +"DATE: %a %d %b %Y | TIME: %H:%M:%S"
}

# Function to get volume
get_volume() {
    volume=$(pactl list sinks | grep '^[[:space:]]Volume:' | head -n 1 | awk '{print $5}')
    mute=$(pactl list sinks | grep '^[[:space:]]Mute:' | head -n 1 | awk '{print $2}')
    
    if [ "$mute" = "yes" ]; then
        echo "VOL: muted"
    else
        echo "VOL: $volume"
    fi
}

# Main loop
while true; do
    xsetroot -name "$(get_volume) | $(get_cpu) | $(get_memory) | $(get_battery) | $(get_datetime)"
    sleep 1
done
```

Make the script executable:

```bash
mkdir -p ~/.local/bin
chmod +x ~/.local/bin/dwm-status
```

#### 6. Create Autostart Script

```bash
nano ~/.dwm/autostart.sh
```

Add this content:

```bash
#!/bin/sh

# Start the status bar
~/.local/bin/dwm-status &

# Set wallpaper
nitrogen --restore &

# Start compositor
picom &

# Start network manager applet
nm-applet &

# Start notification daemon
dunst &

# Start volume control
volumeicon &

# Start Redshift for eye comfort
redshift &
```

Make the script executable:

```bash
mkdir -p ~/.dwm
chmod +x ~/.dwm/autostart.sh
```

#### 7. Set Up .xinitrc for Starting dwm

Create or edit your .xinitrc file:

```bash
nano ~/.xinitrc
```

Add this content:

```bash
#!/bin/sh

# Load resources
xrdb -merge ~/.Xresources

# Start autostart script
~/.dwm/autostart.sh &

# Start dwm
exec dwm
```

#### 8. Launch dwm

You can now start dwm in two ways:

**Option A**: Create a desktop entry for display managers:

```bash
sudo nano /usr/share/xsessions/dwm.desktop
```

Add this content:

```
[Desktop Entry]
Name=dwm
Comment=Dynamic window manager
Exec=dwm
Type=Application
```

Then select "dwm" from the session menu at the login screen.

**Option B**: If starting from console, use:

```bash
startx
```

---

## 🎨 Graphical Environment Essentials

Regardless of your chosen desktop environment or window manager, these components are useful to configure.

### 1. Audio Configuration

```bash
# Install PulseAudio
sudo pacman -S pulseaudio pulseaudio-alsa pavucontrol alsa-utils

# Start and enable PulseAudio
systemctl --user enable pulseaudio
systemctl --user start pulseaudio
```

### 2. Network Configuration

Ensure NetworkManager is installed and running:

```bash
# Install NetworkManager GUI tools
sudo pacman -S network-manager-applet

# Enable and start NetworkManager
sudo systemctl enable NetworkManager
sudo systemctl start NetworkManager
```

### 3. Display Manager Configuration

If you want a graphical login screen:

```bash
# Install LightDM
sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings

# Enable LightDM
sudo systemctl enable lightdm
```

To customize LightDM appearance:

```bash
sudo nano /etc/lightdm/lightdm-gtk-greeter.conf
```

Add these configurations:

```
[greeter]
theme-name = Adwaita-dark
icon-theme-name = Papirus-Dark
font-name = DejaVu Sans 11
background = /usr/share/backgrounds/archlinux/arch-logo.png
clock-format = %a, %b %d %H:%M
indicators = ~host;~spacer;~clock;~spacer;~power
```

### 4. Font Configuration

```bash
# Install popular fonts
sudo pacman -S ttf-dejavu ttf-liberation noto-fonts ttf-roboto ttf-font-awesome ttf-jetbrains-mono

# Install Microsoft fonts (requires AUR helper)
yay -S ttf-ms-fonts
```

Configure font rendering:

```bash
nano ~/.config/fontconfig/fonts.conf
```

Add this content:

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <match target="font">
    <edit name="antialias" mode="assign">
      <bool>true</bool>
    </edit>
    <edit name="hinting" mode="assign">
      <bool>true</bool>
    </edit>
    <edit name="hintstyle" mode="assign">
      <const>hintslight</const>
    </edit>
    <edit name="rgba" mode="assign">
      <const>rgb</const>
    </edit>
    <edit name="lcdfilter" mode="assign">
      <const>lcddefault</const>
    </edit>
  </match>
</fontconfig>
```

---

# PART III: SYSTEM CUSTOMIZATION

## 🔧 System Optimization

### 1. SSD Optimizations

If you're using an SSD (recommended), configure periodic TRIM:

```bash
# Check if TRIM is supported
sudo lsblk --discard

# Enable weekly TRIM
sudo systemctl enable fstrim.timer
```

### 2. CPU Performance Optimization

```bash
# Install CPU frequency scaling tools
sudo pacman -S cpupower

# Set CPU governor to performance (for max performance) or ondemand (for power saving)
sudo cpupower frequency-set -g performance

# Enable cpupower service
sudo systemctl enable cpupower
```

### 3. Memory Management

For systems with sufficient RAM (8GB+), you can optimize swappiness:

```bash
# Create sysctl configuration
sudo nano /etc/sysctl.d/99-swappiness.conf
```

Add this line:
```
vm.swappiness=10
```

This setting reduces swap usage for better performance.

### 4. Disk I/O Scheduler

For SSDs, optimize the I/O scheduler:

```bash
# Create udev rule for I/O schedulers
sudo nano /etc/udev/rules.d/60-ioscheduler.rules
```

Add these lines:
```
# Set scheduler for NVMe drives
ACTION=="add|change", KERNEL=="nvme[0-9]*", ATTR{queue/scheduler}="none"
# Set scheduler for SSDs
ACTION=="add|change", KERNEL=="sd[a-z]|mmcblk[0-9]*", ATTR{queue/rotational}=="0", ATTR{queue/scheduler}="mq-deadline"
# Set scheduler for HDDs
ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="1", ATTR{queue/scheduler}="bfq"
```

---

## 📦 Essential Software

### 1. Web Browsers

```bash
# Firefox
sudo pacman -S firefox

# Chromium (open-source version of Chrome)
sudo pacman -S chromium
```

### 2. Office Applications

```bash
# LibreOffice (full suite)
sudo pacman -S libreoffice-fresh

# Document viewers
sudo pacman -S evince okular
```

### 3. Media Software

```bash
# Media players
sudo pacman -S vlc mpv

# Audio tools
sudo pacman -S audacity rhythmbox

# Image editing
sudo pacman -S gimp inkscape
```

### 4. Development Tools

```bash
# Code editors
sudo pacman -S visual-studio-code-bin code
sudo pacman -S vim nano emacs

# Version control
sudo pacman -S git github-cli

# Build tools
sudo pacman -S cmake ninja meson
```

### 5. System Utilities

```bash
# Archiving tools
sudo pacman -S zip unzip p7zip unrar

# System monitoring
sudo pacman -S htop bashtop neofetch

# Disk utilities
sudo pacman -S gparted ncdu
```

---

## 🔄 AUR Helper Setup

The Arch User Repository (AUR) contains community-maintained packages. To access them, install an AUR helper:

### Install yay (Yet Another Yogurt)

```bash
# Install build dependencies
sudo pacman -S git base-devel

# Clone the yay repository
git clone https://aur.archlinux.org/yay.git

# Build and install yay
cd yay
makepkg -si

# Clean up
cd ..
rm -rf yay
```

### Install Popular AUR Packages

```bash
# Google Chrome
yay -S google-chrome

# Visual Studio Code (Microsoft's build)
yay -S visual-studio-code-bin

# Spotify
yay -S spotify

# Microsoft fonts
yay -S ttf-ms-fonts

# System backup tool
yay -S timeshift
```

---

## 🎨 Advanced Customization

### 1. Shell Customization

#### Install and configure Zsh (recommended alternative to Bash)

```bash
# Install Zsh
sudo pacman -S zsh

# Install Oh My Zsh framework
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Change default shell to Zsh
chsh -s /bin/zsh
```

#### Configure Bash (if preferred)

```bash
# Install Bash completion
sudo pacman -S bash-completion

# Edit Bash configuration
nano ~/.bashrc
```

Add these lines for a better Bash experience:

```bash
# Enable color support
alias ls='ls --color=auto'
alias grep='grep --color=auto'
alias diff='diff --color=auto'

# Better ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

# Command history improvements
HISTSIZE=10000
HISTFILESIZE=20000
HISTCONTROL=ignoreboth
shopt -s histappend

# Check window size after commands
shopt -s checkwinsize

# Prompt customization
PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
```

### 2. GTK Theme Configuration

```bash
# Install themes and icons
sudo pacman -S arc-gtk-theme arc-icon-theme papirus-icon-theme

# Install theme configuration tool
sudo pacman -S lxappearance

# Create GTK configuration
nano ~/.config/gtk-3.0/settings.ini
```

Add this content:

```ini
[Settings]
gtk-theme-name=Arc-Dark
gtk-icon-theme-name=Papirus-Dark
gtk-font-name=DejaVu Sans 11
gtk-cursor-theme-name=Adwaita
gtk-cursor-theme-size=0
gtk-toolbar-style=GTK_TOOLBAR_BOTH_HORIZ
gtk-toolbar-icon-size=GTK_ICON_SIZE_LARGE_TOOLBAR
gtk-button-images=1
gtk-menu-images=1
gtk-enable-event-sounds=1
gtk-enable-input-feedback-sounds=1
gtk-xft-antialias=1
gtk-xft-hinting=1
gtk-xft-hintstyle=hintslight
gtk-xft-rgba=rgb
```

### 3. Cursor Theme Configuration

```bash
# Install cursor themes
sudo pacman -S xcursor-themes

# Configure cursor theme
nano ~/.Xresources
```

Add this content:

```
Xcursor.theme: Adwaita
Xcursor.size: 24
```

### 4. Terminal Customization

#### Alacritty (modern terminal emulator)

```bash
# Install Alacritty
sudo pacman -S alacritty

# Configure Alacritty
mkdir -p ~/.config/alacritty
nano ~/.config/alacritty/alacritty.yml
```

Add this configuration:

```yaml
window:
  padding:
    x: 10
    y: 10
  dynamic_padding: true
  decorations: full
  opacity: 0.95

font:
  normal:
    family: JetBrains Mono
    style: Regular
  bold:
    family: JetBrains Mono
    style: Bold
  italic:
    family: JetBrains Mono
    style: Italic
  size: 11.0
  offset:
    x: 0
    y: 0
  glyph_offset:
    x: 0
    y: 0

colors:
  primary:
    background: '#282c34'
    foreground: '#abb2bf'
  normal:
    black:   '#282c34'
    red:     '#e06c75'
    green:   '#98c379'
    yellow:  '#e5c07b'
    blue:    '#61afef'
    magenta: '#c678dd'
    cyan:    '#56b6c2'
    white:   '#abb2bf'
  bright:
    black:   '#5c6370'
    red:     '#e06c75'
    green:   '#98c379'
    yellow:  '#e5c07b'
    blue:    '#61afef'
    magenta: '#c678dd'
    cyan:    '#56b6c2'
    white:   '#ffffff'

cursor:
  style: Beam
  unfocused_hollow: true

live_config_reload: true

key_bindings:
  - { key: V,        mods: Control|Shift, action: Paste            }
  - { key: C,        mods: Control|Shift, action: Copy             }
  - { key: Key0,     mods: Control,       action: ResetFontSize    }
  - { key: Plus,     mods: Control,       action: IncreaseFontSize }
  - { key: Minus,    mods: Control,       action: DecreaseFontSize }
```

---

# PART IV: REFERENCE & TROUBLESHOOTING

## 📄 Post-Installation Reference

### System Management

```bash
# Update the entire system
sudo pacman -Syu

# Search for packages
pacman -Ss package_name

# Install a package
sudo pacman -S package_name

# Remove a package
sudo pacman -R package_name

# Remove a package and its unused dependencies
sudo pacman -Rs package_name

# List installed packages
pacman -Q

# Query package information
pacman -Qi package_name
```

### User Management

```bash
# Add a new user
sudo useradd -m -G wheel -s /bin/bash username

# Set user password
sudo passwd username

# Change user groups
sudo usermod -aG group username

# Delete a user
sudo userdel -r username
```

### Service Management

```bash
# Start a service
sudo systemctl start service_name

# Stop a service
sudo systemctl stop service_name

# Enable a service to start at boot
sudo systemctl enable service_name

# Disable a service from starting at boot
sudo systemctl disable service_name

# Check service status
sudo systemctl status service_name

# List failed services
systemctl --failed
```

### Network Management

```bash
# Connect to a WiFi network
nmcli device wifi list
nmcli device wifi connect "SSID" password "password"

# Show network interfaces
ip link show

# Show IP address
ip addr show

# Test connectivity
ping archlinux.org
```

---

## 🛠️ Comprehensive Troubleshooting

### Boot Issues

#### System Won't Boot After Installation

1. Boot from Arch installation media
2. Mount your partitions:
   ```bash
   mount /dev/sdX2 /mnt  # Root partition
   mount /dev/sdX1 /mnt/boot/efi  # EFI partition
   ```
3. Chroot into the system:
   ```bash
   arch-chroot /mnt
   ```
4. Reinstall and reconfigure the bootloader:
   ```bash
   grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ARCH
   grub-mkconfig -o /boot/grub/grub.cfg
   ```

#### GRUB Rescue Prompt

If you're stuck at a GRUB rescue prompt:

```
grub rescue> set prefix=(hd0,gpt2)/boot/grub
grub rescue> set root=(hd0,gpt2)
grub rescue> insmod normal
grub rescue> normal
```

Adjust partition numbers as needed (e.g., gpt2 corresponds to the second partition).

#### "Unknown filesystem" Error

```bash
# Rebuild initramfs
mkinitcpio -P
```

### Network Issues

#### No Internet After Installation

```bash
# Check if NetworkManager is running
sudo systemctl status NetworkManager

# Start NetworkManager if not running
sudo systemctl start NetworkManager
sudo systemctl enable NetworkManager

# Connect to WiFi
nmcli device wifi list
nmcli device wifi connect "SSID" password "password"
```

#### WiFi Not Showing in NetworkManager

```bash
# Install additional firmware
sudo pacman -S linux-firmware

# Enable and start iwd
sudo systemctl enable --now iwd

# Configure NetworkManager to use iwd backend
sudo mkdir -p /etc/NetworkManager/conf.d/
echo '[device]' | sudo tee /etc/NetworkManager/conf.d/wifi_backend.conf
echo 'wifi.backend=iwd' | sudo tee -a /etc/NetworkManager/conf.d/wifi_backend.conf

# Restart NetworkManager
sudo systemctl restart NetworkManager
```

### Graphics Problems

#### Black Screen After Boot

1. Boot in recovery mode or add `nomodeset` to kernel parameters at GRUB menu
2. Install proper graphics drivers:
   ```bash
   # For Intel
   sudo pacman -S xf86-video-intel mesa
   
   # For NVIDIA
   sudo pacman -S nvidia nvidia-utils
   
   # For AMD
   sudo pacman -S xf86-video-amdgpu mesa
   ```

#### Screen Tearing

For Intel GPUs:
```bash
sudo mkdir -p /etc/X11/xorg.conf.d/
sudo nano /etc/X11/xorg.conf.d/20-intel.conf
```

Add this content:
```
Section "Device"
    Identifier "Intel Graphics"
    Driver "intel"
    Option "TearFree" "true"
EndSection
```

For NVIDIA GPUs:
```bash
sudo nano /etc/X11/xorg.conf.d/20-nvidia.conf
```

Add this content:
```
Section "Device"
    Identifier "NVIDIA Card"
    Driver "nvidia"
    Option "NoLogo" "true"
    Option "TripleBuffer" "true"
    Option "UseEvents" "false"
EndSection
```

### Other Common Issues

#### Pacman Database Locks

If Pacman refuses to run due to database locks:
```bash
sudo rm /var/lib/pacman/db.lck
```

#### Pacman GPG Key Issues

```bash
# Refresh the keyring
sudo pacman -Sy archlinux-keyring

# Initialize the pacman keyring
sudo pacman-key --init
sudo pacman-key --populate archlinux
```

#### Fixing Broken Packages

```bash
# Force reinstall a package
sudo pacman -S --force package_name

# Check for corrupted packages
sudo pacman -Qk
```

---

## 🔒 Security Hardening

### 1. Firewall Configuration

```bash
# Install firewall
sudo pacman -S ufw

# Set default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH (if needed)
sudo ufw allow ssh

# Allow HTTP/HTTPS (if needed)
sudo ufw allow http
sudo ufw allow https

# Enable the firewall
sudo ufw enable
sudo systemctl enable ufw
```

### 2. Secure Boot Setup

For systems supporting Secure Boot:

```bash
# Install Secure Boot tools
sudo pacman -S sbsigntools efitools

# Create your own keys (follow the interactive prompts)
sudo sbkey-create
```

### 3. Access Control

```bash
# Password quality checking
sudo pacman -S libpwquality

# Edit PAM configuration
sudo nano /etc/pam.d/passwd
```

Add this line:
```
password required pam_pwquality.so retry=3 minlen=10 difok=3 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1
```

---

## 🔄 Maintenance Tasks

### 1. System Backups

#### Using Timeshift

```bash
# Install Timeshift (via AUR)
yay -S timeshift

# Create a backup (via GUI)
sudo timeshift-gtk

# Or via command line
sudo timeshift --create --comments "Initial backup" --tags D
```

#### Using rsync for Home Directory

```bash
# Backup home directory
rsync -avz --progress /home/username /path/to/backup/location
```

### 2. Cleaning Package Cache

```bash
# Remove all cached packages except the latest three versions
sudo paccache -r

# Remove all cached versions of uninstalled packages
sudo pacman -Sc

# Remove ALL cached packages (use with caution)
sudo pacman -Scc
```

### 3. Removing Orphaned Packages

```bash
# Find and remove orphaned packages
sudo pacman -Rns $(pacman -Qtdq)
```

### 4. Checking for Errors

```bash
# Check for systemd errors
sudo systemctl --failed

# View recent logs
journalctl -b -p err
```

---

<div align="center">
  <p><strong>Congratulations on your professional Arch Linux installation!</strong></p>
  <p>You now have a fully customized, optimized, and powerful system built exactly to your specifications.</p>
  <p><em>Remember: Arch Linux is a rolling release distribution - stay up to date with <code>sudo pacman -Syu</code>.</em></p>
  <p>This guide is maintained by the Arch Linux community. For the latest updates, visit the <a href="https://wiki.archlinux.org/">Arch Wiki</a>.</p>
</div>
