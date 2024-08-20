# Git Cheat Sheet

Git cheat sheet

## Table of Contents

- [Git Cheat Sheet](#git-cheat-sheet)
  - [Table of Contents](#table-of-contents)
    - [Most Used Commands](#most-used-commands)

### Most Used Commands

```bash
git checkout main || git checkout master ; for branch in $(git branch | awk {'print $1$2'} | grep -Ev "^master$|^\*master$|^main$|^\*main$") ; do git branch -D ${branch} ; done && git pull && git fetch --all --prune --prune-tags && git status
```