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
OpenVPN yhteyden sain aktiiviseksi menemällä normaalikäyttäjänä hakemistoon, jossa OpenVPN tiedosto oli ja antamalla komennon: `sudo su` (ja salasanan). Näin pääsin ajamaan pääkäyttäjänä (root) komennon `openvpn starting_point_JRissanen.ovpn`, joka aktivoi OpenVPN yhteyden. OpenVPN siis vaatii toimiakseen root-oikeudet luodakseen `tun` (tunnel interface) yhteydet. (askUbuntu. 2016).

Kun OpenVPN yhteys on onnistuneesti aktivoitu, niin tulosteen viimeisellä rivillä lukee: `<päivämäärä & aika> Initialization Sequence Completed` ja itse sain ilmoituksen "VPN Login Message", että yhteys on muodostettu onnistuneeseti.

![Screenshot 2023-04-21 153131](https://user-images.githubusercontent.com/116954333/233709305-52b3e45d-1e4f-48ec-909c-c9ba1dc25518.png)

![Screenshot 2023-04-21 151640](https://user-images.githubusercontent.com/116954333/233709335-9d926a6c-2c17-4a7e-b66d-b4f833bd5ce5.png)

Seuraavaksi minulla kesti ymmärtää, miten saan tehtävät tehtyä siten, että hyökkäyskoneeni ei ole yhteydessä Internettiin, vaan pelkästään VPN:än välityksellä yhteydessä Hack The Box koneeseen. </br>

Eli tähän asti olen tehnyt kaiken virtuaalisella Kali hyökkäyskoneellani ja siksi kun laitoin VPN yhteyden päälle, niin en saanut enää verkkoyhteyttä Kali koneellani, joten luonnollisesti Hack The Box verkkosivukin kaatui, enkä pystynyt tekemään Starting Point tehtäviä. </br>

Kurssilaiseni [Antti Halonen](https://github.com/therealhalonen/penetration_testing) neuvoi pitämään Hack The Boxin auki host koneella ja ottamalla virtuaalisella Kali hyökkäyskoneella VPN yhteyden host koneen Hack The Box koneeseen, jolloin vain host kone (tässä tapauksessa siis oma Windows koneeni, jolla pyöritän virtuaalista Kali hyökkäyskonettani Virtual Boxissa) on kiinni verkossa ja pystyn tekemään tehtävät.

Yritän vielä havainnollistaa seuraavalla kuvankaappauksella:

![Screenshot 2023-04-21 175724](https://user-images.githubusercontent.com/116954333/233710273-cc95de3b-a89c-4e6f-bc80-54748586b5b6.png)

Eli kuvassa on siis vasemmalla puolella virtuaalinen Kali kone ja oikealla pelkästään host (Windows kone) koneen selain, jossa Hack The Box auki.
Kali ei ole verkossa, sillä on pelkästään OpenVPN yhteys Hack The Boxiin ja se pystyy ainoastaan pingaamaan Hack The Boxissa olevan Starting Point koneen osoitetta. </br>
En ole varma ymmärsinkö lopulta Antin vinkin oikein ja oliko tämä "oikeaoppinen" toteutus tapa, mutta minulla se toimi, niinkuin tehtävänannossa haluttiin, joten olen itse tyytyväinen.

Lopuksi, koko tämän VPN rumban jälkeen, katsoin sivun säännöt kohdan "Connect to HTB" (punainen tausta) tai "aktiivinen kohta" (vihreä tausta, minun kuvassa "STARTING POINT") vieressä olevasta kysymysmerkistä:

![Screenshot 2023-04-21 154320](https://user-images.githubusercontent.com/116954333/233661354-ab371cad-2e3f-4723-ade3-cddec170cb04.png)
 
 Otin säännöistä kuvankaappauksen talteen.
 
 ![Screenshot 2023-04-21 154510](https://user-images.githubusercontent.com/116954333/233661489-edcd002e-3cf5-4816-b414-6286922a6cbe.png)

Kohdan: "6. Streaming" tekstin perusteella päättelin, että "Starting Point Machines" koneiden ratkaisut saa julkaista vapaasti.
 
## c) Meow. Käynnistä "Starting point": "Meow". (Spawn machine). Tallenna ruutukaappaus sivusta, jossa näkyy koneen osoite ja tunkeutumistehtäviä. Porttiskannaa kone ja analysoi tulokset. Suorita HTB:n antamat tehtävät. Raportoi normaalisti, "Starting point" -koneista saa julkaista läpikävelyohjeita.
 
Päivä on vaihtunut edellisistä tehtävistä, joten ip-osoitteetkin ovat osittain muuttuneet.
 
Sain Meow koneen spawnattua ja sen ip-osoiten oli: 10.129.1.17.
 
![Screenshot 2023-04-22 111434](https://user-images.githubusercontent.com/116954333/233772126-33fb8b0e-1af5-4698-aadf-562771d1d90a.png)

Ensimmäinen kysymys oli, että mitä tarkoittaa VM. VM on yleinen lyhennys sanalle Virtual Machine, eli virtuaalikone, joten vastasin sen ja se oli oikein. </br>
![Screenshot 2023-04-22 112024](https://user-images.githubusercontent.com/116954333/233772318-9ea181dc-9283-47df-8c72-e539046b6e4d.png)

Toisessa kysymyksessä kysyttiin työkalua, jonka avulla voi ajaa komentoja komentoriviltä ja jota kutsutaan myös shelliksi tai konsoliksi. Oikea vastaus on terminaali ja sen opin jo ensimmäisellä tunnilla Linux palvelimet kurssilla. </br>
![Screenshot 2023-04-22 112418](https://user-images.githubusercontent.com/116954333/233772556-18b93cab-7253-4cc8-a91e-ea883ef908d4.png)

Kolmannessa kysymyksessä kysyttiin, mitä VPN:ää Hack The Boxissa käytetään ja kun sitä eilen laitoin pystyyn, niin tiedän, että vastaus on OpenVPN. </br>
![Screenshot 2023-04-22 112609](https://user-images.githubusercontent.com/116954333/233772699-832c647b-1970-4410-98bd-3134a207dfcf.png)

Neljännessä kysymyksessä kysyttiin "tunnel interface" lyhennettä. OpenVPN käyttää tätä myös ja jos ajan terminaalissa komennon `ifconfig` (kun openvpn on päällä), niin näen OpenVPN:än luoman tunnelin nimellä: `tun0`, joten oikea vastaus on tun. </br>
![Screenshot 2023-04-22 113001](https://user-images.githubusercontent.com/116954333/233772859-58dc404b-e8e6-4f33-a35c-52f41c1bfbd4.png)

Viidennessä kysymyksessä kysyttiin työkalua, joka lähettää kohteeseen ICMP echo pyyntöjä. Olen itse käyttänyt vain `ping` komentoa kun olen lähettänyt näitä pyyntöjä, joten vastasin sen ja se oli oikein. </br>
![Screenshot 2023-04-22 113305](https://user-images.githubusercontent.com/116954333/233773009-b7cb2f26-826a-47ce-84b4-2e6a32e3c9a3.png)

Kuudennessa kysymyksessä kysyttiin yleisintä porttiskanneri työkalua ja sen on opettanut Tero jo moneen otteseen tällä kurssilla olevan `nmap`. </br>
![Screenshot 2023-04-22 113414](https://user-images.githubusercontent.com/116954333/233773183-c57a0938-8630-486a-b777-05bf2fda7c7e.png)

Setsemmännessä kysymyksessä kysyttiin, että mikä palvelu näkyy käyttävän porttia 23/tcp kun skannataan jokin osoite ja olen sen aiemmista tehtävistä oppinut olevan telnet. </br>
![Screenshot 2023-04-22 113650](https://user-images.githubusercontent.com/116954333/233773262-0afbdc58-762f-4413-8f67-9292e6d3a85d.png)

Kahdeksannessa kysymyksessä kysyttiin käyttäjää, joka pystyy kirjautumaan telnettiin ilman salasanaa, eli pääkäyttäjä/root. </br>
![Screenshot 2023-04-22 114006](https://user-images.githubusercontent.com/116954333/233773409-608bd271-e81e-4e04-9514-62c248e44d73.png)

Yhdeksännessä tehtävässä pyydettiin sitten root flagia, eli pitää käyttää hyökkäys koneessa nyt kaikkea, mitä aikaisemmat kahdeksan kysymystä opettivat. </br>
Koska aiemmissa kysymyksissä mainittiin telnet, niin ajattelin tehtävän olevan pitkälti samanlainen kuin ihan ensimmäisessä kurssin harjoituksessa tekemäni hyökkäys metasploitableen, joten lähdin liikkeelle samalla tavalla. </br>
Koska tehtävissäkin kävi ilmi, että telnettiin saa root-käyttäjänä yhteyden ilman sen kummempaa tunnistautumista, niin käytän tehtävän tekemiseen Kalin root-terminaalia.

Ensimmäiseksi käynnistin metasploitablen komennolla: `msfdb run`. </br>
Sitten lisäsin uuden workspacen komennolla: `workspace --add HTBMeow`. </br>
Varmistin vielä, että HTB(Hack The Box) kone vastaa `ping` pyyntöön sekä että google ei vastaa, jolloin olen turvallisessa ympäristössä.

![Screenshot 2023-04-22 120123](https://user-images.githubusercontent.com/116954333/233774545-3df677ed-d525-4146-915e-68a1360e5e14.png)

Seuraavaksi porttiskannasin HTB koneen osoitteen komennolla: `db_nmap 10.129.1.17`. </br>
Sain tuloksen, että portti 23/tcp telnet on auki. </br>
![Screenshot 2023-04-22 120621](https://user-images.githubusercontent.com/116954333/233774693-ea53b303-5b98-445a-a7b5-9abcb596ae94.png)

Otin yhteyden komennolla: `telnet 10.129.1.17` ja siitä aukesi Hack The Box sisäänkirjautumisnäkymä. </br> Tehtävissä oli puhuttu, ettei root-käyttäjä tarvitsisi salasanaa, joten kirjoitin "Meow login" kohtaan: "root" ja sehän toimi ja pääsin sisään. </br>
![Screenshot 2023-04-22 121720](https://user-images.githubusercontent.com/116954333/233775269-37216012-3235-4470-ba60-4a29423f638d.png)

Nyt pitää enää selvittää, miten root flagin saa selville. </br>
Minulla ei ollut aikaisempaa tietoa root flagistä, mutta tehtävä olikin tehty varsin helpoksi ja root flag oli tallennettu heti ensimmäiseen näkymään nimellä "flag.txt" ja löysin sen puoli vahingossa, koska ensimmäinen komento, jonka ajoin päästyäni sisään oli listauskomento `ls`. </br>
Lopuksi piti vain saada tiedoston sisältö näkyviin ja se onnistui komennolla `cat flag.txt`, jonka jälkeen tarvitsi vain kopioida ja liittää root flag HTB:n Meow koneen yhdeksännen kysymyksen kysymyskenttään. </br>

![Screenshot 2023-04-22 122834](https://user-images.githubusercontent.com/116954333/233775801-658a4526-a887-4d0e-bfcf-ceba5d3a7f69.png)

Eipä tämän tehtävän tekemiseen olisi edes metasploitablea tarvinnut, mutta eipä sen käyttämisestä haittaakaan ollut. 

## d) Fawn. Ratkaise Fawn. (HTB Starting point)

Aloitin spwanaamalla Fawn koneen. </br>
![Screenshot 2023-04-22 140547](https://user-images.githubusercontent.com/116954333/233780557-f66afc56-214f-4019-a158-3cd95a1e8051.png)

Ensimmäinen kysymys oli, mikä on FTP:n koko nimi. Kirjoitin Kali koneen terminaaliin: `man ftp` ja sain vastaukseksi "File Transfer Protocol". </br>
![Screenshot 2023-04-22 141014](https://user-images.githubusercontent.com/116954333/233780762-0c467294-6f6a-4b4c-be88-6a183ceb2a55.png)

Toinen kysymys oli että mitä porttia FTP yleensä kuuntelee. En muistanut ulkoa, joten Googlasin "FTP default port" ja sain [Terry Slatterin & Laura Chappellin](https://www.techtarget.com/searchnetworking/tip/Understanding-the-FTP-PORT-command#:~:text=You%20may%20already%20know%20that,TCP%20port%2021%20by%20default.) artikkelista oikean vatauksen eli 21. </br>

Kolmas kysymys oli, että mikä turvallisen (secure) FTP:n lyhenne. Tämä oli helposti pääteltävissä ottamalla vain secure -sanan ensimmäisen kirjaimen lyhenteen alkuun (ja muistin sen muutenkin aiemmista harjoituksista). Eli SFTP. </br>
![Screenshot 2023-04-22 142239](https://user-images.githubusercontent.com/116954333/233781345-3c77fe27-11c7-4ede-b004-bd00c0390d9d.png)

Neljäs kysymys oli sama kuin yksi Meow koneen kysymyksistä, eli mitä komentoa käytetään ICMP echo pyyntöihin ja vastaus on vieläkin `ping`.
![Screenshot 2023-04-22 142618](https://user-images.githubusercontent.com/116954333/233781481-fcff67ca-65a2-492a-8162-b8365942a790.png)

Viidennes kysymys oli, että mikä FTP versio Fawn koneella on käytössä, joten otetaan selvää. </br>
Käytin skannauksessa komentoa: `nmap -sV 10.129.91.73`. Jossa `-sV` on version tunnistamista varten annettu parametri. (Opin tämän tiivistämästäni IppSec videosta). </br>
![Screenshot 2023-04-22 143259](https://user-images.githubusercontent.com/116954333/233782003-df561e8e-ce3c-4ad7-a0ef-2f46272d3d3c.png)

Kuudes kysymys oli, että mikä käyttöjärjestelmä on Fawn koneella. Siihenkin vastaus näkyi edellisessä tulostuksessa kohdasta "Service Info: OS: Unix". </br>
![Screenshot 2023-04-22 143820](https://user-images.githubusercontent.com/116954333/233782229-57a18d73-90f4-409e-be3e-61cdcf852cad.png)

Seitsemäs kysymys oli, että miten saa FTP:n help menun näkyviin. Vastaus on komennolla: `ftp -h`.
![Screenshot 2023-04-22 144013](https://user-images.githubusercontent.com/116954333/233782293-b8b3ba98-4017-4b3d-87bb-7ec2a3367f62.png)

Kahdeksas kysymys oli, että mikä on käyttäjänimi, mitä käytetään kun halutaan kirjautua sisään ilman käyttäjää. </br>
Googlasin kysymyksen ja sain [Stack Overflow:sta](https://stackoverflow.com/questions/3936911/how-can-i-login-anonymously-with-ftp-usr-bin-ftp) vastauksen. Käyttäjänimi on "anonymous". </br>
![Screenshot 2023-04-22 144615](https://user-images.githubusercontent.com/116954333/233782788-8fb4a903-fc20-4c54-82e7-1ab496d4280f.png)

Yhdeksäs kysymys oli, että mikä on vastauksen numerokoodi kun kirjautuu onnistuneesti FTP:llä sisään. </br>
Otin FTP yhteyden Fawn koneeseen komennolla: `ftp 10.129.91.73`, jonka jälkeen annoin käyttäjänimeksi ja salasanaksi äsken opetetun "anonymous" käyttäjän. </br>
Sen jälkeen sain tekstin "230 Login succesful.", joten vastaus on 230. </br>
Kymmenes kysymys oli, että mikä on toinen yleinen tiedostojen listaus komento `dir` komennon ohella, ja sehän on `ls`. </br>
![Screenshot 2023-04-22 145308](https://user-images.githubusercontent.com/116954333/233783211-18728afa-d847-49d7-aaf7-9b81d6db0fcf.png)

Yhdestoista kysymys oli, että millä komennolla saa ladattua tiedostoja FTP palvelimelta. FTP:n käyttöä harjoiteltiin paljon Linux palvelimet kurssilla, joten tiedän komennon olevan `get`. </br>
Kahdestoista kysymys oli sama kun Meow koneen loppu, eli syötä root flag. </br>
Annoin komennon `exit`, jolla sain katkaistua FTP yhteyden, ja sen jälkeen taas komennolla `cat flag.txt` pystyi näkemään root flagin ja syöttämään sen Fawn starting pointin viimeiseen kysymyskenttään. </br>
![Screenshot 2023-04-22 150215](https://user-images.githubusercontent.com/116954333/233783531-5ed85fed-0600-4142-a0e0-4fe2b0ece870.png)

## e) Dancing. Ratkaise Dancing. (HTB Starting point)

Aloitin taas spawnaamalla koneen. </br>
![Screenshot 2023-04-22 151152](https://user-images.githubusercontent.com/116954333/233783973-20c05595-16e6-49e7-864a-99df9abed078.png)

Ensimmäinen kysymys oli, että mikä on SMB:n koko nimi. Googlasin ja löysin [Microsoftin artikkelin](https://learn.microsoft.com/en-us/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) aiheesta. </br>
Vastaus on siis "Server Message Block". </br>
![Screenshot 2023-04-22 151653](https://user-images.githubusercontent.com/116954333/233784202-b1f03683-3307-4952-8472-0c6648e7c3d6.png)

Toinen kysymys oli, että mitä porttia SMB käyttää. Annoin Kali koneella komennon: `nmap -sV -sC  10.129.6.157` ja tulosteesta näin kolme avointa porttia: 135, 139 ja 445. Kokeilin ensin 135, mutta se oli väärä vastaus. Sitten kokeilin seuraavaa eli 139, mutta sekin oli väärin. Viimeinen eli 445 oli sitten oikea vastaus. </br>
Kolmanteenkin kysymykseen sai samalla tulostuksella vastauksen, eli mikä on portin 445 palvelun nimi. Vastaus oli microsoft-ds.
![Screenshot 2023-04-22 153654](https://user-images.githubusercontent.com/116954333/233785118-3c36245b-d946-4f53-88ae-30fce64bbf43.png)

Neljäs kysymys oli, että millä komennolla SMB työkalu listaa tiedot. </br>
Vastauskentässä näky, että komento on kaksimerkkinen, joten kokeilin `ls` ja `la`, mutta ne olivat väärin. </br>
Katsoin vinkin ja siinä neuvottiin käyttämään `-` ja sanan "list" lyhennettä, joten ratkaisu oli siis `-L`. </br>
![Screenshot 2023-04-22 153915](https://user-images.githubusercontent.com/116954333/233785386-b8b35ef1-33cf-419d-9470-895a897b2030.png) </br>
![Screenshot 2023-04-22 154315](https://user-images.githubusercontent.com/116954333/233785409-ab6f631f-ca14-480f-aa97-a6f354a0c70a.png)

Viides kysymys oli, että kuinka monta sharea on HTB:n Dancing starting osiossa. </br>
En ollut varma, miten SMB työkalua käytetään, joten lähdin kokeilemaan/selvittämään asiaa. </br>
Kokeilin ensin komennolla, `smb 10.129.6.157`, mutta sitä ei löytynyt ja terminaali tarjosi sen sijaan eri vaihtoehtoja. </br>
Vaihtoehdoista löytyi "smbd from deb samba", joka näytti mielenkiintoiselta, joten koitin `smbd 10.129.6.157`, mutta mitään ei tapahtunut. </br>
Sitten koitin katsoa apua komennolla `smbd -h`, mutta se ei toiminut, niin avasin smbd:n manuaali sivut komennolla `man smbd`. </br>
![Screenshot 2023-04-22 155607](https://user-images.githubusercontent.com/116954333/233792564-a7f513f9-4bda-4408-a56d-3799578a0b58.png)

Kyseessä oli siis SMB serveri. Sitä en itse tarvitse, joten selasin manuaalin loppuun, "See also" osioon. </br>
Sieltä kolmas vaihtoehto oli "smbclient", joka kuulosti tutulta, joten otin seuraavaksi komennolla `man smbclient` enemmän infoa esille. </br>
![Screenshot 2023-04-22 155857](https://user-images.githubusercontent.com/116954333/233792712-6c76f3ab-ca0e-46fb-ade7-3ce3a090bd80.png)

Heti ensimmäinen rivi: "ftp-like client..." kuulosti juuri siltä, mitä tehtävässä halutaan käytettävän. </br>
Kokeilin siis seuraavaksi komentoa: `smbclient 10.129.6.157`, ja salasanaa kysyessä laitoin "root", mutta tulostus oli seuraava: </br>
![Screenshot 2023-04-22 155857](https://user-images.githubusercontent.com/116954333/233792961-7a63d500-bc12-4b98-87d1-63f04a6c3ba1.png)

Tulostusta tulkitessa, en siis laittanut tarpeeksi montaa `\` merkkiä... </br>
Koitin edellisen kohdan `-L` parametria, eli: `smbclient -L 10.129.6.157` ja salasana taas "root", tulostus oli erilainen: </br>
![Screenshot 2023-04-22 183631](https://user-images.githubusercontent.com/116954333/233793558-12a22cdd-03a3-4b1f-a11f-b7c4d80ec534.png)












## Lähteet

https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h3-the-box </br>
https://www.youtube.com/@ippsec/videos </br>
https://www.youtube.com/watch?v=e9lVyFH7-4o </br>
https://www.geeksforgeeks.org/difference-between-bind-shell-and-reverse-shell/ </br>
https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data </br>
https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/101?utm_source=mozilla&utm_medium=devtools-netmonitor&utm_campaign=default </br>
https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API </br>
https://github.com/therealhalonen/penetration_testing </br>
https://askubuntu.com/questions/100986/any-reason-not-to-start-openvpn-as-root </br>
https://www.techtarget.com/searchnetworking/tip/Understanding-the-FTP-PORT-command#:~:text=You%20may%20already%20know%20that,TCP%20port%2021%20by%20default. </br>
https://stackoverflow.com/questions/3936911/how-can-i-login-anonymously-with-ftp-usr-bin-ftp </br>













