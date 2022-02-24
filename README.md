# Overzicht

{% hint style="info" %}
The Road to Node versie: 3.1.2

Hulp nodig? Check [Telegram](https://t.me/theroadtonode).
{% endhint %}

Op deze site vind je alle benodigdheden om zelf een Bitcoin en Lightning node op te zetten. Veel plezier!

### Introductie

In het introductie onderdeel vind je informatie over het [doel en de waarom](https://docs.theroadtonode.com/introductie/doel-en-waarom) van deze guide. Ook komen de [benodigdheden](https://docs.theroadtonode.com/introductie/benodigdheden) aan bod, want het maken van een node is niet gratis.

### Raspberry Pi

De Raspberry Pi is een mini computer waar je de leukste dingen mee kunt. Maar om er gebruik van je maken, moet je hem eerst [aansluiten](https://docs.theroadtonode.com/raspberry-pi/hardware-aansluiten) en er een [besturingssysteem op zetten](https://docs.theroadtonode.com/raspberry-pi/software-flashen). Ook wil je natuurlijk [de beste prestaties](https://docs.theroadtonode.com/raspberry-pi/boot-vanaf-ssd) uit je Pi halen, wat je in deze guide doet door alles vanaf de SSD te doen. Als je interesse hebt in het anoniem gebruiken van Bitcoin, dan kan dat met [Tor](https://docs.theroadtonode.com/raspberry-pi/tor-installeren). Voor informatie over je Pi kun je de [monitoring](https://docs.theroadtonode.com/raspberry-pi/monitoring) sectie raadplegen. Hier komen standaard zaken voor zoals het checken van de temperatuur van je Pi, maar ook custom zaken zoals overzichten van de software die je draait.

De software die je gaat draaien is vaak afhankelijk van andere software \(in het Engels "dependencies"\). daar is het [dependencies onderdeel](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren) voor in het leven geroepen. Veel onderdelen van de gids zullen naar de dependencies verwijzen.

Als je je Pi ook wil inzetten als Lightning node, dan zal je de nodige funds moeten managen op het apparaat. Een goede beveiliging is dan cruciaal. Het [firewall](https://docs.theroadtonode.com/raspberry-pi/firewall) onderdeel in combinatie met het [port overzicht](https://docs.theroadtonode.com/raspberry-pi/port-overzicht) helpen je bij de beveiliging.

### Bitcoin Core

Het kloppend hart van deze guide. Allereerst halen we zelf de broncode van Bitcoin Core binnen en [installeren](https://docs.theroadtonode.com/bitcoin-core/installatie) het. Om er gebruik van te maken en de rest van de guide te volgen, heb je [bepaalde instellingen](https://docs.theroadtonode.com/bitcoin-core/configuratie-en-starten) nodig. De instellingen zijn bepalend voor latere apps en Lightning. In deze guide wordt Bitcoin Core als implementatie gebruikt. Uit [onderzoek](https://blog.lopp.net/bitcoin-node-performance-sync-tests/#performance-rankings) blijkt dit de snelste Bitcoin implementatie te zijn. Daarnaast wordt hier het meest aan ontwikkeld, bevat het de nieuwste features, heeft het [grootste aandeel](https://bitnodes.io/nodes/) in de markt en is het de meest volwassen implementatie van het protocol.

### Bitcoin Core extensies

Het "extensies" hoofdstuk met betrekking op Bitcoin Core, heeft voornamelijk te maken met tools die rechtstreeks leunen op Core. Neem bijvoorbeeld [Specter](https://docs.theroadtonode.com/bitcoin-core-extensies/specter), een gebruiksvriendelijke tool om een multisig constructie op te zetten. Je kunt met deze guide ook een Electrum server opzetten aan de hand van tools zoals [Electrs](https://docs.theroadtonode.com/bitcoin-core-extensies/electrs), [Electrum Personal Server](https://docs.theroadtonode.com/bitcoin-core-extensies/electrum-personal-server) of [Electrum X](https://docs.theroadtonode.com/bitcoin-core-extensies/electrum-x). Of misschien wil je je eigen blockchain explorer? Dat kan met [BTC RPC Explorer](https://docs.theroadtonode.com/bitcoin-core-extensies/btc-rpx-explorer) en [Mempool.space](https://docs.theroadtonode.com/bitcoin-core-extensies/mempool.space).

### Lightning

Bitcoin zijn tweede laag, het Lightning netwerk, moet het schaalbaarheidsprobleem van Bitcoin oplossen. Wil je echt digitaal cowboyen dan ben je bij Lightning aan het juiste adres. Om Lighting werkend te krijgen, moeten we de programmeertaal [Golang installeren](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren#golang). Dat heeft te maken met de implementatie van het Lightning protocol genaamd Lighting Network Daemon \(LND\). Hier zit Lightning Labs achter en wordt goed onderhouden.

### Lightning extensies

Om makkelijker gebruik te maken van het Lightning netwerk, kun je er allerlei software aan vastklikken. Met een user interface zoals [Ride The Lightning](https://docs.theroadtonode.com/lightning-extensies/ride-the-lightning) maak je op een verschrikkelijke en ongebruiksvriendelijke manier gebruik van LND. Ben je opzoek naar het gebruiksvriendelijke broertje van RTL, kun je eens naar [Thunderhub](https://docs.theroadtonode.com/lightning-extensies/thunderhub) kijken. Het biedt vrijwel dezelfde functionaliteiten als RTL, maar zonder dat het je bloed onder je nagels vandaan haalt.

Eén van de gaafste onderdelen van de gids is [Lightning Terminal](https://docs.theroadtonode.com/lightning-extensies/lightning-terminal). Een tool ontwikkeld door Lightning Labs voor het visueel managen van andere tools \([Pool](https://docs.theroadtonode.com/lightning-extensies/pool), [Loop](https://docs.theroadtonode.com/lightning-extensies/loop) en [Faraday](https://docs.theroadtonode.com/lightning-extensies/faraday)\) bovenop LND.

### Wallets

Het wallets hoofdstuk richt zich op het koppelen van verschillende wallets aan jouw node. Zowel je bitcoin als lightning node worden ondersteunt door verschillende wallets. Zo wordt er uitgelegd hoe je de app [Zap](https://docs.theroadtonode.com/wallets/zap) kunt gebruiken om channels te openen en Lightning transacties te doen - al dan niet via tor - met je eigen node. [Fully Noded](https://docs.theroadtonode.com/wallets/fully-noded) kun je ook aan jouw eigen node hangen, maar richt zich voornamelijk om Bitcoin Core. Met Fully Noded kun je jouw node beheren en bijvoorbeeld PSBT aanmaken en broadcasten.

## Contributie leveren

Wil je ook iets toevoegen aan de guide? Dat kan! En graag zelfs. Om het zo gestroomlijnd mogelijk te maken, is er een flow bedacht:

* Maak een issue aan op de [Github](https://github.com/bitdeal-nl/theroadtonode/issues) pagina. Het woord "issue" kun je hier wat breder interpreteren dan alleen "fouten" of "problemen". Een nieuwe feature kun je ook kenbaar maken middels een issue.
* Geef in de Telegram groep aan aan welke issue je werkt, dan kan een maintainer het op je naam zetten. Zo pakt niet iemand anders het per ongeluk op.
* Maak een fork van de repository en tak een branch af van `nl`.
* Is je feature of hotfix klaar? Maak hem dan kenbaar middels een pull request. Koppel deze meteen aan de eerder aangemaakte issue.

