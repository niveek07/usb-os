# usb-os
Check what os is on bootable usb
Here’s a clean **README.md** you can paste into your repo 👇

---

# usb-os

Identify what operating system is on a **USB stick** — safely and quickly.

*  Scans **USB disks only** (skips the host’s own root/boot devices)
*  Works **read-only** (mounts with `ro,nosuid,nodev,noexec`)
*  Detects **installed OSes** (`/etc/os-release`, `/usr/lib/os-release`, `lsb-release`, `issue`)
*  Detects **live ISOs** (`.disk/info` and `casper/…/filesystem.squashfs` or `live/…/filesystem.squashfs`)
*  Spots **Ventoy/ISO containers**
*  Shows **Pi vs PC** boot hints (`config.txt` / `overlays` vs `EFI/BOOT/BOOTx64.EFI`)

---

## Install

```bash
# One-liner install + run
sudo curl -fsSL https://raw.githubusercontent.com/niveek07/usb-os/main/usb-os \
  -o /usr/local/bin/usb-os && sudo chmod +x /usr/local/bin/usb-os && sudo usb-os
```

**Uninstall:**

```bash
sudo rm -f /usr/local/bin/usb-os
```

---

## Usage

```bash
sudo usb-os             # scan all USB sticks
sudo usb-os -l          # list USB disks/partitions only (no probing)
sudo usb-os -d /dev/sdb # probe exactly one USB device
sudo usb-os -h          # help
```

### Example output

```
==== /dev/sdb ====
  /dev/sdb     size=57.3G  type=disk  fs=iso9660  label=Ubuntu 24.04.2 LTS amd64
  /dev/sdb4    size=51.4G  type=part  fs=ext4     label=writable
  /dev/sdb2    size=5M     type=part  fs=vfat     label=ESP
  /dev/sdb1    size=5.9G   type=part  fs=iso9660  label=Ubuntu 24.04.2 LTS amd64
    -> x86_64 PC-style EFI
    -> Live ISO: Ubuntu 24.04.2 LTS "Noble Numbat" - Release amd64 (20250215)
```

---

## What it looks for

* **Installed OS (on a rootfs partition):**

  * `/etc/os-release` or `/usr/lib/os-release`
  * `/etc/lsb-release`
  * `/etc/issue`
* **Live ISO clues:**

  * `.disk/info`
  * `casper/filesystem.squashfs` or `live/filesystem.squashfs` (then reads the OS inside)
* **Boot style:**

  * Raspberry Pi: `config.txt`, `overlays/`
  * PC/UEFI: `EFI/BOOT/BOOTx64.EFI`
* **Ventoy/ISO containers:** presence of `ventoy/` or `*.iso` files in the USB root

---

## Safety

* Mounts **read-only** and tries partitions first; if the stick is a raw ISO image (no partitions), it will attempt a read-only mount on the **disk** node.
* Skips the devices that back `/`, `/boot`, or `/boot/firmware` on the **host**, even if the host is booted from USB.

---

## Requirements

* Linux with `bash`, `mount`, `umount`, `lsblk`, and `findmnt` (all standard on Debian/Ubuntu/Raspberry Pi OS).
* Run with `sudo` (needed to mount read-only).

---

## Troubleshooting

* **“No OS info found”**
  The USB might be a data drive, or an image with an unusual layout. Try `sudo usb-os -l` to see partitions, or probe a specific device with `-d /dev/sdX`.

* **Nothing shows up**
  Make sure the stick is actually seen by the kernel: `lsblk -o NAME,TRAN,TYPE` should list it with `TRAN=usb` and `TYPE=disk`.


