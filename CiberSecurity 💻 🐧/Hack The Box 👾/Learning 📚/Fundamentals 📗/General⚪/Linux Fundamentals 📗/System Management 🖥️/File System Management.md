## 1. Storage Devices and Partition Naming

* **Device Nodes (`/dev/sd*`):** Physical or virtual SATA/SCSI drives are represented as device nodes in the **`/dev`** directory.
* **`/dev/sda`:** The entire **first physical drive**.
* **`/dev/sdb`:** The **second physical drive**, and so on.


* **Partitions (`/dev/sda1`, `/dev/sda2`):** Trailing numbers represent logical slices carved into that specific drive.
* **Typical Linux Disk Layout:**
* **`/dev/sda1`:** Usually the Boot Partition (`/boot/efi`, FAT32) holding bootloaders like GRUB.
* **`/dev/sda2`:** The Root Filesystem (`/`, ext4) holding the operating system, apps, and user files.
* **`/dev/sda3`:** Swap Space (`swap`) acting as virtual memory overflow.


* **NVMe Naming:** Uses **`/dev/nvme0n1`** for the drive and **`/dev/nvme0n1p1`** for partitions.

---

## 2. Swap Memory

* **Purpose:** Swap acts as virtual memory on disk when physical RAM fills up, or to store idle background tasks.
* **Speed:** Physical RAM is silicon-fast; Swap uses storage drives (SSD/HDD) and is significantly slower.
* **Total Virtual Memory = RAM + Swap.** However, relying on Swap for active processing leads to **thrashing** (system slowdown/freezing).
* **Security & Forensics:** Swap is non-volatile. Unencrypted Swap can retain sensitive data (passwords, encryption keys, tokens) across reboots, which can be extracted using tools like `strings /dev/sda3 | grep -i "password"`.
* **Protections:** Full Disk Encryption (LUKS/dm-crypt) encrypts Swap; developers can lock memory ranges into RAM using the `mlock()` C function to prevent swapping.

---

## 3. The Concept of Mounting

* **What it Is:** Mounting attaches a formatted partition (a collection of storage blocks) to a directory in the Linux global tree (the **Mount Point**).
* **Why it's Needed:** Raw device files like `/dev/sda2` contain raw binary data. Mounting interprets the filesystem (`ext4`) and turns those raw blocks into browseable files and folders.
* **Unified Hierarchy:** Unlike Windows drive letters (`C:\`, `D:\`), Linux attaches every storage device into a single global tree starting at root (`/`).
* **Persistent Mounts:** Automatically configured in `/etc/fstab` using partition UUIDs.

---

## 4. Advanced Mounting Concepts & Edge Cases

* **Multiple Mount Points ("Two Doors, One Room"):** You can mount the same partition (`/dev/sda2`) to two different folders (e.g., `/` and `/mnt/test`). Both folders access the exact same underlying physical data blocks.
* **Physical vs. Virtual Mounts:**
* Browsing `/mnt/test` shows the physical contents written to `/dev/sda2` (`/etc`, `/home`, `/usr`).
* Virtual RAM filesystems (`/proc`, `/sys`) and secondary mounts (`/boot/efi`) live outside `/dev/sda2` and appear empty in `/mnt/test`.


* **Mount Loops:** Mounting `/dev/sda2` onto `/mnt/test` creates a visual directory loop (`/mnt/test/mnt/test/...`) in the Virtual Filesystem (VFS) map in RAM, but it does **not** duplicate data or take up extra disk space.

---

## 5. Quick Command Reference

```bash
# Check block devices and partition tree layout
lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINTS

# Manipulate disk partition table, but with -l just show info about partion table, like lsblk
sudo fdisk -f 

# Check RAM and Swap usage (human-readable + combined total)
free -h --total

# Check disk space utilization on mounted drives
df -h

# Check where a specific partition is currently mounted
findmnt /dev/sda2

# Mount a partition to a folder
sudo mount /dev/sda2 /mnt/test

# Unmount a device or directory
sudo umount /mnt/test

# Remount root as Read-Write without rebooting
sudo mount -o remount,rw /

#Used to prepare a device or file to be used as swap space by creating a Linux swap area
mkswap

#Activates the swap space, allowing the system to use it
swapon

```

