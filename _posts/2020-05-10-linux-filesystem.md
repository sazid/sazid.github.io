---
---
Linux file names are case-sensitive.

Folders are referred to as Directories (if you're coming from
Windows). There are no *Local Disks* in Linux, everything is stored in
the root directory. However, you can mount different directories to
different partitions or storage devices if you want.

The linux file system layout is defined in the FHS (Filesystem
Hierarchy Standard). However, some linux distributions don't really
follow it exactly. Several directory structuring styles have also
changed over the years.

/ - Root directory contains everything that is needed to run the
system. If the linux kernel is the brain, the root directory is akin
to the heart of the system.

/bin - Short for binary. Contains the most basic binaries necessary
for the system, are present here such as ls, cat, etc.

/sbin - System binaries. These are binaries that a system
administrator would use. A standard user won't have access to this
directory without proper permissions.

Both the /bin and /sbin directories contain the binaries that are
necessary for running the system in single user mode.

/boot - Contains everything an os needs to boot. Better not touch
anything here.

/cdrom - legacy mounting point for CD ROMS. Might not be present in
all distros.

/dev - Devices live here. Hardware devices such as keyboard, mouse,
hard disks, etc are present. Disks are referred to by the files sdx.

/etc - etcetera. This directory contains system wide configuration
files.

/lib, /lib32, /lib64 - libraries are stored here. Required for
different binaries.

/media, /mnt - Contains mounted drives such as hard disks, usb drives,
hard drives, etc. The /media directory is used in recent distros to
manage disk drives by the system. When manually mounting something,
use the /mnt and let the /media directory to be managed by the os.

/opt - optional directory. Usually contains vendor provided packages.
You can place the applications that you created here.

/proc - contains information about system processes and resources. You
cnan also find information about cpu such as "cat /proc/cpu" and find
out the uptmie of the system "cat /proc/uptime". As of writing this,
/proc/cpu does not seem to be present in my Fedora Workstation 32.

/root - Home directory for the root user. Unlike a typical user, it
does not contain all the directories found inside a user's home
directory. Note that, this resides outside the /home directory. You
need root permission to access files in this directory. Why is the
root directory present in /root and not in /home/root? The answer is,
so that the root user can access his/her home directory even if the
/home directory is mounted on another partition or disk which may not
be available.

/run - fairly new, found in recent distros. Its a tempfs (temporary
file system), which means everything present here resides in the RAM.
So everything is gone, when you shut down/reboot the system.

/snap - [not standard] contains the Snap package management related
files and directories. This is found in Ubuntu and any system that
utilizes the snap packaging system.

/srv - Service directory. Service data is stored here for example when
you run a web server or a ftp server, you would want to place your
files or directories that you want to serve, here for the users to
access.

/sys - System directory. Present around for a long time. Its a way to
interact with the kernel. Similar to the /run directory in that, this
directory does not actually physically exist, it is created evertime
the system boots up.

/tmp - Temporary directory - Used by applications to store temporary
files here such as files in word processors that you are currently
editing, etc. Should be safe to delete stuffs from this directory.

/usr - User application space. Applications used by the users are
stored here as opposed to /bin and /sbin which contains applications
for the system. Also known as "Unix System Resource". Applications
present here are considered non-essential for the os to function
properly. Most program installed from source code will usually end up
in /usr/local directory. Larger programs might install into the
/usr/share directory. Installed source code will go into /usr/src.

/var - Variable directory. Contains files and directories that are
expected to grow in size. For example /var/log contains the logs for
different applications, /var/crash contains crash logs, /var/cache to
store different cache items.

/home - Home directory for users. Each user has his/her own home
directory in the format /home/username. Contains all user related data
such as user specific configuration files, cache, etc.
/home/username/.themes and /home/username/.icons contain themes and
icons that are available to the current user. If you want to save all
your settings, this is the directory that you want to backup. After
upgrading or updating to a new system, restoring this directory means
that all your settings will stay as is even if you reinstall
applications.
