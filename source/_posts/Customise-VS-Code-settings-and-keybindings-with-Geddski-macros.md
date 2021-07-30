---
title: Customise VS Code settings and keybindings with Geddski macros
date: 2021-07-30 20:28:19
tags:
---

In IntelliJ IDEA, you can comment a line, the cursor is moved to the next line automatically. This is a very easy way to comment several lines. However, in VS Code, default behaviour is that the cursor stays on the same line.

To copy the behavior of IntelliJ, go with:

- Install `macros` author by **geddski** in VS Code.

- Edit `settings.json` and add:

```
    "macros": {
        "commentDown": [
            "editor.action.commentLine",
            "cursorDown"
        ]
    },
```

- Edit `keybindings.json` and add:

```
[
  {
    "key": "cmd+/",
    "command": "macros.commentDown",
    "when": "editorTextFocus && !editorReadonly"
  }
]
```