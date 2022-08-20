---
title: "TIL: Git push.autoSetupRemote"
date: 2022-08-20
description: 'Never run into "fatal: The current branch your-favorite-branch has no upstream branch." again'
---

You can prevent running into the following error:

```bash
> git push        
fatal: The current branch your-favorite-branch has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin your-favorite-branch 
```

Solution: 

```bash
git config --global --add --bool push.autoSetupRemote true
```
