# Documentatie frontend

## Theme installeren


Het standaardthema van edx zelf zal waarschijnlijk niet voldoen aan onze eisen. Daarom kiezen we er dan ook voor om een custom thema te gebruiken. Het installeren van een thema in edx gaat als volgt.

Ga naar de themes folder op de gemounte NFS share.

    $ cd themes
    
Clone in die folder het gewenste thema, hier gebruiken we onze howestx-theme

    $ git clone git@github.com:HowestX/howestx-theme.git
    
Ga naar de net geclonede folder.

    $ cd howestx-theme
    
Installeer via npm bower. Zorg wel dat node al geïnstalleerd is. Ook hoef je deze stap maar een keer uit te voeren, indien je later nog eens verandert van thema mag je dit overslaan.

    $ npm install -g grunt-cli bower
    
Indien de dependencies gewijzigd zijn of wanneer je opnieuw clonet, dit nog uitvoeren.

    $ npm install
    $ bower install
    
Grunt aanzetten, dit zal continu builden wanneer er files gewijzigd worden. Bevordert de workflow enorm.

    $ grunt
    
Indien je grunt eenmalig iets wilt laten doen kan je grunt zeggen wat hij eenmaal moet uitvoeren.
Om te linten gebruik je

    $ grunt test

En om te builden gebruik je

    $ grunt build

### Het custom thema aanpassen
Indien je je custom thema nog wilt aanpassen ga je gewoon naar de folder van het thema en pas daarin de files aan.
Bijvoorbeeld:

    $ cd howestx-theme

### Custom theme gebruiken

Nadat het theme geïnstalleerd is moet je edx nog laten weten dat hij dat thema moet gebruiken.
Verbind eerst met ja vagrant, voer het volgende commando daarvoor uit in je devstack folder:

    $ vagrant ssh

Verander van gebruiker naar edxapp

    $ sudo su edxapp

Open dan met nano de lms.env.json file.

    $ nano ../lms.env.json
    
Pas dan “USE_CUSTOM_THEME” onder “FEATURES” aan en zet dat op true. 
Zoek dan naar "THEME_NAME" en vul daar de naam van je theme in.

## Sass en Less

Sass wordt gebruikt door edX maar het theme waarop wij zullen voortwerken is gebasseerd op Less. De workflow gaat als volgt: als vagrant start worden sass files gecompileerd naar css files. Dit heeft als nadeel dat als er veranderingen gemaakt worden in de sass files, vagrant opnieuw moet opstarten.
Bij het less theme dat we gebruiken worden de less files gecompileerd door Grunt. Deze zal de less files in de gaten houden en compileren indien er veranderingen zijn. Dit heeft als voordeel dat we de browser gewoon moeten refreshen om de veranderingen te zien. Deze manier is dus veel sneller om mee te werken.

## Platform name veranderen

Indien je de platformnaam wilt aanpassen moet je eerst connecteren met je vagrant. Voer het volgende commando uit in de devstack folder:

    $ vagrant ssh

Eens verbonden moet je van gebruiker varenderen naar edxapp.

    $ sudo su edxapp

Dan open je met nano de cms.env.json file

    $ nano ../cms.env.json

In die file pas je dan “PLATFORM_NAME” aan naar de gewenste platformnaam.
        
## De juiste css variabelen bewerken

Uitzoeken welke css variablen aangepast moeten worden doe je best met inspect element in de browser. Dan kan je in de less files zoeken naar de juiste class om te veranderen. Op mac kan je in de Finder een search doen naar de classname. Finder zal dan de files tonen die de classname bevatten. Heel handig omdat je dan de aparte less files niet moet doorzoeken. In Sublime Text kan je een search doen in een folder, zo vindt je snel de juiste files terug.

## De vakken op de homepage stylen

De vakken hebben een paarse, gele, groene, blauwe of zwarte stijling. Het kleur toont onder welk traject het vak ligt. De styling wordt toegepast door een van de volgende klasse mee te geven: course-yellow, course-grey, course-blue, course-green of course-purple.

## Het toewijzen van het traject kleur aan de vakken

Trajectkleuren worden aan de hand van een categorie (bv.: traject-blauw) bepaald die meegegeven wordt voor de vaknamen. Aan de hand van die categorie wordt er dan een kleur toegewezen aan het vak. Dit gebeurt door javascript.
