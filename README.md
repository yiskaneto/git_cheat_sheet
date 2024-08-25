# Git Cheat Sheet

Git cheat sheet

## Table of Contents

- [Git Cheat Sheet](#git-cheat-sheet)
  - [Table of Contents](#table-of-contents)
    - [Most Used Commands](#most-used-commands)
  - [SSH and GPG config](#ssh-and-gpg-config)
    - [SSH](#ssh)
  - [GPG](#gpg)
    - [Reduce GPG passphrase prompts](#reduce-gpg-passphrase-prompts)
    - [GIT with GPG to have verify commits](#git-with-gpg-to-have-verify-commits)
    - [Telling Git about your signing key](#telling-git-about-your-signing-key)
  - [Pass Basic Tutorial](#pass-basic-tutorial)
    - [Clone existing pass repos](#clone-existing-pass-repos)

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

## GPG

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


### Reduce GPG passphrase prompts

When you installed GnuPG, it also installed and runs a service named gpg-agent.exe that is responsible for caching this passphrase so you’re not prompted for it on every single commit. The default TTL for a cached passphrase however is 10 minutes. This is quite short for me, I’m not making commits every 10 minutes so it just ended up feeling like I was prompted for this passphrase every time. gpg-agent.exe contains some configuration options so we can adjust this behavior.

1. List all of gpg-agent.exe current configuration options using the following:
    `gpgconf --list-options gpg-agent`

1. The options we need to configure are `default-cache-ttl` and `max-cache-ttl`. Both of these are configured with the number of seconds to cache the passphrase. The screenshot below shows the default values for both configuration options; `default-cache-ttl` set to 600, and `max-cache-ttl` set to 7200.

1. Create conf file:
    `vim ~/.gnupg/gpg-agent.conf`

1. Add the following text:

```
default-cache-ttl 86400
default-cache-ttl-ssh 86400
max-cache-ttl 86400
max-cache-ttl-ssh 86400
```
1. Reload gpg:
    `gpgconf --kill gpg-agent`

1. Verify properties
    `gpgconf --list-options gpg-agent`

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

### Telling Git about your signing key

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

	```bash
	git config --local user.email "github_commit_email@users.noreply.github.com" ; git config --local user.name "change_me" ; git config --local --unset gpg.format ; git config --local user.signingkey 3BB5C34371567XF2
    ```


## Pass Basic Tutorial

https://medium.com/@davidpiegza/using-pass-in-a-team-1aa7adf36592

1. Generate a GPG Key, you must provide an email and remember it

    `gpg --full-generate-key`

1. Initialize pass by adding associated to the email provided to create the gpg key:

    `pass init -p <store name> <email used to create the gpg key>`

    For example:

    `pass init -p newPasswordStore my@email.com`

    >Note: You can check the initialized password store under `${HOME}/.password-store/`

1. Add a new password to the store, you will be prompted to add the secrets

    ```bash
    pass insert random_folder/AWS_ACCOUNT_NUMBER
    pass insert random_folder/AWS_PROFILE
    pass insert random_folder/AWS_REGION (us-west-1)
    pass insert random_folder/DEMO_TENANT)
    pass insert random_folder/GITHUB_OAUTH_TOKEN
    pass insert random_folder/NEW_S3_BUCKET
    pass insert random_folder/OLD_S3_BUCKET
    ```

1. Add multiple secrets into 1 secret

    `pass insert -m random_folder/AWS_ENV_VARS`

- Following the example on point 3 you can now export the variable referencing the values stored on Pass

    ```bash
    export AWS_REGION=$(pass random_folder/AWS_REGION)
    export TENANT=$(pass random_folder/DEMO_TENANT)
    export AWS_ACCESS_KEY=$(pass random_folder/AWS_ACCESS_KEY)
    export AWS_SECRET_ACCESS_KEY=$(pass random_folder/AWS_SECRET_ACCESS_KEY)
    export GITHUB_OAUTH_TOKEN=$(pass random_folder/GITHUB_OAUTH_TOKEN)
    ```

- Now you can export the variables using eval

    `eval $(pass random_folder/AWS_ENV_VARS)`

- Retrieving password

    `pass random_folder/AWS_ACCOUNT_NUMBER`

- Edit passwords:

    `pass edit aws_env_vars`


### Clone existing pass repos

- mkdir -p  ~/.password-store/
