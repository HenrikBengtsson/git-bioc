# git-bioc
Git commands to keep a the Git repository and the Bioconductor devel SVN repository for a Bioconductor package in sync.


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
$ git bioc pull
```


### Git->Bioc: Update bioc/devel branch with master branch


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
