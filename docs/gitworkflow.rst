.. highlight:: none

************
Git workflow
************

General workflow
================

For developing DFTB+ we use a feature-branching workflow described, for example,
in `Understanding the GitHub Flow
<https://guides.github.com/introduction/flow/>`_. The main points for most
developers are:

* Development happens on the `master` branch, which always contains a clean
  release-ready code.

* Every feature is developed in a separate feature branch, which is derived from
  the `master` branch. If the feature is mature enough (it works correctly, its
  code is clean, it is well documented, thoroughly tested, etc.), the feature
  branch is merged into the `master` branch.

The main official public (upstream) repository only contains two branches: the
branches `master` and `release`, latter containing tagged official releases.
Some short living intermediate branches (e.g. `stage` and `hotfix`) may appear
from time to time, but these are special purpose branches created by the
administrators/release managers and are not being used for feature development.

In order to add a feature, you have to do the following steps:

#. Fork the official (upstream) repository and set up your own repository. (This
   step you have to do only once.)

#. Make sure, your `master` branch is synchronised to the upstream `master`.

#. Derive a feature branch from the `master` branch of your forked project.

#. Develop your feature in your feature branch.

#. When your feature implementation has finished, make sure, it integrates well
   into the most recent version of the code. (The code may have evolved while
   you were implementing your feature.)

#. Issue a *pull request* for your feature branch.

#. Wait for feedback from the core developers and then apply any suggestions or
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
   branch. Therefore, to avoid this extra work, make sure you never change your
   personal `master`, apart from synchronising it with the upstream `master`.

  
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

#. Develop your new feature in your local branch. Make check-ins whenever it
   seems to be logically useful::

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



Merge the changes back into the upstream repository
===================================================

When you have finished implementing your feature, it should be merged back into
the upstream `master` as soon as possible, in order to minimise the number of
possible conflicts. Generally, you should try to implement features in the
smallest meaningful units, so that they can be quickly merged into the upstream
repository.

First, make sure, that your feature integrates well into the most recent code
version. Be aware that the upstream code may have evolved while you were
implementing your feature.

#. First synchronise your `master` branch to upstream `master` as written in the
   section `Synchronising to the upstream master branch`_.

#. Integrate any changes that appeared on `master` during your feature
   development. Depending on how complex your feature branch is (especially how
   many commits it contains), you should follow two different strategies:

   * For simple feature branches with only one or two commits: Rebase your
     feature branch on `master`:

     #. Check out your feature branch::
          
          git checkout some-new-feature

     #. Rebase it on `master`::
          
          git rebase master

     Note, that the rebase method changes your git commits by reverting your
     changes and reapplying them on top of the current code. As long as your
     feature branch was not used (forked) by anybody else, it does not do any
     harm and helps to keep the history of your feature branch linear and
     simple. However, you should never rebase any branches, which you have
     already shared with others.

   * For more complex feature branches with multiple commits: Merge the `master`
     branch into your feature branch:

     #. Check out your feature branch::

          git checkout some-new-feature

     #. Merge the `master` branch into it::

          git merge master
          
        This will result in an extra merge commit.

#. Test whether your updated feature branch still works as expected.

#. Push the latest status of your feature branch to your personal repository on
   GitHub::

       git push origin some-new-feature

   If you used the rebase method above and have pushed your branch to GitHub at
   least once already before the rebase, you may need the option ``-f`` to
   change the git-history (previous git-commits) also on GitHub.

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
