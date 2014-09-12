---
layout: post
id: 2756
alias: let-each-window-have-its-own-command-history
tags: zsh
date: 2014-09-12 12:17:16
title: 让每一个窗口都有自己单独的command history
---

如果用的是`zsh`，则在`~/.zshrc`中加入：

```shell
export HISTCONTROL=ignoredups:erasedups  # no duplicate entries
export HISTSIZE=100000                   # big big history
export HISTFILESIZE=100000               # big big history
setopt extendedhistory                      # append to history, don't overwrite it

# Save and reload the history after each command finishes
export PROMPT_COMMAND="history -a; history -c; history -r; $PROMPT_COMMAND"
```

如果是`bash`，则在`~/.bashrc`中加入：

```shell
export HISTCONTROL=ignoredups:erasedups  # no duplicate entries
export HISTSIZE=100000                   # big big history
export HISTFILESIZE=100000               # big big history
shopt -s histappend                      # append to history, don't overwrite it

# Save and reload the history after each command finishes
export PROMPT_COMMAND="history -a; history -c; history -r; $PROMPT_COMMAND"
```