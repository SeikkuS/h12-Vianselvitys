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

    sudo tail -F /var/log/apache2/access.log

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

nyt kun käynnistän configtestin tulostaa testi seuraavan:

![kuva](https://user-images.githubusercontent.com/105205141/223067595-6028457f-7983-4b42-ace7-0f02386e5ef2.png)

Nähdään että virhe löytyy riviltä 13 tiedostossa seikkuco.conf

Tässä vaiheessa olisi jo helppoa käydä katsomassa suoraan conf-tiedostoa ja tarkistaa syntaksi.

Korjasin tiedoston ja uudelleenkäynnistin palvelimen- kaikki toimii taas.

## e) klo 11.22

poistin WSGI-moduulin komennolla:

        sudo apt-get purge libapache2-mod-wsgi-py3

Tämän jälkeen migratesin projektitiedostossa ja uudelleenkäynnistin apache2 palvelimen:

![kuva](https://user-images.githubusercontent.com/105205141/223069629-04fe023c-1cfa-4ec0-b968-5e22952f3121.png)

        sudo systemctl status apache2.service
        
![kuva](https://user-images.githubusercontent.com/105205141/223069854-13351dcb-17dc-4b9f-918c-da7ee3316ccb.png)

        /sbin/apache2ctl configtest
        
![kuva](https://user-images.githubusercontent.com/105205141/223070489-86070a61-25c6-46ee-aa76-0f8c18933f9b.png)

Configtestissä huomataan, että ohjelma ei osaa lukea .conf -tiedoston WSGIDaemonProcess-komentoa (tuskin muitakaan WSGI-komentoja), voidaan todeta, että komennot ovat syntaksiltaan kirjoitettu oikein, jolloin tullaan siihen tulokseen että ongelma ei ole "misspelled" vaan "defined by a module not included in the server configuration"

Eli ratkaisu on: ladataan wsgi-moduuli takaisin:

        sudo apt-get install libapache2-mod-wsgi-py3

 
![kuva](https://user-images.githubusercontent.com/105205141/223071835-a0f102e9-62c0-4a13-a8fc-3a6ec68b905b.png)

ja palvelin taas toimii!

## f) klo 11.35

avasin settings.py tiedoston projektikansiossani:

        micro settings.py
        
![kuva](https://user-images.githubusercontent.com/105205141/223072381-21f34bb9-4819-4e34-9918-399a58cc71f6.png)

Yhdistämällä selaimella palvelimelle localhost-osoitteella ei päästy määränpäähän.

![kuva](https://user-images.githubusercontent.com/105205141/223072825-19ab5b23-9e84-44b9-8bde-07f851a3f678.png)

kokeilin testiksi selaimella Non-Functional.web sekä what.exe.me.do
sivuja ei löytynyt.

configtest ei palauta erroreita.

/var/log/apache2/error.log ei myöskään logannut mitään asiaan liittyvää.
/var/log/apache2/access.log ei myöskään logannut mitään.

En osaa sanoa, mistä tämä ongelma sitten voisi löytyä jos sitä ei minnekään näistä ole logattu.

Lopetin tehtävän klo 11.47.

## Lähteet: 

h12 -vinkit osio sivulla: https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/

chmod komennot ja oikeudet: https://www.pluralsight.com/blog/it-ops/linux-file-permissions
