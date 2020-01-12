## Change commit message
Use the following command only if you havent pushed the changes to remote. 

``` bash
git commit message change
git commit --amend -m 'message'
```

This will change the hash value because changing commit message is part of a commit. When hash changes, it changes the git history. 

## Adding new file to last commit
Adding new file to the last commit(in case you left off file you meant to commit )
this also changes hash value, whcih changes git history.

``` bash
git add newfilename
git commit --amend (prompt)
```

then check the result with
``` bash
git log --stat
git status
```

## When commiting wrong branch
ex) accidentally commit a file to master when you had to commit it to develop branch

copy hash code you want to undo from master branch
``` bash
git log - copy hash code

# switch to develop branch
git checkout develop
# then commit the file to develop branch
git cherry-pick hashcode
```

## Undo some kind of change in repository 


### git checkout

The `git checkout` command can be used in a __commit or file__ level scope. `git checkout` command is used to update the state of the repository to a specific point in the projects history. 
It's useful for quickly inspecting an old version of your project.

**Checking out a file that is not staged yet** 
``` bash
git status    # file a is not staged yet
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   a
git checkout a  # takes back to last commit 
git status
On branch master
nothing to commit, working tree clean
```
A file level checkout will change the file's contents to those of the specific commit. 



**Checking out previous commit** 

`git checkout` moves the HEAD reference to the specified commit/branch and put you in a detached HEAD state. 
``` bash
git checkout hash code
```
This will put you in 'detached HEAD' state where you can look around, make experimental changes and commit them, and you can discard any commits you make in this state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

``` bash
git checkout -b <new-branch-name>
```

## git reset
`git reset` takes a specified commit and resets the "three trees" (working directory, staging area, commit) to match the state of the repository at that specified commit. 
`git reset` alter the existing commit history - used to undo changes on a private branch. 

**git reset --soft**
Set us back to commit we specified, but keep the changes we made in the **staging area.**
``` bash
git reset --soft hashcode

git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   b     # changes are on staging area

```

**git reset --mixed (default)**
Keep the changes but changes are in the __working directory__ 
``` bash
git reset --mix  hashcode

git status
On branch dev
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	b   # changes are in the working directory

```

**git reset --hard**
All of the tracked file match the state they were in **commit** we specified. Revert all the tracked files back to the state they were, but leave any untracked files alone.
``` bash
git reset --hard hash

git status
On branch dev2
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	c   # leave untracked files only if they exist(file c was not commited)
```

### git clean
Remove untracked files from the working directory
``` bash
git clean -df
```


## git revert

Revert create a new commit to reverse the effect of the earlier commit. **No rewrite new history,  modification or delete the existing commit, but create a new commit** on top of those and undo all the changes. 


Use `git revert` in case people already checkout the commit you want to undo - used to undo changes on a public branch. 

`git revert` can only be run at a commit level scope. 

