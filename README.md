# h3-The-Box
This is a repository for Penetration testing course 2023 Tunkeutumistestaus ict4tn027-3009 course exercise h3

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
  * Bind Shell: Hyökkääjä käynnistää etäkonsolin (remote console) avulla kohdekoneessa palvelun, johon hyökkääjä voi muodostaa yhteyden.
   (GeeksforGeeks, richasalan57. 2022) 

* Lopuksi IppSec hyödynsi Meterpreteriä ja etsi `local_exploit_suggester` työkalun avulla hyökkäyksen, joka toimi 32-bittisellä ja 64-bittisellä Windows käyttöjärjestelmällä (x64/x86).

* Lopullinen hyökkäys, jolla koneeseen päästiin murtautumaan oli `exploit/windows/local/ms10_092_schelevator`.

(IppSec. 2017)

















## Lähteet

https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h3-the-box </br>
https://www.youtube.com/@ippsec/videos </br>
https://www.youtube.com/watch?v=e9lVyFH7-4o </br>
https://www.geeksforgeeks.org/difference-between-bind-shell-and-reverse-shell/ </br>











