# Git Cheat Sheet

Git cheat sheet

## Table of Contents

- [Git Cheat Sheet](#git-cheat-sheet)
  - [Table of Contents](#table-of-contents)
    - [Most Used Commands](#most-used-commands)
  - [SSH and GPG config](#ssh-and-gpg-config)
    - [SSH](#ssh)
    - [GPG](#gpg)
    - [GIT with GPG to have verify commits](#git-with-gpg-to-have-verify-commits)
  - [Telling Git about your signing key](#telling-git-about-your-signing-key)

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


### GIT with GPG to have verify commits

https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key

1. Generate new gpg key:
	`gpg --full-generate-key`

1. List the long form of the GPG keys for which you have both a public and private key. A private key is required for signing commits or tags:
	`gpg --list-secret-keys --keyid-format=long`

1. From the list of GPG keys, copy the long form of the GPG key ID you'd like to use. In this example, the GPG key ID is `3BB5C34371567XF2`:

	```bash
	gpg --list-secret-keys --keyid-format=long
	/Users/hubot/.gnupg/secring.gpg
	------------------------------------
	sec   4096R/3BB5C34371567XF2 2016-03-10 [expires: 2017-03-10]
	uid                          Hubot <hubot@example.com>
	ssb   4096R/4BB6D45482678BE3 2016-03-10
	```
2. Paste the text below, substituting in the GPG key ID you'd like to use. In this example, the GPG key ID is `3BB5C34371567XF2`:

	```bash
	gpg --armor --export 3AA5C34371567BD2
	# Prints the GPG key ID, in ASCII armor format

	# To export the secret use:
	gpg --armor --export 3AA5C34371567BD2
	```

3. Add your GPG public key to Github

## Telling Git about your signing key

1. If you have previously configured Git to use a different key format when signing with --gpg-sign, unset this configuration so the default format of openpgp will be used.
	`git config --global --unset gpg.format`

1. To set your primary GPG signing key in Git, paste the text below, substituting in the GPG primary key ID you'd like to use. In this example, the GPG key ID is the one from before `3AA5C34371567BD2`:
	`git config --global user.signingkey 3BB5C34371567XF2`

1. Optionally, to configure Git to sign all commits by default, enter the following command:
	`git config --global commit.gpgsign true`

1. Finally, add your GPG key to your .bashrc startup file:
	```bash
	[ -f ~/.bashrc ] && echo 'export GPG_TTY=$(tty)' >> ~/.bashrc
	source ~/.bashrc
	```
1. If there's another user set in the global configuration then explicitly set the correct username and email:
2.
	```bash
	git config --local user.email "github_commit_email@users.noreply.github.com" ; git config --local user.name "change_me" ; git config --local --unset gpg.format ; git config --local user.signingkey 3BB5C34371567XF2
  ```
