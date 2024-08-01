# Fedora Relative Topics

## Upgrade Fedora in command line  
```
# dnf upgrade --refresh
# dnf install dnf-plugin-system-upgrade
# dnf system-upgrade download --refresh --releasever=32 --nogpgcheck
# mv /etc/dnf/protected.d/*.conf ~/ (optional, only required if hit pkg protected error, can move it back after system upgraded)
# dnf system-upgrade reboot
```

## Downgrade/Upgrade specific pkgs

```
# dnf --releasever 32 downgrade xen (downgrade xen pkg only)
# dnf distro-sync --allowerasing --refresh (sync with current repo)
# dnf erase xxx (if distro-sync failed, try to erase the conflict packages)
```

## Boot from grub cli

If your boot entry was broken, this might be helpful to boot into system from grub cli.
```
grub> insmod lvm
grub> linux16 (hd0,msdos1)/vmlinuz-5.0.3-300.fc30.x86_64 root=/dev/mapper/fedora_wasa-root ro rd.lvm.lv=fedora_wasa/root
grub> initrd16 (hd0,msdos1)/initramfs-5.0.3-300.fc30.x86_64.img
grub> set root=(lvm,fedora_wasa-root)/
grub> boot
```
There command seems slightly difference in rhel.
```
grub> ls (hd0,gpt2)/
grub> linux (hd0,gpt2)/vmlinuz-5.14.0-427.26.1.el9_4.aarch64 root=UUID=0f5e676b-368d-4357-921b-f2dd60a9109a console=tty0 console=ttyS0,115200n8
grub> initrd (hd0,gpt2)/initramfs-5.14.0-427.26.1.el9_4.aarch64.img
grub> set boot=(hd0,gpt2)
grub> boot
```

## Switch system edition
```
dnf grouplist -v
dnf install @kde-desktop-environment
dnf -y group install 'Fedora Workstation'
systemctl set-default graphical.target
```