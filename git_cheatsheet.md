
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

### good resources:

https://github.com/aashimaahuja/git-advanced