# ripenhanced
Script to copy the data ("enhanced") portion of an audio cd to an ISO file.

In addition to the ISO file, a mount options file is also written to make it easier to set the proper `mount` options when you are interested in reading data from the ISO file later.

### Requirements
These packages will need to be installed before using this script.

* cdrdao
* ddrescue
* hfsutils (optional, only needed if you want to read the Mac data later)

### Usage
`ripenhanced {DEVICE} {ISOFILE}`

You can run this script multiple times if errors are found. Ddrescue will attempt to re-read the failed sectors.

Note: While it looks like a large ISO file is output, the actual disk footprint is smaller due to the use of "sparse" files.

#### Why "sparse" files
"Sparse" files are used because the iso9660 format expects the data to be in the same physical location as it was on the original disc -- after the audio tracks. By using "sparse" files, the space that would have been used by the audio tracks is treated as empty and can be virtually skipped by the filesystem. This allow the same physical layout to be retained (minus the audio tracks) while saving physical disk space. It also makes it easier to mount the iso9660 filesystem later since `mount` has no way to reproduce that gap, but it does have a way to skip sectors. Read more about it on [Wikipedia](https://en.wikipedia.org/wiki/Sparse_file).

### Reading PC (ISO9660) data

You can easily mount the ISO filesystem by doing the following...

Retrieve the mount options:
`cat {ISOFILE}.mntopts.txt`

Mount the filesystem:
`sudo mount -o loop,ro,{MNTOPTS} -t iso9660 {ISOFILE} {MNTPOINT}`

Now that the filesystem is mounted, you can `cd {MNTPOINT}` and then read the files you are interested in. Don't forget to `umount {MNTPOINT}` when you are done.

### Reading Mac (HFS) data

Some ISO files contain support for both Macs and PCs. If you are *really* interested in the Mac portion of the ISO, you can do the following:

Grab the byte offset from the mount options file:
`sed -E "s/[a-z]+=//g" {ISOFILE}.mntopts.txt | sed -E "s/,/*/g" | bc`

Mount the HFS filesystem:
`sudo mount -o loop,ro,offset={BYTEOFFSET} -t hfs {ISOFILE} {MNTPOINT}`

Now that the filesystem is mounted, you can `cd {MNTPOINT}` and then read the files you are interested in. Don't forget to `umount {MNTPOINT}` when you are done.
