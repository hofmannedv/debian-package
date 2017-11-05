How to create a Debian package from source
==========================================
:toc:
:copyright: Frank Hofmann
:revnumber: 0.6
:Author Initials: FH
:edition: 1
:lang: en
:date: November 5, 2017
:numbered:

== Preface ==

Being responsible for a machine means taking care of the hardware as
well as the software components. As seen in everyday life as a system
administrator it is much better to install software as a software
package rather than a bunch of source files. This reduces the costs to
properly maintain the system.

Packages that are available from your preferred distributor are
validated and overseen by a package maintainer. He tested the software,
and assures that it fits to the other software packages available in the
distribution. Furthermore, the package is signed with a GPG key from the
package maintainer. This guarantees the integrity of the package and
shows you that the package is a trusted source.

The package format depends on your Linux distribution. Selected formats
are the following ones:

`deb`:: https://www.debian.org/[Debian GNU/Linux],
https://www.ubuntu.com/[Ubuntu], https://www.armbian.com/[Armbian], https://linuxmint.com/[Linux Mint], http://www.knoppix.org/[Knoppix]

`rpm`:: https://www.redhat.com/[RedHat Linux],
https://www.getfedora.org/[Fedora], https://www.centos.org/[CentOS],
https://opensuse.org/[openSUSE]

`tgz and txz`:: https://www.slackware.com/[Slackware]

`tar.xz`:: https://www.archlinux.org/[Arch Linux]

This document explains how to build a package for Debian GNU/Linux. For
a detailed information about the Debian package format and the tools to
maintain a `deb` based Linux system you may have a look into the Debian
Package Management Book <<dpmb>>.

The package we will work with is named `helloworld` and has the version
number 0.1. For demonstration purposes it simply contains a single
Python script that outputs the famous message "Hello, world!":

```python
#!/usr/bin/python

print ("Hello, world!")
```

== Requirements ==

=== GPG key ===

As step 1, have your GPG key available. Later on, the key will be needed
to sign the package. Keep in mind that unsigned packages are
untrustworthy and cannot be part of the Debian universe.

In case you do not have a GPG key yet create one. You may follow the
three steps below. The first command generates a new key, the second one
exports your new key to a separate file, and the third one adds the key
to your personal keyring.

```sh
$ gpg --gen-key
$ gpg -a --output ~/.gnupg/YOUR_NAME.gpg --export 'YOUR NAME'
$ gpg --import ~/.gnupg/YOUR_NAME.gpg
```

During creation make sure that the given name _YOUR NAME_ is correct.
This name will have to be exactly the same in the package, then.

=== Prepare the software to be packaged ===

We have to prepare a directory to build the package, then.

. create a directory to prepare the environment in which we will build the package
+
```sh
$ mkdir -p ~./build/helloworld/0.1
```
+
. copy the `tar.gz` compressed archive in the directory
+
```sh
$ cp helloworld-0.1.tar.gz ~./build/helloworld/0.1
```
+
. change into the directory, and extract the package
+
```sh
$ cd ~./build/helloworld/0.1
~/build/helloworld/0.1$ tar -xzf helloworld-0.1.tar.gz
```
+
Now, the directory contains both the source code in a separate
directory, and the compressed archive:
+
```sh
~/build/helloworld/0.1$ ls
helloworld-0.1  helloworld-0.1.tar.gz
```

=== The packaging tool chain ===

== Debianization ==

=== Prepare the package structure ==

=== Adjust the control file ===

=== Adjust the copyright file ===

=== Adjust the changelog file ===

== Build the package ==

=== For your hardware architecture ===

=== For a different hardware architecture ===

== Links and References ==

- [[[dpmb]]] Axel Beckert, Frank Hofmann: The Debian Package Management
  Book, https://www.dpmb.org/

- [[[lushpaiPackage]]] Alex Lushpai: How to create debian package from source, https://coderwall.com/p/urkybq/how-to-create-debian-package-from-source

== Acknowledgements ==

Axel Beckert
