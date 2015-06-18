
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

### Bronnen

* [django-auth-ldap, een LDAP authentication backend voor Django](https://pythonhosted.org/django-auth-ldap/install.html)
* [django-python3-ldap, zoals django-auth-ldap, maar voor python3](https://github.com/etianen/django-python3-ldap)
* [User authentication in Django](https://docs.djangoproject.com/en/1.8/topics/auth/)
* [“Using django-auth-ldap with Active Directory”](http://www.spannerbracket.com/wordpress/?p=40)
* [LDAP user attributes](http://www.kouti.com/tables/userattributes.htm)
* [LDAP authentication setup help](https://groups.google.com/forum/#!msg/django-auth-ldap/GVoa82bLfAE/mlDjrhcOuhMJ)
* [Mapping user attributes (django-auth-ldap)](https://pythonhosted.org/django-auth-ldap/users.html#user-attributes)

# edX

### Meer en bronnen

https://openedx.atlassian.net/wiki/display/DOC/Named+Releases
http://edx-installing-configuring-and-running.readthedocs.org/en/latest/birch.html
https://github.com/edx/configuration/wiki/Named-Releases

