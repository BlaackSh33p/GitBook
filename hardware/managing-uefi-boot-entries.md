# Managing UEFI Boot Entries

## Managing UEFI Boot Entries with efibootmgr (Cheatsheet & Guide)

If you’ve ever dual-booted multiple operating systems, you’ve probably noticed leftover UEFI boot entries even after uninstalling an OS. These entries clutter your boot menu, and sometimes even cause confusion when selecting the right system to boot.

On Linux (including Kali), the tool you’ll want to use for this is **`efibootmgr`**. Below is a reference guide and cheatsheet that you can keep handy.

***

### 🔹 What is `efibootmgr`?

`efibootmgr` is a Linux utility to manage UEFI firmware boot entries. It lets you:

* View existing boot entries
* Remove old ones
* Change boot order
* Create new entries
* Temporarily set the next boot device

In short, it’s your go-to tool for cleaning up and organizing UEFI boot entries without needing to enter your BIOS/UEFI setup screen.

***

### 🔹 Common Use Cases

* **Remove leftover entries** after uninstalling an OS
* **Reorder boot devices** (e.g., boot Linux before Windows)
* **Add a missing boot entry** manually
* **Temporarily boot from another OS** without changing permanent settings

***

### 🔹 Cheatsheet

| Action                             | Command                                                                             | Example                                                                          |
| ---------------------------------- | ----------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **List all boot entries**          | `sudo efibootmgr`                                                                   | Shows boot order + entries                                                       |
| **Verbose list (with file paths)** | `sudo efibootmgr -v`                                                                | Useful for debugging                                                             |
| **Delete an entry**                | `sudo efibootmgr -b <ID> -B`                                                        | `sudo efibootmgr -b 0003 -B`                                                     |
| **Disable (deactivate) an entry**  | `sudo efibootmgr -b <ID> -A`                                                        | `sudo efibootmgr -b 0002 -A`                                                     |
| **Enable an entry**                | `sudo efibootmgr -b <ID> -a`                                                        | `sudo efibootmgr -b 0002 -a`                                                     |
| **Change boot order**              | `sudo efibootmgr -o <ID1,ID2,...>`                                                  | `sudo efibootmgr -o 0000,0001,0002`                                              |
| **Set next boot (one-time)**       | `sudo efibootmgr -n <ID>`                                                           | `sudo efibootmgr -n 0001`                                                        |
| **Set timeout (seconds)**          | `sudo efibootmgr -t <sec>`                                                          | `sudo efibootmgr -t 10`                                                          |
| **Create new boot entry**          | `sudo efibootmgr -c -d /dev/sdX -p <part#> -L "<Label>" -l '\EFI\<dir>\<file>.efi'` | `sudo efibootmgr -c -d /dev/sda -p 1 -L "MyLinux" -l '\EFI\mylinux\grubx64.efi'` |

***

### 🔹 Safe Cleanup of Leftover EFI Folders

Removing the boot entry is not always enough. To fully clean up, you can delete the leftover EFI files:

1.  **Check EFI partition**

    ```bash
    lsblk -f
    mount | grep efi
    ```
2.  **Mount if not already mounted**

    ```bash
    sudo mkdir -p /boot/efi
    sudo mount /dev/sda1 /boot/efi   # Replace /dev/sda1 with your EFI partition
    ```
3.  **List EFI directories**

    ```bash
    ls /boot/efi/EFI
    ```

    Example output:

    ```
    Boot  Microsoft  kali  ubuntu
    ```
4.  **Remove the unwanted folder**

    ```bash
    sudo rm -r /boot/efi/EFI/ubuntu
    ```

⚠️ Do **not** delete `Boot`, `Microsoft`, or the entry for your current OS!

***

### 🔹 Tips

* Always double-check entries with `sudo efibootmgr -v` before deleting.
* EFI paths use **backslashes (`\`)**, not forward slashes.
* Boot IDs (like `0000`, `0001`) are **hexadecimal numbers**.

***

### ✅ Final Thoughts

With `efibootmgr`, you never need to fear a cluttered boot menu again. Whether you want to delete, reorder, or create new entries, it puts you in full control of your UEFI boot process — straight from Linux.

Keep this cheatsheet handy for future reference, or share it with anyone dealing with messy dual-boot setups.
