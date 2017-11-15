How to create a Debian package from source
==========================================
:toc:
:copyright: Frank Hofmann
:revnumber: 0.21
:Author Initials: FH
:edition: 1
:lang: en
:date: November 15, 2017
:numbered:

== Preface ==

Being responsible for a machine means taking care of the hardware as
well as the software components. As seen in everyday life as a system
administrator it is much better to install software as a software
package rather than a bunch of source files. This reduces the costs to
properly maintain the system.

Packages that are available from your preferred distributor are
validated and overseen by a package maintainer. He tested the software
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

This document explains how to build a package for Debian GNU/Linux
briefly. For a detailed information about the Debian package format and
the tools to maintain a `deb` based Linux system you may have a look
into the Debian Package Management Book <<dpmb>>. 

To build packages for Debian GNU/Linux these documents are essential:

* The Debian New Maintainer's Guide <<dnmg>>

* The Debian Developer's Reference <<ddr>>

* The Debian Packaging Tutorial <<dpt>>

* The Debian Policy Manual <<dpm>>

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
exports your new key to a separate file and the third one adds the key
to your personal keyring.

```sh
$ gpg --gen-key
$ gpg -a --output ~/.gnupg/YOUR_NAME.gpg --export 'YOUR NAME'
$ gpg --import ~/.gnupg/YOUR_NAME.gpg
```

During creation make sure that the given name _YOUR NAME_ is correct. It
is common to use a combination of first name and last name. This name
will have to be exactly the same in the package, then, when creating the
`control` file of the Debian package. For more information regarding GPG
have a look at the GNU Privacy Handbook <<gph>>.

=== The packaging tool chain ===

To build a Debian package with source code the following software
packages are required on your system:

* build-essential
* autoconf 
* automake
* autotools-dev 
* dh-make 
* debhelper 
* devscripts 
* fakeroot
* xutils
* lintian 
* pbuilder

As user `root` you can install these using the following command:

```sh
# apt-get install build-essential autoconf automake autotools-dev dh-make debhelper devscripts fakeroot xutils lintian pbuilder
```

=== Prepare the software to be packaged ===

We have to prepare a directory to build the package, then.

. create a directory to prepare the environment in which we will build
the package:
+
```sh
$ mkdir -p ~./build/helloworld/0.1
```
+
. copy the `tar.gz` compressed archive in the directory:
+
```sh
$ cp helloworld-0.1.tar.gz ~./build/helloworld/0.1
```
+
. change into the directory, and extract the package:
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

== Debianization ==

At this point we will add the files that are specific to a Debian
package. That is why this step is named _Debianization_ of the software.
This is done in several single steps.

=== Prepare the package structure ===

. change into the directory that keeps the entire source code of the
package. In our example the package contains the file `helloworld.py`,
only:
+
```sh
~$ cd build/helloworld/0.1/helloworld-0.1
~/build/helloworld/0.1/helloworld-0.1$ ls
helloworld.py
```
+
. let's add the files that are specific to a Debian package. The tool
`dh_make` comes into play. The switch `-e` uses the given address as the
email address in the `Maintainer` field of the `debian/control` file.
Building the package use your own email address, instead. Keep in mind
to use the same email address that corresponds to your GPG key.
+
The switch `-f` uses the given file as the original source archive, and
skips the copying of the current program tree to `program.orig`.
+
```sh
~/build/helloworld/0.1/helloworld-0.1$ dh_make -e frank.hofmann@efho.de -f ../helloworld-0.1.tar.gz
```
At the prompt you are asked to select the type of package that should be
created. To choose _single binary_ type `s`.
+
```sh
Type of package: single binary, indep binary, multiple binary, library, kernel module, kernel patch?
 [s/i/m/l/k/n] s

Maintainer name  : Frank Hofmann
Email-Address    : frank.hofmann@efho.de 
Date             : Sat, 04 Nov 2017 21:16:13 +0100
Package Name     : helloworld
Version          : 0.1
License          : blank
Type of Package  : Single
Hit <enter> to confirm: 
Currently there is no top level Makefile. This may require additional tuning.
Done. Please edit the files in the debian/ subdirectory now. You should also
check that the helloworld Makefiles install into $DESTDIR and not in / .
```
+
image::dhmake.png[]
+
This results in a directory called `debian`:
+
```sh
~/build/helloworld/0.1/helloworld-0.1$ ls
debian  helloworld.py
```
+
This directory contains all the package-specific files.
+
image::dhmake2.png[]

=== Adjust the control file ===

The file `debian/control` keeps the dependencies that are needed _to
build_ the package. Using the command `dpkg-depcheck -d ./configure` you
receive a list with all the required packages. In our case there is no
further package needed because Python is an interpreted language.

Next, we have to edit the file `debian/control` and add package-specific
values. For our example it looks as follows:

```
Source: helloworld
Section: python
Priority: optional
Maintainer: Frank Hofmann <frank.hofmann@efho.de>
Build-Depends: debhelper (>= 9)
Standards-Version: 3.9.5
Homepage: http://www.efho.de/
#Vcs-Git: git://anonscm.debian.org/collab-maint/helloworld.git
#Vcs-Browser: http://anonscm.debian.org/?p=collab-maint/helloworld.git;a=summary

Package: helloworld
Architecture: any
Depends: ${shlibs:Depends}, ${misc:Depends}, python 
Description: Prints Hello World in Python
  Prints Hello World in Python
```

=== Adjust the copyright file ===

The file `debian/copyright` contains the license information for the
software package. It is prepared for the release via GNU Public License
2 (GPLv2). For our example it looks as follows:

```
Format: http://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: helloworld
Source: http://www.efho.de/

Files: debian/*
Copyright: 2017 Frank Hofmann <frank.hofmann@efho.de>
License: GPL-2+
 This package is free software; you can redistribute it and/or modify
 it under the terms of the GNU General Public License as published by
 the Free Software Foundation; either version 2 of the License, or
 (at your option) any later version.
 .
 This package is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 GNU General Public License for more details.
 .
 You should have received a copy of the GNU General Public License
 along with this program. If not, see <http://www.gnu.org/licenses/>
 .
 On Debian systems, the complete text of the GNU General
 Public License version 2 can be found in "/usr/share/common-licenses/GPL-2".

```

=== Adjust the changelog file ===

After the copyright information the file `debian/changelog` has to be
adjusted. In our example we add the information "Initial release".

```
helloworld (0.1-1) unstable; urgency=low

  * Initial release

 -- Frank Hofmann <frank.hofmann@efho.de>  Sat, 04 Nov 2017 21:16:13 +0100
```

That is all we need so far -- now we can build the package, finally.

== Build the package ==

To build the package we have to move up one directory and run the
following command:

```sh
~/build/helloworld/0.1/helloworld-0.1$ dpkg-buildpackage -rfakeroot
```

The option `-rfakeroot` allows `dpkg-buildpackage` to run commands as a
priviledged user with the help of the command `fakeroot`. This is
necessary to prepare the package, and to create files and directories.

// === For your hardware architecture ===

The command above results in a longer list of output messages (shown
here in a German language environment):

```sh
dpkg-buildpackage: Quellpaket helloworld
dpkg-buildpackage: Quellpaket helloworld
dpkg-buildpackage: Quellversion 0.1-1
dpkg-buildpackage: Quelldistribution unstable
dpkg-buildpackage: Quellen geändert durch Frank Hofmann <frank.hofmann@efho.de>
dpkg-buildpackage: Host-Architektur amd64
 dpkg-source --before-build helloworld-0.1
 fakeroot debian/rules clean
dh clean 
   dh_testdir
   dh_auto_clean
   dh_clean
 dpkg-source -b helloworld-0.1
dpkg-source: Information: Quellformat »3.0 (quilt)« wird verwendet
dpkg-source: Information: helloworld wird unter Benutzung des existierenden ./helloworld_0.1.orig.tar.gz gebaut
dpkg-source: Information: helloworld wird in helloworld_0.1-1.debian.tar.xz gebaut
dpkg-source: Information: helloworld wird in helloworld_0.1-1.dsc gebaut
 debian/rules build
dh build 
   dh_testdir
   dh_auto_configure
   dh_auto_build
   dh_auto_test
 fakeroot debian/rules binary
dh binary 
   dh_testroot
   dh_prep
   dh_auto_install
   dh_installdocs
   dh_installchangelogs
   dh_perl
   dh_link
   dh_compress
   dh_fixperms
   dh_strip
   dh_makeshlibs
   dh_shlibdeps
   dh_installdeb
   dh_gencontrol
dpkg-gencontrol: Warnung: Feld Depends von Paket helloworld: unbekannte Substitutionsvariable ${shlibs:Depends}
   dh_md5sums
   dh_builddeb
dpkg-deb: Paket »helloworld« wird in »../helloworld_0.1-1_amd64.deb« gebaut.
 dpkg-genchanges  >../helloworld_0.1-1_amd64.changes
dpkg-genchanges: kompletter Quellcode beim Hochladen hinzufügen
 dpkg-source --after-build helloworld-0.1
dpkg-buildpackage: Alles hochzuladen (Originalquellen enthalten)
 signfile helloworld_0.1-1.dsc

Sie benötigen eine Passphrase, um den geheimen Schlüssel zu entsperren.
Benutzer: "Frank Hofmann (Hofmann EDV) <frank.hofmann@efho.de>"
4096-Bit RSA Schlüssel, ID D431AC07, erzeugt 2014-09-05
```

// === For a different hardware architecture ===
//
// TBD.
//
// == Validating the package ==

Congratulations -- you succeeded in building a Debian package -- yeah!
Now, let's have a closer look at the package. Here, `lintian` comes into
play. This tool validates your package in order to find violations
against the strict rules Debian packages have to fulfill.

To run the tests type in the following command:

```sh
lintian helloworld_0.1-1_amd64.deb
```

The tool does not find rule violations but also spelling errors, and
wrong characters. The switch `--pedantic` asks `lintian` to be much more
critical than usual. As you can see below `lintian` is a little bit
grumpy and has discovered three warnings, and one error.

image::lintian.png[]

Except for the first warning we can easily make `lintian` happy and
ajust the package content according to the rule set. The warning
`new-package-should-close-itp-bug` means that there is no bug report
against the ITP package (ITP means _intended to package_). For a regular
Debian package a bug report has to be sent to the bugtracker for package
ITP to notify others that you intend to start packaging this software.

=== Warning: `readme-debian-contains-debmake-template` ===

The file `README.Debian` is intended to keep additional notes regarding
this package. `dh_make` created this file for us:

```
helloworld for Debian
---------------------

<possible notes regarding this package - if none, delete this file>

 -- Frank Hofmann <frank.hofmann@efho.de>  Sat, 04 Nov 2017 21:16:13 +0100
```

In our example we don't have additional information, so we can delete
the file.

=== Warning: `description-starts-with-leading-spaces` ===

This warning is raised because the longer description of our package in
the file `debian/control` starts with more than a single space. As soon
as we remove a single space the warning will disappear.

=== Error: `description-synopsis-is-duplicated` ===

Each package requires both a short and a longer description in
`debian/control`. This error is raised because both descriptions are
identical. As soon as we extended the longer description the error is
gone.

== Links and References ==

- [[[ddr]]] Andreas Barth, Adam Di Carlo, Raphaël Hertzog, Lucas Nussbaum, Christian Schwarz, Ian Jackson: The Debian Developer's Reference, https://www.debian.org/doc/manuals/developers-reference/

- [[[dnmg]]] Josip Rodin, Osamu Aoki: The Debian New Maintainer's Guide, https://www.debian.org/doc/manuals/maint-guide/index.en.html

- [[[dpmb]]] Axel Beckert, Frank Hofmann: The Debian Package Management
  Book, https://www.dpmb.org/

- [[[dpm]]] The Debian Policy Manual, https://www.debian.org/doc/debian-policy/

- [[[dpt]]] The Debian Packaging Tutorial, https://www.debian.org/doc/devel-manuals#packaging-tutorial

- [[[gph]]] The GNU Privacy Handbook, https://www.gnupg.org/gph/en/manual.html

- [[[lushpaiPackage]]] Alex Lushpai: How to create debian package from source, https://coderwall.com/p/urkybq/how-to-create-debian-package-from-source

== About the author ==

Frank Hofmann is an http://www.efho.de/[IT developer, trainer, and
author] and prefers to work from Berlin, Geneva and Cape Town. Co-author of the Debian Package Management Book available from http://www.dpmb.org/.

== Acknowledgements ==

The author would like to thank http://noone.org/[Axel Beckert],
http://geneva-observer.blogspot.com/[Gerold Rupprecht] and Mandy
Neumeyer for their support, and critics while preparing this article.
