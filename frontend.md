# Documentatie frontend

# Theme installeren
    
themes directory, doe dit op de gemounte NFS shares vanaf je eigen computer
    $ cd themes
clone https://github.com/HowestX/howestx-theme
    $ git clone git@github.com:HowestX/howestx-theme.git
    $ cd howestx-theme
moet je maar een keer uitvoeren, maak dat node installed is
    $ npm install -g grunt-cli bower
uitvoeren wanneer dependencies zijn gewijzigd (komt weinig voor) of wanneer je opnieuw clonet
    $ npm install
uitvoeren wanneer dependencies zijn gewijzigd (komt weinig voor) of wanneer je opnieuw clonet
    $ bower install
belangrijkste in de workflow, dit zal continu builden wanneer er files gewijzigd zijn
    $ grunt
om te linten en code style te checken
    $ grunt test
one-time build, meestal wil je gewoon grunt omdat dat continu zal watchen
    $ grunt build

# Theme editen

devstack directory, doe dit op de gemounte NFS shares vanaf je eigen computer
    $ cd devstack
themes directory, doe dit op de gemounte NFS shares vanaf je eigen computer
    $ cd themes
    $ cd howestx-theme
belangrijkste in de workflow, dit zal continu builden wanneer er files gewijzigd zijn
    $ grunt

# Custom theme gebruiken

in de devstack folder en na vagrant up
    $ vagrant ssh
    $ sudo su edxapp
zoek naar “USE_CUSTOM_THEME” onder “FEATURES” zet dat op true
    $ nano ../lms.env.json
dan zoek je naar “THEME_NAME” daar vul je de naam van je theme in
        
# Sass en Less

Sass wordt gebruikt door edX maar het theme waarop wij zullen voortwerken is gebasseerd op Less. De workflow gaat als volgt: als vagrant start worden sass files gecompileerd naar css files. Dit heeft als nadeel dat als er veranderingen gemaakt worden in de sass files, vagrant opnieuw moet opstarten.
Bij het less theme dat we gebruiken worden de less files gecompileerd door Grunt. Deze zal de less files in de gaten houden en compileren indien er veranderingen zijn. Dit heeft als voordeel dat we de browser gewoon moeten refreshen om de veranderingen te zien. Deze manier is dus veel sneller om mee te werken.

# Platform name veranderen

in de devstack folder en na vagrant up
    $ vagrant ssh
    $ sudo su edxapp
zoek naar “PLATFORM_NAME”
    $ nano ../cms.env.json
        
# De juiste css variabelen bewerken

Uitzoeken welke css variablen aangepast moeten worden doe je best met inspect element in de browser. Dan kan je in de less files zoeken naar de juiste class om te veranderen. Op mac kan je in de Finder een search doen naar de classname. Finder zal dan de files tonen die de classname bevatten. Heel handig omdat je dan de aparte less files niet moet doorzoeken.

# De vakken op de homepage stylen

De vakken hebben een paarse, gele, groene, blauwe of zwarte stijling. Het kleur toont onder welk traject het vak ligt. De styling wordt toegepast door een van de volgende klasse mee te geven: course-yellow, course-grey, course-blue, course-green of course-purple.
