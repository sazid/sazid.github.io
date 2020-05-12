---
---

Self-documenting... going to try out **dwm** tiling window manager. Seems pretty
cool.

Run levels are *states* or *modes* that is defined by the services listed in the
**/etc/rc.d/rc<x>.d**, where **<x>** is the number of the runlevel. More
information: (Fedora guides: Runlevels)[https://docs.fedoraproject.org/en-US/Fedora/12/html/Deployment_Guide/ch-services.html#s1-services-runlevels].

The following runlevels exist:
* 0 - Halt
* 1 - Single-user mode
* 2 - Not used (user-definable)
* 3 - Full multi-user mode
* 4 - Not used (user-definable)
* 5 - Full multiuser mode (with an X-based login screen)
* 6 - Reboot

When logging in, if you need to login via a text-only screen then it means
you're running in runlevel 3. If a graphical login screen is present, you're
running in runlevel 5.

The default runlevel can be changed by modifying the **/etc/inittab** file,
which should contain a line similar to the following

```
id:5:initdefault:
```

Changing the number in this line to the desired runlevel. Need a reboot to take
effect.