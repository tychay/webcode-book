# Notes on using Git

## CONCEPTS

There are four states:

- working directory: Like a checkout in svn
- staging area: stored in `.git` which is a list of files to be committed
- `.git` directory: Like a local SVN repository
- remotes: a peer networking between this git and others

Four statuses of a file

- untracked: (git doesn't know existence of file)
- unmodified: (file resembles what is tracked in repository)
- modified: (file changed from what is in repository)
- staged: (file ready to be committed)

Workflow

1. Modify files in working directory
2. Stage the files adding snapshots of them to saying area
3. Do a commit which takes the saying area and stores them permanently in your `.git` directory.

All information (file) is represented as references to a 40-digit "object name" e.g. `363d8b784900d74b3159e8e93a651c0db42629ef`

- SHA1 hash of contents
- can reference by any unique subpart of it (starting with first)
- can compare identical objects by comparing names
- same object is stored under same name
- can verify broken objects by mismatch with sha1
- stored in `.git/objects/36/3d8b784900d74b3159e8e93a651c0db42629ef`


	$ zpipe -d < .git/objects/36/3d8b784900d74b3159e8e93a651c0db42629ef
	blob 14 My first file

Means:

- object **type**: binary file
- **size**: 14 bytes
- **contents**: "My first file"

Different types of objects

- **blob**: file data (usually a file)
- **tree**: like a directory: can store trees and other blobs
- **commit**: pointer to a single tree. Also contains timestamp, author, pointer to previous commit
- **tag**: marks a specific commit as "special" (e.g. releases)

### directory structure including .git directory

- `.`: known as the *working directory* this holds the current checkout of the files you are working on. These files can be removed/replaced as you switch branches.
- `.git/HEAD`: special pointer to current branch
- `.git/config`: git local config (see "configuration" below)
- `.git/description`: description of project
- `.git/hooks/`: pre- and post- action hooks helps you prepare commits, etc.
- `.git/index`: index file (see next)
- `.git/logs/`: history of where branches have been
- `.git/objects/`: all the files, directories… everything (commits, trees, blobs, tags)
- `.git/refs`: files which are pointers to your branches (to different versions of files, etc.)

### git index

Staging area between working directory and your repository. Build up a set of changes that you want to commit. When you create the commit, the index is committed, not the current working directory.

### Installation

Ubuntu:

- `$ apt-get install git-core`

Mac (installer):

- [Download installer](http://code.google.com/p/git-osx-installer)
- or use MacPorts: `$ sudo port install git-core +svn +doc +bash_completion +gitweb`

### Configuration

- `/etc/gitconfig`: System-wide (--system) git configuration
- `~/.gitconfig`: user-wide (--global)
- `.git/config`: repository-wide

To edit:

	$ git config --global user.name "John Doe"
	$ git config --global user.email johndoe@example.com

To check settings:

	$ git config --list
	$ git config user.name

#### Default editor

	$ git config --global core.editor mvim

#### To change diff tool

	$ git config --global diff.tool vimdiff
	$ git config --global merge.tool vimdiff

The latter is the diff tool to resolve merge conflicts. Allowed by default: kdiff3, tkdiff, meld, xxdiff, emerge, vimdiff, gvimdiff, ecmerge, and opendiff

To customize. [Do not](http://jeetworks.org/node/90)

	$ git config --global diff.external somescript.sh

Do not due the above as it open for even small changes. Instead:

	$ git config --global diff.tool default-difftool

(…TODO)

invoke with git difftool

### Help

	$ git help <verb>
	$ git <verb> --help
	$ man git-<verb>

(or go to #git or #github on free node)

## CREATING GIT

### Initialize existing directory

	$ cd <to directory>
	$ git init

(creates .git directory)

	$ git add *
	$ git commit -m 'initial project version'

### Clone an existing repository

	$ git clone git://github.com/schacon/grit.git mygrit

This makes a local repository in a directory "my grit" from a remote in github.

(clone gets the repository from a remote, checkout changes the working copy on a local repository)

Protocols:

- `git://` public protocol
- `http(s)://` good for around firewalls. can be private if http user/password locked
- `user@server:/path.git` SSH transfer product.

## Working on the files

### Status of files

	$ git status

Main tool.
	
	# On branch master
	nothing to commit (working directory clean)

means that you have a clean working directory (no tracked or modified files) and that you are currently on the `master` branch.	git c

	$ git add README

start tracking the file "README" (or if tracked and modified, this will stage it).

Note if the file in `git status` is listed in both staged and modified, it means that the staged file differs from the current one. It will commit the older version.

### File types

#### Blobs

	$ git show 6ff87c4664
	Note that the only valid version of the GPL as far as this project
	is concerned is _this_ particular version of the license (ie v2, not
	v2.2 or v3.x or whatever), unless explicitly otherwise stated.

This will show the contents of the object.

#### Trees

You can do the same with tree objects, but it's best to use `ls-tree`

	$ git ls-tree fb3a8bdd0ce
	100644 blob 63c918c667fa005ff12ad89437f2fdc80926e21c   .gitignore
	100644 blob 5529b198e8d14decbe4ad99db3f7fb632de0439d   .mailmap
	100644 blob 6ff87c4664981e4397625791c8ea3bbb5f2279a3   COPYING
	040000 tree 2fb783e477100ce076f6bf57e4a6f026013dc745   Documentation
	100755 blob 3c0032cec592a765692234f1cba47dfdcc3a9200   GIT-VERSION-GEN
	100644 blob 289b046a443c0647624607d471289b2c7dcd470b   INSTALL
	100644 blob 4eb463797adc693dc168b926b6932ff53f17d0b1   Makefile
	100644 blob 548142c327a6790ff8821d67c2ee1eff7a656b52   README

Note that git doesn't pay attention to any perms other than execute. All files are mode `644` or `755`.

Note that if using **submodules**, trees may contain commits.

#### Commits

It shows the physical state of a tree and a description of how we got there.

	$ git show -s --pretty=raw 2be7fcb476

	commit 2be7fcb4764f2dbcee52635b91fedb1b3dcf7ab4	tree fb3a8bdd0ceddd019615af4d57a53f43d8cee2bf
	parent 257a84d9d02e90447b149af58b271c19405edb6a
	author Dave Watson <dwatson@mimvista.com> 1187576872 -0400
	committer Junio C Hamano <gitster@pobox.com> 1187591163 -0700
	
	    Fix misspelling of 'suppress' in docs
	
	    Signed-off-by: Junio C Hamano <gitster@pobox.com>
- *tree*: sha1 name of tree object containing the directory at a point in time
- *parent(s)*: sha1 name of commits that were previous states. Merges have more than one parent, and "root" commits have none.
- *author*: name of person responsible with change (and date)
- *commiter*: name of prson who created the commit, with date done. Different from author if author wrote the patch and e-mailed it
- *comment*: describes the commit
#### Tag

	$ git cat-file tag v1.5.0
	object 437b1b20df4b356c9342dac8d38849f24ef44f27	type commit	tag v1.5.0
	tagger Junio C Hamano <junkio@cox.net> 1171411200 +0000
	GIT 1.5.0
	-----BEGIN PGP SIGNATURE-----  
	  
	Version: GnuPG v1.4.6 (GNU/Linux)  

	iD8DBQBF0lGqwMbZpPMRm5oRAuRiAJ9ohBLd7s2kqjkKlq1qqC57SbnmzQCdG4ui
	nLE/L9aUXdWeTFPron96DLA=
	=2E+0
	-----END PGP SIGNATURE-----

- May want signature to verify commit as "official"
- There exist "lightweight tags" that are not tags but references whose name beings with `ref/tags`

## Resources

- [Github help](http://help.github.com/)
- [Book: Pro Git](http://git-scm.com/book)
- [Github's Git Reference](http://gitref.org/index.html)

## Misc:

### [Change a remote repository](http://stackoverflow.com/questions/2432764/how-to-change-a-remote-repository-uri-using-git)

	git remote set-url origin git://new.url.here

(or edit the `.git/config` file)

Note that doing this did cause me to lose history.



### [MediaWiki-specific](http://www.mediawiki.org/wiki/Git/Workflow) (+Gerrit)

#### Phabricator

- [thread](http://wikimedia.7.n6.nabble.com/Phabricator-td4382202.html)
