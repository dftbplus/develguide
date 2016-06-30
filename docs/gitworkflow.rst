.. highlight:: none

************
Git workflow
************

General workflow
================

#. Fork the desired repository `REPO` (owned by the user `UPSTREAM`) to
   *your personal* Bitbucket account. You find the `Fork` action in the menu
   indicated by three dots below the project logo.

#. In the settings menu of your personal fork, change the main branch from
   `master` to `develop`. (Since you will only contribute to the `develop`
   branch, you do not need to deal with the `master` branch at all.)

#. Check out your personal fork to your local machine::

       git clone git@bitbucket.org:YOUR_USER_NAME/REPO.git

#. Check whether your local repository contains the `develop` branch, but
   not the `master` branch::

       cd REPO
       git branch

   Only `develop` should pop up in the list of branches, but not `master`.

#. Set up a mirror of the upstream reference repository::

       git remote add upstream git@bitbucket.org:UPSTREAM/REPO.git

#. Fetch the upstream remote::
      
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


Developing your feature
=======================

If you have already forked the project for an other feature branch before,
execute Step 1 in section `Staying up to date with the upstream develop branch`_
before carrying out the following steps.  This way you make sure that your
`develop` branch is synchronized with the upstream one. Otherwise, you can start
directly with the steps below:

#. Create you own feature branch::

       git checkout -b some-new-feature

   You always have to create an extra branch derived from `develop`, if you
   develop a new feature.  You should never work on the develop branch directly,
   or merge anything from your feature branches into it. Its only purpose is to
   mirror the status of the upstream develop branch.

#. Develop your new feature in your local branch. Make check-ins, whenever
   it seems to be logical and useful::

       git commit -m "Some new thing added...."

#. If you want to share your development with others (or make a backup of your
   repository in the cloud), upload the current status of your local feature
   branch by pushing it to your personal repository::

       git push --set-upstream origin some-new-feature

   This also automatically connects the appropriate branch of your personal
   repository on Bitbucket (`origin/some-new-feature`) with your local branch
   (`some-new-feature`), so from now on, if you are on your `some-new-feature`
   branch, a simple::

       git push

   command without any additional options will be enough to transfer your recent
   changes on this branch to Bitbucket.


Staying up to date with the upstream develop branch
===================================================

Time to time you should make sure, that your `develop` branch is up to date with
the upstream `develop` branch.

#. Pull the recent changes from the upstream develop branch into your local
   develop branch::

       git checkout develop
       git pull --ff-only upstream develop

   Upload the changes in your local develop branch to Bitbucket by issuing::

       git push origin develop

   Note: if the ``git pull --ff-only ...`` command fails, you probably have
   messed up your personal develop branch (despite all the warnings above), and
   it can not made to be identical to the upstream one any more. In that case,
   you can revert it via hard reset::

       git reset --hard upstream/develop

   You will then eventually have to derive a new feature branch from the
   resetted `develop` branch, and add your changes on `some-new-feature`
   manually to it. So better try not to polute your `develop` branch.

#. After pulling the recent changes from the upstream `develop` branch, change
   back to your feature branch, to make sure you do not commit anything into
   `develop`::

       git checkout some-new-feature

#. Update your feature branch to incorporate the recent changes on the upstream
   `develop` branch (which you've pulled before), by merging your local
   `develop` branch into `some-new-feature`::

       git merge develop

   If you encounter any conflicts, resolve them, and commit the merge to
   `some-new-feature`.



Merging back the changes into the upstream repository
=====================================================

When you have finished the implementaiton of your feature and you would like to
get it merged into the upstream `develop` branch, issue a pull request.

#. First, make sure, that you have pulled the latest changes of the upstream
   develop branch to your local `develop` branch, and that you have merged those
   changes into your feature branch. (Follow the steps in the previous section.)

#. If not done yet, upload your feature branch to your personal repository
   on bitbucket::

       git push origin some-new-feature

   If your repository was set to private, make sure, that at least the  core
   developers have read access to it.

#. Issue a pull request on bitbucket for your some-new-feature branch. (Look for
   the upwards arrow in the left menu.) Make sure, that the target of your pull
   request the `develop` branch of the upstream repository
   (`UPSTREAM/REPO`).

#. Wait for the comments of core the developers, fix things you are asked for,
   and push the changes to your feature branch on bitbucket. Also, keep your
   `develop` and feature branch up to date with the upstream changes.

#. Once the discussion on your pull request is done, one of the developers with
   write permission to the upstream repository will merge your branch into the
   upstream `develop`-branch. Once this has happened, you should see your
   changes showing up there.


Deleting your feature branch
============================

If your feature had been merged into the upstream code, you can delete your
feature branch locally and on Bitbucket as well:

#. In order to delete the feature branch locally, change to the develop branch
   (or any branch other than your feature branch) and delete your feature
   branch::

       git checkout develop
       git branch -d some-new-feature

#. In order to delete the feature branch on Bitbucket as well, use the command::

       git push origin --delete some-new-feature

This closes the development cycle of your old feature and opens a new one for
the next feature you are going to develop. You can then again create a new
branch for the new feature and develop your next extension starting with the
steps described in section `Developing your feature`_.
