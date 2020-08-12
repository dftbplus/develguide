.. highlight:: none


************************
Workflow for maintainers
************************

In order to ease the process of maintaining, the following strategy is applied
for the development of DFTB+ and related projects.

Roles
=====

We have three groups of developers with different access rights to the
repositories:

- Coordinating developers (repository administrators): They are responsible for
  strategic decisions. Some of them also represent the project externally.

- Core developers (repository maintainers): Experienced, skilled developers
  contributing regularly to the projects and having a good overview of the
  project. They are often responsible for certain sets of features within the
  code.

- Contributors: casual contributors.


Access Rights
=============

The groups as listed above have following rights:

- Repository administrators: They have all possible rights on the repositories,
  including the possibility to grant rights to other developers.

- Repository maintainers: They have write access to project repositories. They
  can review and merge pull requests (PR). They can write into the corresponding
  repository of contributors during the review process. They have access
  to the test server, so that they can execute extended tests on the code in a
  pull request.

- Contributors: They have the usual read and fork rights as granted by GitHub
  and can of course submit pull requests.


Merging pull requests
=====================

The following rules are currently applied when merging pull requests:

- All changes to the master branch should be made via pull requests.

- Pull requests must be approved by a maintainer before they can be merged. Pull
  requests issued by a maintainer must be approved by another maintainer.

- Trivial pull requests can be merged by any maintainer, provided self-submitted
  code has been approved by another maintainer.

- Pull requests with non-trivial or substantial changes (major re-structuring,
  changes in code behavior, large functionality additions, etc.) must be
  discussed with and approved by an admin before merging.

- Pull requests should pass the extended manually triggered buildbot tests (not
  just the automatic Travis tests triggered by GitHub) before they can be
  merged.

- Both, the person making the pull request and the person merging the pull
  request are responsible for the quality and the impact of the merged changes.


Merging strategy
================

Following strategy should be applied when merging the pull request:

- If the PR contains a **single commit** only and is based on current master, it
  can be merged by fast-forward.

- In all other cases, a merge commit should be created.

- The code in the PR should be brought up to date with master by the
  contributors before the review. The contributors are of course free to decide,
  whether they accomplish this via a merge (merging master into the feature
  branch) or via a rebase (rebasing the feature branch on current master -- only
  for skilled contributors!). However, once the review has started, no further
  rebases of the feature branch should happen.


Packaging releases
==================

A DFTB+ release if created by an annotated tag in the git repository either using the online interface provided by GitHub or the command line with

.. code-block:: none

   git tag -as <version>

This task is usually performed by repository administrators.


Updating the conda-forge feedstock
----------------------------------

Before a release tag is create in the main git repository the respective commit should be tested with the conda-forge toolchain.
To do this without a local conda installation, fork the respective feedstock repositories from the conda-forge organisation (*e.g.* if you plan to make a release in the https://github.com/dftbplus/mpifx, you can find the feedstock at https://github.com/conda-forge/mpifx-feedstock) and update the ``recipe/meta.yaml`` file to the release commit.

For a detailed workflow visit the conda-forge documentation about `updating feedstocks <https://conda-forge.org/docs/maintainer/updating_pkgs.html>`_.

To test a potential commit, download the commit using the git archive function and determine its SHA256 checksum with

.. code-block:: none

   > wget https://github.com/dftbplus/dftbplus/archive/<commit>.tar.gz -O dftbplus.tar.gz
   ...
   > sha256sum dftbplus.tar.gz

Update the ``url`` and ``sha256`` in the source field of the recipe.

.. code-block:: yaml
   :caption: recipe/meta.yaml

   source:
     url: https://github.com/dftbplus/{{ name }}/archive/<commit>.tar.gz
     sha256: <sha256>

Also, you want to reset the build number and bump the version number to the prospective release while already on it.
Check the ``patches`` section and remove any patch already applied in master.

If you have a local conda-smithy toolchain available, rerender the feedstock

.. code-block:: none

   > conda update conda-build conda-smithy conda-forge-pinning
   > conda smithy rerender

Read the output of the rerender step carefully, than commit your changes including the maintenance line suggested by conda-smithy and push to your fork of the feedstock.
If you do not have a local conda-smithy toolchain available commit anyway and push to your fork, but request the conda-forge webservice to rerender for you when creating the pull request.

Follow the usual GitHub workflow to create pull request against the feedstock repository, *read* the pull request template and tick of all points you have done, use a strikeout to remove irrelevant points.
Adding an additional unticked item like *bump to release tag* seems prudent.
If you have not yet rerendered the feedstock, add the suggested line to request the conda-forge webservice to rerender for you.

After successfully creating the pull request, wait for the friendly conda-forge webservices to comment into your pull request, usually the linter and, if you requested to rerender, also the webservice bot will comment on your pull request.
Now, you can check the continuous integration runs at Azure pipelines, Travis CI and/or Drone CI.
For DFTB+ this can take several minutes to build all the possible targets.
Everything should pass before you finally create the release tag and insert the correct release URL and SHA256 hash, adding the automerge label is also a possibility to let the conda-forge webservice handle the feedstock update for you.
In case the build fails inspect the logs and open an issue at the upstream repository, than go fix the issue and repeat from the beginning.

Note, some feedstocks are splitted into several packages, like the Python API of DFTB+ is separated from the main DFTB+ package, in this case you have to update several feedstocks at once.
