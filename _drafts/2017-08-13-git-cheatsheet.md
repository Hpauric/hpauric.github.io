---
published: false
---
##Git undo command
Git revert is the command to use if you  want to undo a commit. It will keep the commit in history.
Using these commands
If a commit has been made somewhere in the project's history, and you later decide that the commit is wrong and should not have been done, then git revert is the tool for the job. It will undo the changes introduced by the bad commit, recording the "undo" in the history.
If you have modified a file in your working tree, but haven't committed the change, then you can use git checkout to checkout a fresh-from-repository copy of the file.
If you have made a commit, but haven't shared it with anyone else and you decide you don't want it, then you can use git reset to rewrite the history so that it looks as though you never made that commit.
