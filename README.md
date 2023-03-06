# h12-Vianselvitys

!HUOM! A JA B KOHDISSA EI KUVIA, KOSKA TEHTYÄNI TEHTÄVÄT UNOHDIN COMMITTAA VÄLISSÄ A) JA B) RATKAISUT JA SULJN SELAIMEN, LOPPUTULOS ON NYT KUVATON DOKUMENTOINTI.
6.3.2023

## a) klo 9.15

Tein itselleni kirjoitusvirheet settings.py tiedostoon:

Kirjoitin DEBUG = True kohdalle DEBUG = Tru

Kun yritin laittaa ./manage.py makemigrations, tulosti komento errorin joka havaitsi virheen settings.py:n tämän rivin kohdalla "Tru is not defined"
Tämän lisäksi koska ohjelma ei osannut lukea tätä "Tru" arvoa, tulosti se 18 eri riviä virheitä __init__.py tiedostossa.
Niihin en koskenut, koska tiesin oikean ongelman.
Korjattuani virheen, kaikki toimi mainiosti.

## b) klo 9.41

Siirsin projektitiedostoni "projekti12" työpöydälle. Tämän jälkeen toteutin komennot:

    ./manage.py makemigrations
    ./manage.py migrate
    sudo systemctl restart apache2
    /sbin/apache2ctl configtest
    
Avasin selaimen ja sain tulokseksi Error 403 - Forbidden sekä localhost-sivulla että /admin päätteellä.
    
Erroreita en täältä löytänyt, mutta komennolla: 

    sudo tail -F /var/log/apache2/access.log /var/log/apache2/error.log

havaitsin yhdistäessä virheen, joka kertoi että kyseistä projektitiedostoa ei ole olemassa.

## c) klo 10.57

kirjoitin publicwsgi/ -hakemistossani komennot:

    chmod ugo-rwx seikkuco/
    chmod u+rx seikkuco/
    
Jonka jälkeen toteutin configtestin sekä apache2 status komennot:

    /sbin/apache2ctl configtest
    sudo systemctl status apache2
    
Kumpikaan ei tuottanut mitään erroreita.

    tail -F /var/log/apache2/access.log

Tässä huomasin, että sain vastaukseksi: Permission denied.
Toisin sanoen saatiin väärät oikeudet aikaiseksi. 

![kuva](https://user-images.githubusercontent.com/105205141/223064562-3247d7a2-dfe4-47e4-b90d-da9d09bc3401.png)

Kävin palauttamaan oikeudet käyttäjille komennolla:

    chmod ugo+rwx seikkuco/
    
Tämä tarkoittaa että kaikille käyttäjille lisätään read- ,write- ja execute-oikeudet. (UGO = Users, Groups, Others)

## d) klo 11.13

Avasin conf-tiedostoni komennolla:

    sudoedit /etc/apache2/sites-available/seikkuco.conf
    
Teen virheen, jonka kohtasin aiemmin tehdessäni tätä conf-tiedostoa vahingossa. Laitan threads=5 jälkeen rivinvaihdon välilyönnin sijasta.

![kuva](https://user-images.githubusercontent.com/105205141/223067247-e5f5ea3c-3b11-4f91-b822-f7cffe8debd1.png)






