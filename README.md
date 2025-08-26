# usb-os

```
                     AgentK
```

Identify what operating system is on a **USB stick** — safely and quickly.

*  Scans **USB disks only** (skips the host’s own root/boot devices)
*  Works **read-only** (mounts with `ro,nosuid,nodev,noexec`)
*  Detects **installed OSes** (`/etc/os-release`, `/usr/lib/os-release`, `lsb-release`, `issue`)
*  Detects **live ISOs** (`.disk/info`, `casper/.../filesystem.squashfs`, `live/.../filesystem.squashfs`)
*  Spots **Ventoy/ISO containers**
*  🖥️/🍓 Shows **PC vs Pi** boot hints (`EFI/BOOT/BOOTx64.EFI` vs `config.txt` / `overlays`)
*  Output labels are **sanitized** (no `\x20` escapes; trims long ISO build IDs)

---

## Install

### One-liner (download + run)

```bash
sudo curl -fsSL https://raw.githubusercontent.com/niveek07/usb-os/main/usb-os \
  -o /usr/local/bin/usb-os && sudo chmod +x /usr/local/bin/usb-os && sudo usb-os
```

### Git clone (easier updates)

```bash
# prerequisites
sudo apt-get update && sudo apt-get install -y git
```
```bash
# install
sudo git clone https://github.com/niveek07/usb-os.git /opt/usb-os
sudo chmod +x /opt/usb-os/usb-os
sudo ln -sf /opt/usb-os/usb-os /usr/local/bin/usb-os
```
```bash
# run
sudo usb-os
```
```bash
# update later
cd /opt/usb-os && sudo git pull
```

**Uninstall**

```bash
sudo rm -f /usr/local/bin/usb-os
```
```bash
sudo rm -rf /opt/usb-os   # only if you used the git method
```

---

## Usage

```bash
sudo usb-os             # scan all USB sticks
```
```bash
sudo usb-os -l          # list USB disks/partitions only (no probing)
```bash
sudo usb-os -d /dev/sdb # probe exactly one USB device
```
```bash
sudo usb-os -h          # help
```

**Output style controls**

* `--plain` → disable colors & icons
* `NO_COLOR=1` → disable colors
* `NO_EMOJI=1` → disable icons/emojis

Examples:

```bash
sudo usb-os --plain
NO_EMOJI=1 sudo usb-os
```

---

## Example output

```
/dev/sdb   SanDisk_3.2Gen1 (57.3G)
  NODE      TYPE   FS       SIZE   LABEL
  sdb4      part   ext4     51.4G  writable
  sdb2      part   vfat     5M     ESP
  sdb1      part   iso9660  5.9G   Ubuntu 24.04.2 LTS amd64

● Boot: x86_64 PC-style EFI
● Live ISO: Ubuntu 24.04.2 LTS "Noble Numbat" — Release amd64
```

---

## What it looks for

**Installed OS (rootfs):**

* `/etc/os-release` or `/usr/lib/os-release`
* `/etc/lsb-release`
* `/etc/issue`

**Live ISO clues:**

* `.disk/info`
* `casper/filesystem.squashfs` or `live/filesystem.squashfs` (reads the OS inside)

**Boot style:**

* 🍓 Raspberry Pi: `config.txt`, `overlays/`
* 🖥️ PC/UEFI: `EFI/BOOT/BOOTx64.EFI`

**Ventoy / ISO containers:**

* `ventoy/` folder or one/more `*.iso` at the USB root

---

## Safety

* Mounts **read-only**, tries **partitions first**; if the stick is a raw ISO image (no partitions), will attempt a read-only mount on the **disk** node.
* Skips the devices that back `/`, `/boot`, or `/boot/firmware` on the **host**, even if the host is booted from USB.

---

## Requirements

* Linux with `bash`, `mount`, `umount`, `lsblk`, `findmnt` (standard on Debian/Ubuntu/Raspberry Pi OS).
* Run with `sudo` (mounting requires elevated privileges).

---

## Troubleshooting

* **“No OS info found”**
  The USB may be a data drive or have an unusual layout. Try:

  ```bash
  sudo usb-os -l              # just list what’s there
  sudo usb-os -d /dev/sdX     # probe a specific device
  ```

* **Nothing shows up**
  Confirm the kernel can see the stick:

  ```bash
  lsblk -o NAME,TRAN,TYPE | grep usb
  ```

---

License: **MIT**
Made with 🛠️ by **AgentK**.
