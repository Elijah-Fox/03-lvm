lsblk
yum install lvm2 xfsdump
yum install device-mapper

pvcreate /dev/sdb
vgcreate vg_tmp_root /dev/sdb
lvcreate -n lv_tmp_root -l +100%FREE /dev/vg_tmp_root

mkfs.xfs /dev/vg_tmp_root/lv_tmp_root
mount /dev/vg_tmp_root/lv_tmp_root /mnt

xfsdump -J - /dev/vg_tmp_root/lv_tmp_root | xfsrestore -J - /mnt
for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/$i; done
chroot /mnt/







pvcreate /dev/sdb
vgcreate vg_tmp_root /dev/sdb
lvcreate -n lv_tmp_root -l +100%FREE /dev/vg_tmp_root


mkfs.xfs /dev/vg_tmp_root/lv_tmp_root
mount /dev/vg_tmp_root/lv_tmp_root /mnt
xfsdump -J - /dev/VolGroup00/LogVol00 | xfsrestore -J - /mnt
for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/$i; done

chroot /mnt/

grub2-mkconfig -o /boot/grub2/grub.cfg



cd /boot ; for i in `ls initramfs-*img`; do dracut -v $i `echo $i|sed "s/initramfs-//g; s/.img//g"` --force; done


Открываем конфигурационный файл grub:

nano /boot/grub2/grub.cfg

Меняем все значения, которые задействуют старый том lvm — в моем случае, все совпадения:

...
...  lv=VolGroup00/LogVol00 ...
...

... менялись на:

...
... lv=vg_tmp_root/lv_tmp_root ...
...

exit

shutdown -r now



lvremove /dev/VolGroup00/LogVol00

lvcreate -n LogVol00 -L 8G VolGroup00



mkfs.xfs /dev/VolGroup00/LogVol00

mount /dev/VolGroup00/LogVol00 /mnt

xfsdump -J - /dev/vg_tmp_root/lv_tmp_root | xfsrestore -J - /mnt


for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/$i; done

chroot /mnt/

grub2-mkconfig -o /boot/grub2/grub.cfg

cd /boot ; for i in `ls initramfs-*img`; do dracut -v $i `echo $i|sed "s/initramfs-//g; s/.img//g"` --force; done

nano /boot/grub2/grub.cfg
exit

shutdown -r now





VolGroup00-LogVol00   -   