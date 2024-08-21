# Git Cheat Sheet

Git cheat sheet

## Table of Contents

- [Git Cheat Sheet](#git-cheat-sheet)
  - [Table of Contents](#table-of-contents)
    - [Most Used Commands](#most-used-commands)
  - [SSH and GPG config](#ssh-and-gpg-config)
    - [SSH](#ssh)
    - [GPG](#gpg)

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


### SSH

- Restart SSH:
  `sudo systemctl restart sshd`

- SSH version:
  `ssh -V`

- man files:
  `man sshd_config`

- Ciphers:
  `ssh -Q cipher`

- Copy public key to remote server if we have access using password:
  `ssh-copy-id -i <path to <key>.pub <user>@server`

- SSH option to avoid checking the host key and to not add the host to our known_host file:
  `-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null`

- Grep SSH option, for example keep alive:
  `ssh -TG git@github.com |grep -i alive`

- Remove passphrase from ssh key
  `ssh-keygen -p -f <key>`

### GPG

- Generate a GPG Key

  `gpg --full-generate-key`

  >note: you can create multiple key, just make sure to add a distinc name, email and comment in each one so is easier to recognize each key.

- List public GPG keys
  `gpg --list-keys`

- List private GPG keys
  `gpg --list-secret-keys`

- Export public key
  `gpg --export -a <email address associated to the gpg key> > pub`

- Export private key
  `gpg --export-secret-key -a <email address associated to the gpg ato> > mykey@example.com_gpg_private.key`

- Import public/private key (For private keys the passphrase will be asked)
  g`pg --import public.key`

- List public keys
  `gpg --list-public-keys`

- List private keys
  `gpg --list-secret-keys`
