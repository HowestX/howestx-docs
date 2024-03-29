# Informatie-architectuur

## Front-facing

![Image on front-facing](images/Front-facing.png "Afbeelding front-facing")

### Uitleg

Het front-facing platform is de plaats waar studenten op terechtkomen. Dit is het Learning Management System (LMS) zelf. De studenten loggen hier in met hun Howest-logingegevens. Studenten die echter niet studeren aan de Howest hebben ook de mogelijkheid zich te registreren. Ingelogde studenten kunnen hier courses bekijken, oefeningen maken... De voortgang van studenten worden automatisch bijgehouden.

## Back-facing

![Image on back-facing](images/Back-facing.png "Afbeelding back-facing")

## Uitleg

Het back-facing platform is de plaats waar courses aangemaakt worden. Hier kunnen leerkrachten lessen aanmaken. Het back-facing platform wordt onder edX de ‘Studio’ genoemd. Leerkrachten kunnen hier inloggen met hun standaard Howestlogin.

Nadat leerkrachten ingelogd zijn kunnen ze lessen aanmaken,bewerken, ...

# Technische analyse

Aangezien we met edX als MOOC platform werken, kunnen we heel wat bestaande dingen behouden. De dingen die we zullen behouden worden hieronder opgesomd:

* Eerst en vooral behouden we de taal waarin edX geschreven is, Python.
* Aangezien studenten kunnen inloggen wordt er bij edX zelf al reeds gebruik gemaakt van een loginportaal. We opteren dan ook om de reeds bestaande infrastructuur te gebruiken.
* Bij edX is het reeds mogelijk om oefeningen aan te maken en te wijzigen. Deze functionaliteit wordt uiteraard behouden en gebruikt.
* Ten laatste zit er in edX een functie die de progress van een gebruiker en meer informatie hieromtrent opslaat. 

Om het platform naar onze hand te zetten en functioneler te maken voor het Howestteam zullen er ook een aantal dingen moeten worden toegevoegd. Deze kunt u terugvinden in onderstaande lijst:

* Een LDAP server moet toegevoegd worden als extra authentication store om het inloggen met Howestgegevens mogelijk te maken.
* Aangezien het bestaande platform niet responsief is, zullen we er ook voor zorgen dat ons platform wel responsief is en zo makkelijk te gebruiken is voor de eindgebruiker. Hiervoor bouwen we verder op het IONISx theme. 
* In plaats van SASS zullen we LESS gebruiken voor de CSS opmaak, omdat dit standaard wordt gebruikt bij het IONISx theme.
* Het volledige thema voor de front-facing site moet aangepast worden.

# Functionele analyse

Het MOOC platform is toegankelijk voor zowel studenten als voor leerkrachten al wordt er voor beiden een ander deel van het platform benuttigd. 

Leerkrachten hebben de mogelijkheid zich in te loggen in de edX Studio. Hier is het mogelijk om lessen aan te maken.

Studenten daarentegen krijgen enkel toegang tot het LMS, een ander deel van het platform. Het is van groot belang dat dit deel van het platform er goed uitziet en daarbij ook nog eens responsive is zodat het platform op een groot deel toestellen een goed uitzicht heeft. Deze kant van het platform moet zeker en vast over de Howestbranding beschikken.

De studenten hun progress wordt ook bijgehouden per vak en per hoofdstuk. De studenten kunnen hun progress zien en vergelijken met anderen studenten.

# Basis voor start van productie

De voorbije week werden er heel wat dingen klaargemaakt om zo efficiënt mogelijk aan de productie te kunnen starten. Zo werd de vagrant server klaargemaakt voor gebruik en werkt deze ondertussen. Ten tweede werden wireframes ontwikkeld voor een groot deel van de mogelijk schermen die zowel studenten (edX LMS)  als docenten (edX Studio) kunnen zien. Als laatste werd er al eens gekeken naar de Howest branding en kleuren die nodig zullen zijn bij de front-end. Verder is een basis aangelegd voor de implementatie van een nieuw responsief theme op basis van Bootstrap en Less.

# Sitemap

![Image on sitemap](images/Sitemap.png "Image sitemap")

# Design

Om de effectieve plaats en kleuren van elementen te bepalen is het noodzakelijk om te kunnen vertrekken vanaf een design. Het design dat wij gebruiken is te zien in onderstaande afbeeldingen. Alle kleuren die gebruikt zijn, zijn deze van het kleurpalet van Howest. 

Op de eerste afbeelding is de homepage te zien. Hierop is bovenaan de header terug te vinden, deze bevat het logo, een registerlink en knoppen ( 1 om in te loggen en 1 om te navigeren naar de cursussen). Onder de header is er dan een zoekbalk terug te vinden zodat de gebruiker makkelijk een cursus kan opzoeken. Onderaan is er dan een footer terug te vinden met enkele links en de naam van de instelling. 

Tussen de header en de footer is er een ‘witgedeelte’ terug te vinden. Dit gedeelte bevat de featured vakken. Deze vakken zijn momenteel deze van NMCT, maar kunnen uiteraard aangepast worden. De kleuren die bij deze vakken gebruikt worden, zijn de kleuren van de vakken zelf, zodat dit voor de gebruiker visueel zeer duidelijk is. Wanneer er over de vakken wordt gehoverd, wordt er een kleine border zichtbaar in het kleur van het vak.

![Image design homepage](images/HomepageDesign.png "Image design homepage")

Volgende afbeelding is deze van het dashboard waar de gebruiker op terecht komt na het inloggen. De kleuren boven de naam van het vak tonen onder welk traject het vak valt. Onder de ‘vaknaam’ wordt de progress van de les getoond. De grijze achtergrond kan eventueel vervangen worden door een foto die duidelijker toont over welk vak het gaat.

Vakken worden gesorteerd op meest recente gebruik. Het vak dat het meest recentst bekeken is staat linksboven. De registerknop verdwijnt ook aangezien dat niet meer nodig is als de gebruiker is ingelogd. De loginknop wordt hier vervangen door de username met een openklapmenuutje. 

![Image design courses](images/CoursesDesign.png "Image design courses")

Op onderstaande afbeelding worden de courses voorgesteld. Dit scherm lijkt qua layout heel sterk op deze van het dashboard met dat verschil dat er bovenaan nog een extra navigatie aanwezig is. Zo navigeert de gebruiker tussen cursussen. Daarnaast is navigatie ook mogelijk aan de hand van de vakken waartoe cursussen behoren.

![Image design navigatie courses](images/Courses.png "Image design navigatie courses")

# Ontwikkeling front-end

In een vroeger stadium werd het kleurenpalet van Howest reeds onderzocht. Deze kleuren zijn ondertussen geïmplementeerd in het theme. Zo kreeg de header en de footer de juiste kleur, namelijk de blauwe kleur van Howest en worden titels roze gekleurd.

Ondertussen is al heel wat gewerkt aan de front-end. De header en de footer werden al onderhanden genomen. Er kan intussen besloten worden dat deze klaar zijn. In de header staat linksboven het howestX logo, dit is gebaseerd op het alom bekende witte Howest logo.  Verder bevat de header nog een knop ‘Courses’. Deze navigeert uiteraard naar een overzicht van de courses. Helemaal rechts is er ook nog een ‘Sign in’ knop terug te vinden die de gebruiker doorverwijst naar de loginpagina. 

De loginpagina is ondertussen ook afgewerkt. Zowel de header en footer als de logintab zijn te zien op onderstaande afbeelding. Hier is te zien dat zowel de titel als de link een howestroze kleur heeft. De knop is dan weer het intussen bekende Howestblauw.

![Image logintab](images/Logintab.png "Image logintab")

Verder werd er ook hard gewerkt aan de homepage. Deze is intussen volledig in orde gebracht. De homepage bevat zoals te zien is op onderstaande afbeelding een header en footer. Net onder de header is een zoekvenster terug te vinden waar het mogelijk is om een een course te zoeken. Onder dit zoekvenster zijn er 5 elementen terug te vinden. Deze zijn nu specifiek ontworpen op basis van de NMCT-kleuren en vakken maar dit kan natuurlijk aangepast worden aan bijvoorbeeld deze van Devine of een andere howestrichting. De lessen die er nu opstaan zijn momenteel nog demo courses, deze wordt dan later nog aangepast.

[Image courses ](images/CoursesOntwerp.png "Image courses")

# Work Breakdown

## Week 2

![Image WBS week 2](images/WBS week 2.png "Image WBS week 2")
![Image legende](images/Legende.png	"Image legende")

## Week 3

![Image WBS week 3](images/WBS week 3.png "Image WBS week 3")
![Image legende](images/Legende.png	"Image legende")

## Week 4

Zoals u op onderstaande figuur kunt zien, werd heel wat afgewerkt in week 4. Enkele dingen moeten wel in acht genomen worden.

Zo is de cursus online geplaatst maar zijn er geen extra elementen aangepast om de cursus printable te maken. We hebben hier geen extra tijd meer ingestoken omdat er achteraf gezien niet zo'n grote vraag aanwezig is voor het afdrukken van de cursus. Dit komt omdat er heel wat filmpjes aanwezig zullen zijn.

De progress van leerlingen kan ook bijgehouden worden maar er kan bij ons systeem nog niet gewerkt worden met certificaten. het opzetten van certificaten zorgde voor veel problemen. In de documentatie is wel volledig terug te vinden hoe dit opgezet zou moeten worden.

![Image WBS week 3](images/WBSWeek4.png "Image WBS week 3")

# Uitendelijke design

Aan het design dat in de vorige weken werd gemaakt werd tijdens week 3 en 4 toch nog heel wat dingen aangepast. We zijn met ons definitief ontwerp ook eventjes naar een professionele designer, Angelo Fallein, gegaan voor feedback. Op onderstaande afbeeldingen is te zien hoe het design er nu werkelijk uitziet

![Image homepage definitief](images/HomepageDefenitieve.png "Image homepage definitief")

![Image Course definitief](images/CourseDefinitieve.png "Image course definitief")

![Image login definitief](images/Logindefinitief.png "Image login definitief")

![Image overzicht course](images/Overzichtcourse.png "Image overzicht coursef")


# Problemen

We zijn tijdens het werken heel wat problemen tegengekomen. In dit hoofdstuk worden de belangrijkste problemen aangehaald.

## Algemeen

* Vagrant blijft vaak lastig doen. Het is dan ook soms nodig een ‘vagrant destroy’ uit te voeren. Dit neemt heel wat kostbare tijd in beslag daar de hele Vagrant VM opnieuw geïnstalleerd en klaargezet moet worden.
* Windows zorgde ook voor veel problemen, dit door het feit dat onze Vagrant VM en Windows niet samengaan. De VM maakt gebruik van symlinks die niet worden herkend door Windows. Dit zorgde ervoor dat we hebben moeten overschakelen naar Linux, wat alweer tijd in beslag nam.
* Wanneer er op het platform huiswerk of een examen aangemaakt wil worden, kan dit niet door middel van bijvoorbeeld een vinkje aan te zetten. Er moet altijd ‘Homework:’ of “Final exam: ” in de titel meegeven worden.


## Front-end

* Doordat sommige CSS klassen 3 keer overschreven worden is het soms moeilijk om te weten waar je de styling precies moet aanpassen. Het is een struikelblok, maar eenmaal je de structuur van het theme door hebt, loopt het aanpassen al wat vlotter.
* Als we een html-file toevoegen of aanpassen moeten we lang wachten tot deze te zien is in de browser.

## Back-end

* Het implementeren van LDAP is een zeer complexe en problematische opgave. Door de complexiteit van edX was het dan ook zeer lastig om de correcte files te vinden. Het feit dat edX veel settingfiles inlaadt en overschrijft helpt niet. Eens we de correcte files gevonden hadden en LDAP erin verwerkt hadden bleek het ook nog eens lastig te zijn om de user properties correct te binden in Django. Uiteindelijk is LDAP werkend geraakt mits een kleine beperking omtrent de username die gebruikt moet worden.
* Het opzetten van een fullstack installatie in de cloud is ook een probleempunt. Dit doordat de installatie telkens crasht bij het onderdeel ‘xqwatcher’.
* Een ander probleem dat we tegenkwamen was het feit de certificaten niet werkten. De edX-documentatie was niet up to date, en de beschreven stappen werkten niet.

