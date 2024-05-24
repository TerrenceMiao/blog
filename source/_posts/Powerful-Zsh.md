---
title: Powerful Zsh
date: 2022-08-02 19:42:06
tags:
---

![Powerful Zsh](/img/Powerful%20Zsh.png "Powerful Zsh")

First you have `Zsh`, next install `Oh My Zsh` https://ohmyz.sh/

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Add `Powerlevel10k` _https://github.com/romkatv/powerlevel10k_ and configure it

```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

Set `ZSH_THEME` to **powerlevel10k** in `.zshrc`

```
ZSH_THEME="powerlevel10k/powerlevel10k"
```

Then run `Powerlevel10k` configure:

```
p10k configure
```

Add `zsh-autosuggestions` _https://github.com/zsh-users/zsh-autosuggestions_

```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

Add `zsh-syntax-highlighting` _https://github.com/zsh-users/zsh-syntax-highlighting_

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

and enable them in `.zshrc`:

```
...
plugins=(
  zsh-autosuggestions
  zsh-syntax-highlighting
)
...
```

Install `Fig` (**deprecated**) _~~https://fig.io/~~_, an IDE-style autocomplete but for terminal, and configure in `.zshrc`

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