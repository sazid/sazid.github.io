---
title: Run levels in Linux
---

Self-documenting... going to try out **dwm** tiling window manager.
Seems pretty cool.

Run levels are *states* or *modes* that are defined by the services
listed in the `/etc/rc.d/rc<x>.d`, where `<x>` is the number of the
runlevel. More information: [Fedora guides:
Runlevels](https://docs.fedoraproject.org/en-US/Fedora/12/html/Deployment_Guide/ch-services.html#s1-services-runlevels).

The following runlevels exist:
* 0 - Halt
* 1 - Single-user mode
* 2 - Not used (user-definable)
* 3 - Full multi-user mode
* 4 - Not used (user-definable)
* 5 - Full multiuser mode (with an X-based login screen)
* 6 - Reboot

When logging in, if you need to login via a text-only screen then it
means you're running in runlevel 3. If a graphical login screen is
present, you're running in runlevel 5.

The default runlevel can be changed by modifying the **/etc/inittab**
file, which should contain a line similar to the following

```
id:5:initdefault:
```

Changing the number in this line to the desired runlevel. Need a
reboot to take effect.

**UPDATE**: I just found out that the linux system I'm using right now
  (Fedora 32 Workstation) does not use runlevels like this anymore. It
  uses `systemd` for that purpose. Quoting the `/etc/inittab` file in
  my system:

```bash
# inittab is no longer used.
#
# ADDING CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
#
# Ctrl-Alt-Delete is handled by /usr/lib/systemd/system/ctrl-alt-del.target
#
# systemd uses 'targets' instead of runlevels. By default, there are two main targets:
#
# multi-user.target: analogous to runlevel 3
# graphical.target: analogous to runlevel 5
#
# To view current default target, run:
# systemctl get-default
#
# To set a default target, run:
# systemctl set-default TARGET.target
```
