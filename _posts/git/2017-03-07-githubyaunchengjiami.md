---
layout: blog
istop: true
title: "GitHub as a private version control system remote repository privatization"
background-image: https://icitum.github.io/web/soa-images/2017-06-116099051.jpg
date:  2017-03-07
category: git
tags:
- github
- git-crypt
- encfs
- gpg
- git-remote-gcryp
- sshfs
---
 
## Purpose
 
I plan to manage all server configuration files with git so that I can record configuration changes. However, one question is: How do people collaborate? The server configuration information is very sensitive. If this repository leaks, the entire company's server architecture will be completely leaked. This repository can only be decrypted on the developer's local computer. The server hosting the repository remotely should not know the contents of the file.

The solution is: The local git repository is decrypted. During the upload process, the content is encrypted and the key is stored locally. At the same time, the key can be shared with other developers.

### considered several solutions:

1. ``git-crypt``: It is possible to encrypt part of the file. The principle is to add the encrypted fitter and diff, but the official said that it is only suitable for encrypting part of the file, but not for full repository encryption. Part of the file encryption is very easy to cause information leakage, it must be full repository encryption is suitable.

2. Concatenate ``sshfs`` and the remote server to encrypt the file system ``encfs``: First use ``sshfs`` to load the remote file system and then use ``encfs`` to create an encrypted file system. I estimate that it is impossible to solve the race condition in the case of multiple people at the same time with ``push``, and encfs have security holes. It is not very convenient to load two file systems before using ``push/pull``.

3. ``git-remote-gcryp``t uses ``gpg`` for remote encryption. More in line with the pattern I expected, but using ``gpg`` is not particularly convenient for collaboration. But other methods don't work. Only this method is available.

#### Instructions


###### Install git-remote-gcrypt and gnupg
```
sudo apt-get install git-remote-gcrypt gnupg
```
###### Create a gpg key,
  Need to set user name, email, description, etc. Do not set expiration time
```
gpg --gen-key
```
###### Record the ID of the generated key.

For example, liberxue013 inside 2048R/liberxue013, 2048 represents the number of encryption rounds, the more difficult it is to crack
```
gpg --list-keys
```
###### Generate a test repository
```
mkdir test1 && cd test1
git init .
echo "test" > a.txt
git add . && git ci -m "update"
```
##### Generate a test repository ㅁᄆ Create a test project

Create a project on your github, for example: https://github.com/icitum

######  Configure the remote encryption repository
```
git remote add cryptremote gcrypt::git@github.com:liberxue/liberxue.git
```
###### It is best to specify which key to use for encryption
  This can share this key for others
```
git config remote.cryptremote.gcrypt-participants "liberxue013"
```
###### push to the remote
```
git push cryptremote master
```
* Visit the remote repository to see if the contents of the file and the information in the commit are all encrypted?

## How to share with others


##### Export Key
```
gpg --export-secret-key -a "share@share.com" > secretkey.asc
```
- Share secretkey.asc to others. When copying, remember to compress and send it before sending it.

###### Imported from other people's computers
```
gpg --import secretkey.asc
```
###### Download Code
```
git clone gcrypt::git@github.com:liberxue/liberxue.git test2 // test2 is a git clone in the local text
File name
```
###### Also specify what key to use for encryption
```
git config remote.cryptremote.gcrypt-participants "liberxue013"

```

In this way, you can use ``git`` to manage some private and collaborative information (such as server configuration), or to use github as a private version control system (commit messages are in plain text).



