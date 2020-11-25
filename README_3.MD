# snap

touch /home/file{1..5}

lvcreate -L 5MB -s -n home_snap1 /dev/VolGroup00/LogVol_Home

rm -f /home/file{1..3}

umount /home

lvconvert --merge /dev/VolGroup00/home_snap

mount /home
