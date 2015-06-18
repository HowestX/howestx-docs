# Informatie-architectuur

## Front-facing

![Image on front-facing](images/Front-facing.png "Image front-facing")

### Uitleg
Het front-facing platform is de plaats waar student op terecht komen. Het is dus het learning management system (LMS) zelf. De studenten loggen hier in met hun Howestlogingevens. Studenten die echter niet studeren aan de Howest hebben ook de mogelijkheid zich te registreren. Ingelogde studenten kunnen hier courses bekijken, oefeningen maken…. De progress van studenten worden automatisch bijgehouden.

## Back-facing
![Image on back-facing](images/Back-facing.png "Image back-facing")

## Uitleg
Het back-facing platform is de plaats waar courses aangemaakt worden. Hier kunnen leerkrachten lessen aanmaken. Het back-facing platform wordt onder edX de ‘Studio’ genoemd. Leerkrachten kunnen hier inloggen met hun standaard howestlogin. 


Nadat leerkrachten ingelogd zijn kunnen ze lessen aanmaken,bewerken, ...

# Technische analyse

Aangezien we met edX als MOOC platform werken, kunnen we heel wat bestaande dingen behouden. De dingen die we zullen behouden worden hieronder opgesomd: 
*	Eerst en vooral behouden we de taal waarin edX geschreven is, Python.
*	Aangezien studenten kunnen inloggen wordt er bij edX zelf al reeds gebruik gemaakt van een login portaal. We opteren dan ook om de reeds bestaande infrastructuur te gebruiken.
*	Bij edX is het reeds mogelijk om oefeningen aan te maken en te wijzigen. Deze wordt uiteraard behouden en gebruikt.
*	Ten laatste zit er in edX ook al een functie die de progress van een gebruiker en meer informatie hieromtrent opslaat. 


Om het platform naar onze hand te zetten en zo functioneler te maken voor het Howest team zullen er ook een aantal dingen moet toegevoegd worden. Deze kunt u terugvinden in onderstaande lijst:
*	Een LDAP server moet toegevoegd worden als extra authentication store.
*	Aangezien het bestaande platform niet responsief is, zullen we er ook voor zorgen dat het ons platform wel responsief is en zo makkelijk te gebruiken is voor de eindgebruiker. Hiervoor bouwen we verder op het ionisx theme. 
*	In plaats van SASS zullen we LESS gebruiken voor de CSS opmaak, we gebruiken  LESS omdat dit standaard wordt gebruikt bij ionisx theme.
*	Het volledige thema voor de front-facing site moet aangepast worden.

## Technologieën

![Image on front-facing](images/Front-facingTechnologieën.png "Image front-facing")
Op bovenstaande afbeelding is de technische analuse voor de front-facing site (het LMS) terug te vinden

### Authenticatie

Authenticatie wordt voorzien zoals in standaard Django app, namelijk Django zelf. Als authentication providers gebruiken we zowel LDAP als een modelbacked provider. De LDAP-provider wordt gebruikt om verbinding te maken naar het schoolnetwerk en daar te authentiseren. Als je geen Howestaccount hebt, kan je toch nog registreren. Wanneer dit gebeurd komt je account in de MySQL store terecht.

### Lessen

Lessen worden opgeslagen in een MongoDB database. Het lijkt misschien raar dat er 2 verschillende databases gebruikt worden, maar volgens het edX-team is een document database een dichtere match om de lessen in op te slaan. We zien geen reden om hun expertise in vraag te stellen.

### Design

Het standaarddesign in edX is niet responsive, wat voor ons niet aanvaardbaar is. De dag van vandaag is een reponsive website van groot belang, er zijn immers heel wat mensen die een site met hun smartphone/tablet bezoeken. Daarom ontwikkelen we een responsive design, gebaseerd op LESS en Bootstrap.

# Functionele analyse

Het MOOC platform is zowel toegankelijk voor studenten als voor leerkrachten al wordt er voor beiden een ander deel van het platform benuttigd. 


Leerkrachten hebben de mogelijkheid zich in te loggen in de edX Studio. Hier is het mogelijk om lessen aan te maken.


Studenten daarentegen krijgen enkel toegang tot het LMS, een ander deel van het platform. Het is van groot belang dat dit deel van het platform er goed uitziet en daarbij ook nog eens responsive is zodat het platform op een groot deel toestellen een goed uitzicht heeft. Deze kant van het platform moet zeker en vast over de Howestbranding beschikken.


De studenten hun progress wordt ook bijgehouden per vak en per hoofdstuk. De studenten kunnen ook hun progress zien en zien hoe goed ze het doen in vergelijking met andere studenten.

# Basis voor start van productie

De voorbije week werden er heel wat dingen klaargemaakt om zo efficiënt mogelijk aan de productie te kunnen starten. Zo werd de vagrant server klaar gemaakt voor gebruik en werkt deze ondertussen. Ten tweede werden wireframes ontwikkeld voor een groot deel van de mogelijk schermen die zowel studenten (edX LMS)  als docenten (edX Studio) kunnen zien. Als laatste werd er al eens gekeken naar de Howest branding en kleuren die nodig zullen zijn bij de front-end. Verder is een basis aangelegd voor de implementatie van een nieuw responsief theme op basis van Bootstrap en Less.

# Sitemap

TODO

# Design

Om de effectieve plaats en kleuren van elementen te bepalen is het een noodzaak om te kunnen vertrekken vanaf een design. Het design die wij gebruiken is te zien in onderstaande afbeeldingen. Alle kleuren die gebruikt zijn, zijn deze van het kleurpalet van de Howest. 

Op de eerste afbeelding is de homepage te zien. Hierop is bovenaan de header terug te vinden, deze bevat het logo, een registerlink en een knoppen ( 1 om in te loggen en 1 om te navigeren naar de cursussen). Onder de header is er dan een zoekbalk terug te vinden zodat de gebruiker makkelijk een cursus kan opzoeken. Onderaan is er dan een footer terug te vinden met enkele links en de naam van instelling. 

Tussen de header & de footer is er een ‘witgedeelte’ terug te vinden. Dit gedeelte bevat de featured vakken. Deze vakken zijn momenteel deze van nmct, maar kunnen uiteraard aangepast worden. De kleuren die bij deze vakken gebruikt worden, zijn de kleuren van de vakken zelf, zodat dit voor de gebruiker visueel zeer duidelijk is. Wanneer er over de vakken wordt gehoverd, wordt er een kleine border zichtbaar in het kleur van het vak.

![Image design homepage](images/HomepageDesign.png "Image design homepage")

Volgende onderstaande afbeelding is deze van het dashboard waar de gebruiker op terecht komt na het inloggen. De kleuren boven de naam van het vak toont onder welk traject het vak valt. Onder de ‘vaknaam’ wordt de progress van de les getoond. De grijze achtergrond kan eventueel vervangen worden door een foto die duidelijker toont over welk vak het gaat.

Vakken worden gesorteerd op meest recente gebruik. Het vak dat het meest recentst bekeken is staat linksboven. De registerknop verdwijnt ook aangezien dat niet meer nodig is als de gebruiker is ingelogd. De loginknop wordt hier vervangen door de username met een openklapmenuutje. 

![Image design courses](images/CoursesDesign.png "Image design courses")

Op onderstaande afbeelding worden de courses voorgesteld. Dit scherm lijkt qua lay-out heel sterk op deze van het dashboard met dat verschil dat er bovenaan nog een extra navigatie aanwezig is. Zo navigeert de gebruiker tussen cursussen. Daarnaast is navigatie ook mogelijk aan de hand van de vakken waartoe cursussen behoren.

![Image design navigatie courses](images/Courses.png "Image design navigatie courses")

# Ontwikkeling front-end
In een vroeger stadium werd het kleurpalet van de Howest reeds onderzocht. Deze kleuren zijn ondertussen geïmplementeerd in het theme. Zo kreeg de header en de footer de juiste kleur , namelijk de blauwe kleur van de howest en werden titels roze gekleurd. 


Ondertussen is al heel wat gewerkt aan de front-end zijde. De header & de footer werden al onderhanden genomen. Er kan intussen besloten worden dat deze klaar zijn. In de header staat linksboven het howestX logo dit is gebaseerd op het alom bekende witte Howest logo.  Verder bevat de header nog een knop ‘Courses’ deze navigeert uiteraard naar een overzicht van de courses. Helemaal rechts is er ook nog een ‘Sign in’ knop terug te vinden die de gebruiker doorverwijst naar de loginpagina. 

De loginpagina is ondertussen ook afgewerkt. Zowel de header en footer als de logintab zijn te zien op onderstaande afbeelding. Hier is te zien dat zowel de titel als de link een howestroze kleur heeft. De knop is dan weer het intussen bekende howestblauw.

![Image logintab](images/Logintab.png "Image logintab")

Verder werd er ook hard gewerkt aan de homepage. Deze is intussen volledig in orde gebracht. De homepage bevat zoals te zien is op onderstaande afbeelding een header en footer. Net onder de header is een zoekvenster terug te vinden waar het mogelijk is om een een course te zoeken. Onder dit zoekvenster zijn er 5 elementen terug te vinden. Deze zijn nu specifiek ontworpen op basis van de NMCT kleuren en vakken maar dit kan natuurlijk aangepast worden aan bijvoorbeeld deze van Devine of een andere howestrichting. De lessen die er nu opstaan zijn momenteel nog demo courses, deze wordt dan later nog aangepast 

[Image courses ](images/CoursesOntwerp.png "Image courses")

