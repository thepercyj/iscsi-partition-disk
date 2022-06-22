# iscsi-partition-disk
Guide to partition a ISCSI physical disk volume without LVM in Linux

<sub>*Author: Aman Thapa Magar (Percy)*</sub>

## Introduction:

Tired of hassling though the internet to find the appropriate way to parititon iscsi disk ?. Here I present the simplest way to do it.


### Step 1: List the physical partition table using the command below:

![image](https://user-images.githubusercontent.com/20582994/174970702-67951862-2efb-4fcb-9b2b-25b63409a341.png)

### Step 2: List the mounted partition table using the below command

![image](https://user-images.githubusercontent.com/20582994/174970996-1b2280c6-81ce-4767-b18d-3878219d4530.png)

### Step 3: Verify the contents of the disk such as your files as this important to ensure your data is present.

![image](https://user-images.githubusercontent.com/20582994/174971320-2f2ce7fa-c95d-40f3-82ce-fe43a6e834fa.png)

### Step 4: Unmount the /dev/sdb parition from the root directory
 
![image](https://user-images.githubusercontent.com/20582994/174971623-7515b909-d407-40ef-a9d2-cd221f9de21d.png)

### Step 5: Verify the disk has been unmounted issuing the command below:

![image](https://user-images.githubusercontent.com/20582994/174971757-3086f721-5d75-40ca-b936-571e6f1c352f.png)

### Step 6: Proceed to increase the physical disk from your SAN portal or any other hypervisor platform. In our case we added another 100 GB to make it a total of 200 GB disk.

### Step 7: Rescan the iscsi target to acquire new disk size.

![image](https://user-images.githubusercontent.com/20582994/174971887-5b6b1fde-c767-4d88-be5b-e195fa852b5b.png)

### Step 8: Verify the disk size has been increased and listed in the volume.

![image](https://user-images.githubusercontent.com/20582994/174972090-352990db-14a8-453d-aadf-5ab77d2144fc.png)

### Step 9: Mount the partiton to /mnt or any desired folder

![image](https://user-images.githubusercontent.com/20582994/174972141-848f7aa8-e7d9-4762-8c0c-67a97cc27515.png)

### Step 10: Verify the disk has been mounted to your desired folder in our case its mnt

![image](https://user-images.githubusercontent.com/20582994/174972299-89b6ca35-e210-44f1-aa9d-35f4927b051a.png)

### Step 11: Resize the filesystem to reflect the new size in the partition

![image](https://user-images.githubusercontent.com/20582994/174972478-06e59981-dff8-44e6-8734-de106595c81b.png)

***NOTE: Please use your partiton manager according to your filesystem, we used xfs file system for the above, for ext4 and other file systems please browse the internet for the suitable partition  manager.***

### Step 12: Verfiy the disk size again to see the new reflected partition

![image](https://user-images.githubusercontent.com/20582994/174972686-7e00584a-963d-4f1f-a05a-bbd15b6cf336.png)

### Step 13: Verify the contents of the partition to ensure your data is present and untouched by the above operation.

![image](https://user-images.githubusercontent.com/20582994/174972732-e540f646-b486-4aff-89e3-d850dc2d75e2.png)

### Thats it, enjoy expanding parition with no issue, have a great day/night !!
