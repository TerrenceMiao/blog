---
title: >-
  Making VSCode like IntelliJ after line commented the cursor automatically moved
  to next line
date: 2018-06-16 15:44:06
tags:
---

Want to make VSCode key bindings as closed as IntelliJ key bindings like.

Firstly, install VSCode extension **macros**.

![macros](/blog/img/macros%20extension.png "macros")

Open Code -> Preferences -> Settings. Add following configuration into **User Settings**.

```bash
    "macros": {
        "commentLine": [
            "editor.action.commentLine",
            "cursorDown"
        ]
    }
```

![User Settings](/blog/img/User%20Settings.png "User Settings")

Open Code -> Preferences -> Keyboard Shortcuts. Add following configuration into **keybindings.json** file.

```bash
// Place your key bindings in this file to overwrite the defaults
[
    {
        "key": "cmd+/",
        "command": "macros.commentLine",
        "when": "editorTextFocus && !editorReadonly"
    }
]
```

![keybindings.json](/blog/img/keybindings%20json.png "keybindings.json")

That's newly added Toggle Line Comment looks like after the change:

![Toggle Line Comment](/blog/img/Toggle%20Line%20Comment.png "Toggle Line Comment")