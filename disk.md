
```
mount
mount -a
mount /dev/sda1 /mnt/

cat /etc/fstab
cat /proc/partitions
cat /proc/mdstat

df -h | df -m | df -i
du -h | du -hs | du -m    // disk usage
du -sh /home              // size of directory

fdisk -l /dev/sda
cfdisk /dev/sda
sfdisk -s
sfdisk -d /dev/sda | sfdisk /dev/sdb

lsblk
parted -l
parted /dev/vdc
fdisk /dev/sda

# format
mkfs.ext4 /dev/sda1
mkfs.xfs /dev/sda2
mkswap /dev/sda3
swapon /dev/sda3

# USB
mkdir -p /media/usb
mount /dev/sdd1 /media/usb

mkdir /media/iso
mount /path/to/image.iso /mnt/iso -o loop

umount -l (-f) dir  # lazy

# vyrobim si loop file devices
# udelam z nich raid1 disk
# a ten namontuju
dd if=/dev/zero of=/mnt/d1 bs=1M count=1024
losetup /dev/loop17 /mnt/d1
# sudo mkfs.ext4 /dev/loop17   <- formatovani

```

### SSHFS
```
apt -y install sshfs
sshfs [user1@]host:[remote_directory] mountpoint [options]
mkdir ~/mount_point
sshfs user1@192.168.121.121:/home/user1 /home/user1/mount_point
```

### RAID
```
apt -y install mdadm
cat /proc/mdstat
mdadm --detail /dev/md127

STOP RAID:
umount /mnt/md0
mdadm --stop /dev/md0
# mdadm --remove /dev/md0
mdadm --zero-superblock /dev/vdb /dev/vdc /dev/vdd


mdadm -Cv /dev/md0 -l1 -n2 /dev/sd[ab]1
mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/loop18 /dev/loop19
mdadm --create /dev/md1 -l5  -n5 /dev/sda1 /dev/sdb1 /dev/sdc1	/dev/sdd1 /dev/sde1
mdadm --create /dev/md1 -l5  -n3 /dev/sda1 /dev/sdb1 missing
mdadm --fail /dev/md0 /dev/sda1
mdadm --remove /dev/md0 /dev/sda1
mdadm --add /dev/md0 /dev/sdc1

mkfs.ext4 -F /dev/md0
e2label /dev/md1 raid5

# /etc/fstab
LABEL=raid5       /mnt/md0    ext4    defaults        0       2

mkdir -p /mnt/md0
mount /dev/md0 /mnt/md0
df -h
chmod -R 777 /mnt/md0
chown -R myuser:mygroup Documents
echo "ahoj!" > /mnt/md0/test.txt

```


### LVM
```
apt -y install lvm2  
lvs
lvscan

pvcreate /dev/sdb1
vgcreate data /dev/sdb1
lvcreate --name video --size 20M data
mkfs.ext4 /dev/data/video
e2label /dev/data/video video
mount /dev/data/video /mnt/video

lvdisplay
umount /mnt/video
lvremove /dev/jmeno_skupiny/jmeno_oddilu

# /etc/fstab
LABEL=video       /mnt/video    ext4    defaults        0       2

# swap
fallocate -l 1G /swapfile
dd if=/dev/zero of=/swapfile bs=1024 count=1048576
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
cat /etc/fstab
swapon --show

```


