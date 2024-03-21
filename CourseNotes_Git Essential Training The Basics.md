git system level configurations that will apply to every user of this computer by default.
/etc/gitconfig
User level:
~/.gitconfig
Project level:
my_project/.git/config

to set or modify this configuration in diff level
git config --system %%% : system level.

---

        git config --global %%% : user level.

---

In Project directory:
git config

        git help log == man git-log


     Bullet points are usually asterisks or hyphens.

Can add "tracking numbers" from bugs or support requests.

    commit message example:

[github-issue-num] short description
blank line
long message

           git log --since=yyyy-mm-dd

           grep abbreviation for "globally search for regular expression.


         git diff

show us difference between files in the working directory and staging tree.

           git diff --staged

show us the difference between the file in the staging tree and repository

            --staged == --cached

0:04:31 git diff --color-words
show us word that changed.

        git   diff sha1..sha1

compare 2 commits

      when we renaming a file, all that minor changes that are in there get folded in, as well.

git checkout -- <file>
to discard changes in working directory.
-- to specify you want to discard file change not changing a branch.

git reset HEAD <file>
to unstage them

git commit --amend

- rpevious commit will be discarded. <br> be careful about amend commit that has been pushed to remote repository.
- make us be able to add changes to the last commit because no commits are depending on it's SHA-1, <br> this is not possible for the previous ones.

to retrieve a specific version of [file] from previous commit.
git checkout commit-SHA -- [file]
it will bring in your stagging tree the file after changes in the desired commit.

git revert commit-SHA1

git [-i -f -n] clean
delete untracked files in the repository.

ignore compiled code and [packages and compressed files]
ignore assets by users
ignore log files and database
ignore operating systems generated file.

How to make ignore pattern on user specific [globally] not repository specific ?
git config --global core.excludesfile ~/.[gitignore_global] <-- any filename you chose.

       how to ignore tracked file?

--
git rm --cached [file_name]

git diff View difference between Stage and Working Directory
git diff --staged View difference between HEAD and Stage
git diff HEAD View difference between HEAD and Working Directory

GIT:
is designed to be a file tracking system
Tracks files
Tracks the directories it takes to get to files
Ignore directories with no files

    git ls-tree HEAD

list all files and folders tracked in the HEAD commit.

it's a convention when you need to track empty directory add .gitkeep file in it.

---------------NEW AREA-----------------
git is a content addressable storage system.

blob commits stored in .git/objects/hash
stagging area only keep track of files not directory so we cannot store empty directory.

to print the file type that generate a hash
git cat-file -t [hash]

to print the content
git cat-file -p [hash]

---

References - pointers to a commit:
tags.
branches.
HEAD - a pointer to the current commit.
.git/HEAD
.git/refs/heads <- where branches live.

---

to rename branch
git branch -m oldName newName
if u want to rename the HEAD branch.
git branch -m newName

to delete branch
git branch -d name
force delete[has not merged workd].
git branch -D name
u cannot delete HEAD branch
--
Git file managment:

- untracked
  - Stay in directory
- Staged
  - Stay in staged state.
- Committed
  - changes not visible when switch branch.

---

git add -p
allows u to stage commits in hunks, interactively.
it's useful if u've done too much work for one commit.

working area --add-> staging area --commit--> repo
working area <--checkout-- staging area
working area <--checkout branch|sha -- repo

---

stash
save un-committed work.
the stash is safe from destructive operations
git stash --include-untracked

name stash for easy reference:
git stash save "refName"

git stash [list]
git stash [apply] stash@{}

## git stash -p [select interactively what u want to include]

git diff [different between working area and staging area]
git diff --cached [different between staging area and committed]
git diff HEAD [different between working area and committed ]
--
each command ask 2Q:

- how does this command move info across the 4 Areas[stash, working area, Index, Repository]?
- how does this command change the Repository?
  - ## checkout [branch].
    git rm --cached file
    remove new file from stagging area. [make them untracked]
    git rm -f file
    ------HEAD FIRST GIT--------

why we need version control:

- keep src files safe.
- retain a history of changes.
- treat and attribute credit (or blame!)
- allow for seamless collaboration between multiple team members.

---

# Tree-ish

- A directory containing files and other directories.<br>
  which Git calls "tree" or any identifier that refers to a tree.<br>
- A commit is considered tree-ish because it refers to a tree at the point<br>
  when a commit has been applied.
  - tree-ish can be a commit, a branch, a tag, HEAD,or a tree.
- directory in git is called tree.
  - to list its contents: git ls-tree [tree-ish]
    - to list a specific directory: git ls-tree [tree-ish] [path]
  - blob(binary large object) is a file in git.

---

- to refer to a parent commit of a commit de14521f^ or de14521f~1. HEAD^ or HEAD~1. branchName^ or branchName~1.

---

# filter

- git log --author="name"
- git log --grep="pattern"
- git log -S"pattern"
- git log --since="2 weeks ago"
- git log --until="2 weeks ago"
- git log --since="2014-01-01" --until="2014-02-01"
- git log --after="2014-01-01" --before="2014-02-01"
- git log sha..sha
- git log filename

# format

- git log -p
- git log --stat
- git log --pretty=oneline
- git log --graph
- git log --graph --oneline --decorate --all

# branching

- git branch branchName <- create branch
- git checkout branchName <- switch to branch

  - move state from that branch in repo to my working area and index. modify HEAD

- git checkout -b branchName <- create and switch to branch
- git checkout -- file <- discard changes in working directory

# swithcing with uncommited changes

- if the changes are not conflicting with the branch you are switching to, git will switch to <bt>the branch and bring the changes with you. if not, git will not switch to the branch and <br>will tell you that you have uncommitted changes that conflict with the branch you are trying to switch to.

# comparing branches

- git diff branchName..branchName
- git diff --color-words orderBranch...branchName

- If I want to find out what other branches have all of their commits merged into this branch already, then I can type git branch, and then pass in the dash dash merged option. <br> shorten_title branch. If I want to find out what other branches have all of their commits <br>merged into this branch already, then I can type git branch, and then pass in the dash dash merged option.
- Knowing which branches are fully included in another branch is very useful, especially when we start deleting branches. <br> especially when we start deleting branches. We can make sure that we're not accidentally deleting <br>We can make sure that we're not accidentally deleting un merged commits. un merged commits.

<hr>

# Rename branch

- git branch -m oldName newName
- you've shared these branches with someone else. We haven't talked about that yet. We'll learn about working <br>and collaborating remotely. Once you do that, you don't want to start renaming your branches if other people are already <br>using them by another name. At that point, you'll start having to force the changes when you push them up, and then it may get confusing. <br>So you either rename things before you start sharing them with other people, as long as it's only you working on it, it's not a problem. Or you're going<br> to have to jump through a few more hoops, if you want to push out a new name for other people to use as well, so that you can all stay working in sync.<br>

# Delete branch

- git branch -d branchName
- git branch -D branchName
  - if u checkedout to a branch and u want to delete it u have to checkout to another branch first.
  - if u want to delete a branch that has a commits that not been merged u have to use -D.
  <hr>
  bash profile, or if you have a bashRC file. Those are files that the command line automatically runs every time you open a window.

<hr>

# Reset

- Reset changes the files in the staging index/or working directory to the state <br>they had when a specific commit was made.
  - options;
- mixed(default) to wd.
  - move all changes until the specified commit to the working directory.
  - used if u want to reorganize commits.
  - unstage: git reset HEAD files. -> make index == last commit
- hard copy state of the commit to the index and working area.

  - when you want to make one branch look like another
  - when you want to discard all changes since the specified commit.
  - u made #commits and u realize u don't need them .<br> then reset --hard to the last one u want.
  - ignore all changes in index and wa : git reset --hard HEAD

- soft just move the changes until the specified commit to the staging area.
  - git reset ---soft <tree-ish>
    - if u want to include previous changes until a specific commit in the staging area u can use --soft.

<hr>

# Merging

- One good piece of initial advice is to always run merges with a clean working directory. You'll only further complicate things<br> if you have some changes in your working directory while you're doing merges. You can either commit those working directory <br>changes or eventually, we'll learn how to stash them
- fast forward merge: when the branch that you're merging in has all of its commits included in the branch that you're merging into. <br>So, if you're merging in a branch that has all of its commits included in the branch that you're merging into, then Git will just move the pointer <br>forward to the branch that you're merging in. It'll just move the pointer forward to the branch that you're merging in.
- true merge: when the branch that you're merging in has commits that are not included in the branch that you're merging into. <br>So, if you're merging in a branch that has commits that are not included in the branch that you're merging into, then Git will create a new commit <br>that includes all of the changes from both branches. It'll create a new commit that includes all of the changes from both branches.
- conflicting
  - A conflict occurs when there are two changes in the same line or set of lines in two different commits.
  # resolving conflicts
  - abort : git merge --abort
  - merge tool : git mergetool
  - manual : open the file and fix the conflicts manually.
  # reducing conflicts
  - merge often
  - keep lines short
  - avoid merge commits
  - avoid rebase
  - avoid long lived branches:
    - Some developers use feature flags so that the feature is present in the master code but it's not turned on unless a certain flag is triggered.<br>We're not going to go into depth about feature flags but it's a technique that allows us to keep merging back in those feature branches and all <br>the code into master regularly to reduce merge conflicts without actually activating the feature or the code for the public.
    - Another good strategy is to track changes to master. This is a bit like merging often but in reverse. Instead, when we have master we have our<br> text edits branch, master has some more changes. Now, we can merge those changes from master back into our text edits branch. <br>So now text edits has all of master's changes. They've been resolved.
