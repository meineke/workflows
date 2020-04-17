---
title: Secure Shell
---


## Working with SSH keypairs

### Motivation: Why bother with keys instead of (or in addition to) passwords?

- Necessity. You *need* keys in order to
	- Use many big data clusters and other systems that require non-interactive login
	- Log into EC2 instances on AWS, among other cloud services
	- In many contexts, deploy an app from a git repository
- Convenience and good habits
	- You can use the same keypair for many servers--just upload your public key
	- No typing in passwords, except to decrypt your private key
	- Easier interaction with GitHub means more frequent and atomic pushes
- Security
	- Your goal should be to actually type in a password as little as possible
	- If you can remember your GitHub password, it's probably a bad password--you reused it or made it too simple
	- You need to set up 2FA on github.com, but you have to switch to SSH auth to do so
	- *BUT* if you use keys wrong, they can be *less* secure

### How to get set up quickly

By default, your keys are stored in `~/.ssh/`. (Remember that `~` is short for your home directory, and because `.ssh` starts with a `.`, it's "hidden" because it holds configuration stuff and secret stuff.) Check whether you already have a keypair set up:

`ls ~/.ssh`

If you have files called `id_rsa` and `id_rsa.pub`, you've already generated a keypair. If not, just run `ssh-keygen` to generate one. Accept the default file path and then enter a passphrase when it prompts you--you absolutely don't want to have an unlocked private key lying around!

```
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/lm/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):   # it won't show anything when you type here
Enter same passphrase again:
Your identification has been saved in /home/lm/.ssh/id_rsa.
Your public key has been saved in /home/lm/.ssh/id_rsa.pub.
The key fingerprint is:
[...]
The key's randomart image is:
[...]
```

#### The private and public key files

Your new `id_rsa` file is your private key and you must never share it. Your `id_rsa.pub` file is your public key, and that's what you're going to copy to remote servers to allow your private key to authenticate you. Your public key is not sensitive at all.

#### The `ssh-keygen` command

`ssh-keygen` uses a pseudorandom number generator to get seed values to create a keypair. Some tutorials tell you to add various arguments to the command, for example

`$ ssh-keygen -t rsa -b 4096 -C you@email.com`

will generate a keypair using the RSA cryptosystem, 4096 bits in length, with a comment at the end identifying it as belonging to you@email.com. `-t rsa` is currently the default on almost all implementations of `ssh-keygen`. The default bit length is currently 2048. A 4096-bit key is far harder to crack and you can go ahead and use a 4096-bit key if you prefer, but the default 2048 bits is still (in 2020) adequate.

#### The SSH passphrase

The passphrase is used to encrypt your private key. If you don't set a passphrase, anybody who has access to your private key (`id_rsa` by default) can use it, which is quite risky.

When you enter the passphrase, your private key is decrypted on your own machine. You're not sending the passphrase to the server you're authenticating to, and in fact the server has no idea whether you're using a passphrase on your private key or not.

You can change your passphrase without having any effect on the public key, so even if you've already uploaded your public key somewhere, you can always add or change your private key's passphrase by doing `ssh-keygen -p`.

#### The importance of file paths

By default, SSH will look for your private key at the path `~/.ssh/id_rsa`. If you have multiple private keys, you'll have to specify the file path; for example, `ssh-add ~/.ssh/my_other_key`, or `ssh -i ~/.ssh/my_other_key me@server.school.edu`.

### Add your public key to GitHub

GitHub has instructions on [adding your public key](https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account).

#### But copying and pasting in the terminal is hard!

The <kbd>Ctrl</kbd> key is very special in terminals. In particular, <kbd>Ctrl</kbd>+<kbd>C</kbd> is for interrupting a running process or "canceling" your current line. Here are three quick ways of using the clipboard in a terminal:

- In Git Bash
