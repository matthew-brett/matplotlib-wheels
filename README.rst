########################################
Building and uploading matplotlib wheels
########################################

*******
For OSX
*******

We automate OSX wheel building using this custom github repository that builds
on the travis-ci OSX machines.

The travis-ci interface for the builds is :
https://travis-ci.org/MacPython/matplotlib-wheels

The driving github repository is :
https://github.com/MacPython/matplotlib-wheels

How it works
============

The wheel-building repository:

* does a fresh build of the required C / C++ libraries;
* builds a matplotlib wheel, linking against these fresh builds;
* processes the wheel using `delocate`. `delocate` copies the required dynamic
  libraries into the wheel and relinks the extension modules against the
  copied libraries;
* uploads the built wheel to http://wheels.scipy.org (a Rackspace container
  kindly donated by Rackspace to scikit-learn).

The resulting wheel is therefore self-contained and does not need any external
dynamic libraries apart from those provided as standard by OSX.

The ``.travis.yml`` file in this repository has a line containing the API key
for the Rackspace container encrypted with an RSA key that is unique to the
repository - see http://docs.travis-ci.com/user/encryption-keys.  This
encrypted key gives the travis build permission to upload to the Rackspace
directory pointed to by http://wheels.scipy.org.

Triggering a build
==================

You will need write permission to the github repository to trigger new builds
on the travis-ci interface.  Contact us on the mailing list if you need this.

You can trigger a build by:

* making a commit to the `matplotlib-wheels` repository (e.g. with `git
  commit --allow-empty`); or
* clicking on the circular arrow icon towards the top right of the travis-ci
  page, to rerun the previous build.

In general, it is better to trigger a build with a commit, because this makes
a new set of build products and logs, keeping the old ones for reference.
Keeping the old build logs helps us keep track of previous problems and
successful builds.

Which matplotlib commit does the repository build?
===============================================

By default, the `matplotlib-wheels` repository is usually set up to build
the latest git tag.  By "latest" we mean the tag on the branch most recently
branched from master - see http://stackoverflow.com/a/24557377/1939576. To
check whether you are building the latest tag have a look around line 5 of
`.travis.yml` in the `matplotlib-wheels` repository.  You should see something
like::

    - BUILD_COMMIT='latest-tag'

If this is commented out, then the repository is set up to build the current
commit in the `matplotlib` submodule of the repository.  If it is set to
another value then it will be specifying a commit to build.

You can therefore build any arbitrary commit by specifying the commit hash or
branch name or tag name in this line of the `.travis.yml` file.

Uploading the built wheels to pypi
==================================

Be careful, http://wheels.scipy.org points to a container on a distributed
content delivery network.  It can take up to 15 minutes for the new wheel file
to get updated into the container at http://wheels.scipy.org.

When the wheels are updated, you can of course just download them to your
machine manually, and then upload them manually to pypi, or by using
twine_.  You can also use a script for doing this, housed at :
https://github.com/MacPython/terryfy/blob/master/wheel-uploader

You'll need twine and `beautiful soup 4 <bs4>`_.

You will typically have a directory on your machine where you store wheels,
called a `wheelhouse`.   The typical call for `wheel-uploader` would then
be something like::

    wheel-uploader -v -w ~/wheelhouse matplotlib 1.5.0

where:

* `-v` means give verbose messages;
* `-w ~/wheelhouse` means download the wheels from https://wheels.scipy.org to
  the directory `~/wheelhouse`;
* `matplotlib` is the root name of the wheel(s) to download / upload;
* `1.5.0` is the version to download / upload.

So, in this case, `wheel-uploader` will download all wheels starting with
`matplotlib-1.5.0-` from http://wheels.scipy.org to `~/wheelhouse`, then
upload them to pypi.

Of course, you will need permissions to upload to pypi, for this to work.

.. _twine: https://pypi.python.org/pypi/twine
.. _bs4: https://pypi.python.org/pypi/beautifulsoup4
.. _delocate: https://pypi.python.org/pypi/delocate
