---
title: Powerful Zsh
date: 2022-08-02 19:42:06
tags:
---

![Powerful Zsh](/blog/img/Powerful%20Zsh.png "Powerful Zsh")

First you have `Zsh`, next install `Oh My Zsh` https://ohmyz.sh/

```
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Add `Powerlevel10k` https://github.com/romkatv/powerlevel10k and configure it

```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

p10k configure
```

Set `ZSH_THEME` to **powerlevel10k** in `.zshrc`

```
ZSH_THEME="powerlevel10k/powerlevel10k"
```

Add `zsh-autosuggestions` https://github.com/zsh-users/zsh-autosuggestions and enable in `.zshrc`

```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

```

```
...
plugins=(
  zsh-autosuggestions
)
...
```

Install `Fig` https://fig.io/, an IDE-style autocomplete but for terminal, and configure in `.zshrc`

```
...
# Fig pre block. Keep at the top of this file.
[[ -f "$HOME/.fig/shell/zshrc.pre.zsh" ]] && . "$HOME/.fig/shell/zshrc.pre.zsh"
...
# Fig post block. Keep at the bottom of this file.
[[ -f "$HOME/.fig/shell/zshrc.post.zsh" ]] && . "$HOME/.fig/shell/zshrc.post.zsh"
...
```

References
----------

- How To Make Your Boring Mac Terminal So Much Better, _https://www.youtube.com/watch?v=CF1tMjvHDRA_
- How To Setup Your Mac Terminal, _https://www.josean.com/posts/terminal-setup_