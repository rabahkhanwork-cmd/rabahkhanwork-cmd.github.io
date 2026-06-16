# Symptoms & Diagnostic Signs

## How to Identify Each Issue

---

## Symptom 1: USB Invisible in Boot Menu

### What You See
```
Boot Option Menu
├── Ubuntu (ST500LT012-1DG142)
├── Windows Boot Manager (ST500LT012-1DG142)
└── ubuntu (ST500LT012-1DG142)

[USB drive is NOT listed]
```

### What It Means
The firmware does not recognize the USB as a valid bootable device. This is NOT a hardware failure of the USB port or drive.

### Diagnostic Check
1. Does the USB LED light up when laptop powers on? → Yes = hardware OK
2. Does the USB work in Windows? → Yes = drive is functional
3. Was the USB created with Rufus ISO mode? → Yes = wrong format for UEFI

---

## Symptom 2: BIOS Settings Revert Loop

### What You See
1. Enter BIOS (F2)
2. Change setting (e.g., disable Secure Boot)
3. Save & Exit (F10)
4. System reboots
5. **Immediately returns to BIOS setup screen**
6. Changed setting is back to original value

### What It Means
The BIOS is in a locked security state. Any modification triggers a validation failure, causing the firmware to discard ALL changes and revert to factory defaults.

### Key Indicator
You can ENTER the password to unlock settings (they become editable), but you cannot SAVE because the security layer rejects the write.

---

## Symptom 3: GRUB OsIndications Error

### What You See
```
error: could not set EFI variable 'OsIndications'.
Press any key to continue...
```

### What It Means
GRUB attempted to write an EFI variable to tell the firmware "enter BIOS setup on next boot." The write failed because the NVRAM/EFI storage is locked or corrupted by security settings.

### When It Happens
- Selecting "UEFI firmware setup" from GRUB menu
- Or pressing F2 from GRUB to enter BIOS

---

## Symptom 4: BIOS Flash Invalid Image

### What You See
```
Insyde H2OFFT (Flash Firmware Tool)
Loading New BIOS Image File: Done
Loading EC Image File: Done
Current BIOS Version: C5CN32WW(v2.03)
New BIOS Version: C5CN35WW(v2.06)

[Progress bar reaches 50%]

Error: Invalid firmware image!!!
Please press any key to reset system...
```

### What It Means
The BIOS file is correct (model matches, version is newer), but the security subsystem blocked the EC (Embedded Controller) firmware update. The flash cannot complete because the locked state prevents writes to the firmware storage.

---

## Symptom 5: Kernel Load Failure

### What You See
```
Clonezilla live (VGA 800x600)
Clonezilla live (To RAM)
...

[After selecting first option]

error: invalid magic number
error: you have to load the kernel first

grub> _
```

### What It Means
GRUB found the menu configuration but cannot read the Linux kernel file (`vmlinuz`) from the USB. This happens when:
- The USB was created with ISO format (wrong for UEFI)
- The kernel file path in GRUB config points to wrong location
- Filesystem driver missing for the USB format

---

## Symptom 6: Windows/Ubuntu Menu Instead of Clonezilla

### What You See
When booting with USB plugged in, you see:
```
GNU GRUB
Ubuntu
Windows Boot Manager
```

Instead of:
```
Clonezilla live
Clonezilla live (To RAM)
Local boot
Memtest86+
```

### What It Means
The laptop is booting from the internal disk, not the USB. The USB is either:
- Not detected as bootable (wrong format)
- Skipped by boot priority (Fast Boot enabled)
- Blocked by Secure Boot (unsigned bootloader)
