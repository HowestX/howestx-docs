# Central Authentication Service (CAS)

LDAP is geen goede match voor edX. Waar we wel een beter gevoel bij hebben, is CAS (Central Authentication Service). CAS is een single sign-on protocol. Het voorziet een server met mogelijks verschillende backends voor authenticatie. edX voorziet er ondersteuning voor.

## Installatie op Ubuntu 14.04

### Opzet en dependencies

We zetten een CAS-server op op Azure. We kiezen een Ubuntu 14.04 LTS-machine, en installeren eerst de dependencies:

    sudo apt-get install maven2 default-jdk libssl-dev libtcnative-1

### Certificaten

Copy this: http://www.liquidweb.com/kb/how-to-install-apache-tomcat-8-on-ubuntu-14-04/

### Installatie CAS

Copy this: https://www.helloitsliam.com/2014/12/03/ubuntu-server-setup-for-cas-authentication/

#### Installatie Postgres

Postgres zal de ticketing van CAS voor zijn rekening nemen. We installeren het zo:

    sudo apt-get install postgresql

We moeten ook de certificaten installeren zodat Postgres SSL kan gebruiken. Dat doen we zo:

    sudo su
    cd /var/lib/postgresql/9.3/main/
    cp /home/thomas/new_certs/server.crt .
    cp /home/thomas/new_certs/server.key .

## edX CAS laten gebruiken

### Devstack

Op de devstack maken we enkele wijzigingen in `lms.env.json`:

    ...
    "CAS_SERVER_URL": "https://howestx-cas.cloudapp.net/cas-server-webapp-4.0.0/login",
    ...
    "FEATURES": {
        ...
        "AUTH_USE_CAS": true
    }

The `CAS_SERVER_URL` property already exists, you will have to add `AUTH_USE_CAS` yourself.

Nu moeten we nog het certificaat toevoegen aan de Vagrantserver.

### Fullstack / production stack

## Bronnen

* http://en.wikipedia.org/wiki/Central_Authentication_Service
* http://kogentadono.com/2014/10/16/installing-cas-3-5-2-on-ubuntu-12-04-part-1-tomcat-7-and-cas/
* https://www.helloitsliam.com/2014/12/03/ubuntu-server-setup-for-cas-authentication/
* http://www.lordofthejars.com/2015/01/self-signed-certificate-for-apache.html
* https://groups.google.com/forum/#!msg/edx-code/-P690wW8NXU/WXagVeNWJqMJ
* http://stackoverflow.com/questions/6412468/single-sign-on-sso-how-to-use-active-directory-as-an-authentication-method-fo
 

