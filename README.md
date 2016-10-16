# git-bioc
The official way is to keep a Git repository in sync with a corresponding Bioconductor SVN repository is to make use of `git-svn` after proper [configuration](http://bioconductor.org/developers/how-to/git-mirrors/).  Such a Git repository can be hosted anywhere, including on GitHub.

These `git-bioc` tools provides a set of Git commands that helps you keep the Git repository and the Bioconductor _devel_ SVN repository in sync.  They are meant to be intuitive and easy to remember.

These `git-bioc` tools were designed with the following work process in mind:

* The Git repository is the main source of the Bioconductor package.
* Since the Git repository is the main source, you should as far aa possible strive to make all commits to Git.
* When you think the updates are ready to go live on Bioconductor, then should be committed there:
  - Committing to the Bioconductor devel SVN repository takes place in two steps: (i)`git bioc export` followed by (ii) `git bioc push`).  _Any Git-Bioc SVN conflicts have to be resolved in the first step_.
  - After committing, Bioconductor will at scheduled times take a snapshot of the package ([3 times/week on Monday, Wednesday and Saturday at 19:15 PT](https://twitter.com/henrikbengtsson/status/783744785404145664)) and build and check it. When this is done (~2 days later because all of Bioconductor's packages need to be checked first), the updated package is made it available via the Bioconductor package repository so it can be installed via `biocLite()`.  At this time, the corresponding Bioc devel package page is also updated.
* At times there will be updates committed directly to the Bioconductor SVN (which were not committed to your Git repository first).  This happens when the version of the Bioc package is bumped in preparation of a new major Bioconductor release (twice a year).  It may also happen when Bioconductor core developers find it necessary to make direct adjustments to your package.  It may also happen if you or one of your collaborators by mistake commit to the Bioconductor SVN instead of the Git repository.
  - When this happens, you need to import such Bioc-only updates into the Git repository which takes place in two steps: (i) `git bioc pull` followed by (ii) `git bioc import`.  _Any Git-Bioc SVN conflicts have to be resolved in the second step_.

Note that all synchronization via your Git repository and Bioconductor SVN is done by you locally via these `git-bioc` tools.  Online repositories such as GitHub are not involved in this synchronization.  They are only used to host your Git repository online.


**IMPORTANT ABOUNT THE CURRENT IMPLEMENTATION**:
_I developed git-bioc mainly for my own needs_, because I couldn't remember all failed to remember all the steps in the official [Bioconductor Git Mirror instructions](http://bioconductor.org/developers/how-to/git-mirrors/). **More importantly**, all the packages I've used `git-bioc` for - [affxparser](https://github.com/HenrikBengtsson/affxparser), [aroma.light](https://github.com/HenrikBengtsson/aroma.light) and [illuminaio](https://github.com/HenrikBengtsson/illuminaio) - were previously using the Bioconductor 'Git-SVN bridge' (deprecated and [removed on 2016-01-29](https://stat.ethz.ch/pipermail/bioc-devel/2016-January/008590.html)).  Any Bioconductor package that used this 'Git-SVN bridge' is likely to have a "non-linear" Git history / tree, which complicates our synchronization.  The current implementation of the `git-bioc` tools handles this problem by:

* Exporting to Bioc SVN: All Git commits are incorporated into a single Bioconductor SVN commit.  The [SVN commit message is currently not that informative](https://github.com/Bioconductor-mirror/affxparser/commit/af05fcb413cff368c1f2bd0bd23031c2eb67a969).
* Importing from Bioc SVN: All Bioconductor SVN commits are incorporated into a single Git commit.  The Git commit message is currently not that informative.

**WARNING**: Did you read the previous paragraph?  It is very important!  Make sure you understand it.


## Usage

### Setup
To use the `git bioc` tools, change to the directory of your Bioconductor package and run the following once per package / repository:
```sh
git bioc init
```
This will setup `bioc/devel` and `bioc/release-x.y` branches and bind them to the remote Bioconductor SVN branches.


### Get an overview

To get an overview of the repository and its branches, do:
```sh
$ git bioc info
*** Bioconductor GitHub-SVN Bridge history (master branch):
Switched to branch 'master'
- YES (contains commits by Bioconductor GitHub-SVN Bridge - bioc-sync@bioconductor.org)

*** Bioconductor SVN URLs:
svn-remote.devel.url=https://hedgehog.fhcrc.org/bioconductor//trunk/madman/Rpacks/illuminaio
svn-remote.release-3.0.url=https://hedgehog.fhcrc.org/bioconductor//branches/RELEASE_3_0/madman/Rpacks/illuminaio
svn-remote.release-3.1.url=https://hedgehog.fhcrc.org/bioconductor//branches/RELEASE_3_1/madman/Rpacks/illuminaio
svn-remote.release-3.2.url=https://hedgehog.fhcrc.org/bioconductor//branches/RELEASE_3_2/madman/Rpacks/illuminaio
svn-remote.release-3.3.url=https://hedgehog.fhcrc.org/bioconductor//branches/RELEASE_3_3/madman/Rpacks/illuminaio

*** Git-SVN branch tree:
* 5158646 (git-svn-devel, bioc/master, bioc/devel) bump version after creating 3.3 branch
* 27180e1 Bump versions prior to creation of 3.3 branch
* 3b02bbd Commit made by the Bioconductor Git-SVN bridge.
* 836448e Commit made by the Bioconductor Git-SVN bridge.
* a11385d Bumped version number of all packages after creation of 3.2 branch.
* 617cf7e Bumped versions of all packages prior to creating 3.2 branch.
* 8f4e390 Commit made by the Bioconductor Git-SVN bridge.
* b3be4a2 Commit made by the Bioconductor Git-SVN bridge.
* e5d5193 Commit made by the Bioconductor Git-SVN bridge.
* 67b5048 Commit made by the Bioconductor Git-SVN bridge.
```


### Bioc-Git: Pull down all commits from the Bioconductor SVN repository
To update the local `bioc/devel` branch with what is on the Bioconductor SVN server, do:
```sh
$ git bioc pull
```

### Bioc-Git: Update master branch with bioc/devel branch
To update the local `master` branch with updates on the local `bioc/devel` branch, do:
```sh
$ git bioc import
```


### Git->Bioc: Update bioc/devel branch with master branch
To update the local `bioc/devel` branch with updates on the local `master` branch, do:
```sh
$ git bioc export
[...]
```


### Git->Bioc: Push bioc/devel branch to Bioconductor SVN repository
To push (formally svn commit) the local `bioc/devel` branch to the Bioconductor SVN server, do:
```sh
$ git bioc push
Switched to branch 'bioc/devel'
Your branch is up-to-date with 'bioc/master'.
Committing to https://hedgehog.fhcrc.org/bioconductor/trunk/madman/Rpacks/illuminaio ...
[...]
```



## Installation instructions
To use the `git bioc` commands, place all `git-bioc*` files on the `PATH`.  For instance, if you have `~/bin/` on the `PATH`, then copy them there:
```sh
git clone https://github.com/HenrikBengtsson/git-bioc.git
cp git-bioc/bin/git-bioc* ~/bin/
chmod ugo+x ~/bin/git-bioc*
```
After this, you can verify the installation as:
```sh
$ git bioc
usage: git bioc <subcommand>

Available subcommands are:
   export    Copies updates from master branch to bioc/devel.
   import    Cherry-picks commits from bioc/devel into master branch.
   info      Show information about Bioconductor devel SVN.
   init      Initialize a new git bioc repo.
   pull      Update branch bioc/devel with devel on Bioconductor SVN server.
   push      Commit bioc/devel updates to Bioconductor SVN server.
```
