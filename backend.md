# Documentatie backend

Team notes kunnen voorlopig nog [hier](https://github.com/HowestX/HowestX/wiki/HowestX-team-notes) gevonden worden.

# SSH en git

Om gemakkelijk te gaan gebruiken, maak je best SSH keys aan. Zo kan je zonder in te loggen commits pushen en pullen van git remotes.

## Nieuwe key aanmaken

Om Linux en Mac OS kan je gewoon `ssh-keygen` uitvoeren. Je kan gewoon de defaults aanvaarden.

## Key uploaden op GitHub

Nadat je een key hebt aangemaakt, moet je die uploaden op GitHub zodat GitHub je herkent. Om ze op te vragen, voer je `ssh-add -L` uit. Een SSH key ziet er als volgt uit:

    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/piguDZ+3r6Qwjz/dBA+A+F3Q4Gq3i2VPgU1AxyfRO4Lq0gJHa8bdAOIz86PFFjNg30njgxG2LGVR5WIV9gof4QjBgxFv8FU5wvRFXhWOdNyUt6TjANFo2Fhn5RwilXAWtDDLq5Qo7F9NOL8kXcBxwSuzWTX95rm+D6c2VVAcHS/LIQWWQfui1+xfvX6162ykZsIhL2bW7gvNPem4CgLBx9mfRtzPC3owMZx0YHkroHkOdwfWLJF1zr3sqgWyQiSNZql3GAsIGMuq6Z9un0J1Qzbwb5oydmsl0iBm6T5xqDMvuI4G9teBCUtEq9py1mukUa73I0f/HXdOXCm03iyF glenn@glenn-laptop

Nu kan je git repo's clonen zonder dat je een wachtwoord moet ingeven.

## Remote veranderen

Wanneer een nieuwe Vagrantbox aanmaken, staat de remote naar de orginele `edx-platform`. Dat willen we veranderen naar onze fork.

Eerst verwijderen we de remote:

    git remote rm origin

Dan voegen we onze eigen remote toe:

    git remote add origin git@github.com:HowestX/HowestX.git

Uiteindelijk kunnen we nog de upstream remote toevoegen als `upstream`:

    git remote add upstream https://github.com/edx/edx-platform.git

Pullen doen we met `git pull release origin`. Enkel en alleen objecten ophalen kan met `git fetch`. Om een remote in te stellen voor een branch kan je bijvoorbeeld `git branch --set-upstream-to=origin/release release` gebruiken voor een branch die `release` noemt.

# NVM installeren

Je gebruikt best NVM om node.js te managen. Zo kan je met meerdere node.js versies tegelijk werken.

NVM installeren is erg gemakkelijk:

    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.25.3/install.sh | bas

Daarna installeren we een versie van node.js en gebruiken we deze:

    nvm install 0.12.1
    nvm use 0.12.1

Nu we node.js en npm hebben, kunnen we globale pakketten installeren. Zo hebben we bijvoorbeeld Grunt en Bower nodig:

    npm install -g grunt-cli bower

# LDAP

## Beschrijving protocol

Light-weight Directory Access Protocol (LDAP) is een protocol dat ons toelaat om directoryservices aan te spreken. Directoryservices slaan informatie hiërarchisch op. Het meest voorkomende gebruik van LDAP is het authenticeren van gebruikers.
LDAP maakt gebruik van poort 389 (verwar niet met RDP, dat op poort 3389 werkt). LDAPS, LDAPS over SSL, werkt standaard op poort 636. LDAP maakt gebruik van zowel TCP als UDP.

### Bronnen

http://en.wikipedia.org/wiki/Directory_service 
http://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol 
https://msdn.microsoft.com/en-us/library/aa367008(v=vs.85).aspx 

## LDAP testen op \*NIX

Het programma ldapsearch (veelal te vinden in een package die ldap-utils noemt) kan gebruikt worden om LDAP te testen.
Hier testen we bijvoorbeeld het inloggen (binden in LDAP-taal):
ldapsearch -D "glenn@howestedx.local" -W -H ldap://howest-test-ad.cloudapp.net -b "dc=howestedx,dc=local"

### Bronnen
http://linux.die.net/man/1/ldapsearch 

## LDAP met Python

Zorg dat je eerst binnen een virtualenv werkt.

Installeer `python-ldap` binnen de virtualenv.

    sudo apt-get install libldap2-dev libsasl2-dev
    pip install python-ldap

Start python door `python` uit te voeren.

    ldap importeren
    import ldap

De connectie aanmaken met een LDAP-server die we hebben opgezet op Azure (dit werkt niet binnen het schoolnetwerk, daar kan je `ldap://hogeschool-wvl.be` gebruiken):

    conn = ldap.initialize('ldap://howest-test-ad.cloudapp.net')

Parameters instellen:

    conn.protocol_version = 3
    conn.set_option(ldap.OPT_REFERRALS, 0)

Vragen aan de LDAP-server of er een gebruiker is met als naam `glenn@howestedx.local` en wachtwoord `123`

    conn.simple_bind_s('glenn@howestedx.local', '123')

## LDAP met Django en edX integreren

Zie verder, onder Django, voor meer informatie over het integreren van LDAP met Django en edX.

# Python

## virtualenv

Paketten globaal installeren kan tot problemen leiden: een applicatie gebruikt versie X van een pakket, terwijl een andere applicatie versie Y nodig heeft. Daarvoor heeft de Pythongemeenschap een oplossing: `virtualenv`. Hiermee kan je virtuele omgevingen maken, met Pythonversies en pakketversies die los staan van elkaar.

Allereerst moet je zeker zijn dan pip (de python package manager) geïnstalleerd is. Om deze te installeren, download get-pip.py [hier](https://bootstrap.pypa.io/get-pip.py). Voer dit python bestand uit door `python get-pip.py` uit te voeren in je terminal.

Nu kan je `virtualenv` installeren met `pip`:

    pip install virtualenv

Een nieuwe virtuele omgeving kan je aanmaken met `virtualenv naam`. Dit maakt een nieuwe map genoemd `naam`.

Een virtual enviroment moet je eerst nog activeren. Dit doe je zo:

    . virtualenv_naam/bin/activate

Nu werk je binnen een virtualenv. Paketten die je installeert, zullen enkel de virtualenv beschikbaar zijn.

Wanneer je klaar bent, kan je de virtualenv deactiveren door `deactivate` uit te voeren. Je terminal zal naar de oorspronkelijke staat hersteld worden.

# Django

## LDAP integreren in een test-app

Nu willen we natuurlijk ook kunnen inloggen op Django over LDAP.

### Gebruik maken van zoeken

De traditionele manier werkt als volgt:
* Er is een vaste gebruiker (met vaste configuratie) die voor elke request bindt met de LDAP-server
* Na het binden wordt er gezocht op de LDAP-server naar wat de gebruiker heeft ingegeven. Meestal wordt de scope beperkt (tot een organisational unit binnen een domain controller)
* Wanneer er een resultaat gevonden wordt, wordt er geprobeerd in te loggen met het gevonden resultaat en het de door de gebruiker ingevoerd paswoord

We moeten een filter schrijven om de juiste objecten te selecteren. De input is wat de gebruiker ingeeft als username. Na wat zoeken in de lijst met LDAP user attributes vonden we userPrincipalName, met als beschrijving “user logon name”, exact wat we nodig hebben dus. De filter schrijven we zo:

    AUTH_LDAP_USER_SEARCH = LDAPSearch("cn=users,dc=howestedx,dc=local", ldap.SCOPE_SUBTREE, "(userPrincipalName=%(user)s)")

### Anonymous binding

In plaats van een gebruikersnaam en wachtwoord op te geven voor de eerste bind (waarmee de search wordt uitgevoerd), kan je deze ook leeg laten. Dan wordt een anonymous bind uitgevoerd. Op veel servers kan je dan echter geen zoekacties uitvoeren. Zo ook op de Howestservers.

### Direct bind

Dit is de gemakkelijkste manier, maar iets moeilijker te implementeren (omdat je zelf moet opgeven hoe usernames naar AD objecten moeten worden geconverteerd). Het werkt als volgt: wanneer de gebruikersnaam en paswoord worden ingevuld, wordt er gepoogd om rechtstreeks te binden met de LDAP-server. Dit heeft wel een nadeel: qua auditing is het lastiger. Wanneer je bindt met een dedicated account, waarmee je dan searcht, ziet dat er goed en overzichtelijk uit in je logs. Met een direct bind niet.

### Keuze type binding

Uiteindelijk hebben we gekozen voor direct binds. Dit omdat het ons het meest logische lijkt en we geen dedicated search account hebben op de Howest AD server. Ook binnen Howest wordt simple bind gebruikt.

### LDAPS (LDAP over SSL)

Volgens de documentatie is het enablen van LDAPS zo gemakkelijk als `AUTH_LDAP_START_TLS = True` toevoegen in de configuratie. Dat is echter niet het geval in onze ervaring: in Wireshark zagen we nog altijd normale LDAP-queries, zonder SSL.

### Django LDAP configuratie

Dit is de configuratie die we uiteindelijk hebben gebruikt:

    AUTH_LDAP_START_TLS = True
    
    AUTH_LDAP_SERVER_URI = "ldap://hogeschool-wvl.be"
    
    AUTH_LDAP_BIND_AS_AUTHENTICATING_USER = True
    
    AUTH_LDAP_USER_DN_TEMPLATE = '%(user)s' # Users log in as name.lastname@student.howest.be, it works
    
    AUTH_LDAP_CONNECTION_OPTIONS = {
        ldap.OPT_REFERRALS: 0,
        ldap.OPT_PROTOCOL_VERSION: 3
    }
    
    AUTHENTICATION_BACKENDS = (
        'django_auth_ldap.backend.LDAPBackend',
        'django.contrib.auth.backends.ModelBackend',
    )

### Bronnen

[django-auth-ldap, een LDAP authentication backend voor Django](https://pythonhosted.org/django-auth-ldap/install.html)
[django-python3-ldap, zoals django-auth-ldap, maar voor python3](https://github.com/etianen/django-python3-ldap)
[User authentication in Django](https://docs.djangoproject.com/en/1.8/topics/auth/)
[“Using django-auth-ldap with Active Directory”](http://www.spannerbracket.com/wordpress/?p=40)
[LDAP user attributes](http://www.kouti.com/tables/userattributes.htm)
[LDAP authentication setup help](https://groups.google.com/forum/#!msg/django-auth-ldap/GVoa82bLfAE/mlDjrhcOuhMJ)
[Mapping user attributes (django-auth-ldap)](https://pythonhosted.org/django-auth-ldap/users.html#user-attributes)

## LDAP met edX

Nu we LDAP met Django werkend hebben gekregen, lijkt het een kleine stap om dit over te zetten naar edX, maar dat is niet helemaal waar. edX is heel complex qua configuratie. Er zijn een hele boel verschillende configuratiebestanden, in een hele boel verschillende mappen.

### Toevoegen van pakketten

#### aptpakketten

Eerst moeten we enkele pakketten installeren met `apt`. Deze zijn nodig omdat de Python-pakketten deze gebruiken. Deze pakketten hebben we eerder al toegoevoegd aan onze developmentmachine, nu gaan we deze ook toevoegen aan onze Vagrantmachine. Merk op dat dit enkel voor testing is, voor deployment wil je deze natuurlijk toevoegen aan de provisioneringsscripts.

Voer dit uit als de user `vagrant`:

    sudo apt-get install libldap2-dev libsasl2-dev

#### Pythonpakketten toevoegen

Nu voegen we de Pythonpakketten toe binnen de Vagrantmachine. Daarvoor moeten we eerst de virtual environment activeren. De virtual environment die gebruikt wordt, staat in `/edx/app/edxapp/venvs/edxapp/`. Activeren doe je dus met `. /edx/app/edxapp/venvs/edxapp/bin/activate`.

Nu kan je het pakket dat we nodig hebben installeren met:

    pip install django-auth-ldap

Dit pakket voorziet een Django authentication back-end voor LDAP.

#### LDAP backend configureren

In deze stap configureren we de LDAP backend. Dit is grotendeels hetzelfde als onze voorbeeldapp, maar de configuratiestructuur is hier veel verwarrender. In plaats van een standaard `settings.py`, hebben we een hele hoop verschillende configuratiebestanden. Het bestand dat we willen bewerken, is `lms/envs/common.py`. Dit is het basisbestand voor configuratie van het LMS.

We maken de volgende wijzigingen:

* Bovenaan het bestand importeren we de library: `import ldap`
* Daarna beginnen we met de configuratie. Deze mag eender waar in het bestand voorkomen, maar op het proprop te houden, definieren wij deze boven de authentication backends. Dit is wat we toevoegen:

    # LDAP configuration
    
    AUTH_LDAP_SERVER_URI = "ldap://hogeschool-wvl.be"
    
    AUTH_LDAP_BIND_AS_AUTHENTICATING_USER = True
    
    AUTH_LDAP_USER_DN_TEMPLATE = '%(user)s' # Users log in as name.lastname@student.howest.be
    
    AUTH_LDAP_CONNECTION_OPTIONS = {
        ldap.OPT_REFERRALS: 0,
        ldap.OPT_PROTOCOL_VERSION: 3
    }

* Toevoegen van de LDAP authentication backend: nu moeten we nog de LDAP authentication backend toevoegen aan de aanwezige backends. We voegen hem bovenaan toe: eerst wordt dus altijd naar de LDAP-server gegaan om de authenticatie te proberen. Na het toevoegen ziet het er zo uit:

    AUTHENTICATION_BACKENDS = (
        'django_auth_ldap.backend.LDAPBackend', # This isn't rate limited at the moment
        'ratelimitbackend.backends.RateLimitModelBackend',
    )

Momenteel is de LDAP backend niet rate limited. Dit is triviaal, en we komen er later op terug.

#### Aanpassen van authenticatie in edX

Momenteel zal edX proberen om van het emailadres, waarmee we inloggen, een gebruikersnaam te maken. Dit wordt gedaan door rare regels, de uitkomst is dat we complete foute informatie binnenkrijgen in de LDAP backend. Dat moeten we verhelpen door edX niet de gebruikersnaam, maar het wachtwoord te laten doorsturen.

In `common/djangoapps/student/views.py` maken we de volgende wijziging (lijn 1018):

    -            user = authenticate(username=username, password=password, request=request)
    +            user = authenticate(username=user.email, password=password, request=request)

Hiermee zijn we er nog niet. Momenteel zal een geregistreerde gebruiker kunnen authenticeren over LDAP, maar nieuwe gebruikers kunnen nog niet worden opgehaald van de directory.

#### Mappen van LDAP-attributen naar een Django-user

    AUTH_LDAP_USER_ATTR_MAP = {"first_name": "givenName", "last_name": "sn"}

Zie ook https://pythonhosted.org/django-auth-ldap/users.html

Merk op dat het mappen van deze attributen bij elke login zal gebeuren. Dit is mogelijks niet gewenst (door overhead), en kan worden afgezet met:

    AUTH_LDAP_ALWAYS_UPDATE_USER = False

Wij laten deze optie nu aan staan. Zo zijn we er zeker van dat we altijd correct informatie hebben. Pas wanneer performance een probleem wordt, kan overwogen worden om dit uit te zetten.

#### Rate limiting implementeren

http://django-ratelimit-backend.readthedocs.org/en/latest/usage.html

## Users in Django

Zie [hier](https://docs.djangoproject.com/en/1.8/topics/auth/default/#user-objects)

## Authentication backends

Django is heel flexibel wanneer het aankomt op authenticatie. Het laat je toe om zelf authentication backends te schrijven waardoor een gebruiker kan worden ge-authenticeerd.

De backends worden gedefinieerd in het configuratiebestand van je Django app. Hier is een voorbeeld uit een testapp die we schreven:

    AUTHENTICATION_BACKENDS = (
        'django_auth_ldap.backend.LDAPBackend',
        'django.contrib.auth.backends.ModelBackend',
    )

Hier zien we twee backends. De standaard backend is `ModelBackend`. Die slaat gebruikers en wachtwoorden op in een database. `LDAPBackend` probeert gebruikers te authenticeren bij een LDAP-server.

De backends worden van boven naar beneden afgelopen. In het voorbeeld wordt dus eerst de `LDAPBackend` geprobeerd. Lukt dat niet, wordt de `ModelBackend` geprobeerd.

# edX

## Databases

### MongoDB

### MySQL en Sqlite

Hoewel de documentatie van edX aangeeft dat in een ontwikkelomgeving Sqlite gebruikt wordt, lijkt dit niet meer het geval te zijn. We ondervonden dat edX gebruik maakt van MySQL binnen Vagrant. Je kan een shell openen met `mysql -u root`

## edX named releases

Omdat edX een platform is dat heel snel evolueert, voorziet men nu “named releases”. Dit zijn LTS-versies, een beetje zoals Ubuntu ook werkt. Tussen twee named releases zal een upgrade path voorzien worden. Het is logisch dat we verderwerken op de laatste named release, aangezien die stabiel is, er makkelijk support voor kan worden gevonden, de onderhoudbaarheid vergroot (niet elke dag gaan upgraden) en edX een upgrade path voorziet tussen twee named releases.

De eerste named release was Aspen. Momenteel zitten we aan de tweede named release, Birch. Deze is gereleaset op 24 februari 2015, tamelijk recent dus. De volgende named release zal Cypress gaan noemen.

Wij zullen dus gaan werken op de named release Birch. Graag hadden we geweten wat de plannen zijn voor Cypress, maar daar hebben we geen informatie over teruggevonden.

Een voorbeeld van een upgrade van Aspen naar Birch:

    ./migrate.sh -c devstack

Een enkele regel code, die een script runt, dat alles automatisch migreert. Hier zijn we erg tevreden over, en het versterkt ons vertrouwen in edX als platform.

### Ontwikkelen op een named release

Ontwikkelen op een named release is gemakkelijk: gewoon een environment variable zetten voordat je de Vagrantmachine start:

    export OPENEDX_RELEASE="named-release/birch"
    vagrant up

Opgepast: dit moet je doen voordat je de machine voor de eerste keer start (heb je al een gestart, destroy hem dan). De volledige stappen zijn dus:

    curl -L https://raw.githubusercontent.com/edx/configuration/master/vagrant/release/devstack/Vagrantfile > Vagrantfile
    vagrant plugin install vagrant-vbguest
    export OPENEDX_RELEASE="named-release/birch"
    vagrant up

### Meer en bronnen

https://openedx.atlassian.net/wiki/display/DOC/Named+Releases
http://edx-installing-configuring-and-running.readthedocs.org/en/latest/birch.html
https://github.com/edx/configuration/wiki/Named-Releases

## Architectuur

### Thema's

Het thema in edX kan gemakkelijk aangepast worden zonder het platform zelf aan te passen. Thema’s kunnen in de map themes geplaatst worden, en dan kan je via de configuratie opgeven welk thema je wil gebruiken.

#### Een nieuw thema gebruiken

Clone via git het nieuwe theme in de themes folder van je edX project.

    git clone git@github.com:HowestX/howestx-theme.git

Ga in je virtuele machine, en switch naar de `edxapp` user:

    vagrant ssh
    sudo su edxapp

Bewerk het configuratiebestand voor het lms:

    nano ../lms.env.json

Daar moet je enkele wijzigingen doen:

* `USE_CUSTOM_THEME` (in `FEATURES`) veranderen van `false` naar `true`
* `THEME_NAME` instellen op de theme die je wenst te gaan gebruiken, bij ons is dit `howestx-theme`

#### Stanford edX theme

We hebben het Stanford theme in overweging genomen, maar dat was geen goede fit voor onze noden. De broncode was verwarrend en onoverzichtelijk, daarbovenop was het ook niet responsief.
https://github.com/edx/edx-platform/wiki/Stanford-Theming 
https://github.com/Stanford-Online/edx-theme 

#### Keuze theme

Uiteindelijk hebben we gekozen voor het IONISx. Dit is een Bootstrap-gebaseerd thema voor edX. Het enige moeilijke met dit thema is dat het LESS gebruikt, waar edX SASS gebruikt. Omdat de ontwikkelaars van IONISx geen extra asset-pipeline wilden toevoegen aan edX, stellen ze volgende workflow voor:

* Installeren van het thema op de normale manier
* Installeren van node.js en dependencies voor het builden van dit theme (zie ook “NVM installeren”)
* `npm install` en `bower install` uitvoeren in de folder waarin je theme staat, dit installeert de dependencies en Bootstrap
* Grunt uitvoeren: Grunt kan je starten met `grunt`: dit start grunt in “watch”-modus, telkens een file gewijzigd wordt, wordt het gecompileerd. Doordat er een verbinding is met Vagrant (door gemounte NFS-share), kan je gewoon de pagina refreshen en zie je je veranderingen
* Omdat er gekozen is om geen aparte node.js pipeline toe te voegen aan edx-platform, is het de bedoeling dat css-bestanden die gecompileerd zijn van LESS-bestanden ook in de gitrepository worden ingecheckt. Dit voelt erg onproper aan, maar het is een trade-off, anders moeten we de volledige build pipline van edX aanpassen

De GitHub-pagina van IONISx, waar we ons op baseren, is [hier](https://github.com/IONISx/edx-theme) te vinden. 

#### Thema meegeven in provisioneringsscripts

Zelf hebben we nog geen ervaring met het deployen van edX met Ansible, maar hier is documentatie over hoe je een thema instelt wanneer je dat doet:
https://github.com/edx/edx-platform/wiki/Custom-Theming

