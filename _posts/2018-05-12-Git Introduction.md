---
layout: post
title:  "Git Introduction"
date:   2018-05-12 9:00:11 +0800
author: Ye Zhongkai
categories: Original Article
tags: Git GIT
---

## What is VCS?
I bet you've try to developed (V)ersion (C)ontrol (S)ystem prototype by yourself before. ie. you a product manager, you write a requirement document named v1.0, 
the document need be reviewed by your boss, your client and your teammates. Then you collect all suggestions and modify the document, naming it 
version v1.1. This operation makes everyone understand that the document is been upgraded when you send it to them, also lets you easily start revising from
any version.

![vcs1]({{ site.url }}/images/git_introduction/vcs1.png)

Imagine that there are multiple product managers work together. You are one of them. You guys are preparing for one requirement document. Of course, you can
notify others each time when you are about to start writing, and you guys can transfer the file to each other and merge them manually. In this case,
the efficient way is leverage a vcs.  
Here is a short description from Wikipedia

    The need for a logical way to organize and control revisions has existed for almost as long as  
    
    writing has existed, but revision control became much more important, and complicated when the  
     
    era of computing began. The numbering of book editions and of specification revisions are   
    
    examples that date back to the print-only era. Today, the most capable (as well as complex)   
    
    revision control systems are those used in software development, where a team of people may   
    
    change the same files.
    
    Version control systems (VCS) most commonly run as stand-alone applications, but revision 
    
    control is also embedded in various types of software such as word processors and 
    
    spreadsheets,  collaborative web docs[2] and in various content management systems, 
    
    e.g., Wikipedia's page history. Revision control allows for the ability to revert a document 
    
    to a previous revision, which is critical for allowing editors to track each other's edits, 
    
    correct mistakes, and defend against vandalism and spamming.
    
    Software tools for version control are essential for the organization of multi-developer 
    
    projects.

In software domain, version control system used to provide source code management ability. There are several vocabularies we need to
 understand, ie. `commit`, `branch`, `tag`, `checkout`, `conflict`, `merge`, `clone`, `pull`, `fetch`, `push` which will explain in the following content.


## What is Git?

![git]({{ site.url }}/images/git_introduction/git.jpg)  
 
Let's begin from the history of git. Here the great Linux will be mentioned. At very beginning, changes to Linux kernel were maintained by patches and archived files.
The inconvenient lets the project start to use a vcs called BitKeep. Good time doesn't last long, BitKeep free-charge access was revoked by BitKeep developer. So Linux 
 kernel project community developers start to build their own vcs, then the Git was born in such a background.

So what is Git? Here is the definition and slogan from git official website.

    Git is a free and open source distributed version control system designed to handle everything
    
    from small to very large projects with speed and efficiency.

## Why we need Git?
After knowing the definition of Git, then we will figure out why we need Git? There is a key concept `Distribute Version Contrl System` we need to understand.

### Local Version Control Systems
Local version control system means that all versions of file are stored in the local hard disk. We checkout them locally. The most famous local vcs is [**RCS**](https://www.gnu.org/software/rcs/)
RCS store the patches between each version of the file on the local disk, it can re-create the file by adding up all the patches at that point.
![Local Version Control System]({{ site.url }}/images/git_introduction/Local Version Control System.png)  

### Centralized Version Control Systems
Doing versioning locally is inconvenient when multiple users want to maintain a same file. In this situation, people need a sever to maintain different versions of the file and people can communicate with
this serve and checkout the file they want. Leverage a serve to do this is called (C)entralized (V)ersion (C)ontrl (S)ystem.
There are several VCVS system that been used widely. ie. `SVN`, `CVS`, `Perforce` 
![Local Version Control System]({{ site.url }}/images/git_introduction/Centralized Version Control System.png)  

### Distributed Version Control Systems
Centralized version control system have one weakness point is that it's can't allows you to commit your file when server is unavailable. Imagine that one night a great idea comes out from your mind, and you
decide to start coding, but your house network can't access to company's VCS, in this situation, you can't commit your code, can't merge your branch, what you can do is write the code and commit them together
when you get to your office tomorrow even the code contain many different features. That's so bad. So we need a (D)istribution (V)ersion (C)ontrol (S)ystem.
Then let's re-recognize our new friend Git!
![Local Version Control System]({{ site.url }}/images/git_introduction/Distribution Version Control System.png)  

## Key Concepts
Before start doing the practice, we need to have a quick sync up on git's key concepts. Check the illustration below:

![Git Diagram]({{ site.url }}/images/git_introduction/Git Diagram.png) 

As the graphic shows, git will leverage a totally complete work flow on your local side. You can modify your files in the `Work Directory`, stage them in the `Stage` area and commit them to the local `Repository`.
Once you finish your job on local side, you can communicate with the `Remote Repository` and push all the files and logs to the remote.

The file in Git always have 3 status, they are `Modified`, `Staged` and `Commited` which will show in the following content.

Multiple developers can maintain the source code on same `branch` or create their own `branch`. Usually, we will create a new `branch` for each feature, and merge it to release branch when ready.
and `tag` always be used when the project reach some milestone, like 'v0.0.1'.

## Installation
Install the Git before we start, for installation, please check the official website:
[https://git-scm.com/book/en/v2/Getting-Started-Installing-Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

## Practice
Finally! Ok, let's begin. First step let's set some configurations just like most software does. In Git, there are three level for config, `system` --> `global` --> `local`.
`system` for all users and all their repositories, `system` for one user and related repositories,  `local` for current repository.

### Config
Setting the `user.name` and `user.email` first. These two information used in each commit of Git.
```bash
$ git config --global user.name=zhongkai.ye
$ git config --global user.email=yezhongkai@patsnap.com
```

```bash
$ git config --global -l
user.name=zhongkai.ye
user.email=yezhongkai@patsnap.com
```

### Initial
Let's initial our repository.
```bash
$ git init
Initialized empty Git repository in C:/development/IdeaProjects/project-git-study/.git/
```
Folder `.git/` be created after the initialization. In this blog, let's keep focus on the operations, if you are curious about it, you can check here
[https://git-scm.com/book/en/v2/Appendix-C%3A-Git-Commands-Setup-and-Config](https://git-scm.com/book/en/v2/Appendix-C%3A-Git-Commands-Setup-and-Config) and
[https://medium.freecodecamp.org/understanding-git-for-real-by-exploring-the-git-directory-1e079c15b807](https://medium.freecodecamp.org/understanding-git-for-real-by-exploring-the-git-directory-1e079c15b807)

Create `README.md` for this repository
```bash
$ vi README.md
```
Write a sentence
```bash
This project is used for git test!
```
### Stage/Add
`stage` is synonym of `add`, so it's no different, i prefer to use `stage`, because it's more close to it's real meaning. Check current git status first
```bash
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        README.md

nothing added to commit but untracked files present (use "git add" to track)
```
```bash
$ git stage README.md
```
if you want to stage all modified file, just type
```bash
$ git stage .
```
Then check the status again:
```bash
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   README.md

```
If i want to revoke the stage file, the message above shows us to using 
```bash
$ git rm --cached README.md
rm 'README.md'
```
### Commit
Although we can use `git commit` for quick commit, but in order to develop good habit, i suggest that we using a completion common
`-a` for all, means commit all staged file
`-m` for message, means you put your commit message here, if you don't write message here, a default editor will be open to let you write the message when you commit the file
```bash
$ git commit -a -m 'master branch commit 1'
[master (root-commit) ddfd1dd] master branch commit 1
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```
or you can just commit some specify files
```bash
$ touch commit_test_1.md commit_test_2.md commit_test_3.md
$ git stage .
$ git commit commit_test_1.md commit_test_2.md -m 'master branch commit 2'
[master c9af499] master branch commit 2
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 commit_test_1.md
 create mode 100644 commit_test_2.md
```
### Log
```bash
$ git log --graph --color
* commit c9af499d437bfe78fe435d8447ca54a58238ad00 (HEAD -> master)
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Sun May 13 19:55:34 2018 +0800
|
|     master branch commit 2
|
* commit ddfd1ddaff76435bfd371cc389cd20b7457a926a
  Author: zhongkai.ye <yezhongkai@patsnap.com>
  Date:   Sun May 13 19:47:02 2018 +0800

      master branch commit 1
```
From log, we can see that each commit have their own `commit id`, this id generated in SHA-1, author, commit date and message are also their.
One important thing need to mention is that `HEAD`. What's this? Let's check a illustration  

![Git HEAD]({{ site.url }}/images/git_introduction/Git HEAD.png)

So the `HEAD` means where you are. It's a pointer of your current position. ie. you are on `master` branch, commit `E`, so the `HEAD` also point to
this position.

### Reset and Revert (For Commit Layer)
Oh! I make a mistake, i need to revoke my commit.
```bash
$ git reset --hard ddfd1ddaff76435bfd371cc389cd20b7457a926a
HEAD is now at ddfd1dd master branch commit 1

$ git log
commit ddfd1ddaff76435bfd371cc389cd20b7457a926a (HEAD -> master)
Author: zhongkai.ye <yezhongkai@patsnap.com>
Date:   Sun May 13 19:47:02 2018 +0800

    master branch commit 1
```
This command means i get the first time commit id and reset my work directory, my stage area and my repository to that point. But what's `--hard` mean?

![Git Reset]({{ site.url }}/images/git_introduction/Git Reset.png)

`hard` means reset your current work directory, stage area and repository  
`mixed` means reset your current stage area and repository, but your work directory is not change, you can stage and commit your file later  
`soft` means just reset your repository  

Different from `reset`, `revert` don't change any commit history, ie.
```bash
$ touch commit_test_1.md commit_test_2.md commit_test_3.md
git commit -a -m 'master branch commit 2'
[master 4f716ce] master branch commit 2
 3 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 commit_test_1.md
 create mode 100644 commit_test_2.md
 create mode 100644 commit_test_3.md
 
$ git revert HEAD
[master ed78f61] Revert "master branch commit 2"
 3 files changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 commit_test_1.md
 delete mode 100644 commit_test_2.md
 delete mode 100644 commit_test_3.md
 
$ git log
commit ed78f614426b85cbc6f0c85c0aad4a6ee39f4ee2 (HEAD -> master)
Author: zhongkai.ye <yezhongkai@patsnap.com>
Date:   Sun May 13 21:25:41 2018 +0800

    Revert "master branch commit 2"

    This reverts commit 4f716ce85ce06105349546da7d6f0e8253135128.

commit 4f716ce85ce06105349546da7d6f0e8253135128
Author: zhongkai.ye <yezhongkai@patsnap.com>
Date:   Sun May 13 21:25:24 2018 +0800

    master branch commit 2

commit ddfd1ddaff76435bfd371cc389cd20b7457a926a
Author: zhongkai.ye <yezhongkai@patsnap.com>
Date:   Sun May 13 19:47:02 2018 +0800

    master branch commit 1
    
$ ls
README.md
```
See, there is another revert commit log appeared, and also test file is gone only leave `README.md` in the folder.

### Checkout and Branch
It's time for us to create our first branch now.First let's check where we are.
```bash
$ git branch
* master
```
OK, current now, we are on `master` branch, then i want to create a new branch base on `master` and switch to that branch
```bash
$ git checkout -b develop_wrong_words
Switched to a new branch 'develop_wrong_words'

$ git checkout master
Switched to branch 'master'

$ git branch -d develop_wrong_words
Deleted branch develop_wrong_words (was ed78f61).

$ git checkout -b develop
Switched to a new branch 'develop'

$ git branch
* develop
  master
```

### Merge
In `develop` branch, i modified `README.md`, add a line for merge test
```bash
$ vi README.md
```

```bash
This project is used for git test!

Add a new line for merge test on develop branch.
```

Commit the change
```bash
$ git commit -a -m 'develop branch merge test commit 1'
[develop 73e5020] develop branch merge test commit 1
 1 file changed, 2 insertions(+)
```

Now we switch back to `master` branch and also add a line for merge test.
```bash
$ git checkout master
Switched to branch 'master'

$ vi README.md
```
```bash
This project is used for git test!

Add a new line for merge test on master branch.
```

```bash
$ git commit -a -m 'master branch merge test commit 1'
[master 201346d] master branch merge test commit 1
 1 file changed, 2 insertions(+)
```

After modified `README.md` on both branches. Now we try to merge `develop` branch into `master`
```bash
$ git merge develop
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```
Git tell us there is a conflict in README.md, and automatic merge is failed, we should fix it manually. Let's see what happening in
`README.md`
```bash
$ vi README.md
```

```bash
This project is used for git test!

<<<<<<< HEAD
Add a new line for merge test on master branch.
=======
Add a new line for merge test on develop branch.
>>>>>>> develop
```
This means current now, we have `Add a new line for merge test on master branch.`, but `README.md` in `develop` branch also have a line in same position.
My purpose is put them together, so I delete `<<<<<<<HEAD`, `=======` and `>>>>>>> develop`, save the file.

```bash
$ git commit -a -m 'master branch merge test commit 2'
[master 933a959] master branch merge test commit 2

$ git merge develop
Already up to date.
```
If we need a pretty display we can use merge tools.

```bash
$ git mergetool

This message is displayed because 'merge.tool' is not configured.
See 'git mergetool --tool-help' or 'git help config' for more details.
'git mergetool' will now attempt to use one of the following tools:
opendiff kdiff3 tkdiff xxdiff meld tortoisemerge gvimdiff diffuse diffmerge ecmerge p4merge araxis bc codecompare emerge vimdiff
Merging:
README.md

Normal merge conflict for 'README.md':
  {local}: modified file
  {remote}: modified file
Hit return to start merge resolution tool (vimdiff):
4 files to edit

$ git merge develop
Already up to date.

```

![Merge Tool]({{ site.url }}/images/git_introduction/Merge Tool.png) 

### Tag
We can switch from one branch to another, but `tag` can't. Usually, we will make a tag when we reach some milestone, like 'v1.0.0', and this is convenient for us to checkout a new branch base on
this tag when necessary.
```bash
$ git tag -a v0.0.1 -m 'git study tag version 0.0.1'
```
`-a` means `annotation`, give tag a annotation  
`-m` means `message`, give tag a description  

Use `git tag` and `git show v0.0.1` for detail checking 
```bash
$ git tag
v0.0.1

$ git show v0.0.1
tag v0.0.1
Tagger: zhongkai.ye <yezhongkai@patsnap.com>
Date:   Sun May 13 22:17:52 2018 +0800

git study tag version 0.0.1

commit e0564dbd4473369cfd254f5bc7a8a57ee714a68b (HEAD -> master, tag: v0.0.1)
Merge: f0de13f b2740e5
Author: zhongkai.ye <yezhongkai@patsnap.com>
Date:   Sun May 13 22:11:26 2018 +0800

    master branch merge tool test commit 2

diff --cc README.md
index 2ac4118,0953f6d..5fe5464
--- a/README.md
+++ b/README.md
@@@ -1,6 -1,5 +1,6 @@@
  This project is used for git test!

 +Add a new line for merge test on master branch.
  Add a new line for merge test on develop branch.

- Add a new line for merge tool test on master branch.
 -Add a new line for merge tool test on develop branch.
++Add a new line for merge tool test on develop branch.

```
Also we can make a tag for previous commit.
```bash
$ git tag -a 'v0.0.0' f0de13fcb8a016e4f925021b7bd85bac48f4a685 -m 'git study original tag version 0.0.0'

$ git log
commit e0564dbd4473369cfd254f5bc7a8a57ee714a68b (HEAD -> master, tag: v0.0.1)
Merge: f0de13f b2740e5
Author: zhongkai.ye <yezhongkai@patsnap.com>
Date:   Sun May 13 22:11:26 2018 +0800

    master branch merge tool test commit 2

commit b2740e5f8c1ae371b706473b63bae644571ff4f3 (develop)
Author: zhongkai.ye <yezhongkai@patsnap.com>
Date:   Sun May 13 22:07:44 2018 +0800

    develop branch merge tool test commit 1

commit f0de13fcb8a016e4f925021b7bd85bac48f4a685 (tag: v0.0.0)
Author: zhongkai.ye <yezhongkai@patsnap.com>
Date:   Sun May 13 22:07:15 2018 +0800

    master branch merge tool test commit 1
...
```

### Ignore
We find that there is an additional file been created named `README.md.orig`, it's made by merge tool, we can set global configuration to 
make it don't produce such file. But now, what we need is a `.gitignore` file.
```bash
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   README.md.orig

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
```

```bash
$ vim .gitignore
```
Then add a line like below, this means all file which end with `orig` will be ignored from been tracking
```bash
*.orig
```

```bash
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   README.md.orig

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        .gitignore
        
$ git stage .gitignore

$ git commit .gitignore -m 'Add .gitignore'
[master 7be24fc] Add .gitignore
 1 file changed, 1 insertion(+)
 create mode 100644 .gitignore

$ git status
On branch master
nothing to commit, working tree clean
```

### Diff
`diff` are used for display differences between work directory and stage, between work directory and repository and between stage and repository
ie. i add a new line in `README.md`
```base
This project is used for git test!

Add a new line for merge test on master branch.
Add a new line for merge test on develop branch.

Add a new line for merge tool test on develop branch.

Add a new line for diff test on master branch.
```
put the modify into stage, then add another line
```bash
This project is used for git test!

Add a new line for merge test on master branch.
Add a new line for merge test on develop branch.

Add a new line for merge tool test on develop branch.

Add a new line for diff test on master branch.

Add a new line for diff test on master branch 2.
```
Now let's see the different.
```bash
$ git diff
diff --git a/README.md b/README.md
index 3180d40..a08847f 100644
--- a/README.md
+++ b/README.md
@@ -5,4 +5,6 @@ Add a new line for merge test on develop branch.

 Add a new line for merge tool test on develop branch.

-Add a new line for diff test on master branch.
\ No newline at end of file
+Add a new line for diff test on master branch.
+
+Add a new line for diff test on master branch 2.
\ No newline at end of file
```

```bash
diff --git a/README.md b/README.md
index 5fe5464..a08847f 100644
--- a/README.md
+++ b/README.md
@@ -4,3 +4,7 @@ Add a new line for merge test on master branch.
 Add a new line for merge test on develop branch.

 Add a new line for merge tool test on develop branch.
+
+Add a new line for diff test on master branch.
+
+Add a new line for diff test on master branch 2.
\ No newline at end of file
```

```bash
$ git diff --staged
diff --git a/README.md b/README.md
index 5fe5464..3180d40 100644
--- a/README.md
+++ b/README.md
@@ -4,3 +4,5 @@ Add a new line for merge test on master branch.
 Add a new line for merge test on develop branch.

 Add a new line for merge tool test on develop branch.
+
+Add a new line for diff test on master branch.
\ No newline at end of file
```
From the result we could see that
`git diff` means difference between work directory and stage area
`git diff HEAD` means difference between work directory and HEAD
`git diff --staged` means difference between staged area and HEAD

### Remote
Now it's time to connect our local repository with remote repository. I create a project on company's Gitlab, named `project-git-study`.
PS: Don't forget to add your RSA public key, please check [https://git-scm.com/book/en/v2/Git-on-the-Server-Generating-Your-SSH-Public-Key](https://git-scm.com/book/en/v2/Git-on-the-Server-Generating-Your-SSH-Public-Key)

![Git lab new project]({{ site.url }}/images/git_introduction/Git lab new project.png)

```bash
$ git remote add origin git@git.patsnap.com:yezhongkai/project-git-study.git

$ git remote
origin
```
`origin` is the alias name for upstream remote repository, we can add multiple remote repositories. Let's push our local repository to remote.

```bash
$ git push origin master

fatal: The current branch master has no upstream branch.
To push the current branch and set the remote as upstream, use
    git push --set-upstream origin master
```
Git prompt that we should set upstream branch for master. If we use `$ git push origin master:master` that's will be no problem, but there is a more efficient way, we can use `-u` in `push` command, `-u` equivalent to
`--set-upstream`.
```bash
$ git push -u origin master
Counting objects: 31, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (31/31), 2.61 KiB | 205.00 KiB/s, done.
Total 31 (delta 8), reused 0 (delta 0)
To git.patsnap.com:yezhongkai/project-git-study.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```
Then push our tags to remote
```bash
$ git push origin --tags
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 317 bytes | 317.00 KiB/s, done.
Total 2 (delta 0), reused 0 (delta 0)
To git.patsnap.com:yezhongkai/project-git-study.git
 * [new tag]         v0.0.0 -> v0.0.0
 * [new tag]         v0.0.1 -> v0.0.1
```

If we want to delete any remote branch or tag, we can input
```bash
git push origin --delete develop
git push origin --delete tag v0.0.0
```

If we need to check all the branch and tags in remote repository, we can input
```bash
$ git ls-remote
From git@git.patsnap.com:yezhongkai/project-git-study.git
3e03bac83a574cd4fc047595f620126ac19e1f28        HEAD
b2740e5f8c1ae371b706473b63bae644571ff4f3        refs/heads/develop
3e03bac83a574cd4fc047595f620126ac19e1f28        refs/heads/master
5f2d7d62ef5347ec78faa3047a0424afd876196b        refs/tags/v0.0.0
f0de13fcb8a016e4f925021b7bd85bac48f4a685        refs/tags/v0.0.0^{}
4127094bd475a7d7cfa9673d08abc501cd5a7b92        refs/tags/v0.0.1
e0564dbd4473369cfd254f5bc7a8a57ee714a68b        refs/tags/v0.0.1^{}
```
![Git lab branch and tag]({{ site.url }}/images/git_introduction/Git lab branch and tag.png) 

If we do not have any files and want to take a whole project from remote repository, we can input
```bash
$ git clone git@git.patsnap.com:yezhongkai/project-git-study.git
```

### Rebase
Rebase is a important concept, very like `merge`, but there are some differences.

![Git Rebase]({{ site.url }}/images/git_introduction/Git Rebase.png)

The biggest different between them is the commit history, rebase will let your commit history looks like all changes happened there, but `merge` will have a independent commit.

First, let's switch to `develop` branch, and commit for 3 times. The `README.md` finally looks like
```bash
This project is used for git test!

Add a new line for merge test on develop branch.

Add a new line for merge tool test on develop branch.

Add a new line for rebase test 1.

Add a new line for rebase test 2.

Add a new line for rebase test 3.
```
The commit log looks like
````bash
$ git log --graph --color
* commit 35c6c48dc5639b3ea5b70eef2ec994032ee637bf
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Mon May 14 21:50:58 2018 +0800
|
|     develop branch rebase test commit 3
|
* commit 3cb6ec4931bfb208ecc51d8869ccc8a384335acd
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Mon May 14 21:50:45 2018 +0800
|
|     develop branch rebase test commit 2
|
* commit ebd3f2857814719ae651cb579fc57ff2f8c3e7d6
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Mon May 14 21:50:30 2018 +0800
|
|     develop branch rebase test commit 1
|
* commit b2740e5f8c1ae371b706473b63bae644571ff4f3 (origin/develop)
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Sun May 13 22:07:44 2018 +0800
|
|     develop branch merge tool test commit 1
|
* commit 73e502032551b22e4f0c76ec936fc62de6f1a7d6
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Sun May 13 21:54:14 2018 +0800
|
|     develop branch merge test commit 1
...
````
then i rebase all commit to `master` branch
```bash
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: develop branch rebase test commit 1
error: Failed to merge in the changes.
Using index info to reconstruct a base tree...
M       README.md
Falling back to patching base and 3-way merge...
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Patch failed at 0001 develop branch rebase test commit 1
The copy of the patch that failed is found in: .git/rebase-apply/patch

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```
Modify the `README.md`,  resolve all conflicts.
```bash
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

$ git merge develop
Updating 5386ddb..81ba0f0
Fast-forward
 .gitignore | 4 ++++
 README.md  | 8 +++++++-
 2 files changed, 11 insertions(+), 1 deletion(-)


$ git log --graph --color
* commit 5132ca001c8226f208424e34b41e8357bdb9c699
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Mon May 14 21:50:58 2018 +0800
|
|     develop branch rebase test commit 3
|
* commit 2138aab2da566fd6a254a38502d52dcc7a27dc9e
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Mon May 14 21:50:45 2018 +0800
|
|     develop branch rebase test commit 2
|
* commit 4ca6f7786de38d9e2723f0294a3b713d85c5537c
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Mon May 14 21:50:30 2018 +0800
|
|     develop branch rebase test commit 1
|
* commit 26a98fa8193689a01fbdd8c7dd796ece1529bedb
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Mon May 14 22:01:34 2018 +0800
|
|     develop resolve rebase conflict
|
* commit 5386ddbf8aee9015a4e6ba36b528ad840aaa61e3
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Mon May 14 21:13:58 2018 +0800
|
|     master branch rebase test commit 1
|
* commit a06866a648e5b2dfe15d7124a1c2639f67deb29a (origin/master)
| Author: ChrisYe <yezhongkai@patsnap.com>
| Date:   Mon May 14 17:59:15 2018 +0800
|
|     Update README.md
```

It's another scenario that we want to make the commit log looks more clean and readable when we pull the code from remote, at this moment, we need to use `pull --rebase`

`pull` = `fetch` + `merge`  
`pull --rebase` = `fetch` + `rebase`

I edit `README.md` in Git lab, add a new line for `pull` and `pull --rebase` comparision.

![Git lab pull rebase test]({{ site.url }}/images/git_introduction/Git lab pull rebase test.png)

and i modify `README.md` in local
```bash
$ vi README.md
```
```bash
This project is used for git test!

Add a new line for merge test on master branch. Add a word for rebase test.
Add a new line for merge test on develop branch.

Add a new line for merge tool test on develop branch.

Add a new line for diff test on master branch.

Add a new line for diff test on master branch 2.
```
```bash
$ git commit -a -m "master branch rebase test commit 1"
[master 5acd1d3] master branch rebase test commit 1
 1 file changed, 1 insertion(+), 1 deletion(-)

$ git pull --rebase origin master
From git.patsnap.com:yezhongkai/project-git-study
 * branch            master     -> FETCH_HEAD
First, rewinding head to replay your work on top of it...
Applying: master branch rebase test commit 1

$ git log --graph --color
* commit 5386ddbf8aee9015a4e6ba36b528ad840aaa61e3 (HEAD -> master)
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Mon May 14 21:13:58 2018 +0800
|
|     master branch rebase test commit 1
|
* commit a06866a648e5b2dfe15d7124a1c2639f67deb29a (origin/master)
| Author: ChrisYe <yezhongkai@patsnap.com>
| Date:   Mon May 14 17:59:15 2018 +0800
|
|     Update README.md
|
* commit 3e03bac83a574cd4fc047595f620126ac19e1f28
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Sun May 13 22:47:19 2018 +0800
|
|     master branch diff test commit 1
|
* commit 7be24fc30874b665b72e686afb637b9fbc425d83
| Author: zhongkai.ye <yezhongkai@patsnap.com>
| Date:   Sun May 13 22:41:04 2018 +0800
|
|     Add .gitignore
|
*   commit e0564dbd4473369cfd254f5bc7a8a57ee714a68b (tag: v0.0.1)
|\  Merge: f0de13f b2740e5
| | Author: zhongkai.ye <yezhongkai@patsnap.com>
| | Date:   Sun May 13 22:11:26 2018 +0800
| |
| |     master branch merge tool test commit 2
| |
| * commit b2740e5f8c1ae371b706473b63bae644571ff4f3 (origin/develop, develop)
| | Author: zhongkai.ye <yezhongkai@patsnap.com>
| | Date:   Sun May 13 22:07:44 2018 +0800
| |
| |     develop branch merge tool test commit 1
| |
```

Could see that there is no merge commit, but just a normal commit `Update README.md` in middle of the commit log.

OK, that all for Git basic introduction, hope could helpful to you. :)

Reference:  
[https://en.wikipedia.org/wiki/Version_control](https://en.wikipedia.org/wiki/Version_control)
[https://git-scm.com/book](https://git-scm.com/book)
[https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

