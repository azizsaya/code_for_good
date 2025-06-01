
### basic set-up
```bash
git clone https://github.com/azizsaya/code_for_good.git
git remote -v
git config --global user.name "Aziz Saya"
git config --global user.email "azizsaya@gmail.com"
git config --list --show-origin
git status
git add .
```
### for initializing a repo locally
```bash
git init
```

### alias for log git add, commit and push commands in one?
```bash
git config --global alias.cmp '!f() { git add -A && git commit -m "$@" && git push; }; f'
```
https://stackoverflow.com/questions/19595067/git-add-commit-and-push-commands-in-one

usage: ```bash git cmp "Long commit message goes here" ```


### alias for log
```bash
[alias]
	lg = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset' --abbrev-commit --date=relative
```
usage: ```bash git lg ```

https://www.reddit.com/r/git/comments/1ajh2ll/my_favorite_alias_for_git_log/


### incase the origin isn't set
```bash
git push -u origin master
```

### difference between git pull and git fetch

In the simplest terms, git pull does a git fetch followed by a git merge.

git fetch updates your remote-tracking branches under refs/remotes/<remote>/. This operation is safe to run at any time since it never changes any of your local branches under refs/heads.

git pull brings a local branch up-to-date with its remote version, while also updating your other remote-tracking branches.

From the Git documentation for git pull:

git pull runs git fetch with the given parameters and then depending on configuration options or command line flags, will call either git rebase or git merge to reconcile diverging branches.



### git stash

This video is a tutorial on the **git stash** command, explaining how it works with live animations and real-time command execution. The key points covered include:

- **Purpose of Git stash**: It allows users to temporarily save uncommitted changes and revert their working directory to a clean state.
- **Basic usage**: Running `git stash` stores tracked changes, while `git stash list` shows saved stashes.
- **Managing multiple stashes**: Users can name stashes (`git stash -m "name"`) and apply specific ones using `git stash pop --index`.
- **Creating branches from stashes**: The `git stash branch` command lets users reincarnate stashed changes onto a new branch.
- **Deleting stashes**: Commands like `git stash drop` (removes a specific stash) and `git stash clear` (removes all stashes) are explained.
- **Handling merge conflicts**: The video briefly touches on resolving conflicts when applying stashed changes.

The tutorial is structured with animations to visually demonstrate how Git stash operates under the hood. If you're interested in Git workflow optimization, this video provides a clear and engaging breakdown! You can watch it [here](https://www.youtube.com/watch?v=BSLzA8oCT7g).

### good resources:

https://github.com/aashimaahuja/git-advanced