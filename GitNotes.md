Git CheatSheet: https://education.github.com/git-cheat-sheet-education.pdf

# Overview
It’s important to understand that Git’s idea of a “working copy” is very different from the working copy you get by checking out source code from an SVN repository. Unlike SVN, Git makes no distinction between the working copies and the central repository—they're all full-fledged Git repositories.

This makes collaborating with Git fundamentally different than with SVN. Whereas SVN depends on the relationship between the central repository and the working copy, Git’s collaboration model is based on repository-to-repository interaction. Instead of checking a working copy into SVN’s central repository, you push or pull commits from one repository to another.

Of course, there’s nothing stopping you from giving certain Git repos special meaning. For example, by simply designating one Git repo as the “central” repository, it’s possible to replicate a centralized workflow using Git. This is accomplished through conventions rather than being hardwired into the VCS itself.

# Configure Git
Git comes with a tool called `git config` that lets you get and set configuration variables that control all aspects of how Git looks and operates.These variables can be stored in three different places:
- `[path]/etc/gitconfig` file: Contains values applied to every user on the system and all their repositories.
- `~/.gitconfig` or `~/.config/git/config` file: Values specific personally to you, the user.
- `config` file in the Git directory (that is, `.git/config`) of whatever repository you’re currently using: Specific to that single repository

You can view all of your settings and where they are coming from using:  
`$ git config --list --show-origin`  

**Your Identity**  
After installation, you should configure your name and email address to associate with any commits you make using the following commands:

`git config --global user.name "Your Name"`  
`git config --global user.email "your.email@yourdomain.com"`

ex: 
`git config --list`  
`git config --global user.name “SajinaKandy”`  
`git config --global user.email “sajina.kandy@xxx.com”`  

**Your Editor**  
`git config --global core.editor <your-favourite-editor>`  

**Your default branch name**  
By default Git will create a branch called **master** when you create a new repository with `git init`. To set **main** as the default branch name do:  
`$ git config --global init.defaultBranch main`  

**SSH setup**  
You can use SSH to perform Git operations in repositories on GitHub.com.   
Checking for existing SSH keys: Enter `ls -al ~/.ssh` to see if existing SSH keys are present.  
By default, the filenames of supported public keys for GitHub are one of the following:`id_rsa.pub , id_ecdsa.pub, id_ed25519.pub`  
Now, Either generate a new SSH key or upload an existing key.[Instructions](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).  
`pbcopy < ~/.ssh/id_ed25519.pub`  
On Github, click your profile photo, then click Settings. In the "Access" section of the sidebar, click  SSH and GPG keys. Add SSH key.  

# Create a fork
To create a fork, simply click on `Fork` near the top of the repository page on GitHub.  
<br>
The next step is to **clone** your forked OMR repository so that you can make changes locally. Navigate to the directory you'd like to clone the repository to, and enter the following command:

`git clone git@github.com:yourgithub/omr.git <directory-to-clone-into> <-b branch-to-check-out>`  
The parts within the angled brackets are optional.  

**Set the upstream for a fork**  

```
$ git remote -v
origin	git@github.com:SajinaKandy/openj9.git (fetch)
origin	git@github.com:SajinaKandy/openj9.git (push)
upstream	https://github.com/eclipse-openj9/openj9 (fetch)
upstream	https://github.com/eclipse-openj9/openj9 (push)
```
```
$ git remote add upstream https://github.com/eclipse/omr
fatal: remote upstream already exists.
```
```
$ git remote set-url upstream https://github.com/eclipse/omr
$ git remote -v
origin	git@github.com:SajinaKandy/omr.git (fetch)
origin	git@github.com:SajinaKandy/omr.git (push)
upstream	https://github.com/eclipse/omr (fetch)
upstream	https://github.com/eclipse/omr (push)
```
`$git remote set-url origin git@github.com:SajinaKandy/openj9.git`  
  
`$ git remote add upstream https://github.com/eclipse-openj9/openj9`  
  
Keep the fork updated  
```
git fetch upstream
git pull upstream master
```

`git diff master origin/master`  

# Create a new branch

```
git branch new-branch
git checkout new-branch
```
To do the 2 commands above in a single step, enter  
`git checkout -b new-branch`  

`git branch`, and it will show your local branches and highlight the branch you are currently working on.  

To checkout an existing branch, run the command:  
`git checkout BRANCH-NAME`  
Generally, Git won’t let you checkout another branch unless your working directory is clean, because you would lose any working directory changes that aren’t committed. You have three options to handle your changes:  
1) trash them, 
2) commit them, or 
3) stash them.

**Rename a Branch**  
To rename a branch, run the command:  
`git branch -m OLD-BRANCH-NAME NEW-BRANCH-NAME`  
Alternative  
`git branch --move OLD-BRANCH-NAME NEW-BRANCH-NAME`  

**Delete a Branch**  
Git won’t let you delete a branch that you’re currently on. You first need to checkout a different branch, then run the command:  
`git branch -d BRANCH-TO-DELETE`  

Alternative:
`git branch --delete BRANCH-TO-DELETE`  
<br>
The branch that you switch to makes a difference. Git will throw an error if the changes in the branch you’re trying to delete are not fully merged into the current branch. You can override this and force Git to delete the branch with the -D option (note the capital letter) or using the --force option with -d or --delete :  
`git branch -D BRANCH-TO-DELETE`  

Alternatives  
```
git branch -d --force BRANCH-TO-DELETE
git branch --delete --force BRANCH-TO-DELETE
```
**Compare Branches**  
You can compare branches with the git diff command:  
`git diff FIRST-BRANCH..SECOND-BRANCH`  

# Working with remote branch
You can reference those remote tracking branches ~(listed with `git branch -r`) with the name of their remote.  
You need to fetch the remote branch:  
`git fetch origin aRemoteBranch`  
If you want to merge one of those remote branches on your local branch:  
```
git checkout master
git merge origin/aRemoteBranch
```
**Note 1**: For a large repo with a long history, you will want to add the --depth=1 option when you use git fetch.  
**Note 2**: These commands also work with other remote repos so you can setup an origin and an upstream if you are working on a fork.  
<br>
**Opposite scenario**: If you want to merge one of your local branch on a remote branch (as opposed to a remote branch to a local one, as shown above), you need to create a new local branch on top of said remote branch first:  
```
git checkout -b myBranch origin/aBranch
git merge anotherLocalBranch
```
The idea here, is to merge "one of your local branch" (here anotherLocalBranch) to a remote branch (origin/aBranch).  
For that, you create first "myBranch" as representing that remote branch: that is the `git checkout -b myBranch origin/aBranch` part.  
And then you can merge anotherLocalBranch to it (to myBranch).  
Ex:  
———---  
1. Add nigel's repo as your remote `git add remote nigel git@github.ibm.com:xxnigel/openj9.git`
2. Fetch nigel's my-patching branch: `git fetch nigel my-patching`
3. Clone nigel's branch: `git checkout -b my-patching nigel/my-patching`
   
Ex:  
———--- 
```
git branch mul-changes
git checkout mul-changes
```
Switched to branch 'mul-changes'

you can add nigel's repo as you remote   
`git remote add nigel git@github.ibm.com:nigel/openj9.git`  

`git fetch nigel my-patching`  
to fetch his branch  

`git checkout -b my-patching yi/my-patching `  
to clone a local copy of my branch  
  
then do  
`git cherry-pick shaOfyourCode`  
to cherry-pick your code on top of mine.  
it picks the given sha and patch to the top of your current branch   
  
Ex:  
———--- 
**Fetch your branch from origin**  
1. git fetch origin huge_page_fix
2. git checkout -b huge_page_fix origin/huge_page_fix

# Stage and commit your changes

Staging files is just like "saving" them to include in a commit. To stage a file, enter the following command:  
`git add <filename>`  
<br>
You may also stage all the files you changed in a single step. The option following add determines what gets staged:  

`git add -u` - stage all modifications to existing files only  
`git add .` - stage all modifications to existing files and any new files added  
`git add -A` - stage everything, including deletion of files  
<br>
After staging, you may make a commit, and a text editor will open up where you will need to write a commit message. Enter the command below to make a commit:  
`git commit`  
<br>
**Tip**: `git commit -a` combines the 2 commands:  
```
git add -u
git commit
```

# Push branch to your fork

After you have committed, you may push your new-branch branch to your fork on GitHub that will create a new branch on your fork with the same name. To do so, enter the following command:  
`git push origin new-branch`  
In the command above, `origin` is an alias for the URL of your fork. By default, git sets `origin` to the "remote" repository you cloned from.  

# Make further changes and make corrections

Here are the steps to amending the last commit's changes and the message:  

1. Make your correction(s)
2. Stage the files that you corrected (or add the -a option to the command below)
3. `git commit --amend`
The command above opens a text editor that will allow you to edit the commit message. If you do not need to amend the commit message, then you may use the command `git commit -a --amend --no-edit` instead.  
<br>
If you want to back out earlier commits from your pull request and then add a better commit:  

1. git log (to get your current commit history, so you can decide how many recent commits you want to roll back)
2. git rebase -i HEAD~N (Use Interactive Rebase command to roll back N recent commits)
3. git log (to ensure that you have rolled back the commits you want to discard)
4. Now you can go ahead and make new changes and commit them using the following process.

Next step after making an amend is to update your branch to reflect your Pull Request. Unfortunately, you will not be able to push your changes as you did before, as your commit is now different from the one in your remote branch due to the amend you made resulting in your local and remote branches diverging. You will therefore have to overwrite your remote branch with your local branch. To do that, you need to make a force push to your `new-branch`:  

`git push -f origin new-branch`

# Amend
To modify the commit message you run the amend command:  
`git commit --amend`  
You can also edit the author  
`git commit --amend --author="SajinaKandy <sajina.kandy@xxx.com>"`  
To add or remove files/changes from the latest commit without editing its message, add the --no-edit flag:  
`git commit --amend --no-edit`  

# Contributing

```
git checkout -b Issue_4408
git status
git branch
git add -u  
git status
```
`git commit -v` OR `git commit -am “My Commit Message”` OR  `git commit --amend --no-edit`  
`git push -f origin Issue_4408` OR `git push origin Issue_4408`


`git commit --amend`  

`Closes: ##17011`  
`Signed-off-by: SajinaKandy <sajina.kandy@xxx.com>`  

# Rebase
```
git checkout demo-dev-1
git fetch upstream
git rebase upstream/master OR git rebase -i upstream/master  
git push -f origin demo-dev-1
```

Rebase and/or Squash whenever you want to refresh  
```
git checkout master
git pull --ff-only upstream master
git fetch upstream
git reset --hard upstream/master
git push origin master --force
```

Keep in mind, that this makes your repo look exactly like upstream - if you have commits, they will be eaten!  
If you want to update to upstream (but still keep your commits), you want `git merge upstream/master`.   
If you want to start a new patch based on upstream, the best way to do it is `git checkout -b my-new-feature upstream/master`  
Of course, this all expects you have a remote named `upstream`, which you can create by doing   
`git remote add upstream https://[upstream guy's repo url]`  


Suppose that some time has passed between the time you opened your PR and the time it was ready to be reviewed. In this case, it is a good idea to update your branch so that you may test your code changes being made on top of a more recent version of remote project repo. The process of doing that is known as **rebasing**. To explain rebasing, let's look at how you began developing on your `new-branch` branch. You created `new-branch` basing it on the `master` branch you got when you first cloned your fork. Let's suppose you added the commits `b1` and `b2` to `new-branch`.  

It is likely that more commits have been added to remote repo resulting your base (local `master` you created `new-branch` off of) branch being out of sync with remote's `master` branch that you are trying to merge your changes into.  

The goal of rebasing is to apply your commits on top of the current version of the branch you are trying to merge your changes into (in this case, `master` branch of remote repo).

**Rebasing** is also useful when you are trying to resolve **merge conflicts**. Merge conflicts arise when some other developer gets their commits merged into `master` in remote repo after the point where your branch is based on if your commits modify/delete the lines that are affected by their commits. Git cannot automatically determine what to keep and what to discard. You will have to manually make edits to those conflicting regions in the source files.
<br>
Suppose that you have a merge conflict that needs to be resolved. The first step would be to add the repository as a remote to fetch updates from. Let's alias the URL of the remote repo as `upstream`.

`git remote add upstream https://github.com/<remote-url>`  
Next, fetch the history from upstream. Performing fetch of a remote repository updates what git knows about its history.  

`git fetch upstream`  
Next, you need to rebase your changes on top of `upstream`'s `master` branch.  

`git rebase -i upstream/master`  
The `-i` option in the command above is the option to enter interactive rebase. It opens your text editor listing the commits being added from your branch (lines starting with `pick`) on top of upstream's master branch. There are many other things you can do in an interactive rebase (eg, reword, squash, change commit order, etc) but in this case, no edits to the rebase operations are needed. Saving and closing your editor will make git apply the commits in the order they were, until you hit the first merge conflict.

Git will tell you the files where there are unresolved conflicts. Open a conflicting file and look for `<<<<<<<`. This marks the beginning of a conflicting region. A conflicting region looks like this:  
```
<<<<<<
// existing code on upstream/master
========
// your changes
>>>>>>
```
`=======` is used for separating your changes from existing changes. `>>>>>>>` marks the end of the conflicting region. Manually edit the conflicting region and remove the 3 conflict region indicating lines.  

Once you are done resolving these conflicts, stage the edited files using `git add`.  
<br>
Now that your files are conflict-free and staged, rebase operation can be continued using the command below:  
`git rebase --continue`  
Repeat the conflict resolution steps if another conflict occurs.  
<br>
Rebasing diverges your local new-branch from your fork's new-branch since they are no longer based on the same commit history. Therefore, you will need to overwrite your remote branch with your local branch using the command below:  
`git push -f origin new-branch`  

# Squash commits into one with Git

`git rebase --interactive HEAD~[N] `                 <— N number of commits to squash  

Editor will open up with reversed order of commits   
```
pick d94e78 Prepare the workbench for feature Z     --- older commit
pick 4e9baa Cool implementation 
pick afb581 Fix this and that  
pick 643d0e Code cleanup
pick 87871a I'm ready! 
pick 0c3317 Whoops, not yet... 
pick 871adf OK, feature Z is fully implemented      --- newer commit
```
Convert to:
```
pick d94e78 Prepare the workbench for feature Z     --- older commit
s 4e9baa Cool implementation 
s afb581 Fix this and that  
s 643d0e Code cleanup
s 87871a I'm ready! 
s 0c3317 Whoops, not yet... 
s 871adf OK, feature Z is fully implemented      --- newer commit
```
[Our task here is to mark all the commits as squashable, except the first/older one: it will be used as a starting point.]  

Now create the new commit. You have just told Git to combine all seven commits into the first commit in the list. It's now time to give it a name: your editor pops up again with a default message, made of the names of all the commits you have squashed. Better to edit this commit message for clarity.  

# To stash an in-between commit

An alternative solution uses the stash:  
**Before**:  
`$git stash list`  

```
$git log --oneline -3
7049dd5 (HEAD -> master) c111  
3f1fa3d c222  
0a0f6c4 c333  
```
- 1 - git reset head~1 <--- head shifted one back to c222; working still contains c111 changes
- 2 - git stash push -m "commit 111" <--- staging/working (containing c111 changes) stashed; staging/working rolled back to revised head (containing c222 changes)
- 3 - git reset head~1 <--- head shifted one back to c333; working still contains c222 changes
- 4 - git stash push -m "commit 222" <--- staging/working (containing c222 changes) stashed; staging/working rolled back to revised head (containing c333 changes)
- 5 - git stash pop stash@{1} <--- oldest stash entry with c111 changes removed & applied to staging/working
- 6 - git commit -am "commit 111" <-- new commit with c111's changes becomes new head

**note** you cannot run 'git stash pop' without specifying the stash@{1} entry. The stash is a LIFO stack -- not FIFO -- so that would incorrectly pop the stash@{0} entry with c222's changes (instead of stash@{1} with c111's changes).  
**note** if there are conflicting chunks between commits 111 and 222, then you'll be forced to resolve them when attempting to pop. (This would be the case if you went with an alternative rebase solution as well.)  

**After**:  
```
$git stash list
stash@{0}: On master: c222
```
```
$git log -2 --oneline
* edbd9e8 (HEAD -> master) c111
* 0a0f6c4 c333
```
# Save the code you're working on.

You could use `git stash --all` to save the changes and any new files for all. Or you could create a "dummy" commit on your branch using `git commit -m a "WIP"` (which is my preference).  

# Diff
Your working copy and staging area:  
`git diff`  
Staging area and the latest commit:  
`git diff --staged`  
Your working copy and commit 4ac0a6733:  
`git diff 4ac0a6733`  
Commit 4ac0a6733 and the latest commit:  
`git diff 4ac0a6733 HEAD`  
Commit 4ac0a6733 and commit 826793951  
`git diff 4ac0a6733 826793951`  

`colordiff -w --suppress-common-lines /home/dev/repo/run/comp/net/abc.cpp /home/dev/WorkingDir/repo/run/com/net/abc.cpp` 

# Undo
The most commonly used undo tools are `git checkout`, `git revert`, and `git reset`. Some key points to remember are:

- Once changes have been committed they are generally permanent
- Use `git checkout` to move around and review the commit history
- `git revert` is the best tool for undoing shared public changes
- `git reset` is best used for undoing local private changes
In addition: `git log` for finding lost commits `git clean` for undoing uncommitted changes `git add` for modifying the staging index.
<br>
Use the checkout command in order to checkout the repository’s version of the   

`git checkout compiler/x/codegen/BinaryEvaluator.cpp`

<br>

```
git reset --hard origin/master
git reset —hard [discard all local changes]
git stash branch Issue_4408
```
`git-clean` - Remove untracked files from the working tree  

**Step 1** is to show what will be deleted by using the -n option:

Print out the list of files and directories which will be removed (dry run)  
`git clean -n -d`

**Clean Step** - beware: this will delete files:  

Delete the files from the repository  
`git clean -f`  

- To remove directories, run git clean -f -d or git clean -fd
- To remove ignored files, run git clean -f -X or git clean -fX
- To remove ignored and non-ignored files, run git clean -f -x or git clean -fx
Note the case difference on the X for the two latter commands.  

This command will recursively clean the main repository and all submodules. Warning: this will remove all untracked files as well.  
git clean -xffd && git submodule foreach --recursive git clean -xffd  
To see which files will be deleted without actually deleting yet, add the -n flag.  
git clean -nxffd && git submodule foreach --recursive git clean -nxffd  
