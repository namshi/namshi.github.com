---
layout: post
title: "Git and Github at Namshi: our flow and tips&tricks"
date: 2014-05-04 20:38
comments: true
categories: [git, github, tips, tricks, namshi]
---

At [Namshi](https://www.namshi.com) for versioning and managing our large and assorted codebase we use [GIT](http://git-scm.com/) and of course [Github](http://github.com/namshi) (I like to call it "Facebook for nerds").

In the true spirits of "giving back to the community" we also sometimes release publicy some libs that we use internally, [feel](https://github.com/namshi/NamshiUtilityBundle) [free](https://github.com/namshi/utils) [to](https://github.com/namshi/notificator) [use](https://github.com/namshi/jose) [them](https://github.com/namshi/ng-watchers) [and](https://github.com/namshi/emailvision) [contribute!](https://github.com/namshi)

### Our GIT flow

Git is so powerful and flexible that at the beginning you feel kinda lost: *"How should I use it?"*  *"Should I use merge or rebase?"*  *"master, develop, release branches?"*
And the you try to find the answer via google and you find the solution: ["GIT FLOW!"](http://nvie.com/posts/a-successful-git-branching-model/).

> No, no, and again **NO!** (please say it loud).

"Git flow" is one of the several way to use Git, but it's not a standard and it could not fit well in your current organisation/codebase/team flow.

You should embrace the power of Git, and find your tailor-made solution.

At Namshi we basically have two main branches: master and develop.

On master you exactly have what's running on production, while develop is our development branch (surprisingly)

When we pick a task we usually create a branch from develop. Once we are done with a task we rebase our feature branch from develop, in order to be sure that the new
code works and doesn't break anything before being merged on develop. Then we open a pull request(PR) on Github. Once the PR is reviewed and approved
it gets merged in the develop branch.
There is (still) a lot of discussion (and flames) about using the rebase command, because basically it rewrites the current commits history.
It's true that potentially you can screw your codebase, but we are still safe because you can still rely on your teammates local repositories and
gain from the integrity, stability (before merging on the develop branch you can be sure that you didn't break anything) and clean commits history point of view.

Once we decide that we can ship new stuff we create a new branch (usually a release branch) tag it, deploy on staging and then live.
Then we merge the tag on master (with the [--no-ff option](https://www.kernel.org/pub/software/scm/git/docs/git-merge.html) and rebase develop with master.
Even here we use the rebase command because we end up with develop that has the same commits sequence as master plus new commits: it's so easy to spot the differences between the two branches.


### Git Config

There are several configuration settings that you can put in your `~/.gitconfig` file and get a lot of benefits on your daily git usage.

#### colors
Give some love to git outputs:

```
git config --global color.ui true
```

#### filemode
Tired of having weird `git status` output because some files has just changed file permissions?
```
git config --global core.filemode false
```

#### rerere
The git rerere functionality stands for *"reuse recorded resolution"* and as the name implies, it allows you to ask git to remember how you've resolved a hunk conflict so that the next time it sees the same conflict, git can automatically resolve it for you.
```
git config --global rerere.enabled true
```

#### global exclude
Instead of set everytime common files to ignore in your local .gitignore file (i.e. your IDE config file, .DS_Store etc) you can use a global ignore files
that will automatically apply to all your repos.
```
git config --global core.excludesfile ~/.gitignore_global
```
**tip:** I have a `my_temp` entry in my .gitignore_global file, so that I can create a cirpo_temp dir in my working copies and be free to put any experimental and temporary code/quick testing code
in it without changing project on my IDE and using the actual codebase.


#### pull with rebase, not merge
Everytime we pull from a remote repository, we do a `git pull --rebase` because we don't want to mess up with our local commits.
Instead of specify it everytime, you can make the default beaviour when you pull:
```
git config --global branch.auto-setuprebase always
```

#### autocorrect
Git can autocorrect you:

```
git config --global help.autocorrect true
```

#### default tracking
Bored of setting up tracking branches by hand?
```
git config --global push.default upstream
```

```
➜  namshi.github.com git:(source) ✗ git cmmit
git: 'cmmit' is not a git command. See 'git --help'.

Did you mean this?
	commit
```

### Git aliases
Git supports aliases via the `git config --global alias.<alias_name> "<git command>"`, but here in Namshi we tend to use the shell aliases
because git is the only revision control system and source code management we use.

#### checkout
```
alias ck='git checkout'
```

#### create a new branch
```
alias ckb='git checkout -b'
```

#### push
```
alias push='git push'
```

#### force push after a rebase
```
alias pushf='git push -f'
```
#### pull
```
alias pull='git pull'
```
#### sync main branches

```
alias align='git pull --rebase origin master && git pull --rebase origin develop'
```

#### fetch
```
alias fetch='git fetch'
```

#### commit
```
alias ci='git commit'
```

#### revert all changes
```
alias undo='git reset --hard HEAD'
```

#### delete merged branch
How many times you have so many branches in your local repo that are already merged?
Let's get rid of them!
```
alias dmb='git branch --merged | xargs git branch -d'
```

#### log
```
alias glog='git log --oneline --decorate'
```

#### status
A shorter version:
```
alias st='git status -sb'
```
#### diff
This only highlights the changed words, nicely inline.
```
alias gdiff='git diff --word-diff'
```

### Tips

* You messed up with a merge/rebase? Don't freak out, just use `git reflog` to check the local history command and sha1, and then `git reset --hard <sha1>`
* After rebasing develop in your feature branch you end up with too many conflicts and messed commits history? Don't panic, create a new branch from develop and
`git cherry-pick` all the commits you need.
* Are my commits already on branch X? `git branch --contains <sha1>` is there to rescue you.
* You want to fetch a file from another branch without changing your current branch? `git checkout <other_branch_name> -- path/to/file`

 Here you can find the complete list of [aliases](https://gist.github.com/cirpo/86d43ef8ef87b4cfd031), and [here](https://gist.github.com/cirpo/343376f1ac7fb00204a0) the global .gitconfig we are using

 Do you have any other tips or tricks to share? Write them down in the comments :)

