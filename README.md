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















## Lähteet

https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h3-the-box </br>
https://www.youtube.com/@ippsec/videos </br>
https://www.youtube.com/watch?v=e9lVyFH7-4o </br>
https://www.geeksforgeeks.org/difference-between-bind-shell-and-reverse-shell/ </br>
https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data </br>











