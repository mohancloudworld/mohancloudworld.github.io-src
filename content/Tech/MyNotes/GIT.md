Date: 2015-11-10
Title: GIT

Install:
--------
(1) yum install git
    (or)
    yum install git-core

First-Time Git Setup:
---------------------
(1) Define your Identity
	git config --global user.name "<user name>"
	git config --global user.email <email ID>
		
(2) To check the settings
	git config --list

Frequently Used Commands:
-------------------------
(1) To begin tracking new files, to stage files, and to do other things like marking merge-conflicted files as resolved
	git add <file>...

(2) To commit the added/staged changes
	git commit
    Note: If you modify a file after you run git add, you have to run 'git add' again to stage the latest version of the file. Otherwise, if you commit, the version of file(s) as it was when you last ran the 'git add' command is how it will go into the commit, not the version of the file as it looks in your working directory when you run git commit.

(3) To add modified (not new) files and commit together
	git commit -a

(4) (a) To compares what is in your working directory with what is in your staging area
	    git diff
    (b) To compares what is in your staging area with your last commit
	    git diff --cached
	    (or)
	    git diff --staged

(6) To know the status
	git status

(7) Branches 
    (a) To list existing branches, [the current branch is marked with a "*" mark]
        git branch
    (b) To create a branch from the current state/commit,
        git checkout -b <branch-name>
    (c) To create a branch from a specific branch/commit
        git checkout [branch-name or commit-name] -b <new-branch-name>
    (d) To delete a branch
        git branch -d <branch-name>

IGNORING FILES:
---------------
You can tell git to ignore certain files by creating a file called .gitignore in the top level of your working directory, with
contents such as:

# Ignore any file named foo.txt.
foo.txt
# Ignore (generated) html files,
*.html
# except foo.html which is maintained by hand.
!foo.html
# Ignore objects and archives.
*.[oa]
# To add .gitignore to repository
!.gitignore

Fixing un-committed mistakes:
-----------------------------
(a) If you've messed up the working tree, but haven't yet committed your mistake, you can return the entire working tree to
the last committed state with
	git reset --hard HEAD
This command can also be used to revert to the original state, if merging a branch has resulted in un-resolvable conflicts.

(b) If you have done a wrong commit and want to revert back to previous commit
	git reset --hard HEAD~1 # This will delete the commit from git and also changes from files
	git reset --soft HEAD~1 # This will delete the commit from git and but changes to the files will not be deleted (git will be in cached state)
    See: http://nakkaya.com/2009/09/24/git-delete-last-commit/
    
(c) If you have done wrong commit and also push it to remote origin then it is not possible to reset/revert that commit on remote branch. Instead we can do a new commit of the previous commit (i.e., desired state).
    git revert HEAD
    See: http://nakkaya.com/2009/09/24/git-delete-last-commit/
    
(d) If you just want to restore just one file, say your hello.rb, use git checkout instead
	git checkout -- hello.rb
	git checkout HEAD hello.rb

(e) To change the author name & email for "ALL" previous commits
	git filter-branch --commit-filter 'export GIT_AUTHOR_NAME="author-name"; export GIT_AUTHOR_EMAIL=author-email; git commit-tree "$@"'
(f) To change the committer name & email for "All" previous commits
	git filter-branch --commit-filter 'export GIT_COMMITTER_NAME="committer-name"; export GIT_COMMITTER_EMAIL=committer-email; git commit-tree "$@"'

Ensuring good performance:
--------------------------
On large repositories, git depends on compression to keep the history information from taking up too much space on disk
or in memory. This compression is not performed automatically. Therefore you should occasionally run git gc:
	git gc
Note: This can be very time-consuming, so you may prefer to run git-gc when you are not doing other work.

How to set up your own private Git server on Linux:
---------------------------------------------------
(1) On Remote Server, create the repository,
	mkdir myrepo.git
	cd !$
	git --bare init
(2) On Local Machine,
	(a) Remove any defined 'origin' variable
		git remote rm origin
	(b) Now we can add our new remote
		git remote add origin git@server.com:myrepo.git
		(or)
		git remote add origin ssh://git@myserver.com:sshport/~/myrepo.git
(3) To push the code to server
	git push origin master
Note: The 'push' should be done after comminting changes into local repository. 
    	
See: http://tumblr.intranation.com/post/766290565/how-set-up-your-own-private-git-server-linux

Clone and Checkout code from Git server:
----------------------------------------
(1) On Local Server, create the new repository,
	mkdir newrepo
	cd !$
	git init
(2) On Local Machine, configure remote server
	git remote add origin git@server.com:myrepo.git
	(or)
	git remote add origin ssh://git@myserver.com:sshport/~/myrepo.git
	(or)
	From another repo on local file system:
	git remote add origin /home/user/myrepo.git
(3) To get the code from server
	git pull origin master
/*(4) To checkout code from local repository
	git checkout master
*/

Clone between two bare repositories (new_bare <== bare)
-------------------------------------------------------
(1) git --bare init 
(2) git remote add origin file:///myrepo.git
(3) git fetch origin master:master

GitHub: To sync local repositories with github.com over ssh, without password
------------------------------------------------------------------------------
(1) Add ssh key to github repository
	See: https://help.github.com/articles/generating-ssh-keys/
(2) On local repository, the git "origin" remote should be pointing to the ssh url. To set it, 
	git remote set-url origin git@github.com:<Username>/<Project>.git

