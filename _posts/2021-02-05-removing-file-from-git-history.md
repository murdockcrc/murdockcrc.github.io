---
layout: ''
title: Removing file from Git history
date: 2021-02-05 23:00:00 +0000
tags: []

---
# Intro

You might want to remove some files commited to source control. This is the case with:

* Secrets accidentally checked into SCM.
* Large binary files which make the size of the repo too large.

# Git considerations

Removing the file from the repo and creating a new commit will remove the file from the file system, but the file remains in the Git history.

Entirely removing a file requires you to rewrite the repo's history since the time that file was written into it.

```

git filter-branch --index-filter "git rm --cached --ignore-unmatch 'path/largevideo.mp4'"

```

You have now removed the file from your local history.

If you `git pull` now, you will get the file again, as it still lives in the Git history in the server. We now need to remove the file from the server's Git history:

```
git push --force
```

Other team members must now pull from the server, as their histories are now different and they won't be able to push.