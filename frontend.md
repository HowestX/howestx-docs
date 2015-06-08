# Documentatie frontend

# Theme installeren

    $ cd themes # themes directory, doe dit op de gemounte NFS shares vanaf je eigen computer
    $ git clone git@github.com:HowestX/howestx-theme.git # clone https://github.com/HowestX/howestx-theme
    $ cd howestx-theme
    $ npm install -g grunt-cli bower # moet je maar een keer uitvoeren, maak dat node installed is
    $ npm install # uitvoeren wanneer dependencies zijn gewijzigd (komt weinig voor) of wanneer je opnieuw clonet
    $ bower install # uitvoeren wanneer dependencies zijn gewijzigd (komt weinig voor) of wanneer je opnieuw clonet
    $ grunt # belangrijkste in de workflow, dit zal continu builden wanneer er files gewijzigd zijn
    $ grunt test # om te linten en code style te checken
    $ grunt build # one-time build, meestal wil je gewoon grunt omdat dat continu zal watchen

# Theme editen

$ cd devstack # devstack directory, doe dit op de gemounte NFS shares vanaf je eigen computer
$ cd themes # themes directory, doe dit op de gemounte NFS shares vanaf je eigen computer
$ cd howestx-theme
$ grunt # belangrijkste in de workflow, dit zal continu builden wanneer er files gewijzigd zijn

# Custom theme gebruiken
