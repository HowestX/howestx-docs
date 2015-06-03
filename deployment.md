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

## Bronnen

http://docs.vagrantup.com/v2/provisioning/index.html
http://en.wikipedia.org/wiki/Vagrant_(software)

