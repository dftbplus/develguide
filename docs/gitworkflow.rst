.. highlight:: none

************
Git workflow
************

General workflow
================

For developing DFTB+ we use the workflow described by Vincent Driessen in `A
successful Git branching model
<http://nvie.com/posts/a-successful-git-branching-model/>`_. The main points for
most developers are:

* The `master` branch only contains official (tagged) releases.

* Development happens on the `develop` branch, which always contains a clean
  release-ready code.

* Every feature is developed in a separate feature branch derived from the
  `develop` branch. If the feature is mature enough (it works correctly, its
  code is clean, it is well documented, automatic tests have been created etc.),
  the feature branch will be merged into the `develop` branch.

The main official public (upstream) repository only contains two branches: the
branches `master` and `develop`. Some short living intermediate branches
(e.g. `release` and `hotfix`) may appear from time to time, but these are
special purpose branches created by the administrators/release managers and are
not being used for feature development.

In order to add a feature, you have to do the following steps:

#. Fork the official (upstream) repository and set up your own repository. (This
   step you have to do only once. If you've already forked the upstream
   repository and set up your own repository, skip it.)

#. Derive a feature branch from the `develop` branch of your forked project.

#. Develop your feature in your new branch.

#. Regularly update your `develop` branch from the upstream repository to make
   sure that your `develop` branch remains synchronised to the upstream one.

#. Regularly merge your `develop` branch into your feature branch. This ensures
   that your feature branch is based on the most recent state of the upstream
   `develop` branch.

#. When the feature implementation has finished, do repeat steps 4 and 5 once
   more. Then issue a *pull request* of your feature branch into the `develop`
   branch of the upstream repository.

#. Wait for feedback from the core developers and then apply any suggestions or
   required changes to your feature branch. Also make sure to keep your feature
   branch up to date with the upstream `develop` on the main repository by
   executing steps 4 and 5.

#. When you obtain the notification that your feature branch has been merged to
   the upstream `develop` branch, delete the feature branch in your personal
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

#. Go to the settings menu of your personal fork and make sure that in the
   branches sub-menu the default branch is set to `develop` (which should be
   automatically the case). Since you will only contribute to the `develop`
   branch, you do not need to deal with the `master` branch at all.

#. Check out your personal fork to your local machine::

       git clone git@github.com:YOUR_USER_NAME/dftbplus.git

#. Check whether your local repository contains the `develop` branch, but
   not the `master` branch::

       cd dftbplus
       git branch

   Only `develop` should appear in the list of branches and not `master`.

#. Set up a mirror of the upstream reference repository::

       git remote add upstream git@github.com:dftbplus/dftbplus.git

#. Fetch the upstream remote repository::
      
       git fetch upstream

#. Check out the develop branch. (Actually, you should already be on that
   automatically, as you've set the default branch to be the develop branch)::

       git checkout develop

#. Reset your local develop branch to be identical to the upstream develop
   one::

       git reset --hard upstream/develop

  You should see no changes, as the two branches were identical. They should be
  always automatically remain identical, if you follow the strategy outlined in
  this document. Pull requests to the upstream repository are only accepted if
  they are derived from a develop-branch *identical* to the upstream one.


Set up your own repository
--------------------------

Set up your identity
....................

When you contribute to our project, it is important that the author information
of your commits contain your full name and a valid (preferably your official)
email address. Set up those for your repository (or globally by adding the
``--global`` option) by ::

    git config user.name 'FULL_NAME'
    git config user.email 'EMAIL_ADDRESS'


Add check on commit message formatting
......................................

We use the commonly adopted git commit message format containing a short
imperative subject line and optional detailed description separated by an empty
line (see for example `How to Write a Git Commit Message
<https://chris.beams.io/posts/git-commit/>`_). Using a simple commit message
hook, git can check that your commit messages align with this format. Please
copy our special `git commit hook
<https://gist.github.com/aradi/a651ee97cc6bd09acb237794a05eaa7f>`_ as
`.git/hooks/commit-msg` into your repository and make it executable (``chmod +x
.git/hooks/commit-msg``).

You may wish to make this a global hook for all of your git repositories by
adding it to an init.templatedir directory. This can be added for `all`
repositories with ::
  
  git config --global init.templatedir '~/.git-templates'
  mkdir -p ~/.git-templates/hooks

The commit-msg file can then be placed in `.git/hooks/commit-msg`. We would then
also suggest setting the permission to be user writable only ::
  
  chmod -R 700 ~/.git-templates

Any new local repositories will use any specified settings from this directory,
unless overridden by a local `.git/` directory within the repository
itself. Existing repositories need to be reinitialised to use the
init.templatedir ::

  git init

Again, any local `.git/` directory overrides settings in `~/.git-templates`

  
Developing your feature
=======================

If you have already forked the project for another earlier feature branch,
execute Step 1 in the section `Staying up to date with the upstream develop
branch`_ before carrying out the following steps below.  In this way you make
sure that your `develop` branch is synchronised with the upstream
one. Otherwise, you can start directly with the steps below:

#. Create you own feature branch::

       git checkout -b some-new-feature

   To develop a new feature you should always create a new branch derived from
   `develop`.  You should never work on the develop branch directly, or merge
   anything from your feature branches onto it. Its only purpose is to mirror
   the status of the upstream develop branch.

#. Develop your new feature in your local branch. Make check-ins whenever it
   seems to be logical and useful::

       git commit -m "Some new thing added...."

#. If you want to share your development with others (or make a backup of your
   repository in the cloud), upload the current status of your local feature
   branch by pushing it to your personal repository::

       git push --set-upstream origin some-new-feature

   This also automatically connects the appropriate branch of your personal
   repository on GitHub (`origin/some-new-feature`) with your local branch
   (`some-new-feature`), so from now on, if you are on your `some-new-feature`
   branch, a simple::

       git push

   command without any additional options will be enough to transfer your recent
   changes on this branch to GitHub.


Staying up to date with the upstream develop branch
===================================================

From time to time you should make sure that your `develop` branch is up to date
with the upstream `develop` branch.

#. Pull the recent changes from the upstream develop branch into your local
   develop branch::

       git checkout develop
       git pull --ff-only upstream develop

   Upload the changes in your local develop branch to GitHub by issuing::

       git push origin develop

   Note: if the ``git pull --ff-only ...`` command fails, you probably have
   messed up your personal develop branch (despite all the warnings above), and
   it can no longer be made to match the upstream one. In that case, you can
   revert it via a hard reset::

       git reset --hard upstream/develop

   You will then have to derive a new feature branch from the reset `develop`
   branch and then add your changes manually to this new branch. So its better
   to not change your personal `develop` branch.

#. After pulling the recent changes back from the upstream `develop` branch,
   change back to your feature branch to ensure you do not commit anything into
   `develop`::

       git checkout some-new-feature

#. Update your feature branch to incorporate the recent changes on the upstream
   `develop` branch (which you've pulled before), by merging your local
   `develop` branch into `some-new-feature`::

       git merge develop

   If you encounter any conflicts, resolve them, and commit the merge to
   `some-new-feature`.



Merge the changes back into the upstream repository
===================================================

When you have finished implementing your feature and you would like to get it
merged into the upstream `develop` branch, issue a pull request.

#. Firstly make sure that you have pulled the latest changes of the upstream
   develop branch to your local `develop` branch and that you have merged those
   changes into your feature branch. (Follow the steps in the previous section.)

#. If not done yet, push your feature branch back to your personal repository on
   GitHub::

       git push origin some-new-feature

   If your repository was set to private make sure that at least the core DFTB+
   developers have read access to it.

#. Issue a pull request on GitHub for your `some-new-feature` branch (Look for
   the upwards arrow in the left menu). Make sure that the target of your pull
   request is the `develop` branch of the upstream repository
   (`dftbplus/dftbplus`).

#. Wait for the comments of other developers, apply any fixes you are asked to
   make, and push the changes to your feature branch on GitHub. Also, keep your
   `develop` and feature branch up to date with any upstream changes.

#. Once the discussion on your pull request is finished, one of the developers
   with write permission to the upstream repository will merge your branch into
   the upstream `develop` branch. Once this has happened, you should see your
   changes showing up there.


Delete your feature branch
==========================

If your feature has been merged into the upstream code you should then delete
your feature branch, both locally and on GitHub as well:

#. In order to delete the feature branch locally, change to the develop branch
   (or any branch other than your feature branch) and delete your feature
   branch::

       git checkout develop
       git branch -d some-new-feature

#. In order to delete the feature branch on GitHub as well use the command::

       git push origin --delete some-new-feature

This closes the development cycle of your old feature and opens a new one for
the next feature you are going to develop. You can then again create a new
branch for the new feature and develop your next extension starting with the
steps described in section `Developing your feature`_.
