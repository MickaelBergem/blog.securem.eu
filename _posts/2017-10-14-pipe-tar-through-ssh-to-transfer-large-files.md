---
layout: post
title: "Pipe tar through SSH to transfer large files"
description: ""
category: "Tips and tricks"
tags: []
---
{% include JB/setup %}

## Using tar and ssh to efficiently transfer large files across hosts

Ever ran out of disk space to create a (compressed) archive of a folder you want to backup or transfer to another host?

Here is the solution: **directly pipe the output of `tar`** (used to create archives of files and folders, and also often used to compress this final archive) **through ssh** to retrieve the compressed archive on another host. This allow you to backup large volumes of data when the source host doesn't have enough space to store the original data + a compressed archive of the data.

From the distant host (the host you want to copy the backup to):

    # Execute this on the host that will receive the data from the source server
    ssh user@source-server "tar czpf - /some/important/data /some/other/file/or/folder" | tar xzpf - -C /backup/destination/folder

This will copy the two files/folders into `/backup/destination/folder` on the destination host (the one executing the command).

If on the other hand, you want to **send** the data to another server and you are executing the SSH commands from the source server, use the following command:

    # Execute this on the machine with the data available locally
    tar cpf - /some/important/data /some/other/file/or/folder | ssh user@destination-server "tar xpf - -C /backup/destination/folder/"

