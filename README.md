# iscsi-partition-disk
Guide to partition a ISCSI physical disk volume without LVM in Linux

Author: Aman Thapa Magar

Introduction:

Tired of hassling though the internet to find the appropriate way to parititon iscsi disk ?, Here I present the simplest way to do it.


Step 1: List the physical partition table using the command below:

[root@bindweb /]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0  100G  0 disk 
|-sda1            8:1    0    1G  0 part /boot
`-sda2            8:2    0    9G  0 part 
  |-centos-root 253:0    0    8G  0 lvm  /
  `-centos-swap 253:1    0    1G  0 lvm  [SWAP]
sdb               8:16   0  100G  0 disk /mnt
sr0              11:0    1  918M  0 rom  

In our case, /dev/sdb is the iscsi mounted partition, you can verify your partition by issuing the command [lsscsi] to view your current iscsi physical disk.


Step 2: List the mounted partition table using the below command

[root@bindweb /]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.9G     0  1.9G   0% /dev
tmpfs                    1.9G     0  1.9G   0% /dev/shm
tmpfs                    1.9G  8.8M  1.9G   1% /run
tmpfs                    1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/mapper/centos-root  8.0G  1.8G  6.3G  22% /
/dev/sda1               1014M  261M  754M  26% /boot
tmpfs                    379M     0  379M   0% /run/user/1000
/dev/sdb                 100G   33M  100G   1% /mnt


Step 3: Verify the contents of the disk such as your files as this important to ensure your data is present.

[root@bindweb /]# cd /mnt
[root@bindweb mnt]# ls
files
[root@bindweb mnt]# cd files/
[root@bindweb files]# ls
test1  test2

Step 3: Unmount the /dev/sdb parition from the root directory
 
[root@bindweb /]# umount /dev/sdb /mnt

Step 4: Verify the disk has been unmounted issuing the command below:

[root@bindweb /]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0  100G  0 disk 
|-sda1            8:1    0    1G  0 part /boot
`-sda2            8:2    0    9G  0 part 
  |-centos-root 253:0    0    8G  0 lvm  /
  `-centos-swap 253:1    0    1G  0 lvm  [SWAP]
sdb               8:16   0  100G  0 disk 
sr0              11:0    1  918M  0 rom  

Step 4: Proceed to increase the physical disk from your SAN portal or any other hypervisor platform. In our case we added another 100 GB to make it a total of 200 GB disk.

Step 5: Rescan the iscsi target to acquire new disk size.

[root@bindweb /]# iscsiadm -m session --rescan
Rescanning session [sid: 2, target: iqn.2010-06.com.test:test-volume-percy-1062d78c-5468-4179-b92a-aa3582f1808d-tgt0, portal: 10.23.23.10,3205]

Step 6: Verify the disk size has been increased and listed in the volume.

[root@bindweb /]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0  100G  0 disk 
|-sda1            8:1    0    1G  0 part /boot
`-sda2            8:2    0    9G  0 part 
  |-centos-root 253:0    0    8G  0 lvm  /
  `-centos-swap 253:1    0    1G  0 lvm  [SWAP]
sdb               8:16   0  200G  0 disk 
sr0              11:0    1  918M  0 rom  

As, you can see our disk has been increased from 100 GB to 200 GB for the partition /dev/sdb

Step 7: Mount the partiton to /mnt or any desired folder

[root@bindweb /]# mount /dev/sdb /mnt/
[root@bindweb /]# 

Step 8: Verify the disk has been mounted to your desired folder in our case its mnt

[root@bindweb /]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.9G     0  1.9G   0% /dev
tmpfs                    1.9G     0  1.9G   0% /dev/shm
tmpfs                    1.9G  8.8M  1.9G   1% /run
tmpfs                    1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/mapper/centos-root  8.0G  1.8G  6.3G  22% /
/dev/sda1               1014M  261M  754M  26% /boot
tmpfs                    379M     0  379M   0% /run/user/1000
/dev/sdb                 100G   33M  100G   1% /mnt

As you can see the disk size has not been reflected in the mounted partiton hence we need to grow the filesystem to reflect the new size.

Step 9: Resize the filesystem to reflect the new size in the partition

[root@bindweb /]# xfs_growfs /dev/sdb
meta-data=/dev/sdb               isize=512    agcount=4, agsize=6553600 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=26214400, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=12800, version=2
         =                       sectsz=4096  sunit=1 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 26214400 to 52428800
[root@bindweb /]# 

NOTE: Please use your partiton manager according to your filesystem, we used xfs file system for the above, for ext4 and other file systems please browse the internet for the suitable partition manager.

Step 10: Verfiy the disk size again to see the new reflected partition

[root@bindweb /]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.9G     0  1.9G   0% /dev
tmpfs                    1.9G     0  1.9G   0% /dev/shm
tmpfs                    1.9G  8.8M  1.9G   1% /run
tmpfs                    1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/mapper/centos-root  8.0G  1.8G  6.3G  22% /
/dev/sda1               1014M  261M  754M  26% /boot
tmpfs                    379M     0  379M   0% /run/user/1000
/dev/sdb                 200G   33M  200G   1% /mnt
[root@bindweb /]# 

As you can see the partition has been reflected in the mnt directory to 200 GB

Step 11: Verify the contents of the partition to ensure your data is present and untouched by the above operation.

[root@bindweb /]# cd /mnt/
[root@bindweb mnt]# ls
files
[root@bindweb mnt]# cd files/
[root@bindweb files]# ls
test1  test2
[root@bindweb files]# 

Thats it, enjoy expanding parition with no issue, have a great day/night !!
