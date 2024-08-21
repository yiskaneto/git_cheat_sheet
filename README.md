# Git Cheat Sheet

Git cheat sheet

## Table of Contents

- [Git Cheat Sheet](#git-cheat-sheet)
  - [Table of Contents](#table-of-contents)
    - [Most Used Commands](#most-used-commands)
  - [SSH and GPG config](#ssh-and-gpg-config)

### Most Used Commands


- Delete all branches except main or master, pull, fetch and prune tags and the print out the repo status:
  ```bash
  git checkout main || git checkout master ; for branch in $(git branch | awk {'print $1$2'} | grep -Ev "^master$|^\*master$|^main$|^\*main$") ; do git branch -D ${branch} ; done && git pull && git fetch --all --prune --prune-tags && git status
  ```

  ## SSH and GPG config

  1. https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key:
	 ```bash
	 mkdir -p ~/.ssh ; chmod -R 700 ~/.ssh
	 ssh-keygen -t ed25519 -C <email>
	 ```

  1. Add you public key to github
  1. Authenticate your dev machine to github:
  	`ssh -T git@github.com`

  1. if a password was set for the ssh key then you can add it to the ssh-agent:
  	`eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519`

  1. Set the global email and user:

  	```bash
  	git config --global user.email "github_commit_email@users.noreply.github.com" ; git config --global user.name "change_me"
  	```
