## hostname

Configure the system hostname by replacing /etc/hosts and /etc/hostname.

On Debian and Debian-based systems you must also replace a file in /etc/cloud else the hostname resets on reboot.

**Note**: This playbook reboots the server if any of these files change.
