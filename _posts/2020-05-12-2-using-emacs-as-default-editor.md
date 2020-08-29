---
---

I've learnt the basics of vim a few years ago and I've been able to
*just use it* whenever necessary. I felt quite comfortable in editing
small config files with vim. However, I've tried to use it for large
codebases and everytime I tried it, for some reason, I just couldn't
work with it properly.

Recently, I've started using linux as my daily driver again (took a
break when I started play Apex Legends on Windows :p). This time,
linux seems so much more usable to me. Gnome 3 has become quite
efficient and I think it just stands out on its own as a separate kind
of desktop environment similar to OSX and Windows. I used to dislike
how slow and inefficient Gnome 3 was, that's not the case anymore!.
Well... I still don't like the really big title bars in gui
applications (take not from Windows & OSX!). However, I love the
overview screen when you press the Super key (Windows key).

So, as part of the ongoing endeavour I decided to give emacs a go
again (learnt the basics once but forgot everything...). This time
I've decided to dedicate more time to know about the application
properly. Anyway, now that I've decided to dedicate my time to emacs,
I'll need to stop using vim for basic editing for sometime, so I
needed to configure the terminal default editor. In order to do that,
just put the following in the `~/.profile`.

```bash
# Set emacs as the default EDITOR
VISUAL="/usr/bin/emacs -nw"
export VISUAL
EDITOR="/usr/bin/emacs -nw"
export EDITOR
```

That should be it! Oh btw, if you need to use the Menu bar available
in emacs terminal at the top of the window, press `F10`.
