## FuseJS - Low level bindings for Fuse
[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/c4milo/fusejs?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://travis-ci.org/c4milo/fusejs.svg)](https://travis-ci.org/c4milo/fusejs)

Fusejs are a set of NodeJS bindings for [Fuse](http://fuse.sourceforge.net/) low level API.
It allows you to write filesystems, in userspace, using Javascript and NodeJS.
Even though most of the Fuse functions are already binded, this module has not
been used in production. Use it at your own risk.

## How it works
Fuse low level API is inherently asynchronous, therefore, there is no need for libuv thread pool.

FuseJS workflow looks like:

```                          
          node example/hello.js /tmp/hello_fs -ofsname=hellofs -orw -d
                                         ↕
                                Google V8 / FuseJS
Flow starts here!                        ↕
	ls -lah /tmp/hello_fs             libfuse
         	  ↕                          ↕
       		glibc                      glibc
Userspace     ↕                          ↕
---------------------------------------------         
Kernel        ↕                          ↕
			  ↕                          ↕
             VFS ↔ ↔ ↔ ↔ ↔ ↔ ↔ ↔ ↔ ↔ ↔  FUSE

             							...
                                        Ext4
                                        NFS
                                        ZFS
```

## Fuse operations supported
The following Fuse low level operations are fully supported:

* **init:** Initializes filesystem. Called before any other filesystem method
* **destroy:** Cleans up filesystem. Called on filesystem exit
* **lookup:** Looks up a directory entry by name and get its attributes.
* **forget:** Forgets about an inode. The nlookup parameter indicates the number of lookups previously performed on this inode. If the filesystem implements inode lifetimes, it is recommended that inodes acquire a single reference on each lookup, and lose nlookup references on each forget. The filesystem may ignore forget calls, if the inodes don't need to have a limited lifetime. On unmount, it is not guaranteed that all referenced inodes will receive a forget message.

* **getattr:** Gets file attributes
* **setattr:** Sets file attributes
* **readlink:** Reads symbolic link
* **mknod:** Creates file node. Create a regular file, character device, block device, fifo or socket node.
* **mkdir:** Creates a directory
* **unlink:** Removes a file
* **rmdir:** Removes a directory
* **symlink:** Creates a symbolic link
* **rename:** Renames a file
* **link:** Creates a hard link
* **open:** Opens a file
* **read:** Reads data
* **write:** Writes data
* **flush:** Flushes data. This is called on each close of an opened file. Since file descriptors can be duplicated (dup, dup2, fork), for one open call there may be many flush calls.Filesystems shouldn't assume that flush will always be called after some writes, or that it will be called at all.
* **release:** Releases an open file. Release is called when there are no more references to an open file: all file descriptors are closed and all memory mappings are unmapped. For every open call there will be exactly one release call.
* **fsync:** Synchronizes file contents
* **opendir:** Opens a directory
* **readdir:** Reads a directory
* **releasedir:** Releases an open directory. For every opendir call there will be exactly one releasedir call.
* **fsyncdir:** Synchronizes directory contents
* **statfs:** Gets file system statistics
* **setxattr:** Sets an extended attribute
* **getxattr:** Gets an extended attribute
* **listxattr:** Lists extended attribute names
* **removexattr:** Removes an extended attribute
* **access:** Checks file access permissions. This will be called for the access() system call. If the 'default_permissions' mount option is given, this method is not called. This method is not called under Linux kernel versions 2.4.x
* **create:** Creates and opens a file. If the file does not exist, first create it with the specified mode, and then open it. If this method is not implemented or under Linux kernel versions earlier than 2.6.15, the mknod() and open() methods will be called instead.
* **getlk:** Tests for a POSIX file lock
* **setlk:** Acquires, modifies or releases a POSIX file lock. If the locking methods are not implemented, the kernel will still allow file locking to work locally. Hence these are only interesting for network filesystems and similar.
* **bmap:** Map block index within file to block index within device. This makes sense only for block device backed filesystems mounted with the 'blkdev' option
* **ioctl (not supported yet):** Forever ioctl
* **poll (not suppported yet):** Polls for IO readiness

## Installation
### OSX
In order to use FuseJS you need to install any of the Fuse implementations for OSX. OSXFuse is the one that has been used throughout the FuseJS development. Go to http://osxfuse.github.com/ and follow the instructions to get it installed. Additionally, FuseJS toolchain uses `pkg-config`, you need to have it installed in your system as well, in order to compile FuseJS. It usually should come by default in your operating system, if not, then use your package manager to install it.

* download https://github.com/downloads/osxfuse/osxfuse/OSXFUSE-2.5.2.dmg
* sudo port install pkgconfig (OSX)
* ```npm install fusejs```


### Linux
* sudo apt-get install libfuse-dev libck-dev
* ```npm install fusejs```

## API Documentation
All the API Documentation can be found at:

* https://github.com/c4milo/fusejs/blob/master/fuse.js.

You can also take a look at the examples in: (TODO!)

* https://github.com/c4milo/fusejs/tree/master/examples


## TODO
* Fix signal handling, specially SIGINT. Whenever the filesystem is mounted and the nodejs process receives SIGINT, FUSE signal handlers don't seem to be getting called.
* Filesystem examples
* **Tests >_>**

## License
(The Mozilla Public License v 2.0 License)
Copyright 2015 FuseJS Authors. All rights reserved.

This Source Code Form is subject to the terms of the Mozilla Public
License, v. 2.0. If a copy of the MPL was not distributed with this
file, You can obtain one at http://mozilla.org/MPL/2.0/.
