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

Siitä aukesi sivu, joka selitti statuksen tarkoitusen. </br>

![Screenshot 2023-04-20 164926](https://user-images.githubusercontent.com/116954333/233394058-a8a047dc-f32e-4e0c-8007-4ba1241e12f3.png)

Ilmeisesti 101-status tarkoittaa, että palvelin vaihtaa protokollaa kyseisen pyynnön kohdalla ja vaihdetun protokollan tyyppi on määritelty "Upgrade" kohdassa. </br>
Huomasin, että labran pyynnössä oli myös tämän sivun esimerkin mukaisesti "WebSocket" määritys, joten seurasin sivun [linkkiä](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) aiheesta.

![Screenshot 2023-04-20 170204](https://user-images.githubusercontent.com/116954333/233393809-794306f4-835a-438a-b463-940584d5fb52.png)

Ilmeisesti WebSocketin avulla on mahdollista avata kaksisuuntainen kommunikaatio sessio käyttäjän selaimen ja palvelimen välillä. </br>
En ymmärrä täysin tätä toimintalogiikkaa, joten en jää tässä vaiheessa siihen jumiin, vaan selvitän muita tapoja.

![Screenshot 2023-04-20 165017](https://user-images.githubusercontent.com/116954333/233396452-5cc22253-a905-46f0-b2a9-c5c3d777d1a8.png)

Klikatessani itseni "Corporate gifts" välilehdelle, huomasin, että selaimen teksi muuttui tehtävän kuvauksessa annettua SQL querya vastaavan näköiseksi.

![Screenshot 2023-04-20 172837](https://user-images.githubusercontent.com/116954333/233397944-9d7f07eb-0298-4775-9fe5-81f3ed4d11cd.png)

Koitin syöttää hakukenttään `category=Gifts+released=1` tekstin ja sain seuraavanlaisen tulostuksen:

![Screenshot 2023-04-20 173352](https://user-images.githubusercontent.com/116954333/233406493-938093df-e480-433d-bd23-156ab148e147.png)

Eli sivun alaotsikko vain muuttui ja tuotteet katosivat. </br>
Tämä tulostus ei vielä minulle paljoa kerro, mutta ainakin jotain tapahtui.

Tutkin eri tulosteita hakukentistä ja huomasin, että yksittäisen tuotteen hakukentässä oli kohta `product?productId=<numero>` ja vaihtamalla viimeistä numeroa sai yksittäisiä tuotteita näkyviin, joita ei ollut alku sivun "All" välilehdellä näkyvissä, joten päättelin, että nämä tuotteet ovat piilotettuja. </br>

![Screenshot 2023-04-20 180834](https://user-images.githubusercontent.com/116954333/233409190-644182e4-f2f5-4820-81c0-ede94e1c9c38.png)

![Screenshot 2023-04-20 180949](https://user-images.githubusercontent.com/116954333/233409583-b3982baf-96c3-45e8-9b27-5d4e083d567e.png)

Kokeilin lukuisia eri vaihtoehtoja (esim: `*`, `--`, `blank`, `0` jne...) `productid=` kohtaan, mutta en saanut mitään edistävää tulosta.

![Screenshot 2023-04-20 181225](https://user-images.githubusercontent.com/116954333/233411697-f346a246-7193-4cb5-ad5a-3c28d8cbc49c.png)
![Screenshot 2023-04-20 181302](https://user-images.githubusercontent.com/116954333/233411715-03a0ff34-f218-4b63-9dbc-a2dc9581eb64.png)
![Screenshot 2023-04-20 181434](https://user-images.githubusercontent.com/116954333/233411736-ed170468-0fd1-4798-90e3-95c711bd0454.png)

Lopulta en keksinyt enää mitä kokeilla, joten palasin takaisin labran ohjeistukseen ja katsoin ratkaisun: </br>

![Screenshot 2023-04-20 183458](https://user-images.githubusercontent.com/116954333/233416255-64b220fb-cf42-4723-b1e5-6cc8a371955f.png)

Eli oikea ratkaisu olisi ollut syöttää `category` kohdalle parametriksi: `'+OR+1=1--'`. </br>
`1=1` antaa parametrille arvon `true`, joka puolestaan palauttaa kaikki tulokset kun loppupsa kommentoidaan pois käyttämällä: `--`. </br>
Tämä on aiemmista tehtävistä tuttu ratkaisu tapa ja olin itsekin oikeilla jäljillä tehdessäni harjoitusta. </br>
En vain osannut ilman ratkaisua kirjoittaa oikean muotoista SQL-injektiota oikeaan paikkaan. Tämä siksi, että olen aiemmin tehnyt vain tekstikenttiin hyökkäyksiä, enkä verkkosivun hakukenttiin.

![Screenshot 2023-04-20 185030](https://user-images.githubusercontent.com/116954333/233420321-f504b11d-8c42-4dab-b440-cac3f6274b8b.png)

## b) HTB. Tee HackTheBox.com tunnus. Avaa OpenVPN-yhteys "Starting Point" verkkoon. Estä tunnelin ulkopuolinen liikenne eli liikenne oikeaan Internettiin (vinkit alla). Testaa, että normaali Internet-liikenteesi on estetty.

Kun sain tunnuksen tehtyä, lähdin luomaan Hack The Boxiin VPN yhteyttä, [Tero Karvisen vinkkien mukaan](https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h3-the-box), kohdasta: "Connect to HTB".

![Screenshot 2023-04-21 145423](https://user-images.githubusercontent.com/116954333/233665049-803205d3-35fd-431e-b68d-49a1b5df3b32.png)

Prosessi oli seuraava: Connect to HTB -> Starting Point -> OpenVPN -> VPN Access: EU-Starting Point, VPN Server: EU-StartingPoint 1 -> Protocol: UDP 1337 -> Download VPN.

![Screenshot 2023-04-21 145532](https://user-images.githubusercontent.com/116954333/233674151-88092108-84ea-448b-8ab2-011b15ae0097.png)

![Screenshot 2023-04-21 145556](https://user-images.githubusercontent.com/116954333/233674173-2b90caa0-46ca-4d3e-acfd-0eb50115b11f.png)

![Screenshot 2023-04-21 145700](https://user-images.githubusercontent.com/116954333/233674189-fa9974b0-0c47-47be-af2e-4ead84bd2ef1.png)

Seuraavaksi piti määrittää "Network Connections" kohdasta juuri lataamani VPN käyttövalmiiksi. </br>
Eli: Network Connections -> VPN Connections -> Add a VPN connection... -> Import a saved VPN configuration... (Scroll menun viimeinen kohta) -> Create... -> Open starting_point_omattiedot.ovpn -> Save.

![Screenshot 2023-04-21 145830](https://user-images.githubusercontent.com/116954333/233701145-22127dfd-d5a6-449c-913a-d69d1c357267.png)

![Screenshot 2023-04-21 145916](https://user-images.githubusercontent.com/116954333/233701166-5d25599c-db9f-42f5-a3fe-4c2f065e3250.png)

![Screenshot 2023-04-21 145945](https://user-images.githubusercontent.com/116954333/233701188-8691a0ee-5ac8-4cd9-94e1-17aa8b689564.png)

![Screenshot 2023-04-21 150014](https://user-images.githubusercontent.com/116954333/233701202-30a02694-62ce-4bbc-8bfa-b832a4a4ac39.png)

![Screenshot 2023-04-21 150121](https://user-images.githubusercontent.com/116954333/233701220-d0aafc07-883c-44d6-abed-b48bb42b4c99.png)

Nyt minulla on siis Kali hyökkäyskoneellani OpenVPN yhteys (olemassa, ei vielä aktiivinen) Hack The Box Starting Point koneisiin. </br>
OpenVPN yhteyden sain aktiiviseksi menemällä normaalikäyttäjänä hakemistoon, jossa OpenVPN tiedosto oli ja antamalla komennon: `sudo su` (ja salasanan). Näin pääsin ajamaan pääkäyttäjänä (root) komennon `openvpn starting_point_JRissanen.ovpn`, joka aktivoi OpenVPN yhteyden. 



Seuraavaksi minulla kesti ymmärtää, miten saan tehtävät tehtyä siten, että hyökkäyskoneeni ei ole yhteydessä Internettiin, vaan pelkästään VPN:än välityksellä yhteydessä Hack The Box koneeseen. </br>
Kurssilaiseni [Antti Halonen](https://github.com/therealhalonen/penetration_testing) neuvoi pitämään Hack The Boxin auki host koneella ja ottamalla virtuaalisella Kali hyökkäyskoneella VPN yhteyden host koneen Hack The Box koneeseen.

Eli tähän asti olen tehnyt kaiken virtuaalisella Kali hyökkäyskoneellani. </br>




Seuraavaksi katsoin sivun säännöt kohdan "Connect to HTB"(punainen tausta) tai "<aktiivinen kohta>" (vihreä tausta, minun kuvassa "STARTING POINT") vieressä olevasta kysymysmerkistä:

![Screenshot 2023-04-21 154320](https://user-images.githubusercontent.com/116954333/233661354-ab371cad-2e3f-4723-ade3-cddec170cb04.png)
 
 Otin säännöistä kuvankaappauksen talteen.
 
 ![Screenshot 2023-04-21 154510](https://user-images.githubusercontent.com/116954333/233661489-edcd002e-3cf5-4816-b414-6286922a6cbe.png)

Kohdan: "6. Streaming" tekstin perusteella päättelin, että "Starting Point Machines" koneiden ratkaisut saa julkaista vapaasti.
 
 




















## Lähteet

https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h3-the-box </br>
https://www.youtube.com/@ippsec/videos </br>
https://www.youtube.com/watch?v=e9lVyFH7-4o </br>
https://www.geeksforgeeks.org/difference-between-bind-shell-and-reverse-shell/ </br>
https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data </br>
https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/101?utm_source=mozilla&utm_medium=devtools-netmonitor&utm_campaign=default </br>
https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API </br>
https://github.com/therealhalonen/penetration_testing </br>













