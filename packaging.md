How to create a Debian package from source
==========================================
:toc:
:copyright: Frank Hofmann
:revnumber: 0.1
:Author Initials: FH
:edition: 1
:lang: en
:date: November 5, 2017
:numbered:

== Preface ==

* it is better to install software as a software package, rather than a source file
** validated and overseen by a package maintainer
** fits to the other software
** signed with a GPG key
* package format depends on the Linux distribution (selection):
`deb`:: Debian GNU/Linux, Ubuntu, Armbian, Linux Mint, Knoppix
`rpm`:: RedHat Linux, Fedora, CentOS, openSUSE
* this document explains how to build a package for Debian GNU/Linux

== Requirements ==

=== GPG key ===

* have your GPG key available
** the key will be needed to sign the package
** unsigned packages are untrustworthy and cannot be part of the Debian universe
* if not yet done create one

```sh
$ gpg --gen-key
$ gpg -a --output ~/.gnupg/YOUR_NAME.gpg --export 'YOUR NAME'
$ gpg --import ~/.gnupg/YOUR_NAME.gpg
```
* make sure that the name _YOUR NAME_ is correct
** will be exactly the same in the package, then

=== Prepare the software to be packaged ===

* 

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
