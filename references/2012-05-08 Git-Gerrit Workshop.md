# 2012-05-08 Git tutorial

- `.git/hooks`: helps you prepare commit message
- `.git/objects`: all the files, directories, everything
- `.git/config`: sometimes you have to change it.
- `.git/refs`, `.git/HEAD`: files which are pointers (to different versions of files etc.)

# create a file:

- $ `echo "My first file" > first`
- $ `git add first (creates  a binary file .git/objects/36/3d8b784900d74b3159e8e93a651c0db42629ef)
- $ `zpipe -d < .git/objects/`36/3d8b784900d74b3159e8e93a651c0db42629ef ("blob 14 My first file") (binary file, 14 bytes long)
- This is called staging to index.

## first commit:

- `git commit -m "Test"` (takes all files in index and creates a version for it with an identifier, author, message. With -m it adds a message)
- `echo .git/COMMIT_EDITMSG`
- `git log` (history of commit messsage. Does not store diffs/patches. Stores entire files with patches)
- `git status` (nothing to commit)

## change file

- edit a file
- git add first (you must do it again each time. The current file doesn't store, but the one from the last add)
- git commit -a (this automatically adds the file for you. Avoid using this until you know what's in your respository)

## Working with a messy repository

Gerrit usually wants one commit to the repository for review. Submit one commit.

Beginner: Focus on getting one commit right, wait for it to be reviewed. Otherwise people will review at different orders.

- `git reset`
- `git reset --hard` (please delete all local changes not recorded to git (even adds). Be careful because you will lose local changes)

- `echo Second file > a`
- `git diff`
- `git add a`
- `git diff` (still nothing!)
- `git diff --cached` (real diff, including new files added)
- edit files
- `git status` (notice some files have changed but are not staged)

## Let's try to go back

- `git branch` (see we're master)
- `git branch -vv` (see verbose version of where we're all)
- `cat .git/HEAD` (we can see that we're the curent head)
- `git log` (find commit identifier)
- `git reset --soft c31ca9f298e5e300c3dc16ee26629aeeb8aba12e`
- do the commands above to see we're different state.

Nothing is lost. But if we start from here, we will create a new history. It's not shown in log.

- `git reflog` (shows a history of all your movements)
- `git log ###` (shows the log of different hsitories)
- `git log --graph --pretty=oneline --abbrev-commit` (http://stackoverflow.com/questions/1064361/unable-to-show-a-git-tree-in-terminal) [alias] tree = log --graph --decorate --pretty=oneline --abbrev-commit

## How to remember all the commit tags?

- `git diff HEAD^^ HEAD^` (allows you to navigate backwards from head by relative instead of explicit)
- `man git-rev-parse` (memorize this!)

## To work with Gerrit

You need to understand git remote.

- `git remote` (shows nicknames of other git repositories)
- `git remote -v` (full name)

(for us "http" is read-only, and "ssh" is for pushing)

- `git remote rm review` (remove a remote)

- `git fetch origin` (please connect to a repository and fetch all changes here)
- `git fetch --all` (please connect to all remotes and get all things)

Pulls all changes but doesn't change anything in the working directory.

- `git pull` ( git fetch && git merge origin/master )

Advice: Don't use git fetch and then merge changes manually. If you fetch changes (nothing happens). But if you merge changes (it will merge your stuff with the master repository as a commit and gerrit will block it unless you are a git master)

- `git cherry-pick` (don't do this either because it geenrates a manually)

- `git merge --ff-only origin/master` ( merge from upstream, but only merge them if there is nothing to commit )
- `git fetch origin`

*remote/branchname*: ex. origin/master (take master branch from remote origin)

- `git branch -vv` ('ahead': we have one commit newer, 'behind'L we are one commit behind)

Now to compare how different our tree is from mediawiki.

- `git diff HEAD origin/master` (see different)
- `git log HEAD ^ORIGIN/MASTER`
- `git log origin/master ^master` (any changes that are on remote)
- `git log ^master origin/master` (any local changes)

## Pushing changes to gerrit

- `git push origin HEAD` (where to push, from what.  It asks you for http username and password to connect, so you lose)
- `git remote add review ssh://saper@l/mediawiki-core.git` (adds an ssh repsotiory
- `git remote -v`
- `git fetch --all`
- `git push review HEAD` (please push my commit to review)

We got connect but rejected "cannot update reference as a fast forward" (means that we don't have permission on gerrit).

- `git push review HEAD:refs/for/master` (push something meant for master)

gerrit has recorded this is our e-mail address that is mismatched.

- `git remote add q ssh://qubec@l/mediawiki-core.git`
- `git push q HEAD:refs/for/master`

Complaining does not have commit change ID in commmit id. All changes to gerrit have change id.

We will go back to the previous commit with --soft

- `git reset --soft HEAD^`

need a `.git/hooks/` that does magic called `commit-msg`

- `git refloh`
- `git commit -c ?`

we get magic CHange-ID for gerrit

- `git push q HEAD:refs/for/master`

We're done.

## One last thing

- `git checkout -b change9 FETCH_HEAD`

(clean commit that has been reported to repository)

- `git reset --soft HEAD^` (go back one commit and improve)

(but want same changeID and change the commit message)
Can use it to upload patchset
(go one commit)

shortcut:

- `git commit --ammend`

## SSH interfacce

`ssh l gerrit query owner:…`