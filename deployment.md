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

Eerst de ubuntu package sources updaten.

    sudo apt-get update -y
    sudo apt-get upgrade -y
    sudo reboot
    
De OPENEDX_RELEASE variabele instellen op de named release ‘birch’ en de one-step installation file afhalen en uitvoeren.

    export OPENEDX_RELEASE=named-release/birch
    wget https://raw.githubusercontent.com/edx/configuration/$OPENEDX_RELEASE/util/install/vagrant.sh -O - | bash

Nadat edx geïnstalleerd is passen we de installatie aan naar onze fork.
Verwijder eerst de default edx-platform map.

    sudo rm -rf /edx/app/edxapp/edx-platform
    
Voeg aan /edx/app/edx_ansible/server-vars.yml de volgende lijn toe:

    edx_platform_repo: "https://github.com/<user>/edx-platform.git"
    
Voer dan nog het volgende commando uit, dit update het edx-platform met de geforkte repo.

    sudo /edx/bin/update edx-platform master

Nu het edx-platform van onze fork gebruikt word moeten we enkel nog ons thema gebruiken.
Ga hiervoor naar de themes folder.
Daarin clone je ons thema

    git clone git@github.com:HowestX/howestx-theme.git
    
Ga naar de net geclonede folder

    cd howestx-theme
    
Daar installeer je via npm bower, zorg wel da node al geïnstalleerd is.

    npm install -g grunt-cli bower
    
Nog even de dependencies installeren

    npm install
    bower install
    
En dan via grunt alles builden

    grunt build

Nu is het thema geïnstalleerd, nu moeten we edx nog laten weten dat hij dit thema moet gebruiken.
Open hiervoor de lms.env.json file. Plaats daarin "USE_CUSTOM_THEME" op "TRUE" en zet "THEME_NAME" op "howestx-theme"

Nu zou je een werkende fullstack moeten hebben gebaseerd op onze fork.
