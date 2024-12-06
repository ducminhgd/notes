---
category: git
---
# Git

## Auto create remote branch if not exists

`git config --global --add --bool push.autoSetupRemote true`

## Permantly delete files in git history

Purpose: remove sensitive or secret-contained files

Steps:

1. Remove file
2. Add the file to `.gitingore`
3. Permantly remove a file from Git history: `git filter-branch --index-filter "git rm -rf --cached --ignore-unmatch <file-name>" HEAD`
4. Force push to re-write history `git push --force`.
