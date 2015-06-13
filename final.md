# HowestX documentation

## Preface

This is the documentation for HowestX. HowestX is a pilot project for Howest (Howest University College West Flanders). The goal of this project was to evaluate the feasibility of adapting a MOOC platform. Three groups each evaluated a MOOC platform of their choice, HowestX evaluated the edX Open Platform.

## Technical documentation

### Pre-requisite knowledge

This section lists pre-requisite knowledge, tools and technologies that the implementors should be familiar with before reading on. This section will briefly introduce these technologies in an edX context, and include links to online documentation to read up on the subjects presented.

#### Python

Python is a dynamic, high-level programming language with a focus on readability. Most of the edX platform is written in Python.

##### Virtual environments

Python uses packages to manage dependencies. This can introduce problems when you run multiple Python applications on a single machine: when upgrading a packages for one application, the second application may break if depends on a different version than the new dependencies.

For this, the Python community has introduced *virtual environments*. These provide isolate packages in seperate environments. Usually, there will be one environment per application.

Virtual environments (or *venvs* for short) need to be *activated* before you can do work in them. There will be a `bin/` folder in a virtual environment, containing multiple scripts. You can *source* the `activate` script to enable the virtual environment. This is done like this:

    $ . ./venv/bin/activate

Notice the dot. This means the argument will a script to source.

After sourcing, you will notice that your shell `$PATH` has changed. The `python`, `pip` and other executables will be those from inside the virtual environment. When you install a package with `pip`, it will be installed in the virtual environment.

##### pip

*pip* is the most popular package manager for Python. Packages can be installed using `pip install packagename`.

##### Django

Django is the most popular Python web framework.

#### Less

Less is a CSS pre-processor. It extends CSS with variables and functions, leading to a more maintable frontend codebase. Less files are compiled down to CSS files so a browser can read them.

#### Vagrant

The goal of Vagrant in the edX project is to give all developers the same development environment. It does this through the use of virtual machines: Vagrant is  a wrapper around a VM provider backend. For the edX platform, the VirtualBox backend is used.

#### Ansible

Ansible is a way to automate provisioning of  (virtual) machines. It's used extensively in the edX project due to the complexity of edX deployments. With Ansible, task are neatly split up (there are roles to set up repositories, roles to set up ElasticSearch, roles to install Python packages, ...).

#### LDAP

LDAP (*Lightweight Directory Access Protocol*) is a protocol that allows clients to search, modify and connect to internet directories. We will use it here in an authentication contexts: we will make clients authenticate to an LDAP server.

### edX architecture

The architecture of edX is very complex. To give you a rough idea, in the codebase you can find Python, Ruby, node.js and Java sources. Here, we will take a quick look at the edX Platform architecture and how to interact with it.

#### Persistance

#### Authentication

##### Default users

#### Differences between edX distributions

##### Devstack

##### Fullstack

##### Production stack

#### Themes

edX now has a flexible theme structure. Aparte from configuraion to use the new theme, the platform isn't modified in any way when creating or updating a theme.

### Development and deployment recipes

#### Vagrant recipes

##### Installing Vagrant on a Mac OS machine

Mac OS users can download Vagrant as a `.dmg` from [its website](http://www.vagrantup.com/downloads.html). Installation works as usual, but you will have to install VirtualBox seperately from [here](https://www.virtualbox.org/wiki/Downloads).

##### Installing Vagrant on a Linux machine

We suggest that Linux users **do not** install Vagrant using their packet manager. The Vagrant in the Debian and Ubuntu repositories is often out of date, and this make cause strange errors when developing on the edX platform. Instead, we encourage Linux users to install Vagrant from [its website](http://www.vagrantup.com/downloads.html).

VirtualBox may be installed through the package manager, but Debian users will have to [enable non-free packages](http://serverfault.com/questions/240920/how-do-i-enable-non-free-packages-on-debian).

##### Setting up a Vagrant devstack machine

You can set up a Vagrant virtual machine for local development and testing. These instructions are for Mac and Linux systems, Windows users can use [Cygwin](https://www.cygwin.com/), but, as mentioned previously, we do not recommend this.

    $ mkdir howestx
    $ cd howestx
    $ curl -L https://raw.githubusercontent.com/edx/configuration/master/vagrant/release/devstack/Vagrantfile > Vagrantfile
    $ vagrant plugin install vagrant-vbguest
    $ vagrant up

This is what happens:

* Inside a new `howestx` directory, we download a `Vagrantfile`.
* We then install the `vagrant-vbguest` plugin. This only needs to happen once.
* Then we start up the virtual machine using `vagrant up`:
  - If this is the first time you set up an edX Vagrant development box, it will have to download a VirtualBox base image. This image will be roughly 3GB.
  - If this is the first time this specific virtual machine starts up, the machine be provisioned. This happens automatically. The virtual machine will download and run ansible scripts. This may take a while (15 minutes). This only happens once, unless you destroy or reprovision the virtual machine.
  - If the virtual machine already exists, it will be booted. This only takes a few minutes.

##### Accessing a running Vagrant devstack machine

You can access a runnig Vagrant devstack machine through SSH:

    $ vagrant ssh

##### Shutting down a running Vagrant machine

You may shut down a running Vagrant machine using `halt`:

    $ vagrant halt

It is very important to **halt all Vagrant virtual machines before shutting down the host machine**. This does not happen automatically. If you shut down the host with running Vagrant guests, corruption of the virtual machines may occur.

##### Dealing with corrupted Vagrant virtual machines

If your Vagrant guest has gotten corrupted or otherwise unworkable, you may do one of two things:

* Reprovision the virtual machine: reprovisioning the virtual machine will re-run the Ansible scripts on the existing virtual machine.
* Destroy the virtual machine: destroying the Vagrant virtual machine will remove the NFS mounts and all VirtualBox files.

Usually, destroying the virtual machine makes more sense, since you will then start from a clean slate and creating a new Vagrant virtual machine won't take much longer than reprovisioning (provided that the VirtualBox image is still cached).

You may reprovision a Vagrant virtual machine like this:

    $ vagrant provision

You may destroy a Vagrant virtual machine like this:

    $ vagrant destroy

Note that destroying a Vagrant virtual machine will not remove the `Vagrantfile`. You can immediately `vagrant up` again. Also, if you have ever brought this box up, the VirtualBox base image will be cached, and Vagrant will not have to download the base image.

###### Advanced Vagrant trouble

Occasionally, Vagrant will still have trouble (happens rarely, e.g. when the host crashes, or if you manually edit Vagrants private files). You can then try to follow the following steps:

* Purge Vagrant from your system
* Delete `~/.vagrant.d`, if it still exists
* Delete all Vagrant VMs using the VirtualBox GUI
* Remove all Vagrant NFS exports from `/etc/exports`
* Reinstall Vagrant

#### Devstack recipes

Assuming you have a running 

### edX license

For a detailed description, see the [Open edX Licensing page](https://open.edx.org/open-edx-licensing) on the Open edX website.

The gist is that `edx-platform`, `edx-configuration` and `edx-ora2` are released under the AGPL. This means that derived code must also be released under the AGPL, that you may modify the code, and that you must disclose the source code. Other source code is Apache licenced, meaning that you do not need to distribute sources.

## Usability

### Comparison of different MOOC platforms

Why did we use edX?

### Personas

#### A student

##### Setup

A student wants to enroll in a course, follow it, make exercices and eventually earn a certificate.

#### A teacher

##### Setup

A teacher wants to create a course so students can follow it.

#### A system administrator

##### Setup

A system administrator deploys and maintains the edX platform.

#### A developer

##### Setup

A developer extends the edX platform.

##### Front-end workflow

The front-end workflow comprises editing of template files and styling.

###### Pre-requisites

The front-end developer should have a provisioned Vagrant machine, with a custom theme and grunt running as described in the frontend documentation.

###### Example: updating the color of a link

TODO

* After looking up which LESS file is responsible for the link color, the frontend developer opens it in his local editor. He can open the file through the NFS share, there is no need to edit from within Vagrant (although this is possible).
* When saving, grunt will automatically pick this up, and recompile the assets.
* On reloading the browser, the frontend developer can immediately check his work and repeat the edit-check cycle if necessary.
* When satisfied, the frontend developer commits his changes. These can now be pushed. A system administrator can then reprovision the production machines to use the theme in production.

###### Example: editing a template

##### Back-end workflow

The back-end workflow comprises editing of Python source files and Python configuration files.

## Conclusions


