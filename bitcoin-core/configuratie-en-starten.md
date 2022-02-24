# Configuratie en starten

{% hint style="info" %}
Tijd: 5 minuten
{% endhint %}

Bitcoin Core heeft [allerhande instellingen](https://en.bitcoinwiki.org/wiki/Running_Bitcoind) net zoals jouw mobiel instellingen heeft. Het mooie aan het mobieltje is dat de instellingen overzichtelijk gegroepeerd onder elkaar staan. Je kunt ze makkelijk wijzigen met een druk op de knop. Bij Core werkt dat net even anders. Je moet eerst een lijst vinden met mogelijke instellingen, je inlezen en daarna bepalen wat je nodig hebt. Best wel een gedoe, daarom staat hieronder een configuratie bestand wat zou moeten werken voor deze guide. Wil je meer customizen dan is [deze site](https://jlopp.github.io/bitcoin-core-config-generator/) van Lopp een hele goede resource.

## Configuratie

Gezien vanuit de home directory \(`/home/ubuntu`\) hebben we nu een map genaamd `bitcoin` met de broncode. Naast deze map hebben we een map nodig genaamd `.bitcoin` dus maak hem aan.

```bash
mkdir ~/.bitcoin
```

De instellingen van Bitcoin Core zetten we in een bestand in de .bitcoin map genaamd bitcoin.conf.

```bash
nano ~/.bitcoin/bitcoin.conf
```

Net als bij het [torrc bestand](https://docs.theroadtonode.com/bitcoin-core/tor-aanpassen) moeten we wat regels copy-pasten. Je kunt alle regels, inclusief de commentaar regels aangegeven met een `#`, kopiëren en plakken.

```bash
# Maak het mogelijk JSON-RPC commando's te accepteren
server=1

# Draai Bitcoin Core (bitcoind) in de achtergrond
daemon=1

# Listening mode aan
listen=1

# Zorg ervoor dat Core een index bijhoudt van alle txs
txindex=1

# Als je wil dat Specter sneller werkt
blockfilterindex=1

# Als je Specter (zie Bitcoin Core extensies hoofdstuk in de guide) wil draaien
disablewallet=0

# Cache grootte in MB's voor de database. Ophogen naar 3000 is wel lekker voor de IBD.
# Later kan het teruggeschroeft worden naar 500.
dbcache=3000

# Wil je jouw node op zowel tor als clearnet, laat de instellingen dan voor wat het is.
# Wil je jouw node enkel op tor? Doe dan het volgende:
# - Haal # weg voor onlynet=onion. Je zegt hiermee dat alleen tor toegestaan is.
# - Haal # weg voor proxy=127.0.0.1:9050. Je zegt hiermee dat alles via tor moet lopen.
# - Haal # weg voor bind=127.0.0.1.
# - Zet # voor onion=127.0.0.1:9050. Je hebt immers met proxy aangegeven dat ALLES via tor moet.
# - Zet # voor discover=1. Je zet hiermee de discover functie uit.
# - Zet # voor upnp=1. Je zet hiermee de UPnP functie uit.

#onlynet=onion
#proxy=127.0.0.1:9050
#bind=127.0.0.1
onion=127.0.0.1:9050
discover=1
upnp=1

# Nodig voor Lightning in een later stadium van de guide
zmqpubrawblock=tcp://127.0.0.1:28332
zmqpubrawtx=tcp://127.0.0.1:28333

# Standaard gebruik maken van native segwit (bech32) adressen
addresstype=bech32
changetype=bech32
```

Wacht even met gulzig op `Ctrl + X` drukken om op te slaan! Wil je dat jouw node enkel bereikbaar is via tor? Doe dan het onderstaande. Wil je dat jouw node ook via het normale internet bereikbaar is, laat de instellingen dan voor wat het is en sla het bestand op.

* Haal de `#` weg voor `onlynet=onion`. Je zegt hiermee dat alleen tor toegestaan is.
* Haal de `#` weg voor `proxy=127.0.0.1:9050`. Je zegt hiermee dat alles via tor moet lopen.
* Haal de `#` weg voor `bind=127.0.0.1`.
* Zet een `#` voor `onion=127.0.0.1:9050`. Je hebt immers met proxy aangegeven dat ALLES via tor moet.
* Zet een `#` voor `discover=1`. Je zet hiermee de discover functie uit.
* Zet een `#` voor `upnp=1`. Je zet hiermee de UPnP functie uit.

Druk na alle veranderingen op `Ctrl + X` en daarna `Y` om op te slaan.

## Authenticatie

We willen een gebruiker aanmaken om deze toegang verlenen tot bitcoind. Dit doen we met een scriptje. In het volgende commando wordt de gebruiker `XXX` meegegeven. Maar dit kun je veranderen in iets wat je leuk vindt.

```bash
python3 ~/bitcoin/share/rpcauth/rpcauth.py XXX
```

De output zal hierop lijken:

```bash
String to be appended to bitcoin.conf:
rpcauth=XXX:kdfjhad93rhasdjfhas$lkajshdflakshdf93ehf3qiflkjf9f39rhf39p3930fhaldkjfhsdkljbdflg
Your password:
sdflkashf93hfhalfhasdfjh3ejfhb=
```

Wat staat hier? De eerste regel geeft aan dat we iets moeten toevoegen aan het `bitcoin.conf` bestand. De volledige regel die begint met `rpcauth` moeten we dus toevoegen. We hebben een wachtwoord toebedeeld gekregen. Daar heb je het mee te doen. Open `bitcoin.conf` opnieuw.

```bash
nano ~/.bitcoin/bitcoin.conf
```

Voeg onderaan het bestand de `rpcauth=user:salt$hash` regel toe, maar dan met alle cijfers en letters zoals het commando je gaf. Sla het op met `Ctrl + X` en bevestig met `Y`. We hebben nu aan bitcoind verteld dat de gebruiker genaamd `xxx` mag inloggen met het wachtwoord `sdflkashf93hfhalfhasdfjh3ejfhb=`. Sla dit wachtwoord dus op, je zal het later nodig hebben.

Je kunt meerdere rpcauth regels toevoegen. Zo kun je verschillende gebruikers aanmaken en toegang geven.

## Firewall en router

Als je UFW hebt ingesteld is het belangrijk om port 8333 open te zetten. Dit zorgt ervoor dat andere nodes kunnen verbinden met die van jou. Het openzetten van deze port is alleen noodzakelijk als je je node \(ook\) over clearnet draait. Draai je jouw node enkel over tor, dan kun je deze paragraaf overslaan en verder naar "starten".

```bash
sudo ufw allow 8333 comment "Port voor Bitcoin Core peers"
```

In het configuratie bestand een paar paragraven hierboven staat `upnp=1`. Dit houdt in dat als jouw router UPnP ondersteunt, Bitcoin Core die port zal openzetten op je router. Ondersteunt jouw router UPnP niet of heb je het uitgezet? Dan moet je even op [portforward.com](https://portforward.com/) kijken hoe jij op jouw specifieke router port 8333 openzet en het verkeer doorsluist naar je Pi.

## Starten

Dan is het nu tijd om deel te nemen aan het Bitcoin netwerk! Knal dit je Pi in:

```bash
bitcoind
```

Done! Je kunt live bijhouden wat er gebeurt met:

```bash
tail -n 200 -f ~/.bitcoin/debug.log
```

Per block dat gecontroleerd wordt, zie je de tekst `progress=X.XXXXX` voorbij komen. Dit is een getal van 0 tot 1. Nul betekent dat je nog niets gesynchroniseerd hebt, 1 betekent dat je klaar bent. Met `Ctrl + C` zet je de live feed weer stop.

De initiële block download \(IBD\) zal zo'n 60 tot 80 uur duren.

Met de installatie van Core heb je ook een tool geïnstalleerd genaamd `bitcoin-cli`. Hiermee kun je met bitcoind praten.

## Instellingen checken

Maak gebruik van de `bitcoin-cli` om netwerk informatie op te halen van jouw node.

```bash
bitcoin-cli getnetworkinfo
```

Met dit commando krijg je wat informatie over het netwerk. Als het goed is ziet het er ongeveer zo uit als je enkel van tor gebruik maakt.

{% hint style="info" %}
Het onion-adres wijkt af van het adres dat je in [de vorige stap](https://docs.theroadtonode.com/bitcoin-core/tor-aanpassen#onion-adres) hebt aangemaakt \(als je dat hebt aangemaakt\). Dat het afwijkt is juist goed. Het onion-adres uit de vorige stap is enkel voor het uitvoeren van commando's op je node van buitenaf en gaat over port 8332. Het onderstaande onion-adres wordt door bitcoind zelf aangemaakt en gaat over port 8333. Dit wordt gebruikt om met andere nodes te communiceren.
{% endhint %}

```json
"networks": [
    {
        "name": "ipv4",
        "limited": true,
        "reachable": false,
        "proxy": "127.0.0.1:9050",
        "proxy_randomize_credentials": true
    },
    {
        "name": "ipv6",
        "limited": true,
        "reachable": false,
        "proxy": "127.0.0.1:9050",
        "proxy_randomize_credentials": true
    },
    {
        "name": "onion",
        "limited": false,
        "reachable": true,
        "proxy": "127.0.0.1:9050",
        "proxy_randomize_credentials": true
    },
],
"localaddresses": [
    {
        "address": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.onion",
        "port": 8333,
        "score": 4
    }
]
```

Hieraan zie je het volgende:

* IPv4 is niet bereikbaar
* IPv6 is niet bereikbaar
* Onion is wel bereikbaar \(tor\)
* Jouw onion-adres binnen het "localaddresses" blok

Heb je zowel tor als clearnet aan staan, dan zal je output er zo uit zien:

```json
"networks": [
    {
        "name": "ipv4",
        "limited": false,
        "reachable": true,
        "proxy": "",
        "proxy_randomize_credentials": false
    },
    {
        "name": "ipv6",
        "limited": false,
        "reachable": true,
        "proxy": "",
        "proxy_randomize_credentials": false
    },
    {
        "name": "onion",
        "limited": false,
        "reachable": true,
        "proxy": "127.0.0.1:9050",
        "proxy_randomize_credentials": true
    }
],
"localaddresses": [
    {
        "address": "69.69.69.69",
        "port": 8333,
        "score": 3
    },
    {
        "address": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.onion",
        "port": 8333,
        "score": 4
    }
]
```

Hieraan zie je het volgende:

* IPv4, IPv6 en onion \(tor\) zijn bereikbaar
* Jouw IP-adres binnen het "localaddresses" blok
* Jouw onion-adres binnen het "localaddresses" blok

Wijkt jouw output af? Ga terug naar start.