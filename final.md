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

#### Less and Sass

Less and Sass are CSS pre-processors. They extend CSS with variables and functions, leading to a more maintable frontend codebase. Less and Sass files are compiled down to CSS files so a browser can read them.

Less and Sass serve the same purpose, and have similar features, but are not compatible. Sass is mostly used in the Ruby world, Less is mostly used in the node.js world.

#### Vagrant

The goal of Vagrant in the edX project is to give all developers the same development environment. It does this through the use of virtual machines: Vagrant is  a wrapper around a VM provider backend. For the edX platform, the VirtualBox backend is used.

In edX, Vagrant is used to run the development stack. This makes sure all developers are working with the same software, and the same versions. This makes it easier to reproduce problems and bugs.

Vagrant machines are specified using a `Vagrantfile`, a file that specifies the virtual machine's properties: RAM, vCPUs, what ports that should be forwarded from the host and, most important of all, the base image.

Base images, also known as base boxes, are virtual machines on which projects can base themselves. For example there is an Ubuntu 12.04 base, a FreeBSD base, ...

The first time a box is started, it will be *provisioned*. The base image will be started, and on it, the provision scripts will run. This only has to happen once, but you can force reprovisioning if something is wrong.

#### Ansible

Ansible is a way to automate provisioning of  (virtual) machines. It's used extensively in the edX project due to the complexity of edX deployments. With Ansible, task are neatly split up (there are roles to set up repositories, roles to set up ElasticSearch, roles to install Python packages, ...).

#### LDAP

LDAP (*Lightweight Directory Access Protocol*) is a protocol that allows clients to search, modify and connect to internet directories. We will use it here in an authentication contexts: we will make clients authenticate to an LDAP server.

#### Central Authentication Service (CAS)

A CAS server provides Single Sign-On for an organisation. This means that users sign in once, and are then signed in automatically across other applications. This is not the case with LDAP: using LDAP, users have sign in into every application and authentication happens on the LDAP.

A CAS server provides multiple pluggable backends. You can plug in a SQL database, an LDAP server...

Using a CAS server is comparable to OAuth. When users click *Log in*, they are taken to the CAS server's login page. After authenticating with the CAS server, they are taken back the application.

### edX architecture

The architecture of edX is very complex. To give you a rough idea, in the codebase you can find Python, Ruby, node.js and Java sources. Here, we will take a quick look at the edX Platform architecture, and took a quick look at how edX is expected to be used.

#### Differences between edX distributions

edX has different distributions (often called *stacks*), each with a different purpose..

##### Devstack

The *devstack* is intended for local development. Its lifecycle is managed through Vagrant. To create a devstack virtual machine, you download a `Vagrantfile` from edX, and run `vagrant up`. This will download a VirtualBox base image and provision the machine.

Development on the devstack is easy, because Vagrant exposes a few folders (`edx-platform`, `themes` `ora` and `cs_comments_service`) as NFS mounts, allowing you to edit file on the host computer. This means that you don't have to edit files on the guest.

##### Fullstack

The *fullstack* is a distribution that runs edX in production mode, with all services on one machine. This is not recommended for production deployments. This distribution is ideal for staging and evaluating.

##### Production stack

The *production stack* is what is recommended for production. The production stack is hard to deploy, since it's very complex, but an Amazon Web Services template is provided. It contains multiple machines, databases and load balancers.

#### Persistance

There are main database systems in use for edX:

* MySQL, a popular relational database
* MongoDB, a popular JSON-based NoSQL database

Some online resources may say that Sqlite is used in the devstack, but this is no longer the case. Devstack provisions a MySQL database.

##### Usage of MySQL

MySQL is used to keep user data (login data, user progress, ...).

##### Usage of MongoDB

MongoDB is used to store courses (the courses you design in edX Studio). Historically, this information was stored in XML files. MongoDB was a better fit than a relational database to store this data in.

#### Authentication

Authentication is handled through standard Django. The Django framework provides a flexible way to handle authentication. It's possible to define multiple *authentication backends*, the framework will try each of them in succession until either one successfully authenticates the user, or none are left. User details, such as login name, email address, and for the standard backend, a password, are saved in a relational database.

##### CAS

LDAP isn't a great match for edX. It requires the installation of extra packages, requires modifications to the edX platform, and feels clunky.

edX actually has a built-in solution for central authentication: it provides support for a Central Authentication Service server. Enabling a CAS server is a matter of enabling a few simple configuration entries.

However, this is not ideal if you want to allow users to register themselves. You can't easily use the existig capabilities of edX/Django, you would have to write a registration handler and authentication backend for CAS, seperate from your edX codebase.

Thus, CAS is not a good match if you want hybrid authentication (authentication through LDAP, but also allowing registering and logging in directly through edX).

##### Default users

There are a number of default user accounts accounts available:

* `staff@example.com`, password: `edx`, staff account that can create courses
* `verified@example.com`, password: `edx`, student account for testing verified courses
* `audit@example.com`, password: `edx`, student account for testing course auditing (meaning: following the course without paying for it)
* `honor@example.com`, student account for testing honor code certificates

#### LMS and CMS

The edX platform is split in two main front-facing sites: the LMS (Learning Management System) and the CMS (Content Management System).

##### The Learning Management System

The *Learning Management System*, or LMS for short, is where students spend their time. This is the main platform, which lists courses, allows students to enroll into courses and allows students to partake in courses.

##### The Content Management System

The *Content Management System*, often called edX Studio, Studio or CMS, allows teachers to create, edit, preview and publish courses.

#### Themes

edX now has a flexible theme structure. Aside from configuring to use the new theme, the platform isn't modified in any way when creating or updating a theme.

Themes go into a `themes/` folder. The theme to use can then be specified in the configuration.

##### Existing themes

There are only a few existing themes on which we can base a custom theme.

* The built-in theme: this is the theme that is inside the edX platform core, and the default that gets loaded. This means that you have to edit the platform to update the theme, which is not something you want to do, especially since edX supports external themes.
* The Standford theme: this is the theme that Stanford University uses. This theme is not responsive, which was a dealbreaker for us.
* IONISx theme: the theme developed by the French IONIS Education Group. This theme is based on Bootstrap, and responsive by default.

##### Choice of theme

We decided to adapt the IONISx theme to our needs. Being responsive from the start is a huge plus, and the external theming support helps provides a clean split between frontend and backend code.

##### Less in the IONISx theme

The IONISx theme used Less (not Sass, like the edX platform). The edX platform doesn't understand Less. Installing a Less pre-processor on the platform would require a whole slew of packages to be brought in and an extra build pipeline. That's not ideal.

Our solution is to check in the compiled files into source control. That's not very pretty, but it works. A `grunt` watcher watches for changes, and executes the build.

This makes it easy to release the theme: the production edX platform can just pull in the repository.

#### Certificates

Some people 

#### Configuration

Configuration in edX is very complex. Here are some hints on the possible files:

* `lms.env.json` and `cms.env.json` are generated by provisioning script. You can edit them to test.
* `common.py` sets all the default variables, which can be overwritten by Ansible configuration
* `server-vars.yml` (full location: TODO) sets configuration that will be used to provision the machine. This will be used to populate `*.env.json`. A list of all possible option can be found [here](https://github.com/edx/configuration/blob/master/playbooks/roles/edxapp/defaults/main.yml).

Be wary that there are `.json` and `.yml` files, be mindful of the different syntax.

### edX license

For a detailed description, see the [Open edX Licensing page](https://open.edx.org/open-edx-licensing) on the Open edX website.

The gist is that `edx-platform`, `edx-configuration` and `edx-ora2` are released under the AGPL. This means that derived code must also be released under the AGPL, that you may modify the code, and that you must disclose the source code. Other source code is Apache licenced, meaning that you do not need to distribute sources.

### Development and deployment recipes

#### Vagrant recipes

##### Installing Vagrant on a Mac OS machine

Mac OS users can download Vagrant as a `.dmg` from [its website](http://www.vagrantup.com/downloads.html). Installation works as usual, but you will have to install VirtualBox seperately from [here](https://www.virtualbox.org/wiki/Downloads).

##### Installing Vagrant on a Linux machine

We suggest that Linux users **do not** install Vagrant using their package manager. The Vagrant in the Debian and Ubuntu repositories is often out of date, and this may cause strange errors when developing on the edX platform. Instead, we encourage Linux users to install Vagrant from [its website](http://www.vagrantup.com/downloads.html).

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

Occasionally, Vagrant will still have trouble (happens rarely, e.g. when the host crashes, or if you manually edit Vagrant's private files). You can then try to follow the following steps:

* Purge Vagrant from your system
* Delete `~/.vagrant.d`, if it still exists
* Delete all Vagrant VMs using the VirtualBox GUI
* Remove all Vagrant NFS exports from `/etc/exports`
* Reinstall Vagrant

#### Devstack recipes

This section assumes you have a running devstack virtual machine with Vagrant.

##### Access to the Vagrant guest

###### SSH access

###### File access

#### Theming recipes

##### Bootstrapping a custom theme based on the IONISx theme

You can bootstrap a custom theme on devstack by following these steps:

- Install global dependencies: install [node.js](https://nodejs.org/) from its website, then install `grunt-cli` and `bower` globally (you only need to do this once on every development machine):

    $ npm install -g grunt-cli bower

- Clone the IONISx theme to a repository of your own. This will allow you to push back commits you make to this repository.
- Go into the `themes/` folder of edX on your local machine. It's mounted into the Vagrant virtual machine, so you can access it from your own computer.
- Clone the repository here. You may rename the folder. The name of the folder is your theme name, and this is very important. This is what you will have to provide to the configuration files.
- Rename the `_ionisx.scss` file located in the `static/sass/` folder to match your theme name (name of the folder). Your theme will not function if you don't do this.
- Now install the dependencies of the theme:

    $ npm install
    $ bower install

- Run `grunt`. The default mode will watch until you press `^C`. As soon as you save, Grunt will pick it up and recompile all the assets. The edX platform can't process the Less files, so it's important to compile these down to CSS.

##### Using a custom theme in devstack

To use a custom theme in the devstack, you can update `lms.env.json`. This file is located in the home directory of the `edxapp` user.

    $ vim ~/lms.env.json

Two modifications need to be made:

1. In the `FEATURES` list, set `USE_CUSTOM_THEME` to `true`
1. Set the `THEME_NAME` entry to the name of your theme (for example, `howestx-theme`)

It should look like this:

    {
        ...
        "FEATURES": {
            ...,
            "USE_CUSTOM_THEME": true
        },
        ...
        "THEME_NAME": "howestx-theme",
        ...
    }

You need to run (or restart if it's already running) `paver` to see the effect in your browser:

    edxapp@~/edx-platform$ paver devstack lms

##### Editing a custom theme

##### Using a custom theme in fullstack

Edit the `/edx/app/edx_ansible/server-vars.yml` file on the fullstack server and add the following variables:

    edxapp_use_custom_theme: true
    edxapp_theme_name: 'howestx-theme'
    edxapp_theme_source_repo: 'git://github.com/howestx/howestx-theme.git'
    edxapp_theme_version: 'HEAD'

Then run the update script:

    sudo /edx/bin/update edx-platform master

You may specify another branch of the `edx-platform` by changing `master` in the update command.

#### Deployment recipes

##### Fullstack deployment

###### On an Ubuntu 12.04 machine

You can install a fullstack deployment on an Ubuntu 12.04. It's important that you do this on a "fresh" server (nothing installed yet) using the following:

    $ sudo apt-get update -y
    $ sudo apt-get upgrade -y
    $ sudo reboot

After rebooting, you can install using the one-step install script:

    $ wget https://raw.githubusercontent.com/edx/configuration/master/util/install/sandbox.sh -O - | bash

You can also install using  named release:

    $ export OPENEDX_RELEASE=named-release/birch
    $ wget https://raw.githubusercontent.com/edx/configuration/$OPENEDX_RELEASE/util/install/vagrant.sh -O - | bash

Be aware that this does **not** work with Ubuntu 12.04.5 (the standard on Azure and DigitalOcean)! You should install the latest release using the first script.

###### Elasticsearch SSL certificate trouble

In the latest elease, you may see an error with Elasticsearch's certificate:

    TASK: [elasticsearch | download elasticsearch] ********************************

    failed: [localhost] => {"failed": true, "item": ""}

    msg: Failed to validate the SSL certificate for download.elasticsearch.org:443. Use validate_certs=no or make sure your managed systems have a valid CA certificate installed. Paths checked for this platform: /etc/ssl/certs, /etc/pki/ca-trust/extracted/pem, /etc/pki/tls/certs, /usr/share/ca-certificates/cacert.org, /etc/ansible

A work-around is to edit the Ansible configuration file:

    vim /var/tmp/configuration/playbooks/roles/elasticsearch/defaults/main.yml

Change the variable `elasticsearch_url` from `https` to `http`. **Only use this as a temporary work-around for testing!** The certificate will probably be fixed in a coming release. You now need to reprovision the server:

    cd /var/tmp/configuration/playbooks && sudo ansible-playbook -c local ./edx_sandbox.yml -i "localhost,"

###### Using a edX platform fork

You use a custom `edx-platform` for the fullstack too. Add the following to `/edx/app/edx_ansible/server-vars.yml`:

    edx_platform_repo: "https://github.com/HowestX/edx-platform.git"

Obviously using your own repository. Then you need to remove the existing platform:

    sudo rm -rf /edx/app/edxapp/edx-platform

Then reprovision the server:

    sudo /edx/bin/update edx-platform release

`release` is a git branch. You can always specify a custom branch.

##### Production deployment

TODO

#### Certificates recipes

##### Turning on certificates in fullstack

To turn on certifates in fullstack, first edit the `lms/envs/common.py` file:

    $ vim /edx/app/edxapp/edx-platform/lms/envs/common.py

Search for an option called `CERTIFICATES_HTML_VIEW`, and set it to true:

    ...
    # Certificates Web/HTML Views
    'CERTIFICATES_HTML_VIEW': True,
    ...

Now sync and migrate the databases:

    $ . . ../venvs/edxapp/bin/activate
    $ ./manage.py cms syncdb --settings=aws
    $ ./manage.py lms syncdb --settings=aws
    $ ./manage.py cms migrate --settings=aws --delete-ghost-migrations
    $ ./manage.py lms migrate --settings=aws --delete-ghost-migrations
    
#### Importing and exporting courses

EdX already provedes the functionality to import and export courses. This provides an easy way to for example take a course from your platform and put it on edX's own platform.

To Export or import a course, open the course in the studio. Then click on `settings`. In the sub-menu that opens you have the options to import and export. Courses are saved in a `.tar.gzip`.
    
#### Internationalisation recipes

When offering an online service it might be useful to provide that service in multiple languages to expand your possible userbase. But properly translating an entire service takes a lot of effort and time, luckily edX also provides a full translation of it's contents in a lot of languages. Setting it up is a breeze.

First of all you will need a `.transifexrc` file. This file contains the information that edX will use to login to transifex, the service that provides the translations.

Make a `.transifexrc` file on the following location

    nano ~/.transifexrc

Give it the following content

    [https://www.transifex.com]
    hostname = https://www.transifex.com
    username = user
    password = pass
    token =

Change 'user' and 'pass' to your own credentials. Token is to remain empty.
Then run the following commands

    source /edx/app/edxapp/edxapp_env
    cd /edx/app/edxapp/edx-platform
    
Now we must make sure that the languages we wish to support are marked as active in `conf/locale/config.yaml`.
Open that file and uncomment any language you wish to support.

    nano conf/locale/config.yaml

Any new languages have to be pulled in using the following command

    tx pull -l <lang_code>
    
Run the following command to make sure the changes take effect
    
    paver i18n_fastgenerate

Restart the lms and cms

    sudo /edx/bin/supervisorctl restart edxapp:

Now go to the language settings on your Django admin panel, you can find these at `<your_website>/admin/dark_lang`. For example `http://www.howestx.be/admin/dark_lang`
There you must add a configuration, this configuration contains what languages users can select.
Note that everything must be typed in lowercase, '_' becomes '-' and everything is comma seperated.
For example

    en,nl-nl,fr,ar,es-419

Save this configuration.
Now edX will be displayed in the user's preferred language.
If you would want to change the default language of the entire platform, you must edit the `EDXAPP_LANGUAGE_CODE` in `/edx/etc/server-vars.yml`.

## Usability

### Comparison of different MOOC platforms

If a modern college is interested in offering the capability of massive online learning, a MOOC platform is the way to go. However, developing one is an extremely complex and time consuming process. Luckily there are already a lot of free open source MOOC platforms that could, in theory, be easily adapted to fit a college’s specific needs. Here, we are going to briefly compare a couple of MOOC platforms.

We will compare the following platforms:
* edX
* Peer 2 Peer University
* openMooc

We are interested in the following criteria:
* Usability (is the platform easy to use as an end user?)
* Activity (is the platform still actively developed?)
* Complexity and adaptability (is it easy to adapt to our needs?)

#### edX

EdX is a large open souce MOOC platform that was developed as a joint venture between the Massachussets Institute of Technology and the Harvard university. UC Berkeley has also joined.

The platform already has a lot of succes, boasting more than 3 million users (as of October 2014), and being used by institutions such as Stanford.

##### Usability

EdX as a platform is very usable to an end user, it offers an intuitive user interface which is alo rather attractive to the eye. This does not translate over to mobile, as the default theme is not mobile ready. It also seems to have a lot of functionality already built-i.n

##### Activity

We were happy to see edX being a very active platform. Not only does it have a remarkeable amount of users, it also has a lot of developers behind it. When looking up the github page of the platform, we saw commits as recent as 2 hours ago.

It’s also a rather ‘hot topic’ online, showing a surge in activity.

![Google trends graphic on edX](images/GoogleTrends_edX.png "EdX on Google trends")

##### Complexity

EdX so far appears to be the perfect choice for any school interested in setting up a MOOC platform of their own. However, it’s sheer complexity might cause some issues.

EdX is an absolutely huge platform, that is also highly fragmented. Simply finding the source code of a specific part of it is a huge task on it’s own.

However, there is also an immense amout of documentation and an active community ready to help. edX claims to offer support to an institution attempting to adapt edX to it’s own needs.

#### Peer 2 Peer University

P2PU was founded with funding from the Hewlett foundation, the Shuttleworth foundation and the university of California Ivine. It was founded by a group of people that felt the existing ways of online learning were inadequate, especially the social aspect.

##### Usability

P2PU offers an attractive and intuitive user interface. It also appears to be mobile ready, which is a big plus.

##### Activity

P2PU is definitely still used by a large user base, but it shows signs of decline. It’s GitHub is not that active and contains open issues as old as 3 years (the oldest bug dates from 25 May 2012). Google trends also reveals a steady decline.

![Google trends graphic on P2PU](images/GoogleTrends_P2PU.png "P2PU on Google trends")

##### Complexity
P2PU is what could be called a medium sized platform. It appears to be a nicely organised platform, that should be easy to adapt. It also offers quite a bit of documentation. The big issue for us is the fact that there are so many old open issues. Is the development grinding to a halt?

#### OpenMooc

OpenMooc is a MOOC platform that hails from Spain, it was made to promote virtual education by using IT in higher education. Since it was made in Spain, multilanguage support has been built into its core.

##### Usability

OpenMooc’s default user interface is atrocious. It's also not mobile ready. A lot of work would have to be put in to make this a usable platform. A couple of things did draw our attention however, like the platform's ability to follow up on your own as well as other people's questions and it's native support for LDAP.

##### Activity
As far as we can tell, OpenMooc isn’t a widely used platform. A couple of smaller institutions allegedly use it but there is no proof of any major organisation showing interest in this platform. It’s GitHub is also very inactive, most commits are already a year old. Google trends also confirms this.

![Google trends graphic on OpenMooc](images/GoogleTrends_OpenMooc.png "OpenMooc on Google trends")

##### Complexity

This is a rather small MOOC platform. It seems rather well organised, but very badly documented. Also setting up this platform seems to not be a trivial matter.

#### Conclusion

There are a lot of platforms out there, the  platforms we compared are the most prominent. They all offer the ability to host a decent MOOC platform, yet edX and P2PU are by far the largest and offer the most usability out of the box. Deiciding between these two is not easy, edX offers more functionality whereas P2PU offers a seemingly easier to adapt codebase. However, edX is seeing way more use and development. P2PU as a platform seems to be heavily on the decline, so trying to build a lasting MOOC solution based on that platform appears unwise.

In the end, edX seems the best choice. It may be a complex system that will require a thorough analysis, but it’s active development, good support and documentation should easily make up for that.

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
* Front-end developer pushes teh compilied CSS files. This way the the team members don't need to install Grunt.

###### Example: editing a template

##### Back-end workflow

The back-end workflow comprises editing of Python source files and Python configuration files.

## Price

### Case studies

#### MongoDB University

http://www.wiredacademic.com/2013/04/is-the-price-right-a-mooc-startup-case-study/
https://university.mongodb.com/about/how-mongodb-university-online-courses-are-produced
http://moocnewsandreviews.com/edx-and-stanford-partnering-on-open-source-mooc-platform/
http://www.kurzweilai.net/online-learning-at-stanford-goes-open-source-with-openedx
https://groups.google.com/forum/#!msg/edx-code/YEJxCrCNpLM/x2NHZH7yZQIJ

## Evaluation and Conclusion

We will now evaluate the edX platform according to criteria specified by Howest

### Is the platform user friendly?

EdX as a platform is very user friendly. For students it has a very nice user interface that allows them to quickly find what they need. Teachers can easily create, manage and share courses.

### Can the platform be put in production easily?

This is where the most issues will be found. EdX is an immensely complex system that is difficult to set up. Furhermore when we tried to setup a named release, it kept failing to deploy. EdX was also developed with AWS in mind, not really offering a lot of support for Azure.

### Is the platform still being developed?

There is still a very healthy and active development going on. When checking the github page we generally see commits that are 2-3 hours old. More and more people are also starting to use edX and there are eve  consultants who are specializing in this platform.

### Is the future of the platform certain?

EdX has an immense userbase and a lot of big organisations like MIT and Harvard backing it up. Google trends is also showing a positive trend.

### Are others also using the platform?

Yes, it's a very widely used platform. A lot of major intitutions have opted for edX as their MOOC platform of choice.
It also boasts more than 3 million users (as of 2014).

### Is it easy to adapt the platform?

It's a very big and complex platform, adapting it is not an easy matter. Somtimes simply finding the correct files to work in can be a task on it's own. However, there is plenty of very detailed documentation and a very active and helpful community that should alleviate most issues.

### Is is easy to set up a development environment?

In order to set up a development environment, vagrant is preferrable. On Linux and Mac this is an easy thing to use, but on Windows it has proven to be very difficult. It's also very fragile and can break easily, requiring you to reset the development environment.

### Is the platform maintainable?

Once the development environments are set up, maintaining is easy. The back-end and front-end are fully seperated and fully pluggable, leading to easy development.

### Is there internationalisation within the platform?

EdX provides internationalisation that is a breeze to set up. You can make use of a simple web interface. It makes use of transifex to obtain it's translations. Sadly, dutch is not yet translated.

### Can we monetise the platform?

Yes, it's possible to ask money for verified certificates. This model has workde for organisations such as edx.org, MongoDB university,...

### Can we brand the platform?

It's possible to brand the entire platform, there is no dependance on edX for this matter. It's also very easy to do this.

### Is there paid support?

No paid support is offered, however edX provides some support to any organisation looking to set up their own fork. The community is also a source of help just like the excellent documentation. If necessary there are even paid consultants specialised in edX.

### What are the possibiltie for the future?

This platform can certainly be used as a base to develop upon. There is a lot that can be done with interactivity, Azure Active Directory can be integrated and courses could be published on edx.org . The next named release is to be called `Cypress` and will allegedly contain changes in social profile, 3rd party auth and single sign on, among other features.
