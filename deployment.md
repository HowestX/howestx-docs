# Deployment

Deployment van edX is moeilijk. Dat kan niet anders, het is dan ook een groot en complex systeem.

## Vagrant

Vagrant is een manier om virtuele ontwikkelomgevingen te creeeren. Het maakt op de achtergrond gebruik van een service om virtuele machines te draaien, veelal Virtualbox, maar ook andere back-ends, zoals LXC, Docker, ...

In edX wordt Vagrant gebruikt om de devstack te draaien. Het voordeel is dat iedereen in dezelfde omgeving werkt. Het nadeel is dat edX redelijk wat gebruikt maakt van symlinks, en dat het developen op Windows daardoor heel wat moeilijker tot praktisch onmogelijk wordt. Het platformafhankelijke geldt dus niet per se voor de edX devstack op Windows.

Het aanmaken van een nieuwe devstackmachine wordt gedaan door middel van een `Vagrantfile`. Een `Vagrantfile` bevat alle informatie die nodig is om de virtuele machine op te bouwen: poorten die geforward moeten worden van de host naar de VM, shared folder die moeten worden opgezet op de host, hoeveelheid RAM en vCPUs, en belangrijkst van allemaal: de box (image) waarvan vertrokken moet worden.

Base boxes zijn images waarop verder gebouwd kan worden. Zo is bijvoorbeeld een box voor Ubuntu 12.04, of een box voor OpenBSD met Chef en Puppet.

De `Vagrantfile` voor de edX devstack kan hier gevonden worden: https://github.com/edx/configuration/blob/master/vagrant/release/devstack/Vagrantfile

Wanneer je even snel kijkt, zie je dat de box wordt afgehaald van `http://files.edx.org/vagrant-images/#{openedx_releases[rel][:file]}`. Dat is jammer, we hadden gehoopd dat edX een standaardbox zou gebruiken, zodat het wat duidelijker zou zijn wat er allemaal wordt opgezet.

Provisioneren van een Vagrant box is het installeren van dependencies en software. Dit gebeurt wanneer je `vagrant up` doet, en slechts een keer in de levensduur van de box. Er zijn ook manieren om dit te forceren (`vagrant up --provision`).

### Bronnen

http://docs.vagrantup.com/v2/provisioning/index.html
http://en.wikipedia.org/wiki/Vagrant_(software)

## Fullstack deployment

Fullstack is een manier om edX op een enkele server te deployen. Alle services (LMS, Studio
, ...) worden op een enkele machine gedraaid. Dit is geen ideale deploymentsituatie, maar is voldoende als stagingomgeving.

Om een fullstack server op te zetten zijn de volgende specs aan te raden:

Ubuntu 12.04 amd64 (met oraclejdk),
Min 2GB geheugen, 4GB aangeraden,
Min 2.00GHz CPU,
Min 25GB Disk space, 50GB aangeraden

### Installeren op Ubuntu 12.04

    export OPENEDX_RELEASE=named-release/birch
    wget https://raw.githubusercontent.com/edx/configuration/$OPENEDX_RELEASE/util/install/vagrant.sh -O - | bash

### Problemen met Elasticsearch's SSL certificaat

Mogelijks krijg je een error met de laatste release van de configuratiescripts:

    TASK: [elasticsearch | download elasticsearch] ********************************
    
    failed: [localhost] => {"failed": true, "item": ""}
    
    msg: Failed to validate the SSL certificate for download.elasticsearch.org:443. Use validate_certs=no or make sure your managed systems have a valid CA certificate installed. Paths checked for this platform: /etc/ssl/certs, /etc/pki/ca-trust/extracted/pem, /etc/pki/tls/certs, /usr/share/ca-certificates/cacert.org, /etc/ansible

Dan kan je de Elasticsearch role aanpassen in Ansible:

    vim /var/tmp/configuration/playbooks/roles/elasticsearch/defaults/main.yml

Pas de variable `elasticsearch_url` aan van `https` naar `http`. Het is een tijdelijke workaround, maar het werkt tot edX hun certificaten fixt. Daarna kan je de machine herprovisioneren:

    cd /var/tmp/configuration/playbooks && sudo ansible-playbook -c local ./edx_sandbox.yml -i "localhost,"

Bron: https://groups.google.com/forum/#!topic/edx-code/AGPrF4Fmv64

### Custom theme met fullstack

Voeg dit toe aan `/edx/app/edx_ansible/server-vars.yml` op de fullstack-server (maak dit bestand aan als het nog niet bestaat):

    edxapp_use_custom_theme: true
    edxapp_theme_name: 'howestx-theme'
    edxapp_theme_source_repo: 'git://github.com/howestx/howestx-theme.git'
    edxapp_theme_version: 'HEAD'

Voer daarna het updatescript uit:

    sudo /edx/bin/update edx-platform master

### Naam en andere configuratie aanpassen

Voeg een variabele toe aan `/edx/app/edx_ansible/server-vars.yml`, net zoals je een theme aanpast. Je kan dezelfde naam gebruiken als in bv. `lms.env.json`:

    EDXAPP_PLATFORM_NAME: 'howestX'

### Remote aanpassen voor edx-platform

Wanneer je een eigen `edx-platform` repository hebt, kan je deze laten gebruiken door de fullstack. Voeg daarvoor de volgende regel toe aan `/edx/app/edx_ansible/server-vars.yml`:

    edx_platform_repo: "https://github.com/HowestX/edx-platform.git"

Daarna verwijder je het bestaande platform:

    sudo rm -rf /edx/app/edxapp/edx-platform

Voer daarna de provisionering opnieuw uit:

    sudo /edx/bin/update edx-platform release

`release` is een git branch. Je kan ook `master` of een andere kiezen.

### Search enablen in fullstack

Aangezien er geen configuratievariabelen voorzien zijn in `server-vars.yml`, moeten we de configuratie binnen `edx-platform` gaan aanpassen. We gaan de globale configuratie aanpassen. Edit ` lms/envs/common.py` en zit de volgende variabelen op `True`:

    'ENABLE_COURSEWARE_SEARCH': True,
    ...
    'ENABLE_DASHBOARD_SEARCH': True,
    ...
    'ENABLE_COURSE_DISCOVERY': True,
    ...

Herprovisioneer nu de fullstack server.

## Deployment met Amazon Webservices

Voor AWS bestaan er publieke AMI's, voor europa is dat `ami-aa76d0dd`. Aangeraden is dat je deze deployed op een t2.medium instance.

Start deze server en connecteer ernaar via ssh (de user is 'ubuntu').

    chmod 400 {path-to-keypair}
    ssh -i {path-to-keypair} ubuntu@{public-ip}

Update dan de codebase

    sudo /edx/bin/update configuration release
    sudo /edx/bin/update edx-platform release

Indien U een 'Unable to resolve host' error krijgt, voeg dan nog het volgende toe aan `/etc/hosts`

    127.0.1.1 {whatever ip}
    
Indien er een fout is in de 'edx-platform release' voer dan het volgende uit

    cd /edx/app/edxapp/edx-platform
    sudo -u edxapp git remote prune origin
    
U kan nu verbinden met het LMS op poort 80 en het CMS op poort 18010. Indien er een 502 foutmelding word weergegeven, herstart dan de mongo ansible role

    cd /edx/app/edx_ansible/edx_ansible/playbooks && sudo /edx/app/edx_ansible/venvs/edx_ansible/bin/ansible-playbook -i localhost, -c local run_role.yml -e 'role=mongo' -e 'mongo_create_users=True'

De basisauthenticatie voor de site is:

    username: edx
    password: edx

De standaardlogingegevens zijn:

    user: staff@example.com
    password: edx
    
Indien U een 500 foutmelding krijgt, doe dan het volgende

    cd /edx/app/edxapp/edx-platform && sudo -u www-data /edx/bin/python.edxapp manage.py lms syncdb --migrate --settings aws
    cd /edx/app/edxapp/edx-platform && sudo -u www-data /edx/bin/python.edxapp manage.py cms syncdb --migrate --settings aws
    
Meer over het beheren van een fullstack:
https://github.com/edx/configuration/wiki/edX-Managing-the-Full-Stack
