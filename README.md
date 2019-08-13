# Faster Arch Linux

```sh
sudo cp etc/systemd/journald.conf.d/* /etc/systemd/journald.conf.d # may have to make journald.conf.d 
sudo cp etc/logind.conf /etc/systemd/
sudo cp etc/mkinitcpio.conf /etc/
sudo cp etc/udev/rules.d/* /etc/udev/rules.d/ # may have to make rules.d
sudo cp etc/makepkg.conf /etc/makepkg.conf
```


### Silent Boot
On most modern system the boot time can be decreased by limiting the verbosity of their system to a strict minimum, it is also much more aesthetically pleasing.

edit /etc/default/grub lines `GRUB_CMDLINE_LINUX` & `GRUB_CMDLINE_LINUX_DEFAULT`

```sh
GRUB_CMDLINE_LINUX_DEFAULT="quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_priority=3 vt.global_cursor_default=0 i915.fastboot=1"
GRUB_CMDLINE_LINUX="quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log_priority=3 vt.global_cursor_default=0 i915.fastboot=1"
```

edit `/etc/sysctl.d/20-quiet-printk.conf`
```sh
kernel.printk = 3 3 3 3
```

```sh
systemctl edit --full systemd-fsck-root.service
systemctl edit --full systemd-fsck@.service
```

add these two lines between `ExecStart=/usr/lib/systemd/systemd-fsck` & `TimeoutSec=0`

```sh
StandardOutput=null
StandardError=journal+console
```

####  To remove `GRUB loading. Weclome to GRUB!` on boot
##### Only for systems with efi
```sh
cd grub-shusher
make
sudo -s
./grub-kernel /boot/efi/EFI/***/grubx64.efi
```

### Programs You Can Install
* **preload** - Caches programs you use in RAM to launch faster
* **dbus-broker** - is a drop-in replacement for the libdbus reference implementation, which aims "to provide high performance and reliability, while keeping compatibility to the D-Bus reference implementation".
* **irqbalance** - distribute hardware interrupts across processors on a multiprocessor system in order to increase performance


```sh
sudo pacman -S dbus-broker irqbalance
yay -S preload

systemctl enable --now dbus-broker.service
systemctl enable --now preload.service
systemctl enable --now irqbalance.service
```

### Bonus iwd
iwd (iNet wireless daemon) is a wireless daemon for Linux written by Intel that aims to replace WPA supplicant. The core goal of the project is to optimize resource utilization by not depending on any external libraries and instead utilizing features provided by the Linux Kernel to the maximum extent possible.

iwd can work in standalone mode or in combination with comprehensive network managers like ConnMan, systemd-networkd and NetworkManager. 

In my experience it connects much faster.

```sh
sudo pacman -S iwd
systemctl enable --now iwd.service
```

For usage:
* [Standalone](https://wiki.archlinux.org/index.php/Iwd#iwctl)
* [ConnMan](https://wiki.archlinux.org/index.php/ConnMan#Using_iwd_instead_of_wpa_supplicant)
* [NetworkManager](https://wiki.archlinux.org/index.php/NetworkManager#Using_iwd_as_the_Wi-Fi_backend)

### Notes
For KDE Desktop Environment Users:
Set splash theme to none