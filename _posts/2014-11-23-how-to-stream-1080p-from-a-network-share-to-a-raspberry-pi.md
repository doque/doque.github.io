---
layout: post
title: "How to stream 1080p from a Network Share to a Raspberry Pi"
description: "This post shows you how to set up your raspberry pi to stream full-HD files from a network share without lag or stutter"
category: 
tags: ["raspberry pi", network, 1080p, media]
---
{% include JB/setup %}

I've set up a Raspberry Pi as a media TV recently only to find out that the performance of streaming HD files from my network shares was far from optimal. Even though the Pi is connected via Ethernet, I'd face stutter and lags. I've shared the files using standard Windows SMB network shares and then moved to NFS (which resulted in slightly better performance, but still wasn't quite satisfactory).

The solution I've found is that you can mount your NFS shares locally by placing mount points in your `/etc/fstab` which acts as a virtual directory on your Pi (without taking up any space at all):

    pi@raspbmc:~$ cat /etc/fstab
	proc            /proc           proc    defaults         0       0
	devpts          /dev/pts        devpts  rw,nosuid,noexec,relatime,gid=5,mode=620        0       0
	/dev/mmcblk0p1  /boot           vfat    defaults,noatime         0       0
	/dev/mmcblk0p2  /               ext4    defaults,noatime 0       0

	# this is the line that matters
	192.168.0.100:/movies /media/movies          nfs      udp,noatime,rsize=32768,wsize=32768,nolock,nfsvers=3
	
If you change your `/etc/fstab` file, you will need to re-mount using `sudo mount -a`. 
The `rsize` and `wsize` parameters seem to do the trick.

Once you've done that, you need to add that newly mounted directory `/media/movies` to your sources. You can do so by either manually adding the source or editing `~/userdata/sources.xml` on your pi directly:

    <sources>
        <programs>
            <default pathversion="1"></default>
        </programs>
        <video>
            <default pathversion="1"></default>
            
            <!-- Our NFS shared source -->
            <source>
                <name>movies</name>
                <path pathversion="1">/media/movies/</path>
                <allowsharing>true</allowsharing>
            </source>
            
        </video>
        <music>
            <default pathversion="1"></default>
        </music>
        <pictures>
            <default pathversion="1"></default>
        </pictures>
        <files>
            <default pathversion="1"></default>
        </files>
    </sources>

The best part - you only need to do this once. Everytime you restart the Pi it will automatically re-mount the NFS share. Also you don't need to worry about storage - the NFS share is only linked virtually and files are copied to your Pi's memory card.
