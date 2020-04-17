---
title: SSH Keys
---

## TL;DR

- What is all this about?
    - Keypairs are a way of using two little text files (a private key and a public key) to authenticate yourself (prove you are who you say you are) instead of using a password.
    - The two parts of a keypair are linked: data that is encrypted with a public key can only be decrypted with the corresponding private key.
    - You do still use a passphrase to encrypt your private key, which is then a strong form of two-factor authentication.
    - You use keypairs in lots of contexts where passwords wouldn't work at all, wouldn't be secure enough, or would be inconvenient.
- Create a keypair:
    - Open a terminal (Git Bash in Windows, Terminal in MacOS) and type `ssh-keygen`
    - Accept default path and enter a passphrase
- Get it set up with GitHub:
    - Copy the contents of `~/.ssh/id_rsa.pub` to GitHub ([instructions](https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account))
    - Test out your keys by doing `ssh -T git@github.com`
    - Do ``eval `ssh-agent` `` (note the backticks), then `ssh-add`
    - Test out a `git push` to make sure everything works
- Get it set up with a server you'll be using:
    - `ssh-copy-id myusername@myserver.com`
    - `ssh myusername@myserver.com` to test

### Motivation: Why bother with keys instead of just using passwords?

- Necessity. You **need** keys in order to
	- Use many big data clusters and other systems that require non-interactive login
	- Log into EC2 instances on AWS, among other cloud services
	- In many contexts, deploy an app from a git repository
- Convenience and good habits
	- You can use the same keypair for many servers--just upload your public key
	- No typing in passwords, except to decrypt your private key
	- Easier interaction with GitHub means more frequent and atomic pushes
    - Many remote development workflows are difficult or impossible without using keys
- Security
	- Your goal should be to actually type in a password as little as possible
	- If you can remember your GitHub password, it's probably a bad password--you reused it or made it too simple
	- You need to set up 2FA on github.com, but you have to switch to SSH auth to do so
	- **But** if you use keys wrong, they can be **less** secure

### How to get set up quickly

By default, your keys are stored in `~/.ssh/`. (Remember that `~` is short for your home directory, and because `.ssh` starts with a `.`, it's "hidden" because it holds configuration stuff and secret stuff.) Check whether you already have a keypair set up:

```console
ls ~/.ssh
```

If you have files called `id_rsa` and `id_rsa.pub`, you've already generated a keypair. If not, just run `ssh-keygen` to generate one. Accept the default file path and then enter a passphrase when it prompts you--you absolutely don't want to have an unlocked private key lying around!

You'll see something like this:

```console
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

`ssh-keygen` uses a pseudorandom number generator to get seed values to create a keypair. Some tutorials tell you to add various arguments to the command, for example

`$ ssh-keygen -t rsa -b 4096 -C you@email.com`

will generate a keypair using the RSA cryptosystem, 4096 bits in length, with a comment at the end identifying it as belonging to you@email.com. `-t rsa` is currently the default on almost all implementations of `ssh-keygen`. The default bit length is currently 2048. A 4096-bit key is far harder to crack and you can go ahead and use a 4096-bit key if you prefer, but the default 2048 bits is still (in 2020) adequate.

#### The SSH passphrase

The passphrase is used to encrypt your private key. If you don't set a passphrase, anybody who has access to your private key (`id_rsa` by default) can use it, which is quite risky. It's easy to screw up and accidentally put your private key on a server you don't control, for example.

When you enter the passphrase, your private key is decrypted on your own machine. You're not sending the passphrase to the server you're authenticating to, and in fact the server has no idea whether you're using a passphrase on your private key or not.

You can change your passphrase without having any effect on the public key, so even if you've already uploaded your public key somewhere, you can always add or change your private key's passphrase by doing `ssh-keygen -p`.

#### The importance of file paths

By default, SSH will look for your private key at the path `~/.ssh/id_rsa`. It's simplest if you just keep the default.

If you do for whatever reason have multiple private keys, you'll have to specify the file path; for example, `ssh-add ~/.ssh/my_other_key`, or `ssh -i ~/.ssh/my_other_key me@server.school.edu`.

### Add your public key to GitHub

GitHub has instructions on [adding your public key](https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account).

I recommend giving your new public key a descriptive name so you know, for example, what laptop you generated it on. Then you can delete it when you get rid of that laptop.

#### But copying and pasting in the terminal is hard!

The <kbd>Ctrl</kbd> key is very special in terminals. In particular, <kbd>Ctrl</kbd>+<kbd>C</kbd> is for interrupting a running process or "canceling" your current line. Here are three quick ways of using the clipboard in a terminal:

- In the default Git Bash terminal:
    - Right-click the title bar `->` Options... `->` Keys `->` Ctrl + Shift + letter shortcuts (should be checked)
    - Now you can copy/paste with <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>C/V</Kbd>
- If you have an <kbd>Ins</kbd> key on your keyboard:
    - <kbd>Ctrl</kbd>+<kbd>Ins</kbd> / <kbd>Shift</kbd>+<kbd>Ins</kbd> to copy/paste
- In many/most terminals, you can right-click to paste, and highlight with the cursor then right-click to copy

### Use the `ssh-agent` so you don't have to type your passphrase

**Mac users:** This one is simpler for you. You should be prompted to save your key to the keychain the first time you use it. You can then skip the rest of the `ssh-agent` stuff.

**Windows users:** `ssh-agent` is a little service that runs on your laptop and caches your decrypted private key. As long as it's running and has your decrypted key in memory, you don't have to use your passphrase. 

```
lm@laptop:~$ eval `ssh-agent`
Agent pid 13379
lm@laptop:~$ ssh-add
Enter passphrase for /home/lm/.ssh/id_rsa:
Identity added: /home/lm/.ssh/id_rsa (/home/lm/.ssh/id_rsa)
```

#### That's still too much typing

GitHub has some [instructions](https://help.github.com/en/github/authenticating-to-github/working-with-ssh-key-passphrases#auto-launching-ssh-agent-on-git-for-windows) that give you a chunk of code you can paste into a special config file in your home directory called `.profile`. This will automatically start the `ssh-agent` when you run Git Bash.

### Using your keys to log into servers

Now let's put your public key on a server you'll be accessing. MSiA students: try doing this with wolf.

`ssh-copy-id myusername@myserver.com`

You should get some helpful output. Now try `ssh myusername@myserver.com`. You should get right in without getting a prompt.

So ssh keys aren't just for GitHub! Being able to silently authenticate to a server gives you lots of nice options.

### Working with your GitHub repositories from a server

So let's say you have a private repository on GitHub with your code, and you need to pull it and run it on a server. How do you do it? By using "ssh agent forwarding."

Open up `~/.ssh/config`. Create the file if it doesn't already exist. Add these lines, substituting in your own username and server:

```
Host myserver
HostName myserver.edu
UserName myusername
ForwardAgent yes
```

Now when you ssh to your server, you'll still be able to use your locally cached credential. There's more detail on this in [GitHub's documentation on agent forwarding.](https://developer.github.com/v3/guides/using-ssh-agent-forwarding/)

**Weird Unix-y thing:** If you get a permissions error about `.ssh/config`, change the permissions:

`chmod 600 ~/.ssh/config`
