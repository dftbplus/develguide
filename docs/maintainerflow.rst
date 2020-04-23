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

- Coordinating developers, repository administrators: They are responsible for
  strategic decisions. Some of them also represent the project in certain
  commercial collaborations.

- Core developers, repository maintainers: Experienced, skilled developers
  contributing regularly to the projects and having a good overview over the
  project. They are often responsible for certain set of features within the
  code.

- Contributors: casual contributors.


Access Rights
=============

The groups as listed above have following rights:

- Repository administrators: They have all possible rights on the repositories,
  including the possibility to grant rights to other developers.

- Repository maintainers: They have write access to project repositories. They
  can review and merge pull requests (PR). They can write into the corresponding
  repository of an of contributors during the review process. They have access
  to the test server, so that they can execute extended tests on the code in a
  pull request.

- Contributors: They have the usual read and fork rights as granted by GitHub.


Merging pull requests
=====================

The following rules are currently applied when merging pull requests:

- All changes to the master branch should be made via pull requests.

- Pull requests must be approved by a maintainer before they can be merged. Pull
  requests issued by a maintainer must be approved by an other maintainer.

- Trivial pull requests can be merged by any maintainer, provided it had been
  approved according to the rule above.

- Pull requests with non-trivial or substantial changes (major re-structuring,
  changes in code behavior, large functionality additions, etc.) must be
  discussed with and approved by an admin before merging.

- Pull requests should pass the extended manually triggered buildbot tests (not
  just the automatic travis tests) before they can be merged.

- Both, the person making the pull request and the person merging the pull
  request are responsible for the quality and the impact of the merged changes.


Merging strategy
================

Following strategy should be applied when merging the pull request:

- If the PR contains a **single commit** only and is based on current master, it
  can be merged by fast-forward.

- In all other cases, a merge commit should be created.

- The code in the PR should be brought up to date with master by the contributor
  before the review. The contributor is of course free to decide, whether she/he
  does it via a merge (merging master into the feature branch) or via a rebase
  (rebasing the feature branch on current master -- only for skilled
  contributors!). However, once the review has started, no rebasing of the
  feature branch should happen any more.
