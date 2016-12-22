# Open Management Infrastructure (OMI)

## Table of Contents
- [Glossary of Terms](#glossary-of-terms)
- [Setting up a machine to build OMI](#setting-up-a-machine-to-build-omi)
  - [Dependencies to build a native package](#dependencies-to-build-a-native-package)
  - [Setting up a system to build a universal package](#setting-up-a-system-to-build-a-universal-package)
- [Cloning the repository](#cloning-the-repository)
- [Building the agent](#building-the-agent)
  - [Building test agents](#building-test-agents)
  - [Building release agents](#building-release-agents)
- [Code of Conduct](#code-of-conduct)

If you are an active contributor to the OMI project, you should
[set up your system]
(https://github.com/Microsoft/ostc-docs/blob/master/setup-git.md)
and follow our [common workflow]
(https://github.com/Microsoft/ostc-docs/blob/master/workflow-workflow.md).
New to git? Read [guidelines for development]
(https://github.com/Microsoft/ostc-docs/blob/master/setup-rules.md).

-----

### Glossary of Terms 

A short glossary might be helpful if this project is new to you:

Term | Meaning
---- | -------
ULinux | A "Universal Linux" build is a type of build that will install and run on any Linux system that we support. We have two universal builds for Linux: One for 32-bit systems, and one for 64-bit systems.

-----

### Setting up a machine to build OMI

There are two ways to build OMI:

1. As an RPM or DEB package that can be installed on the local system, or

2. As a universal package (installable on any system).

Building a universal package (actually, a set of packages) is a
superset of a local installation, so we will cover building locally
first.

### Dependencies to build a native package

Note that it's very nice to be able to use the [updatedns]
(https://github.com/jeffaco/msft-updatedns) project to use host names
rather than IP numbers in a Hyper-V environment. On CentOS systems,
this requires the bind-utils package (updatedns requires the 'dig'
program). The bind-utils package isn't otherwise necessary.

- On CentOS 7.x
```
 sudo yum install git bind-utils gcc-c++ rpm-devel pam-devel openssl-devel rpm-build krb5-devel
```
- On Ubuntu 14.04
```
 sudo apt-get install git pkg-config make g++ rpm librpm-dev libpam0g-dev libssl-dev libkrb5-dev
```

- On Mac OS/X:

On Mac OS/X, OMI dependencies are installed via [Homebrew][]. Once a
[Mac OS/X machine is set up]
(https://github.com/Microsoft/ostc-docs/blob/master/setup-macosx.md)
properly, use a command like:

```
brew install pkg-config openssl
```

to install necessary bits to build OMI for Mac OS/X.

[Homebrew]: https://github.com/Microsoft/ostc-docs/blob/master/setup-macosx.md#install-homebrew

- Notes on other platforms

When building a machine for ULINUX builds (such as SuSE 10), we
suggest using the O/S distribution CD to install the packages. It's
not as easy, but that's the only way to guarentee that packages aren't
updated such that generated binaries are not backwards
compatible. (See notes on building a universal package, elsewhere in
this document.) Note that ULinux builds are controlled via the
configure script, discussed below.

Also note that since you won't use 'yum', you must also handle the
dependent packages manually (keep adding lines to the 'rpm install'
command line until all dependencies are satisfied).

Similar methods would be utilized if building a Redhat system that is
not registered for use for up2date.

For universal builds, we recommend the use of a SuSE 10 system. The
SuSE 10 release is slightly older than RedHat 5.0, and thus builds
backwards compaibility binaries for all of the Linux platforms that we
support.

### Setting up a system to build a universal package

To build a universal package, we need and older Linux system (we
typically use SuSE 10.0 for this), as binary images created with older
Linux systems are generally upwards compatible when installed on newer
Linux systems.

A notable exception: We use the OpenSSL package, and we can't tell if
we need OpenSSL v0.9.8 or OpenSSL v1.0.x. As a result, we have a [special
process] (https://github.com/Microsoft/ostc-openssl/blob/master/README.md)
to build both both versions of OpenSSL that we can link against.

Once OpenSSL is set up, you need to configure omsagent to include the
```--enable-ulinux``` qualifier, like this:<br>```./configure --enable-ulinux``` 

### Cloning the repository

Note that there are several subprojects, and authentication is a hassle
unless you set up an SSH key via your GitHub account. [Set up your machine]
(https://github.com/Microsoft/ostc-docs/blob/master/setup-git.md)
properly for a much easier workflow.

To clone the repository to build OMI, issue the following command:

```
git clone --recursive git@github.com:Microsoft/Build-omi.git bld-omi
```

After this, you need to make sure that you're on the master branch for each
of the subprojects. To do this, issue the following commands:

```
cd bld-omi
git checkout master
git submodule foreach git checkout master
```

You can also use an alias like ```git co-master``` if you followed 
[Configuring git](https://github.com/Microsoft/ostc-docs/blob/master/setup-git.md)
recommendations.


### Building the Agent

There are multiple ways to build OMI:

- Build for development purposes, enabling unit tests, and
- Build for release purposes

##### Building Test Agents

- To build OMI in developer mode:

```sh
cd bld-omi
pushd bld-omi/omi/Unix
./configure --dev
make -j
popd
```

- Run regression tests

```sh
pushd bld-omi/omi/Unix
./regress
popd
```

##### Building Release Agents

From the bld-omi directory (created above from 'git clone', do the
following:

```
cd omi/Unix
./configure
make
```

Note that the ```configure``` script takes a variety of options. You
can use ```configure --help``` to see the options available.

When the build completes, you should have a native package that you
can install on your system. The native package should be in a
subdirectory off of bld-omi/omi/Unix/output.

To build a universal build, the configure line must be modified to
include the `--enable-ulinux` qualifier, like this:

```
./configure --enable-ulinux
```

As mentioned earlier in this document, the system must be configured
via this [special process]
(https://github.com/Microsoft/ostc-openssl/blob/master/README.md)
in order to build universal images for any of our supported platforms.

Finally, note that Microsoft builds OMI to use a special set of
directories and features to support Microsoft providers. If you wish
to build OMI as Microsoft normally does, the configure line should be:

```
./configure --enable-preexec --prefix=/opt/omi --localstatedir=/var/opt/omi --sysconfdir=/etc/opt/omi/conf --certsdir=/etc/opt/omi/ssl --enable-ulinux
```

### Code of Conduct

This project has adopted the [Microsoft Open Source Code of Conduct]
(https://opensource.microsoft.com/codeofconduct/).  For more
information see the [Code of Conduct FAQ]
(https://opensource.microsoft.com/codeofconduct/faq/) or contact
[opencode@microsoft.com](mailto:opencode@microsoft.com) with any
additional questions or comments.
