# Fix GRUB

Fix GRUB after Windows update.

## Prelimary checks

1. **Disable secure boot**, some systems can prevent GRUB execution if secure boot is enabled!
2. Sort correctly your EFI table in BIOS

## Fix GRUB using live system

1. Flash a live system (`.iso` file) into a USB/CD with a similar kernel of your broken system (e.g. same distro live ISO)
2. Mount your broken system on live system following your distro documentation
3. Re-install GRUB

### Mount broken system

Following commands are tried on _OpenSUSE Tumbleweed_.

Note your partitions devices, in particular your _root_ and _efi_ partions:

```bash
fdisk -l
```

You could see something like this:

```
/dev/nvme0n1p1       2048     534527    532480   260M EFI System
/dev/nvme0n1p2     534528     567295     32768    16M Microsoft reserved
/dev/nvme0n1p3     567296  522397695 521830400 248.8G Microsoft basic data
/dev/nvme0n1p4 1998360576 2000408575   2048000  1000M Windows recovery environment
/dev/nvme0n1p5  522397696  942319615 419921920 200.2G Linux filesystem
/dev/nvme0n1p6  942319616 1917495295 975175680   465G Linux filesystem
/dev/nvme0n1p7 1918881792 1998360575  79478784  37.9G Linux swap
/dev/nvme0n1p8 1917495296 1918881791   1386496   677M Microsoft basic data
```

I have 4 partitions:

- `/dev/nvme0n1p1` EFI partition (we must install GRUB here!)
- `/dev/nvme0n1p5 ` _root_ filesystem (we must bind this!)
- `/dev/nvme0n1p6` _home_
- `/dev/nvme0n1p7` _swap_

Mount broken filesystem:

```bash
mount /dev/nvme0n1p5 /mnt    # mount root
mount -t proc none /mnt/proc
mount --rbind /dev /mnt/dev
mount --rbind /sys /mnt/sys
```

Switch to it using `chroot`:

```bash
chroot /mnt /bin/bash
```

Mount the remaining partitions:

```bash
mount -a
```

### Re-install GRUB

```bash
grub2-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=openSUSE
grub2-mkconfig -o /boot/grub2/grub.cfg

reboot
```












