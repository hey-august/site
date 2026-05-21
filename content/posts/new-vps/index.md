---
title: Setting up a new VPS
date: 2026-03-04
---

[There is a lot to learn from doing something from the first time.](https://en.wikipedia.org/wiki/Shoshin)

I got a good deal on a server for Black Friday, but had been putting off actually doing anything with it.

Most of my development work is ai-assisted these days (whose isn't?), but I decided to start this long-delayed task in a decidedly old-fashioned way: by googling "Set up new root server". After all, this is a universal task for anyone building web-connected applications (even personal ones) that don't run on a hyperscaler. So there must be an abundance of learning resources... right? Maybe even a WikiHow article?

Not right. Instead there were a bunch of SEO-optimized how-to results. After skimming a few, I decided to trust this article:

https://docs.cherryservers.com/knowledge/basic-ubuntu-server-setup

I began following its instructions, capturing my observations, questions, and mistakes as I went, documented in the rest of this blog post. Section titles are mine. Who knows, maybe I'll turn it into that WikiHow article!

---

## First SSH

`ssh root@your_server_ip`

This was simple enough. I got an email from my cloud vendor with these details, and the password for the `root` user. I've been using, and preferring, SSH for GitHub authentication for some time now.

## Package manager housekeeping

`sudo apt update && apt upgrade -y`

I know what these commands do. Thanks to my recent experiments with Debian-in-Docker, and because I come to Unix by way of macOS, I think of **apt** as a clunkier Homebrew. (I'm sure this is a crude comparison to Linux diehards.)

At my current stage of understanding, I know apt has a more complex entry point than brew. Search and installation appear to use different base commands, for example: `apt-cache` and `apt-get`, respectively. 

I also know that apt can only install packages on its list, which must be manually fetched with `apt update`; and I know that it's common practice to delete the package list after use to save space in production deployments, though at this moment I can't remember the command and I don't care about space right now.

## User management

`adduser username`

Creating a non-root user. The password prompt makes sense. How to store this in my password manager? I suppose as a login, labeled "Server".

I'm prompted to enter text strings in subsequent prompts for Full Name, Room Number, Work Phone, Home Phone, and Other. I love the version of me this conjures; someone with a Room Number (!), like a professor with an office, or someone with their own corporate office. It's a lovely image.

`usermod -aG sudo username`

Giving my new non-root user sudo privileges.

`ssh-copy-id username@ip`

Setting up password-less authentication for my new user. Of course I first need to select a keypair. I like to use unique ssh keys for different purposes. I have never used `ssh-copy-id` before, but running it with `--help` showed me how to specify the key to copy over.

```
> ssh-copy-id -i my-key-name.pub username@ip
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "my-key-name.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
username@ip's password:

Number of key(s) added:        1

Now try logging into the machine, with: "ssh -i ./my-key-name 'username@ip'"
and check to make sure that only the key(s) you wanted were added.
```

The tool helpfully suggests logging in with `ssh -i ./my-key-name 'username@ip'`, which works beautifully.

## Firewall

```
sudo apt install ufw -y
sudo ufw allow OpenSSH
sudo ufw allow 2222/tcp
```

Now it's time to set up UFW. I'm not sure where my SSH service is running, so I'm enabling both options to be safe.

```
> sudo ufw enable
Firewall is active and enabled on system startup
```

Time to flip the switch and try logging back in! I left the other shell open just in case, but signin worked fine.

`sudo hx /etc/ssh/sshd_config`

You can use any editor for this, of course, but I had already installed [helix](https://helix-editor.com/) for fun. I changed `PermitRootLogin` to `no` and saved.

```
> sudo systemctl restart ssh
> sudo sshd -T | grep permitrootlogin
permitrootlogin no
```

Restart SSH daemon and test config.

Done!