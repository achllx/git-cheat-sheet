# Git Bash Command Cheatsheet

**Collections of useful git bash command**

*--apply gitignore untuk project yang udah kadung kotor*
    
    git rm -r --cached .
    git add .
    git commit -m "fixed untracked files"

*--turn off SSL verification*
    
    git config --global http.sslVerify false

*--Clone repo*

directly from master :
    
    git clone [link repo]

specific branch cloning :
    
    git clone -b [branch name] [link repo]

clone to a specific folder (... same as above command, just add folder name after repo link) :
    
    ... [link repo] [folder name]

*--Check current working directory status*

    git status
    
*--Pull / sync with a branch*

	git pull origin [branch name]

if already set-upstream/-u during push, just use command like below :

    git pull
    
notes :

    - when pulling to another branch, for example to release, and then there's new file in release, it will automatically merge with the branch you're currently working on
        usually, vim editor will appear immediately, if there's nothing to be changed just save the file by typing :wq then enter
    - same as above, but it turns out the file you're working on has different content than the one in the branch you pull, it will result in conflict, for resolving see below
  
*--Switch branch / create new branch*

If the branch already exists, it will switch to it. If it doesn't exist, when pushing it will create a new branch :

    git checkout -b [branch name]
    
If you need to clone / rollback to the repository's position at a specific commit :

    git checkout -b [new arbitrary branch name] [commit sha id]
    
*--Initiate a folder as a git repository*

    git init
    
*--Add remote origin*

    git remote add origin [repo url]
    
*--Show current remote origin*

    git remote show origin

*--Change remote origin url*

    git remote set-url origin [repo url]

*--Commit changes*

to add modified files :

    git add .

if you want to add specific file :

    git add [file name]

commit the added files :

    git commit -m [message]

if mistakenly input the commit message and want to redo it :

    git reset --soft HEAD~1
    
*--Go back to the previous commit (changes in files you've made will be discarded)* :

    git reset --hard HEAD~1
    
*--If the file has been deleted because you went back to the previous commit, but then you want that file back* :

    git reflog (a list of your commits along with their sha id will appear)
    git checkout -b [new arbitrary branch name] [commit sha id]

*--Push changes* :

Push changes after committing :

    git push origin [branch name]

Push changes along with setting upstream origin :

    git push -u origin [branch name]

Once you've set upstream as above, when you want to push, just use this command :

    git push (it will automatically push to the previously set branch)

If there's a conflict during push, and you want to rebase / replace what's currently in the remote (this is if you're working on a branch with others, ask for consent before forcing) :

    git push origin [branch name] --force
    
*--Merge branch with a branch on remote* :

    git checkout [branch name 1 (local branch being worked on)]
    git merge [branch name 2 (branch to be merged, usually release/master)]

or you can use this command :

    git checkout [branch name 2] [branch name 1]

*--Rebase* :

    git checkout [branch name 1 (local branch being worked on)]
    git rebase [branch name 2 (branch to be rebased, usually release/master)]
    
notes : 

    - Don't rebase branches used together, such as master or release branches, it will be confusing later because other developers will need to merge again with the resulting rebased branch
    - if merge combines branches, then rebase is an alternative, it will also combine 2 branches into 1, but the commit history will be sorted, for example:
        - there's branch 1 with commits a-b-c
        - branch 2 is a clone result of branch 1, then commits d-e are made
        - oh, someone else directly pushed to branch 1, so the commits change to a-b-c-d
        - branch 2 could just merge with branch 1, but later the commit history will be like this: a-b-c-d-e (e contains commits d-e from branch 2)
        - well, branch 2 could do rebase, so it becomes like this: a-b-c-d-e-f (commit d-e from branch 2 becomes commit e-f)
    - the result is a linear commit history, but if everyone does rebase, then the branch will be replaced every time, 
      so rebase is suitable when there's only 1 developer working on a feature in a repository, then it turns out someone did a hotfix on the master branch, 
      well, later when this developer deploys it's better to just rebase so the history is neater than merge  

*--Resolve conflict* :

try running this command :

    - git mergetool
    - a vimdiff will appear like this :
    ╔═══════╦══════╦════════╗
    ║       ║      ║        ║
    ║ LOCAL ║ BASE ║ REMOTE ║
    ║       ║      ║        ║
    ╠═══════╩══════╩════════╣
    ║                       ║
    ║        MERGED         ║
    ║                       ║
    ╚═══════════════════════╝
    LOCAL – This is the file in your local branch you're working on
    BASE – condition of the file before being changed
    REMOTE – This is the file you pull using pull command
    MERGED – result of merge
    if you want to edit, the navigation method to switch between local, base, remote, merged, press ctrl+w
    - if you want to take changes from remote use command :diffg REMOTE
      if you want to take changes from local use command :diffg LOCAL
      if you want to take changes from base use command :diffg BASE
    - :wqa (for save and exit)
    - git commit -m "message"
    - git clean (for delete extra files created by diff tool)
    - git push (when done)
