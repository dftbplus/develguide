.. highlight:: none

*****************************
Git workflow for contributors
*****************************

General workflow
================

For developing DFTB+ we use a feature-branching workflow described, for example,
in `Understanding the GitHub Flow
<https://guides.github.com/introduction/flow/>`_. The main points for most
developers are:

* Development happens based on the `master` branch, which always contains a
  clean release-ready code.

* Every feature is developed in a separate feature branch, which is derived from
  the `master` branch. If the feature is mature enough (it works correctly, its
  code is clean, it is well documented, thoroughly tested, etc.), the feature
  branch is merged into the `master` branch.

* In order to ease integration, feature branches should be **short living** and
  pull requests should only contain a **reasonably small amount** of
  changes. Try to chop your implementation into small self containing changes
  and issue those in separate consecutive pull requests. (No one likes or is
  efficiently able to review a code change containing thousands of lines.)


In order to add a feature, you have to do the following steps:

#. Fork the official (upstream) repository and clone it. (This step you have to
   do only once.)

#. Synchronize your `master` branch (``origin/master``) to be identical to
   upstream `master` (``upstream master``).

#. Derive a feature branch from the `master` branch of your forked project.

#. Develop and finish your feature in your feature branch.

#. Integrate eventual changes form the upstream master branch.

#. Issue a *pull request* for your feature branch.

#. Wait for feedback from the maintainers and then apply any suggestions or
   required changes to your feature branch.

#. When you obtain the notification that your feature branch has been merged to
   the upstream `master` branch, delete the feature branch in your personal
   repository.

#. In order to develop the next feature, execute the above steps again,
   *starting from step 2*.

Below you find a detailed description of each step, using the DFTB+ main
repository as an example. If you work on an other DFTB+ related project, replace
the repository name `dftbplus` with the actual repository name.



Fork the project
================

Fork the repository
-------------------

#. Fork the desired repository (e.g. `dftbplus`) owned by the user `dftbplus` to
   *your personal* GitHub account. You will find the `Fork` button in the upper
   right corner on the project page.

#. Check out your personal fork to your local machine::

       git clone git@github.com:YOUR_USER_NAME/dftbplus.git

#. Set up a mirror of the upstream reference repository::

       git remote add upstream git@github.com:dftbplus/dftbplus.git



Set up your own repository
--------------------------

Set up your identity
....................

When you contribute to our project it is important that the author information
of your commits contain your full name and a valid (preferably your official)
email address. Set up those for your repository (or globally by adding the
``--global`` option) by ::

    git config user.name 'FULL_NAME'
    git config user.email 'EMAIL_ADDRESS'


Add check on commit message formatting
......................................

We use the commonly adopted git commit message format containing a short
imperative subject line and an optional detailed description which is separated
by an empty line (see for example `How to Write a Git Commit Message
<https://chris.beams.io/posts/git-commit/>`_). Using a simple commit message
hook, git can check that your commit messages follow this format. Please copy
our special `git commit hook
<https://gist.github.com/aradi/a651ee97cc6bd09acb237794a05eaa7f>`_ as
`.git/hooks/commit-msg` into your repository and make it executable (``chmod +x
.git/hooks/commit-msg``).

You may wish to make this a global hook for all of your git repositories by
adding it to an init.templatedir directory. This can be added for `all`
repositories with ::
  
  git config --global init.templatedir '~/.git-templates'
  mkdir -p ~/.git-templates/hooks

The commit-msg file can then be placed in `~/.git-templates/hooks/commit-msg`.
We would then also suggest setting the permission to be user writable only ::
  
  chmod -R 700 ~/.git-templates

Any new local repositories will settings specified from this directory, unless
overridden by a local `.git/` directory within the repository itself. Existing
repositories need to be reinitialised in their top directory to use the
init.templatedir ::

  git init

Again, any local `.git/` directory overrides settings in `~/.git-templates`


Synchronising to the upstream master branch
===========================================

Before you start developing a feature, you should make sure that you implement
your feature in the most recent version of the code. This minimises the chances
of conflicts (and additional work needed from you) when your feature is later
merged into the upstream repository:

#. Pull the recent changes from the upstream `master` branch into your local
   `master` branch::

       git checkout master
       git pull --ff-only upstream master

   Upload the changes in your local `master` branch to GitHub by issuing::

       git push origin master

   **Note:** if the ``git pull --ff-only upstream master`` command fails, you
   have probably polluted your personal master branch, and it can no longer be
   made to exactly match the upstream one. In that case, you may revert it via a
   hard reset::

       git reset --hard upstream/master

   You will then have to derive a new feature branch from the reset `master`
   branch and then add your changes manually to this new feature
   branch. Therefore, to avoid this extra work, make sure you *never change
   your personal `master` branch*, apart from synchronising it with upstream
   `master`.

  
Developing your feature
=======================

#. Check out your `master` branch, which you should have synchronised to
   upstream `master` as described in the previous section::

     git checkout master

#. Create you own feature branch::

     git checkout -b some-new-feature

   To develop a new feature you should always create a new branch derived from
   `master`.  You should never work on the `master` branch directly, or merge
   anything from your feature branches onto it. Its only purpose is to mirror
   the status of the upstream `master` branch. The feature branch name should be
   short and descriptive for the feature you are going to implement.

#. Develop your new feature in your local branch. Make sure to add regression
   tests for your feature in the test directory and to update the documentation.
   You can commit your changes by ::

       git commit -m "Add some new feature ..."

   You may make multiple commits if your development naturally dividides into
   multiple steps. But please note, that too many commits and especially commits
   containing broken or non-functional code make finding bugs (e.g. by
   git-bisection) a real pain. Therefore, make sure that your branch only
   contains *essential commits with working code in each commit*.

   In case, you wish to remove some intermediate commits in your feature branch,
   you may use the interactive rebasing::

     git rebase -i HEAD~N

   where ``N`` should be replaced by the number of commits you would like to
   rearrange/squash. As interactive rebasing changes the git-history, make sure
   that

   * you only squash commits of your feature branch, no earlier ones,

   * you squash your commits before any other branches had been derived from
     your feature branch and

   * you squash your commits before any other branches had been merged into your
     feature branch.


Merge the changes back into the upstream repository
===================================================

First, make sure, that your feature integrates well into the most recent main
code version. Be aware that the upstream code may have evolved while you were
implementing your feature.

#. Synchronise your `master` branch to the upstream `master`, as written in the
   section `Synchronising to the upstream master branch`_.

#. Integrate any changes that appeared on `master` during your feature
   development.

   * If your feature branch consists of one/two commits only, it does not
     contain any merge-commits and no other branches had been derived of it (and
     you are an experienced git user) you may rebase your branch on current
     `master`:

     - Check out your feature branch::
          
         git checkout some-new-feature
       
     - Rebase it on `master`::
           
         git rebase master
     
       Resolve any conflicts arrising during the rebase process.

   * Othwerise use a normal merge to update your feature branch with the latest
     development on master:
     
     - Check out your feature branch::
     
         git checkout some-new-feature
     
     - Merge the `master` branch into it::
     
         git merge master
            
     This will result in an extra merge commit.

#. Test whether your updated feature branch still works as expected (having
   regression tests for your feature can help here).

#. Publish your feature branch to your personal repository on GitHub::

       git push origin some-new-feature

#. Issue a pull request on GitHub for your `some-new-feature` branch (look for
   the upwards arrow in the left menu).

#. Wait for the comments of other developers, apply any fixes you are asked to
   make, and push the changes to your feature branch on GitHub.

#. Once the discussion on your pull request is finished, one of the developers
   with write permission to the upstream repository will merge your branch into
   the upstream `master` branch. Once this has happened, you should see your
   changes showing up there.


Delete your feature branch
==========================

Once your feature has been merged into the upstream code you should delete your
feature branch, both locally and on GitHub as well:

#. In order to delete the feature branch locally, change to the `master` branch
   (or any branch other than your feature branch) and delete your feature
   branch::

       git checkout master
       git branch -d some-new-feature

#. In order to delete the feature branch on GitHub as well use the command::

       git push origin --delete some-new-feature

This closes the development cycle of your feature and opens a new one for the
next one you are going to develop. You can then again create a new branch for
the new feature and develop your next extension starting with the steps
described in section `Synchronising to the upstream master branch`_.


A few notes about Submodules
============================

The DFTB+ program uses several libraries from elsewhere in the project. Some of
those libraries (e.g. MpiFx, ScalapackFx, libNEGF, libMBD) are included within
the repository via the git `submodule` mechanism.


Checking out submodules
-----------------------

When checking out the code, you should pull the submodules with ::

  git submodule update --init --recursive


Updating submodules after changing to a branch
----------------------------------------------

If you change between branches, the branch you change into may reference a
different commit of a submodule as the branch you just have left. You can
recognise this by looking at the status of the submodules after the branch
change, e.g. by issuing ::

  git status

The directories containing affected submodules will have their status set to
"modified". These submodules must be realigned to the correct commit (to the
commit recorded for the current branch) before you do any other work in the
branch. This you can for all submodules by issuing ::

  git submodule update --recursive

If not only the submodule commit id but also the repository URL of a submodule
changes when switching to a new branch, you have to synchronise the repository
URLs first before doing the update, e.g. ::

  git submodule sync --recursive
  git submodule update --recursive
  

Changing submodule content
--------------------------

If you need to modify the submodules, you should fork their respective projects
and work according their development workflow.

If you want to update DFTB+ to use a new version of a given submodule, do the
following steps:

#. Go to the ``origin`` folder containing the submodule.

#. Fetch the relevant branch from the upstream-project of the submodule.

#. Check out the commit which should be used by DFTB+. (If this commit is on a
   different branch as the one recorded in the `.gitmodules` file in the DFTB+
   source folder, make sure to correct the branch name there.)

#. Update the submodule commit ID's (recorded in `CMakeFiles.txt`) by
   executing ::

     ./utils/test/check_submodule_commits -u

   from the DFTB+ source folder.

#. Stage the submodule folder and the `CMakeFiles.txt` file for a commit and
   commit your changes.


Referencing submodules
----------------------

Since the code should be available for users without accounts on github.com, all
submodules are included as web (https) links instead of ssh references. 

If you work on the integration of the submodules, you might find it useful to
globally configure git to substitute ssh links for the https references by
issuing the command ::

  git config --global url.ssh://git@github.com/.insteadOf https://github.com/

You can alternatively set up this substitution for only your local `dftbplus`
repository. You should run this command in the directory containing your copy
and leave out the ``--global`` option.
