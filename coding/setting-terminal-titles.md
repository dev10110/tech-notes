---
layout: default
title:  Setting Terminal Titles
date:   2023-01-09
parent: Coding
---

# Setting Terminal Titles

Add this function to the `~/.bashrc`:
```
function set-title() {
  if [[ -z "$ORIG" ]]; then
    ORIG=$PS1
  fi
  TITLE="\[\e]2;$*\a\]"
  PS1=${ORIG}${TITLE}
}
```

Source the bash file.

Run 
```
set-title NEWTITLE
```


Source: https://unix.stackexchange.com/questions/177572/how-to-rename-terminal-tab-title-in-gnome-terminal
