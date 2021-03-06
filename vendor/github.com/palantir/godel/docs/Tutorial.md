The following tutorial demonstrates how to install, configure and use gödel on an example project. We will be creating a
project called echgo2 that is a simple program that echoes user input in a variety of ways.

Following every step of the tutorial from beginning to end will show the entire end-to-end process of creating a new
project and using a variety of gödel features to configure it. It is also possible to jump to any section of interest
directly. Each step of the tutorial provides a general summary of the step, the expected preconditions before the step,
the actions to take during the step, and the conditions that should exist after the step.

The repository at https://github.com/nmiyake/echgo2 contains the result of walking through the tutorial.

The tutorial consists of the following steps:

* [Add gödel to a project](https://github.com/palantir/godel/wiki/Add-g%C3%B6del)
* [Add Git hooks to enforce formatting](https://github.com/palantir/godel/wiki/Add-git-hooks)
* [Generate IDE project for Gogland](https://github.com/palantir/godel/wiki/Generate-IDE-project)
* [Format Go files](https://github.com/palantir/godel/wiki/Format)
* [Run static checks on code](https://github.com/palantir/godel/wiki/Check)
* [Run tests](https://github.com/palantir/godel/wiki/Test)
* [Build](https://github.com/palantir/godel/wiki/Build)
* [Run](https://github.com/palantir/godel/wiki/Run)
* [Dist](https://github.com/palantir/godel/wiki/Dist)
* [Publish](https://github.com/palantir/godel/wiki/Publish)
* [Generate license headers](https://github.com/palantir/godel/wiki/License-headers)
* [Go generate tasks](https://github.com/palantir/godel/wiki/Generate)
* [Define excludes](https://github.com/palantir/godel/wiki/Exclude)
* [Write integration tests](https://github.com/palantir/godel/wiki/Integration-tests)
* [Sync a documentation directory with GitHub wiki](https://github.com/palantir/godel/wiki/GitHub-wiki)
* [Verify project](https://github.com/palantir/godel/wiki/Verify)
* [Set up CI to run tasks](https://github.com/palantir/godel/wiki/CI-setup)
* [Update gödel](https://github.com/palantir/godel/wiki/Update-g%C3%B6del)
* [Update legacy gödel](https://github.com/palantir/godel/wiki/Update-legacy-godel)
* [Other commands](https://github.com/palantir/godel/wiki/Other-commands)
* [Conclusion](https://github.com/palantir/godel/wiki/Tutorial-conclusion)

It is recommended that the tutorial be run in a Docker image to provide maximal environment isolation. A base Docker
image that can be used for the tutorial is available in the `docs/templates/baseimage` directory.

The Dockerfile in the directory has some values specified using `ENV` that can be customized if desired. If you want to
run the tutorial directly on a host rather than in a Docker image, you should set/export the `ENV` values.

This tutorial uses `github.com/nmiyake/echgo2` as the project path. This is fine for the main path of the tutorial.
However, if you want to walk through publishing the repository to GitHub, you should modify this project path to be a
path to a repository that you can create/push to GitHub (although it is possible to push a project with this path to any
GitHub repository, using a project path that mirrors the GitHub location is more realistic).

Start the tutorial by building the Docker image:

```
➜ cd ${GOPATH}/src/github.com/palantir/godel/docs/templates/baseimage
➜ ./build.sh
Sending build context to Docker daemon  3.584kB
Step 1/9 : FROM golang:1.10.2
 ---> 6b369f7eed80
Step 2/9 : ENV GODEL_VERSION 2.4.0
 ---> Running in 547cd20e3b9d
Removing intermediate container 547cd20e3b9d
 ---> e71eeff7a9d3
Step 3/9 : ENV GODEL_CHECKSUM 48e50289db0acfce7f8a68b12f8ab433a2fcbf67e80b7658dfc1e105228228c1
 ---> Running in a12624ef6c18
Removing intermediate container a12624ef6c18
 ---> 4a6c208a2564
Step 4/9 : ENV PROJECT_PATH github.com/nmiyake/echgo2
 ---> Running in 8ba493bc138c
Removing intermediate container 8ba493bc138c
 ---> 867c9d408573
Step 5/9 : ENV GIT_USERNAME "Tutorial User"
 ---> Running in 3afbcfcbbfd0
Removing intermediate container 3afbcfcbbfd0
 ---> e3a677672529
Step 6/9 : ENV GIT_EMAIL "tutorial@tutorial-user.com"
 ---> Running in 2fad3751d74d
Removing intermediate container 2fad3751d74d
 ---> 37a208d68bf4
Step 7/9 : RUN apt-get update && apt-get install -y tree
 ---> Running in aae3f8b8b1a7
Get:1 http://security.debian.org/debian-security stretch/updates InRelease [94.3 kB]
Ign:2 http://deb.debian.org/debian stretch InRelease
Get:3 http://deb.debian.org/debian stretch-updates InRelease [91.0 kB]
Get:4 http://deb.debian.org/debian stretch Release [118 kB]
Get:5 http://deb.debian.org/debian stretch Release.gpg [2434 B]
Get:6 http://security.debian.org/debian-security stretch/updates/main amd64 Packages [488 kB]
Get:7 http://deb.debian.org/debian stretch-updates/main amd64 Packages [5476 B]
Get:8 http://deb.debian.org/debian stretch/main amd64 Packages [9500 kB]
Fetched 10.3 MB in 3s (3170 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
The following NEW packages will be installed:
  tree
0 upgraded, 1 newly installed, 0 to remove and 30 not upgraded.
Need to get 46.1 kB of archives.
After this operation, 106 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian stretch/main amd64 tree amd64 1.7.0-5 [46.1 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 46.1 kB in 0s (368 kB/s)
Selecting previously unselected package tree.
(Reading database ... 15055 files and directories currently installed.)
Preparing to unpack .../tree_1.7.0-5_amd64.deb ...
Unpacking tree (1.7.0-5) ...
Setting up tree (1.7.0-5) ...
Removing intermediate container aae3f8b8b1a7
 ---> 4b6736bd5793
Step 8/9 : RUN git config --global user.name "${GIT_USERNAME}" &&     git config --global user.email "${GIT_EMAIL}" &&     mkdir -p ${GOPATH}/src/${PROJECT_PATH}
 ---> Running in 148d6b5b593d
Removing intermediate container 148d6b5b593d
 ---> 075039d532f0
Step 9/9 : WORKDIR /go/src/${PROJECT_PATH}
 ---> Running in 6337686c6dfd
Removing intermediate container 6337686c6dfd
 ---> 7bc4deeef8cb
Successfully built 7bc4deeef8cb
Successfully tagged godeltutorial:setup
```

Now, run the Docker image interactively:

```
➜ docker run -it godeltutorial:setup
root@010fdbb9adec:/go/src/github.com/nmiyake/echgo2#
```

You can now follow the rest of the tutorial. The following steps are also valid on a host as long as the proper
environment variables are set and the required programs are available.

Start the tutorial by creating the directory for your project and setting it to be the working directory:

```
➜ mkdir -p ${GOPATH}/src/${PROJECT_PATH} && cd $_
➜ pwd
/go/src/github.com/nmiyake/echgo2
```

Initialize a git repository, add a README and commit it:

```
➜ git init
Initialized empty Git repository in /go/src/github.com/nmiyake/echgo2/.git/
➜ echo 'echgo2 is a program that echoes input provided by the user.' > README.md
➜ git add README.md
➜ git commit -m "Initial commit"
[master (root-commit) 58cb4f5] Initial commit
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```

Tutorial end state
------------------
* `${GOPATH}/src/${PROJECT_PATH}` exists, is the working directory and is initialized as a Git repository

Tutorial next step
------------------
[Add gödel](https://github.com/palantir/godel/wiki/Add-godel)
