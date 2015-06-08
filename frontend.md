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

        $ vagrant ssh # in de devstack folder en na vagrant up
        $ sudo su edxapp
        $ nano ../lms.env.json # zoek naar “USE_CUSTOM_THEME” onder “FEATURES” zet dat op true
        $ dan zoek je naar “THEME_NAME” daar vul je de naam van je theme in
        
# Sass en Less

Sass wordt gebruikt door edX maar het theme waarop wij zullen voortwerken is gebasseerd op Less. De workflow gaat als volgt: als vagrant start worden sass files gecompileerd naar css files. Dit heeft als nadeel dat als er veranderingen gemaakt worden in de sass files, vagrant opnieuw moet opstarten.
Bij het less theme dat we gebruiken worden de less files gecompileerd door Grunt. Deze zal de less files in de gaten houden en compileren indien er veranderingen zijn. Dit heeft als voordeel dat we de browser gewoon moeten refreshen om de veranderingen te zien. Deze manier is dus veel sneller om mee te werken.

# Platform name veranderen

        $ vagrant ssh # in de devstack folder en na vagrant up
        $ sudo su edxapp
        $ nano ../cms.env.json # zoek naar “PLATFORM_NAME”
        
# De juiste css variabelen bewerken

Uitzoeken welke css variablen aangepast moeten worden doe je best met inspect element in de browser. Dan kan je in de less files zoeken naar de juiste class om te veranderen. Op mac kan je in de Finder een search doen naar de classname. Finder zal dan de files tonen die de classname bevatten. Heel handig omdat je dan de aparte less files niet moet doorzoeken.
