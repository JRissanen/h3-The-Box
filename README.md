# h3-The-Box
This is a repository for Penetration testing course 2023 Tunkeutumistestaus ict4tn027-3009 course exercise h3

---
Olen tehnyt kaikki harjoitukset omalla tietokoneellani. </br>

Koneen infot: </br>
Edition: Windows 10 Pro </br>
Version: 22H2 </br>
OS build: 19045.2728 </br>
Processor: Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz   3.19 GHz </br>
Installed RAM: 16,0 GB </br>
System type: 64-bit operating system, x64-based processor </br>

Virtuaalikoneet ovat Oracle VM VirtualBoxissa, jonka versio on: Version 6.1.40. </br>

Virtuaalikoneiden infot: </br>

Metasploitable: </br>
Type: Linux </br>
Version: Debian (64-bit) </br>
Base Memory: 1024 MB </br>
Video Memory: 16 MB </br>
Processors: 1 CPU

Kali: </br>
Type: Linux </br>
Version: Debian (64-bit) </br>
Base Memory: 2024 MB </br>
Video Memory: 128 MB </br>
Processors: 2 CPU

Varmistan aina ennen tehtävien aloittamista, että molemmat virtuaalikoneet ovat yhteyksissä toisiinsa pingaamalla kali-koneelta metasploitable2-konetta: `ping 192.168.60.3`. </br>
Varmistan myös aina, ettei kumpikaan virtuaalikone saa verkkoyhteyttä pingaamalla google.com sekä googlen ensisijaista dns-serveriä 8.8.8.8: `ping google.com` ja `ping 8.8.8.8`.

Jos haluat enemmän tietoa minun tunkeutumistestausympäristöstä, niin voit lukea artikkelini: </br>
https://github.com/JRissanen/h1-OmaLabra

---

## x) Katso ja tiivistä (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.) Yksi valitsemasi [IppSec:n murtautumisvideo](https://www.youtube.com/@ippsec/videos).

### IppSec: HackTheBox - [Arctic](https://www.youtube.com/watch?v=e9lVyFH7-4o)

* IppSec aloittaa jokaisen hyökkäyksen `nmap -sV -sC -oA nmap <kohteen ip-osoite>` -komennolla, jotta
saa hyvän kuvan kyseisen kohde osoitteen porteista.
  * `nmap` skannaa kohteen portit.
  * `-sV` yrittää tunnistaa porttia käyttävän palvelun version
  * `-sC` skannaa turvallisilla scripteillä (oletus NSE=Nmap Scipting Engine documentation).
  * `-oA` tulosta kaikissa formaateissa.

* Löysi nmap skannauksen avulla haavoittuvaisen portin ja meni siihen selaimella. Löysi sivulta
ColdFusion -nimisen sovelluksen hakemistoja, joista löytyi "administrator/" hakemisto ja pääsi 
sitä kautta ColdFusion sovelluksen admin sisäänkirjautumissivulle.

* Etsi hyökkäyksiä `searchsploit coldfusion` ja löysi sopivan, metasploitablella ajettavan, 
"File Upload" hyökkäyksen.

* Metasploitable aikakatkaisi hyökkäyksen, koska alkuperäisen osoitteen latausaika on 20-30s, joten 
IppSec käytti Burp Suite -nimistä ohjelmaa hyökkäyksen ajamiseen/debuggaamiseen.

* Hyökkäyksen seurauksena sai tehtyä Reverse Shellin ja hyödynsi seuraavaksi Unicorn -nimistä työkalua,
jotta sai tehtyä Meterpreter Bind Shellin.
  * Reverse Shell: Hyökkääjän on käynnistettävä palvelin omalla koneellaan, johon kohdekone ottaa yhteyden.
  * Bind Shell: Hyökkääjä käynnistää etäkonsolin (remote console) avulla kohdekoneessa palvelun, johon hyökkääjä voi muodostaa yhteyden. </br>
   (GeeksforGeeks, richasalan57. 2022) 

* Lopuksi IppSec hyödynsi Meterpreteriä ja etsi `local_exploit_suggester` työkalun avulla hyökkäyksen, joka toimi 32-bittisellä ja 64-bittisellä Windows käyttöjärjestelmällä (x64/x86).

* Lopullinen hyökkäys, jolla koneeseen päästiin murtautumaan oli `exploit/windows/local/ms10_092_schelevator`.

(IppSec. 2017)

## a) We like to shop. Ratkaise Portswigger Academyn "[Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)". (Tee tarvittaessa tunnus Portswigger Academyyn).

Aloitin tekemällä käyttäjän PortSwiggerin sivuille ja lukemalla labran ohjeistuksen.

![Screenshot 2023-04-20 161553](https://user-images.githubusercontent.com/116954333/233378766-c5da33ea-1730-4855-8811-153c5ab11de4.png)

Eli ennakkoon annetun infon mukaan tiedän, että haavoittuvuutta kannattaa etsiä tuote kategoria suodattimesta (product category filter). </br>
Tiedän myös, että SQL query on mallia `SELECT * FROM products WHERE category = 'Gifts' AND released = 1`. </br>
Labran ratkaisuun pitää siis tehdä SQL-injektiohyökkäys, jonka seurauksena saa kaikkien tuotteiden tiedot esiin joka kategoriasta sekä vapauteuista että ei vapautetuista (released & unreleased).

Aloitin labran "Access the lab" painikkeesta ja aukesi seuraava näkymä:

![Screenshot 2023-04-20 162803](https://user-images.githubusercontent.com/116954333/233382874-20ecde37-1bb1-438c-9382-503340c1039d.png)

Klikkailin sivun kuvakkeita ja tekstejä, mutta en löytänyt yhtäkään tekstikenttää, johon olisi voinut syöttää tekstiä. </br>
Avasin Developer Toolin F12 painikkeella ja menin "Network" välilehdelle, tyhjensin sen vasemman yläreunan roskakorista ja latasin sivulla näkyvästä "Home" linkistä sivun uudelleen ja katsoin tulostusta:

![Screenshot 2023-04-20 164320](https://user-images.githubusercontent.com/116954333/233392355-d0dbfbd0-8b7e-47aa-ad8e-a3dcf1d206e9.png)

Ensin huomasin, ettei yksikään pyynnöistä ollut POST-metodin pyyntö, joihin oli hyökäytty aiemmissa harjoituksissa. </br>
Sitten huomasin yhden pyynnön olevan "HTTP status 101", joten lähdin sitä tutkimaan avaamalla "Headers" välilehdellä näkyvän "101 Switching Protocol" tekstin vieressä olevan [kysymysmerkin](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/101?utm_source=mozilla&utm_medium=devtools-netmonitor&utm_campaign=default). </br>

![Screenshot 2023-04-20 164320](https://user-images.githubusercontent.com/116954333/233389493-9303b938-75bf-4f41-9b7a-4cc36bc5c0d5.png)

Siitä aukesi sivu, joka selitti statuksen tarkoitusen. </br>
Ilmeisesti 101-status tarkoittaa, että palvelin vaihtaa protokollaa kyseisen pyynnön kohdalla ja vaihdetun protokollan tyyppi on määritelty "Upgrade" kohdassa. </br>
Huomasin, että labran pyynnössä oli myös tämän sivun esimerkin mukaisesti "WebSocket" määritys, joten seurasin sivun linkkiä aiheesta.

![Screenshot 2023-04-20 164926](https://user-images.githubusercontent.com/116954333/233394058-a8a047dc-f32e-4e0c-8007-4ba1241e12f3.png)
![Screenshot 2023-04-20 170204](https://user-images.githubusercontent.com/116954333/233393809-794306f4-835a-438a-b463-940584d5fb52.png)

We










## Lähteet

https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h3-the-box </br>
https://www.youtube.com/@ippsec/videos </br>
https://www.youtube.com/watch?v=e9lVyFH7-4o </br>
https://www.geeksforgeeks.org/difference-between-bind-shell-and-reverse-shell/ </br>
https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data </br>
https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/101?utm_source=mozilla&utm_medium=devtools-netmonitor&utm_campaign=default </br>











