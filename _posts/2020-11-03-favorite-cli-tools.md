---
---

A personal list of some of the best CLI tools I've found so far
and using on a regular basis.

- `exa`          (replacement for `ls`, [GitHub](https://github.com/ogham/exa))
- `fd`           (replacement for `find` but more user friendly, [GitHub](https://github.com/sharkdp/fd))
- `bat`          (replacement for `cat` but sooo much better!, [GitHub](https://github.com/sharkdp/bat))
- `rg (ripgrep)` (replacement for `grep`, searches for content inside files, super fast!, [GitHub](https://github.com/BurntSushi/ripgrep))

All of these are written in rust, neat! I also aliased all of
them:

```
alias ls="exa --long"
alias li="exa --icons"
alias cat="bat"
alias find="fd"
alias grep="rg"
```

