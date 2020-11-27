# 03-lvm
otus 03 LVM
# -> 8Gb 
![результат 8Gb.png](https://github.com/Elijah-Fox/03-lvm/blob/main/images/8Gb.png)

* Команды

yum install lvm2 xfsdump nano

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

cd /boot ; for i in ls initramfs-*img; do dracut -v $i echo $i|sed "s/initramfs-//g; s/.img//g" --force; done

Открываем конфигурационный файл grub:

nano /boot/grub2/grub.cfg

Меняем все совпадения:

lv=VolGroup00/LogVol00

меняем на:

lv=vg_tmp_root/lv_tmp_root

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

cd /boot ; for i in ls initramfs-*img; do dracut -v $i echo $i|sed "s/initramfs-//g; s/.img//g" --force; done

nano /boot/grub2/grub.cfg

exit

shutdown -r now


# /home /var

![результат /var + /home.png](https://github.com/Elijah-Fox/03-lvm/blob/main/images/var_home.png)

выделить тома под /home /var

* Команды

**для /var**

pvcreate /dev/sdc /dev/sdd

vgcreate vg_var /dev/sdc /dev/sdd

lvcreate -L 950M -m1 -n lv_var vg_var

mkfs.ext4 /dev/vg_var/lv_var

mount /dev/vg_var/lv_var /mnt

cp -aR /var/* /mnt/      # rsync -avHPSAX /var/ /mnt/

xfsdump -J - /var | xfsrestore -J - /mnt

mkdir /tmp/oldvar && mv /var/* /tmp/oldvar

umount /mnt

mount /dev/vg_var/lv_var /var

echo "`blkid | grep var: | awk '{print $2}'` /var ext4 defaults 0 0" >> /etc/fstab


**для /home**

lvcreate -n LogVol_Home -L 2G /dev/VolGroup00

mkfs.xfs /dev/VolGroup00/LogVol_Home

mount /dev/VolGroup00/LogVol_Home /mnt/

cp -aR /home/* /mnt/  

rm -rf /home/*

umount /mnt

# snap
README_3.MD

snap.png
