# Notes on deployment

How I deploy this package.

For anyone reading, this readme and all files in this folder are mainly just
notes for myself; they have little to do with pygcode itself.
However, if you're interested in deploying your own PyPi package, then hopefully
this can help.

Method based on the articles:

* http://peterdowns.com/posts/first-time-with-pypi.html and
* https://hynek.me/articles/sharing-your-labor-of-love-pypi-quick-and-dirty/


Deployment also heavily uses the `./deploy.sh` script in this folder.
At this time, running `./deploy.sh --help` displays:

    Usage: ./deploy.sh {build|test|and so on ...}

    This script is to maintain a consistent method of deployment and testing.

    Deployment target: pygcode 0.2.0

    Arguments:
        Setup:
            setup:      Installs packages & sets up environment (requires sudo)

        Compiling:
            build:      Execute setup to build packages (populates ../dist/)
                        creates both 'sdist' and 'wheel' distrobutions.

        Virtual Environments:
            lsenv           List created virtual environments for this lib
            rmenv py#       Remove virtual environment
            remkenv py#     Remove, then create re-create virtual environment
            envprereq py#   install environment prerequisites (official PyPi)

        Deploy:
            deploy test     Upload to PyPi test server
            deploy prod     Upload to PyPi (official)

        Install:
            install sdist py#       Install from local sdist
            install wheel py#       Install from local wheel
            install pypitest py#    Install from PyPi test server
            install pypi py#        Install from PyPi (official)

        Testing:
            test dev py#        Run tests on local dev in a virtual env
            test installed py#  Run tests on installed library in virtual env

        Help:
            -h | --help     display this help message

        py#: when referenced above means
            'py2' for Python 2.7.12
            'py3' for Python 3.5.2

# PyPi deployment

## Install Required Tools

`./deploy.sh setup`

## PyPi rc

`cat ~/.pypirc`

    [distutils]
    index-servers =
      prod
      test

    [prod]
    repository = https://upload.pypi.org/legacy/
    username=FraggaMuffin
    password=secret

    [test]
    repository=https://test.pypi.org/legacy/
    username=FraggaMuffin
    password=secret

`chmod 600 ~/.pypirc`


## Build and Test `sdist` and `wheel`

**Build**

    ./deploy.sh build

**Test `sdist`**

    # Python 2.x
    ./deploy.sh remkenv py2
    ./deploy.sh envprereq py2
    ./deploy.sh install sdist py2
    ./deploy.sh test installed py2

    # Python 3.x
    ./deploy.sh remkenv py3
    ./deploy.sh envprereq py3
    ./deploy.sh install sdist py3
    ./deploy.sh test installed py3

**Test `wheel`**

    # Python 2.x
    ./deploy.sh remkenv py2
    ./deploy.sh install wheel py2
    ./deploy.sh test installed py2

    # Python 3.x
    ./deploy.sh remkenv py3
    ./deploy.sh install wheel py3
    ./deploy.sh test installed py3



## Upload to PyPi Test server


    ./deploy.sh deploy test


**Test**

    # Python 2.x
    ./deploy.sh remkenv py2
    ./deploy.sh envprereq py2
    ./deploy.sh install pypitest py2
    ./deploy.sh test installed py2

    # Python 3.x
    ./deploy.sh remkenv py3
    ./deploy.sh envprereq py3
    ./deploy.sh install pypitest py3
    ./deploy.sh test installed py3

have a look at:
https://testpypi.python.org/pypi/pygcode
to make sure it's sane


## Upload to PyPy server

all good!? sweet :+1: time to upload to 'production'

    ./deploy.sh deploy prod

**Test**

    # Python 2.x
    ./deploy.sh remkenv py2
    ./deploy.sh install pypi py2
    ./deploy.sh test installed py2

    # Python 3.x
    ./deploy.sh remkenv py3
    ./deploy.sh install pypi py3
    ./deploy.sh test installed py3

have a look at:
https://pypi.python.org/pypi/pygcode
to make sure it's sane


# Deployment in Git

create pull request `develop` -> `master`

## Update change log

update [change log](../dist/README.md)

## Push Release to Git (aka `tag`)

merge `develop` branch to `master`, then...

Switch local to `master` and tag with relevant version

    git checkout master
    git pull
    git tag ${version} -m "<change description>"
    git push --tags origin master

have a look at the
[releases page](https://github.com/fragmuffin/pygcode/releases)
and it should be there.

edit release and copy relevant content from [change log](../dist/README.md)

tadaaaaaa!... go to sleep; it's probably late

# Cleanup

    ./deploy.sh rmenv py2
    ./deploy.sh rmenv py3
