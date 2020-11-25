# /home /var

выделить тома под /home /var


# команды

для /var

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


для /home

lvcreate -n LogVol_Home -L 2G /dev/VolGroup00

mkfs.xfs /dev/VolGroup00/LogVol_Home

mount /dev/VolGroup00/LogVol_Home /mnt/

cp -aR /home/* /mnt/  

rm -rf /home/*

umount /mnt
