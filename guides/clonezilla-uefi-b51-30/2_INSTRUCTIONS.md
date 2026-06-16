# Step-by-Step Instructions

## How to Create a Working Clonezilla USB for UEFI

---

## Step 1: Download the Correct File

### What You Need
- Clonezilla **ZIP** image (NOT ISO)

### Where to Get It
1. Go to: https://clonezilla.org/downloads.php
2. Select your architecture (usually amd64)
3. Choose **"zip"** format
4. Download the file (e.g., `clonezilla-live-3.3.2-31-amd64.zip`)

### Why ZIP Not ISO?
| Format | Boot Type | UEFI Compatible |
|--------|-----------|-----------------|
| ISO | Legacy/BIOS only | ❌ NO |
| ZIP | UEFI native | ✅ YES |

---

## Step 2: Format USB as FAT32

### Windows
1. Insert USB drive
2. Open File Explorer → This PC
3. Right-click USB drive → Format
4. File system: **FAT32** (not NTFS, not exFAT)
5. Allocation unit size: Default
6. Quick Format: ✅ Checked
7. Click Start

### Linux
```bash
sudo mkfs.vfat -F 32 /dev/sdX
# Replace sdX with your USB device (e.g., sdb, sdc)
```

### Why FAT32?
UEFI firmware can only read FAT32 (or sometimes FAT16). It cannot read NTFS or exFAT during the boot phase.

---

## Step 3: Extract ZIP to USB Root

### Critical Rule
Extract the contents **directly to the root** of the USB. Do NOT put them inside a folder.

### Correct Structure
```
E:├── 📁 EFI
│   └── 📁 BOOT
│       └── BOOTX64.EFI
├── 📁 live
│   ├── vmlinuz
│   └── initrd.img
├── 📁 syslinux
└── 📄 Clonezilla-Live-Version
```

### WRONG Structure (Do NOT do this)
```
E:└── 📁 clonezilla-live-3.3.2-31-amd64  ← EXTRA FOLDER!
    ├── 📁 EFI
    ├── 📁 live
    └── 📁 syslinux
```

### How to Fix Wrong Structure
If extraction created a parent folder:
1. Open the parent folder
2. Select ALL contents (Ctrl+A)
3. Cut (Ctrl+X)
4. Go to USB root (E:\)
5. Paste (Ctrl+V)
6. Delete the now-empty parent folder

---

## Step 4: Configure BIOS

### Before Entering BIOS
**CRITICAL:** You must remove all BIOS passwords first, or settings will not save.

### Remove BIOS Passwords
1. Power on laptop
2. Press **F2** repeatedly to enter BIOS
3. Go to **Security** tab
4. Remove each password:
   - Administrator Password → Enter current → Leave blank → Enter
   - User Password → Enter current → Leave blank → Enter
   - Master Password → Enter current → Leave blank → Enter
   - HDD Password → Enter current → Leave blank → Enter
5. Go to **Exit** tab
6. Select **Save Changes and Exit**

### Configure Boot Settings
1. Re-enter BIOS (F2 at power-on)
2. Go to **Security** tab
3. **Secure Boot** → Set to **Disabled**
4. Go to **Boot** tab
5. **USB Boot** → Set to **Enabled**
6. **Boot Mode** → Set to **UEFI** (not Legacy/CSM)
7. (Optional) **Fast Boot** → Set to **Disabled**
8. Go to **Exit** tab
9. **Save Changes and Exit**

### Verify Settings Saved
1. Re-enter BIOS (F2)
2. Check that Secure Boot is still Disabled
3. Check that USB Boot is still Enabled
4. If yes, settings are now saving properly ✅

---

## Step 5: Boot from USB

1. Insert the prepared USB drive
2. Power on laptop
3. Press **Boot Menu key** immediately:
   - Lenovo: **F12**
   - HP: **F9** or **Esc**
   - Dell: **F12**
   - ASUS: **F8** or **Esc**
   - Acer: **F12**
4. Look for USB option:
   - "USB UEFI"
   - Your USB brand name (e.g., "SanDisk", "Kingston")
   - "UEFI: USB"
5. Select it and press Enter

### What You Should See
```
Clonezilla live (VGA 800x600)
Clonezilla live (VGA 800x600 & To RAM)
Clonezilla live (VGA with large font & To RAM)
Clonezilla live (Verify integrity of the boot medium)
Clonezilla live (Speech synthesis)
Other modes of Clonezilla live
Local operating system (if available)
Memtester (VGA 800x600 & To RAM)
Memtest using Memtest86+
Network boot via iPXE
UEFI firmware setup
```

### If You Still See Windows/Ubuntu
The USB is not booting. Go back to Step 1 and verify:
- You downloaded ZIP (not ISO)
- USB is FAT32
- Files are at root level
- BIOS passwords are removed
- Secure Boot is disabled
