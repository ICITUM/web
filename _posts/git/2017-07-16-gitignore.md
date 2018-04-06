---
layout: blog
istop: true
title: "Git ignores the correct posture of rules and .gitignore rules"
background-image: https://icitum.github.io/web/soa-images/2017-06-116099051.jpg
date:  2017-07-16 23:45:56
category: git
tags:
- github
- git
- gitignore
- Git ignores rules
---

# Realize the demand
If you want to ignore a file or folder in git and do not want to submit the file or folder to the repository, you can use the .gitignore file in the root directory (if you don't have one, you need to manually create it). Each line of this file holds a matching rule such as:

# Create gitignore file

```
touch .gitignore
```
## Comment Git ignores rules
```
# This is a comment - will be ignored by Git
 
*.a       # Ignore all files ending in .a
!lib.a    # But except lib.a
/-liberxuesite     # Just ignore the liberxuesite file in the project root, excluding subdir/liberxuesite
liberxue/    # Ignore all files in the liberxue folder/directory and the folder itself
doc/*.txt # Ignoring doc/notes.txt but not including doc/server/arch.txt
```
## gitignore ignores the rule

The rules are very simple, do not explain too much, but sometimes in the project development process, suddenly whim to want to add some directories or files to ignore the rules, according to the above method definition and found that did not take effect, because gitignore can only ignore those Originally, there is no track file. If some files have been included in version management, modifying .gitignore is invalid. The solution then is to first delete the local cache (change to the untrack state), and then submit:

```
git rm -r --cached .
git add .
git commit -m 'update .gitignore'

```