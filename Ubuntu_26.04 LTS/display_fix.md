# Screen Brightness Adjustment Fix on ASUS TUF A15 (Ubuntu 26.04 LTS)

If you are facing issues with screen brightness adjustment on your ASUS TUF A15 running Ubuntu 26.04 LTS, the following method can resolve it.

## Solution

1. Open the GRUB configuration file:
   ```bash
   sudo nano /etc/default/grub
   ```

2. Locate the line starting with `GRUB_CMDLINE_LINUX_DEFAULT` and add `acpi_backlight=native` after `quiet splash`. For example, change:
   ```text
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   to:
   ```text
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash acpi_backlight=native"
   ```

3. Update GRUB:
   ```bash
   sudo update-grub
   ```

4. Restart your system.
---
*Tested and documented by Satyakiran.*
