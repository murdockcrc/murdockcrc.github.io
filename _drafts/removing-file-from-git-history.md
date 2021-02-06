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

\`\`\`

git filter-branch --index-filter "git rm --cached --ignore-unmatch 'path/margevideo.'"

\`\`\`