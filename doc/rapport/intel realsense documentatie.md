﻿**Intel realsense
Object detection** 

 1. Wat is een intel realsense?
 2. Hoe werkt object detectie?
 3. Waarom object detectie op deze manier?
 4. Voorbeeld toepassing

# Wat is een intel realsense?

De intel realsense is een RGB en een diepte camera in één. De camera zijn belangrijkste taak is het meten van de objecten in de ruimte. De camera kan namelijk op de x, y en z as meten. Als men de juiste verhoudingen ingeeft (schaal factor) (lengte horizontale as) kan men aan de hand van de lengte en breedte 1pixel = totaal pixels x-as/werkelijke lengte x-as de uiteindelijke werkelijke afstand bekomen. Door deze formule toe te passen kan men dus deze factor (lengte en breedte van 1 pixel) vermenigvuldigen met de x en y coördinaat(pixel van de camera) om de waarde in de realiteit te bekomen.

# Hoe werkt de object detectie?
Voor object detectie maak ik gebruik van de openCV library (je kan best ook de numphy library importeren). Deze library beschikt over veel mogelijkheden om foto's te bewerken en te interpreteren.

OpenCV is momenteel enkel toepasbaar in c++ en python. Ik heb persoonlijk voor python gekozen aangezien we dit ook bij raspberry pi gebruiken.

Het process van object detectie in een gecontroleerde environment (goede belichting (geen of zwakke schaduwen) en 1 bepaalde achtergrond kleur achter de objecten).

1.  Zet de camera stream op in python.

2.  Converteert de camera RGB naar GBR aangezien dat openCV met GBR kleuren interpreteert.
    
3.  Maak een mask zodanig dat de objecten wit zijn en de ondergrond zwart is. Deze kan men eventueel in een aparte window weergeven zodanig dat men de mask kan fine tunen.
Deze mask wordt bekomen door een bepaalde grenswaarde van het RGB spectrum in te stellen. Als men bijvoorbeeld een bepaalde kleur achtergrond heeft kan men deze weg krijgen door de bovenste en de onderste grenswateren in 2 RGB variabelen op te slaan en vervolgens te vergelijken met de RGB frame. In plaats van deze waardes aan de RGB stream te onttrekken maakt men met deze selectie van RGB een mask. Op deze mask zou men dan uiteindelijk de objecten in het wit moeten zien en de achtergrond in het zwart.
Als men de mask nog beter wilt maken kan men dit doen door voorafgaand een gaussiaanse vervaging toe te passen. Hierdoor gaan de randen van objecten soft worden (i.p.v. hard) en zal er dus een meer gelijkelijk gemiddelde worden gevormd. Als men alleen een zwarte / witte kleur wilt wegfilteren kan men eventueel ook de RGB nog eerst converteren naar een graag scale.

4.  Pas een contour detection toe op de mask. Hiermee zullen dus de randen van de objecten zichtbaar worden.
De contour detection gaat een lijn trekken op de rand tussen wit en zwart van de mask. Voor een goede visuele representatie kan men deze best op de RGB stream tekenen.

5.  Teken een box rond de contour.
Om de objecten in de ruimte te kunnen bepalen kan men best de contour omzetten in 4 punten. Aangezien men ander veel te veel data heeft. Deze punten worden bekomen door een rectangle rond elke contour te tekenen.

6.  Teken een gedraaide box zodanig dat hij nog de volledig contour omvat maar het kleinste totale oppervlakte bevat.
Om de positie van het object nauwkeuriger te kunnen bepalen kan men best de rectangle in de meeste gevallen een deeltje draaien. Als de rectangle namelijk zodanig draait dat hij de kleinste oppervlakte heeft maar toch nog de hele contour bevat zullen de 4 punten veel dichter bij de effectieve waarden liggen.

8.  Log de 4 punten/ het centrum van de gedraaide rectangle in de console.
    
9.  Extra: maak een filter zodanig enkel x aantal van de grootste objecten worden weergegeven. Dit zorgt ervoor dat wanneer er wat kleine ruis op het scherm is dit niet gedetecteerd wordt.
    
10.  Om ervoor te zorgen dat de robotarm niet gedetecteerd wordt als een object kan men hier nog een extra mask overtrekken. Dit doet men bijvoorbeeld door een rectangle in de gefilterde mask kleur voor dat de mask wordt gemaakt op de stream te plaatsen.
   
# Waarom object detectie op deze manier?

Ik ben begonnen met object detectie te doen met een deep learning algoritme. Bij het standaard project van de intel realsense wordt een klein deep aangeleerd netwerk meegeleverd (coffee table). Het goede aan een deep learn netwerk is dat dit kan uitgevoerd worden in een ongecontroleerde omgeving. Het programma zal namelijk zoeken naar patronen als men de pixels van de photo afgaan. Het nadeel is echter dat men voor elk object dat men wilt detecteren, men dit eerst de computer moet aanleren door een hoop fotos te implementeren (bv mens => 50 foto’s van mensen). Een bijkomend nadeel is dat zoo een algoritme enorm veel processing power (vooral grafisch) vereist aangezien hij eerst de afbeelding moet renderen en dan op elke object van zijn deep geleerd netwerk moet gaan testen( of er een overeenkomstig patroon is). Dit zorgt dus voor een niet real time toepassing.

# Voorbeeld toepassing

**Voorwoord:**
Voor het menuutje in het begin heb ik gebruik gemaakt van de tkinter library. Wanneer men op een knop drukt zal de integer windowselector veranderen van waarde en zo bepalen welk stuk van de code en welke camera weergegeven wordt. Nadat de camera stream is opgezet zal er dus steeds een loop voor elke frame worden uitgevoerd. Naar gelang welke int is ingesteld wordt er dus een ander algoritme en code op de frame toegepast.

**Deep learning detection** *(slechte optie voor dit project)*:
In de voorbeeld toepassing heb ik bij het opstarten een menuutje gemaakt (met de tkinter python library). De RGB cam geeft een scherm weer van de RGB camera en deep learning object detection. De Depth cam doet hetzelfde maar dan met de diepte camera. Overlay cam zal de overlay cam gedeeltelijk transparant maken en boven op de RGB cam leggen.

**Contour detection** *(zeer performante optie voor gecontroleerde omgeving)*:
In het menu vindt men ook een knop die enkel de mask weergeeft en uitvoert. Vervolgens is er ook een voorbeeld dat de contouren op de RGB stream tekent (door middel van de mask). En de laatste knop tekent uiteindelijk een rechthoek en een gekantelde rechthoek rond het object (en logt de x en y coördinaten in de console).
