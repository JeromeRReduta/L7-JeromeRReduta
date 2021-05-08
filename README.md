# File Systems

In this lab assignment you will get experience working with file systems: creating a new FS, manipulating files, and inspecting the data organized on a disk.

## The Disk

As usual, everything on a Unix system is a file. The first thing we should do is find out what 'file' represents our main (boot) disk. You can use the `df` command to print out all the currently-mounted file systems, including their sizes and space available (use the `-H` option for human-readable sizes).

**(1.)** What device file represents your root (`/`) partition?

/dev/vda2 is mounted on /.

**(2.)** What is the output of `ls -l` on this file? What is different about this file compared to most?

total 49
lrwxrwxrwx   1 root root     7 Jan 19 01:32 bin -> usr/bin
drwxr-xr-x   4 root root  1024 Jan 28 10:23 boot
drwxr-xr-x  20 root root  3700 Apr 30 21:02 dev
drwxr-xr-x  38 root root  4096 Mar 26 20:57 etc
drwxr-xr-x   3 root root  4096 Jan 28 10:10 home
lrwxrwxrwx   1 root root     7 Jan 19 01:32 lib -> usr/lib
lrwxrwxrwx   1 root root     7 Jan 19 01:32 lib64 -> usr/lib
drwx------   2 root root 16384 Jan 28 09:47 lost+found
drwxr-xr-x   2 root root  4096 Jan 19 01:32 mnt
drwxr-xr-x   2 root root  4096 Jan 19 01:32 opt
dr-xr-xr-x 172 root root     0 Apr 30 21:02 proc
drwxr-x---   5 root root  4096 Jan 28 10:51 root
drwxr-xr-x  17 root root   440 Apr 30 21:02 run
lrwxrwxrwx   1 root root     7 Jan 19 01:32 sbin -> usr/bin
drwxr-xr-x   4 root root  4096 Jan 28 09:58 srv
dr-xr-xr-x  13 root root     0 Apr 30 21:02 sys
drwxrwxrwt   8 root root   160 Apr 30 23:29 tmp
drwxr-xr-x  10 root root  4096 Mar 16 12:47 usr
drwxr-xr-x  12 root root  4096 Mar 26 20:57 var

For comparison, here's a run of 'ls -l' on home directory:

total 1172
-rw-r--r-- 1 jrreduta wheel      8 Mar  9 02:18 bubba.txt
-rw-r--r-- 1 jrreduta wheel      0 Feb 19 21:05 cdTrace.txt
drwxr-xr-x 2 jrreduta wheel   4096 Mar 16 12:56 counting-code
-rw-r--r-- 1 jrreduta wheel 837487 Feb 19 21:11 fishTrace.txt
drwxr-xr-x 3 jrreduta wheel   4096 Mar  8 03:45 L4-JeromeRReduta
drwxr-xr-x 4 jrreduta wheel   4096 Mar 27 08:40 L5-JeromeRReduta
drwxr-xr-x 3 jrreduta wheel   4096 Apr 30 23:29 L6-JeromeRReduta
-rw-r--r-- 1 jrreduta wheel   3878 Feb 19 21:13 mkdirTrace.txt
drwxr-xr-x 3 jrreduta wheel   4096 Feb 20 01:41 OS-Labs
drwxr-xr-x 5 jrreduta wheel   4096 Mar 23 19:14 P1-JeromeRReduta
drwxr-xr-x 3 jrreduta wheel   4096 Mar 17 02:01 P1-Refactor
drwxr-xr-x 5 jrreduta wheel   4096 Mar 25 17:31 P2-JeromeRReduta
drwxr-xr-x 5 jrreduta wheel   4096 Apr 27 06:29 P3-JeromeRReduta
-rw-r--r-- 1 jrreduta wheel 305443 Feb 19 21:06 slTrace.txt
drwxr-xr-x 2 jrreduta wheel   4096 Mar 16 13:52 struct-stuff
drwxr-xr-x 3 jrreduta wheel   4096 Mar 28 06:51 test_P2

For one, the two columns are filled with "root root," as opposed to "jrreduta wheel" in the home directory ls -l run. Some of the names on the right of the root file ls -l run have arrows leading to other names. Lastly, there's an "l" permission on the first letter of some of the entries in the root ls -l run.


Since disks are just files, we can create a disk by creating a file. (Yo dawg, I heard you like disks...):

```
dd if=/dev/zero of=./my-disk bs=1048576 count=1000
```

**(3.)** How big is this file, in megabytes? How long did it take to write this file to your VM's disk?

Okay, so we created a blank file (filled with zeros from /dev/zero -- which is also a device file, by the way). Now we can put a file system on it:

```
mkfs.ext4 ./my-disk
```
From the output text:

"1048576000 bytes (1.0 GB, 1000 MiB) copied"
"12.166 s"


**(4.)** How many inodes are created by this process?

Just one?

## Mounting a File System

The act of *mounting* a file system makes the data on a disk available to us under a particular *mount point*. A mount point is just a directory -- it doesn't have to be empty, but mounting a disk to a particular directory will hide (replace) whatever contents were there already. So most of the time, we'll mount our file systems to an empty directory.

In order to mount file systems, you must be the root user. Switch to root (`sudo su - `, and then:

```
mkdir /tmp/mount
touch /tmp/mount/test-file
ls -l /tmp/mount/
mount ~yourusername/my-disk /tmp/mount
ls -l /tmp/mount/
```

**(5.)** What are the contents of /tmp/mount (shown by `ls -l`) before and after you mount the disk?

Before: test-file
After: lost + found 

**(6.)** How much space was used by creating the file system? In other words, how much free space does your disk have right now? You know the command to print this information out...

/dev/vda2        32G  3.9G   26G  14% /

Apparently, /dev/vda2 has 26G available, even though I mounted /tmp/mount on my-disk.


Make a directory in this new file system for your regular user account (call it 'my_files'). You will need to update its permissions with `chown` to make it accessible. Afterward, log out of the root account and continue as a regular user.

**(7.)** What is the output of `ls -ld /tmp/mount/my_files`? Are the permissions set up correctly?

Output: 
drwxr-xr-x 2 jrreduta root 4096 May  8 06:31 /tmp/mount/my_files

## Manipulating The File System

Back as your regular user (you should `exit` if you are still root), cd to /tmp/mount/my_files. Let's create a couple files here:

```
touch fileA
umask 000
touch fileB
umask 777
touch fileC
umask 077
whoami | md5sum > whoami.txt
```

**(8.)** What is the output of `ls -l`? What difference does the `umask` command make to the file permissions?

Output:
-rw-r--r-- 1 jrreduta wheel  0 May  8 06:33 fileA
-rw-rw-rw- 1 jrreduta wheel  0 May  8 06:33 fileB
---------- 1 jrreduta wheel  0 May  8 06:33 fileC
-rw------- 1 jrreduta wheel 36 May  8 06:34 whoami.txt

First line is default
Second is umask 000
Third is umask 777
Fourth is umask 077


**(9.)** What `umask` command would set the default permissions for files to `------r--` ?

If my permissions were 777, then I would use umask 776.

777 - 776 - 001 = ------r--

**(10.)** What inode number is associated with fileB? (`ls -li`)

fileB has inode number 14.

Okay, let's create a couple more files here. First, let's generate a file with some random data:

```
dd if=/dev/random of=./random-file bs=1024 count=1
```

**(11.)** What is the inode number of random-file?

random-file's inode number is 19.

We can also create a file with a nice greeting:

```
echo "hello world" > hello-file
```

**(12.)** After doing this, what happens when you run `grep -a hello ~/my-disk`? What's happening here?
Grep is meant to be used on a file or file system. However, because ~/my-disk is treated as a disk, the data inside is treated differently. Therefore, running grep gives undefined results.

Speaking of the file that represents our disk, let's inspect it a bit closer:

```
debugfs ~/my-disk
stat <number>
```

Where 'number' is the inode number of fileB and random-file.

**(13.)** What is the output of these two `stat` commands?

fileB:
debugfs:  stat <14>
Inode: 14   Type: regular    Mode:  0666   Flags: 0x80000
Generation: 2777043408    Version: 0x00000000:00000001
User:  1000   Group:   998   Project:     0   Size: 0
File ACL: 0
Links: 1   Blockcount: 0
Fragment:  Address: 0    Number: 0    Size: 0
 ctime: 0x609630b9:7b2ecfb8 -- Sat May  8 06:33:29 2021
 atime: 0x609630b9:7b2ecfb8 -- Sat May  8 06:33:29 2021
 mtime: 0x609630b9:7b2ecfb8 -- Sat May  8 06:33:29 2021
crtime: 0x609630b9:7a635c64 -- Sat May  8 06:33:29 2021
Size of extra inode fields: 32
Inode checksum: 0xbfd5ffdd
EXTENTS:

random-file:
debugfs:  stat <19>
Inode: 19   Type: regular    Mode:  0666   Flags: 0x80000
Generation: 1712271518    Version: 0x00000000:00000001
User:  1000   Group:   998   Project:     0   Size: 1024
File ACL: 0
Links: 1   Blockcount: 8
Fragment:  Address: 0    Number: 0    Size: 0
 ctime: 0x609633d1:3f94053c -- Sat May  8 06:46:41 2021
 atime: 0x609633d1:3f94053c -- Sat May  8 06:46:41 2021
 mtime: 0x609633d1:3f94053c -- Sat May  8 06:46:41 2021
crtime: 0x609633d1:3f94053c -- Sat May  8 06:46:41 2021
Size of extra inode fields: 32
Inode checksum: 0xd82189b6
EXTENTS:
(0):32895



debugfs supports several commands to view file system information. Use `help` or `?` to show these commands.

**(14.)** Paste the superblock information for your FS here.

Filesystem volume name:   <none>
Last mounted on:          /tmp/mount
Filesystem UUID:          fa4b9462-d66b-4f91-9068-005db2f46338
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent 64bit flex_bg sparse_super large_file huge_file dir_nlink extra_isize metadata_csum
Filesystem flags:         signed_directory_hash
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              64000
Block count:              256000
Reserved block count:     12800
Free blocks:              247252
Free inodes:              63989
First block:              0
Block size:               4096
Fragment size:            4096
Group descriptor size:    64
Reserved GDT blocks:      124
Blocks per group:         32768
Fragments per group:      32768
Inodes per group:         8000
Inode blocks per group:   500
Flex block group size:    16
Filesystem created:       Sat May  8 06:25:52 2021
Last mount time:          Sat May  8 06:26:04 2021
Last write time:          Sat May  8 06:26:04 2021
Mount count:              1
Maximum mount count:      -1
Last checked:             Sat May  8 06:25:52 2021
Check interval:           0 (<none>)
Lifetime writes:          521 kB
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:               256
Required extra isize:     32
Desired extra isize:      32
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      1172a215-1373-4c73-af5c-ca3a77546d70
Journal backup:           inode blocks
Checksum type:            crc32c
Checksum:                 0xb6dd5b8c
Directories:              3
 Group  0: block bitmap at 126, inode bitmap at 134, inode table at 142
           28619 free blocks, 7981 free inodes, 3 used directories, 7974 unused inodes
           [Checksum 0x69e0]
 Group  1: block bitmap at 127, inode bitmap at 135, inode table at 642
           32639 free blocks, 8000 free inodes, 0 used directories, 8000 unused inodes
           [Inode not init, Checksum 0xd4bb]
 Group  2: block bitmap at 128, inode bitmap at 136, inode table at 1142
           28672 free blocks, 8000 free inodes, 0 used directories, 8000 unused inodes
           [Inode not init, Checksum 0xd0cb]
 Group  3: block bitmap at 129, inode bitmap at 137, inode table at 1642

...


**(15.)** What command would you use to delete a directory via debugfs?

"rmdir"

Okay, we're all done here. cd out of your file system, switch to root, and then un-mount it with:

```
umount /tmp/mount
```

Then you'll bundle your file system up and turn it in (seriously). To do this, compress it first:

```
gzip -9 my-disk
```

Then check in my-disk.gz to GitHub.

## Stat

In the last part of this assignment, you'll develop a simple C program that prints file information using the `stat` system call. (see `man 2 stat` for more information). Given a file path, stat will populate a struct with inode information.

The struct:

```

struct stat {
	dev_t     st_dev;         /* ID of device containing file */
	ino_t     st_ino;         /* Inode number */
	mode_t    st_mode;        /* File type and mode */
	nlink_t   st_nlink;       /* Number of hard links */
	uid_t     st_uid;         /* User ID of owner */
	gid_t     st_gid;         /* Group ID of owner */
	dev_t     st_rdev;        /* Device ID (if special file) */
	off_t     st_size;        /* Total size, in bytes */
	blksize_t st_blksize;     /* Block size for filesystem I/O */
	blkcnt_t  st_blocks;      /* Number of 512B blocks allocated */

	/* Since Linux 2.6, the kernel supports nanosecond
	   precision for the following timestamp fields.
	   For the details before Linux 2.6, see NOTES. */

	struct timespec st_atim;  /* Time of last access */
	struct timespec st_mtim;  /* Time of last modification */
	struct timespec st_ctim;  /* Time of last status change */

	#define st_atime st_atim.tv_sec      /* Backward compatibility */
	#define st_mtime st_mtim.tv_sec
	#define st_ctime st_ctim.tv_sec
};

```

So we can do something like:

```
    struct stat statbuf = { 0 };
    if (stat(path, &statbuf) == -1) {
        perror("stat");
        return;
    }
```

Your program will accept one command-line argument: the file path. It will then determine whether the file is owned by the same user running the process. So if a file is owned by 'mmalensek' and the user running your program is also 'mmalensek' it will print:

```
This file is owned by the current user
```

Otherwise,

```
This file is owned by someone else
```

In this case (not owned by the process), you will need to find out whether or not we can access the file (either we are in the same group with read permission, or the last (others) field has read permission). So:

```
This file is owned by someone else but *IS* readable by this process
```

or

```
This file is owned by someone else and is *NOT* readable by this process
```

Check the code in as inode_reader.c.


# Summing Up

Make sure you answered each question, as well as checked in:

* inode_reader.c
* my-disk.gz
