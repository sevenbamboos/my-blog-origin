---
title: Git & Github Basic
date: 2016-06-01 22:59:32
categories: Tool 
tags:
- Git
- Github
- cmder
---

# Git
- reference http://www.jianshu.com/p/6d1ce5b65523 and http://learngitbranching.js.org/?demo
- Install http://cmder.net/ (including MsysGit)
- Config
```
git config --global user.name "xxx" 
git config --global user.email "xxx@yyy"
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
git config --list //~\.gitconfig
```

## Check in
```
git init
git status
git add .
git commit -m "xxx"
git commit -a -m "xxx" // without git add
```

## Revert
```
git reflog //review commit id for revert
git reset --hard xxx //commit id or HEAD^ (the next version), HEAD^^ (the next next version)
git checkout -- filename //revert when there is no local change **added**
git reset HEAD filename && git checkout -- filename //revert **added** change, then revert local change
```

## Change
```
git rm xxx
git rm --cache xxx // remove tracked file, but keep it locally
git rm -f xxx // remove file physically.
git mv xxx yyy
```

## Branch
```
git checkout -b xxx // git branch xxx && git checkout xxx
git merge xxx
git branch -d xxx //delete branch
```

## gitignore when it doesn't work:
```
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```

# Github
## Generate SSH key
```
ssh-keygen -t rsa -C"mail" //id_rsa and id_rsa.pub
ssh -T github.com
```

## .ssh/config
```
Host github.com
User git
Hostname ssh.github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
Port 443
```

## Upload
```
git remote add origin git@github.com:sevenbamboos/test.git
git push --set-upstream origin master
git push origin master
git pull origin master
```
