# Solutions & Fixes

## Complete Resolution Path

---

## Solution 1: Remove BIOS Password Locks (CRITICAL)

### Problem
Multiple BIOS security layers (Admin, User, Master, HDD passwords) prevent ANY firmware modification. This causes settings to revert, flash failures, and EFI variable errors.

### Fix
1. Enter BIOS (F2 at power-on)
2. Navigate to **Security** tab
3. Remove ALL passwords one by one:
   ```
   Administrator Password → [Enter current] → [Leave blank] → [Enter]
   User Password → [Enter current] → [Leave blank] → [Enter]
   Master Password → [Enter current] → [Leave blank] → [Enter]
   HDD Password → [Enter current] → [Leave blank] → [Enter]
   ```
4. Save & Exit (F10)
5. Re-enter BIOS to verify settings now stick

### Result
- BIOS settings save permanently
- USB boot options appear in boot menu
- GRUB can write EFI variables
- BIOS flash utility can write firmware

---

## Solution 2: Use ZIP Image + FAT32 for UEFI

### Problem
ISO images are designed for Legacy/BIOS boot. UEFI firmware cannot locate the bootloader in ISO-created directory structures.

### Fix
1. Download Clonezilla **ZIP** image (not ISO)
2. Format USB as **FAT32**
3. Extract ZIP contents to **root** of USB (no parent folder)

### Directory Structure After Fix
```
USB Root
├── EFI/BOOT/BOOTX64.EFI
├── live/vmlinuz
├── live/initrd.img
├── syslinux/
└── ...
```

### Result
- USB appears in UEFI boot menu
- Clonezilla GRUB menu loads correctly
- Kernel and initrd load without errors

---

## Solution 3: Manual Kernel Load (Emergency)

### Problem
GRUB shows `invalid magic number` or `you have to load the kernel first`.

### Fix: GRUB Command Line Method
1. At Clonezilla GRUB menu, press **C**
2. Find USB device:
   ```
   grub> ls
   grub> ls (hd0,msdos1)/
   ```
3. Set root to USB:
   ```
   grub> set root=(hd0,msdos1)
   ```
4. Load kernel:
   ```
   grub> linux /live/vmlinuz boot=live union=overlay username=user config components quiet
   ```
5. Load ramdisk:
   ```
   grub> initrd /live/initrd.img
   ```
6. Boot:
   ```
   grub> boot
   ```

### Alternative: Edit Menu Entry
1. Highlight first menu item
2. Press **E**
3. Check the `linux` line for wrong paths
4. Fix or remove device prefix before `/live/vmlinuz`
5. Press **Ctrl+X** to boot

### Result
Clonezilla starts even when automatic menu fails.

---

## Solution 4: BIOS Flash via Windows (After Unlocking)

### Problem
BIOS update fails at 50% with `Invalid firmware image` due to security locks.

### Fix (After Removing Passwords)
1. Boot into Windows
2. Download correct BIOS from Lenovo support
3. Run `.exe` as Administrator
4. Click **Install** → **Flash BIOS**
5. Press **Enter** to start
6. Do NOT power off during flash
7. System reboots automatically when done

### Result
- BIOS firmware updated to latest version
- NVRAM/EFI storage repaired
- SPD memory compatibility fixed
- All settings now save properly

---

## Solution 5: Backup Ubuntu Using fsarchiver

### Problem
Need to backup Ubuntu partition (~150GB used) to external disk (267GB available partition). `dd` creates raw image that won't fit and can't restore to smaller disks.

### Fix: File-Based Backup
```bash
# Mount destination
sudo mkdir -p /mnt/target
sudo mount /dev/sdc6 /mnt/target
sudo mkdir -p /mnt/target/backup

# Save partition table (for reference)
sudo sfdisk -d /dev/sda > /mnt/target/backup/partition-table.txt

# Backup small EFI partitions (fast)
sudo dd if=/dev/sda1 of=/mnt/target/backup/sda1-efi.img bs=4M status=progress
sudo dd if=/dev/sda4 of=/mnt/target/backup/sda4-efi.img bs=4M status=progress

# Backup Ubuntu (only used data, compressed)
sudo fsarchiver savefs /mnt/target/backup/ubuntu.fsa /dev/sda5

# Verify and unmount
sudo ls -lh /mnt/target/backup/
sudo umount /mnt/target
```

### Result
- `ubuntu.fsa`: ~80GB compressed (from 150GB used)
- Fits in 267GB destination partition ✅
- Can restore to ANY size disk (128GB, 256GB, 512GB+)
- EFI partitions preserved for bootability

---

## Solution 6: Restore to New Disk

### Fix
```bash
# Create partitions on new disk (can be smaller!)
# Then restore Ubuntu:
sudo fsarchiver restfs /path/to/ubuntu.fsa id=0,dest=/dev/sdX5

# Restore boot partitions:
sudo dd if=sda1-efi.img of=/dev/sdX1 bs=4M status=progress
sudo dd if=sda4-efi.img of=/dev/sdX4 bs=4M status=progress
```

### Result
Fully bootable Ubuntu system on new disk, regardless of size.
