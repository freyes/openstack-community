# Developing OpenStack Charms

## Overview

The OpenStack charms are part of the OpenStack project, and follow the same development process as other projects.

For full details please refer to the OpenStack [development documentation][] and the charm specific [coding guidelines][].  This also includes details on signing up to become an OpenStack foundation member and getting set up with Gerrit and Launchpad to start development.

[development documentation]: http://docs.openstack.org/infra/manual/developers.html
[coding guidelines]: https://github.com/openstack-charmers/openstack-community/blob/master/python-coding-guidelines.md

## Git repositories

### Naming

All OpenStack charm repositories can be found on [github.com][] under the /openstack organisation.

Repositories are named `charm-<charm-name>` in order to namespace the repositories within the /openstack organization appropriately.

[github.com]: https://github.com/openstack?query=charm

### Branches

All charm repositories contain multiple branches; the master branch contains the current development focus, and a stable/YY.MM branch contains the current stable released charm.  Stable branches are named based on the point in time they where made, for example stable/16.04.

### Releases

The OpenStack Charms project produces an integrated release of charms every 3 months; Each release will have an associated stable branch of the format stable/YY.MM, for example stable/16.04 being the charm release from April 2016; only the most recent stable branch is actively maintained.

## Development Workflow

Broadly the workflow for making a change to a charm is:

```
git clone http://github.com/openstack/charm-cinder
cd charm-cinder
git checkout -b bug/XXXXXX master
```

Make the changes you need within the charm, and then run unit and pep8 tests using tox:

```
tox
```

resolve any problems this throws up, and then commit your changes:

```
git add .
git commit
```

Commit messages should have an appropriate title, and more detail in the body; they can also refer to bugs:

```
Closes-Bug: #######
Partial-Bug: #######
Related-Bug: #######
```

Gerrit will automatically link your proposal to the bug reports on launchpad and mark them fix commited when changes are merged.

Execute pep8 and unit tests:

```
tox
```

Finally, submit your change for review (if they pass pep8 and unit tests!):

```
git review
```

This will push your proposed changes to Gerrit and provide you with a URL for the review board on [review.openstack.org][]

To make amendments to your proposed change, update your local branch and then:

```
git commit --amend
git review
```

[review.openstack.org]: http://review.openstack.org


Read the OpenStack [development documentation][] for full details.

## Stable charm updates

Any update to a stable charm must first be applied into the master branch; it should then be cherry-picked in a review for the stable branch corresponding to your target release (ensuring that any interim releases have the fix landed):

```
git checkout -b stable/bug/XXXX stable/YYYY
git cherry-pick -x <hash of master branch commit>
git review
```

Where XXXX is the launchpad bug ID corresponding to the fix you want to backport and YYYY is the release name you are targeting e.g. 16.04

NOTE: when cherry-picking a commit and/or modifying the commit message, always ensure that the original Change-Id is left intact.

# Charm Testing

Every proposed change to a charm is run through the following tests during the review verification process:

* merge check
* pep8/lint checks (including charm proof) (tox -e pep8)
* unit tests (tox -e py27)
* test_charm_single:  One charm, deployed in its default config against the current LTS and the latest Ubuntu release.
* test_charm_amulet_smoke:  Partial cloud, deployed with reference config against the current LTS with the latest OpenStack release test defined within the charm.
* test_charm_amulet_full:  Same as amulet_smoke, but test all defined release combinations.

Only when the verification and gate processes have completed successfully will the change be landed in the target repository and branch.

The OpenStack Charm are compliant with the OpenStack [Consistent Testing Interface]; take a read on how this works to understand in full.

[Consistent Testing Interface][https://governance.openstack.org/reference/cti/python_cti.html]

# Review changes

Gerrit is fairly easy to query to get the full list of [Charm reviews][].

[Charm reviews]: https://review.openstack.org/#/q/project:%22%255Eopenstack/charm.*%22
