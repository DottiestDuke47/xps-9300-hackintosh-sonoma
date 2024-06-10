
# xps-9300-hackintosh-sonoma

Running macOS Sonoma on a Dell XPS 9300 for educational purposes.
This guide is provided as-is with no support. Proceed at your own risk.

## What works:
- Mostly everything, with the exception of Fingerprint and the headphone jack (Will be fixed in the future maybe using ComboJack, I just haven't gotten around to it yet.)

# Steps to install:

## Configure UEFI
### Set options as follows:

**Enable :**
 - SATA Operation : AHCI 
 - Fastboot : Thorough 
 - Integrated NIC : Enable

**Disable :**
 - Secure Boot
 - Intel SGX
 - SMM Security Migration
 - Wake on AC
 - Wake on Dell USB-C Dock
 - Power On Lid Open
 - Enable UEFI Network Stack
 - Sign Of Life : Early Logo Display / Early keyboard backlight 
 - cfg lock and DVMT: DO
   AT YOUR OWN RISK!!! It may brick your laptop. But this is the most
   important step for power management. Without it, the laptop might
   idle at 70-100 degrees celsius.

## Create Bootable USB

- Follow instructions at https://dortania.github.io/OpenCore-Install-Guide/installer-guide/
- Modify config.plist with generated system ROM, UUID and Serial
- When booting the USB, press space when you arrive at opencore boot menu to show the installer dmg
  

## Disable CFG Lock.
### This step varies on BIOS version, but these are the steps I followed:
This is requried to make performance usable on the XPS. Without it the fan was constantly on and the laptop perpetually ran hot.
 

**THIS STEP COULD BRICK YOUR LAPTOP. PLEASE EXCERSISE CAUTION WHEN MESSING AROUND WITH YOUR UEFI!!!**

- Ideally, learn what you are doing and follow the guide in the CFG Lock folder.

or

These steps were used for BIOS Version 1.23.0. If you are not on this BIOS version, please follow the guide instead!

- Copy setup_var.efi to the root of your bootable USB
- on the OpenCore Menu, press space and choose opencore shell
- Run the following command to disable CFG Lock:
`setup_var.efi 0x43 0x0 -n CpuSetup -r`
- Verify CFG Lock is off by pressing space at the opencore menu and running ControlMsrE2.efi

## Post-Install:

- Follow Steps on https://dortania.github.io/OpenCore-Post-Install/universal/security/applesecureboot.html

Namely:
- Set SecurebootModel to j230k
- Generate a unique ApECID
- Boot into recovery (press space on Opencore boot screen) and run the following command:
`bless --folder "/Volumes/Macintosh HD/System/Library/CoreServices" --bootefi --personalize`