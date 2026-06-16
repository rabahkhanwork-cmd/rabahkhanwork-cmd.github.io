# Problem Statement

## Clonezilla UEFI Boot Failure on Lenovo B51-30

### Overview
Attempting to boot Clonezilla from a USB drive on a UEFI-only laptop fails completely. The USB is not recognized as a bootable device, and the system falls back to the internal disk's GRUB menu (Windows/Ubuntu dual-boot).

### Primary Issues

1. **USB Drive Not Detected in Boot Menu**
   - Boot Option Menu shows only internal disk entries
   - No USB option appears despite drive being plugged in
   - System boots straight to Windows/Ubuntu GRUB

2. **BIOS Settings Cannot Be Saved**
   - Changes made in BIOS (disable Secure Boot, enable USB boot) revert immediately
   - "Save and Exit" loops back to BIOS setup screen
   - All changes lost — Secure Boot re-enables, USB boot disables

3. **GRUB EFI Variable Error**
   - Error: `could not set EFI variable 'OsIndications'`
   - Occurs when attempting to enter BIOS firmware settings from GRUB
   - Indicates NVRAM/EFI write protection

4. **BIOS Flash Failure**
   - BIOS update utility reaches 50% then fails
   - Error: `Invalid firmware image` (InsydeH2O Secure Flash)
   - Flash aborts, system reboots back to Windows

5. **Clonezilla Kernel Load Failure**
   - When USB is eventually detected (using wrong image format)
   - Error: `invalid magic number`
   - Error: `you have to load the kernel first`
   - GRUB drops to command line

### Environment
- **Laptop:** Lenovo B51-30
- **BIOS:** InsydeH2O (UEFI firmware)
- **Internal Disk:** Seagate ST500LT012 (500GB)
- **OS:** Windows 10 + Ubuntu dual-boot
- **USB Drive:** 16GB (used successfully for Windows/Ubuntu installs previously)
- **Clonezilla Version:** 3.3.2-31-amd64

### Attempted Methods That Failed
- Rufus with ISO image in ISO mode
- Rufus with ISO image in DD mode
- Changing BIOS boot mode to Legacy/CSM
- Multiple USB ports (USB 2.0 and 3.0)
- BIOS update via Windows utility (failed at 50%)
- Removing CMOS battery (did not help)
