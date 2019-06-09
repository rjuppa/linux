
```
mount
mount -a
mount /dev/sda1 /mnt/

cat /etc/fstab
cat /proc/partitions
cat /proc/mdstat

df -h | df -m | df -i
du -h | du -hs | du -m

fdisk -l /dev/sda
cfdisk /dev/sda
sfdisk -s
sfdisk -d /dev/sda | sfdisk /dev/sdb

lsmod
sudo parted -l
sudo parted /dev/vdc
sudo fdisk /dev/sda

# format
mkfs.ext4 /dev/sda1
mkfs.xfs /dev/sda2
mkswap /dev/sda3
swapon /dev/sda3

# USB
sudo mkdir -p /media/usb
sudo mount /dev/sdd1 /media/usb

sudo mkdir /media/iso
sudo mount /path/to/image.iso /mnt/iso -o loop

umount -l (-f) dir  # lazy

# vyrobim si loop file devices
# udelam z nich raid1 disk
# a ten namontuju
sudo dd if=/dev/zero of=/mnt/d1 bs=1M count=1024
sudo losetup /dev/loop17 /mnt/d1
# sudo mkfs.ext4 /dev/loop17   <- formatovani

```

### SSHFS
```
sudo apt install sshfs
sshfs [user1@]host:[remote_directory] mountpoint [options]
mkdir ~/mount_point
sshfs user1@192.168.121.121:/home/user1 /home/user1/mount_point
```

### RAID
```
sudo apt install mdadm
mdadm -Cv /dev/md0 -l1 -n2 /dev/sd[ab]1
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/loop18 /dev/loop19
sudo mkfs.ext4 -F /dev/md0
sudo mkdir -p /mnt/md0
sudo mount /dev/md0 /mnt/md0
df -h
sudo chmod -R 777 /mnt/md0
sudo chown -R myuser:mygroup Documents
echo "ahoj!" > /mnt/md0/test.txt

```


### LVM
```
sudo apt-get install lvm2  
sudo lvs
sudo lvscan
sudo lvcreate -n lv1 -L 10G vg1
sudo mkfs.ext4 /dev/vg1/lv1
sudo mount /dev/vg1/lv1 /media/files
(pvcreate, vgcreate, vgs)

# swap
sudo fallocate -l 1G /swapfile
sudo dd if=/dev/zero of=/swapfile bs=1024 count=1048576
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
cat /etc/fstab
sudo swapon --show

```


