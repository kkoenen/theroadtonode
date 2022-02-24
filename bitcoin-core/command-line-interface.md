# Command line interface

{% hint style="info" %}
Tijd: 5 minuten
{% endhint %}

Nu je Core hebt opgestart en draaien op de achtergrond, wil je waarschijnlijk ook zien wat het doet. Een leeg scherm zegt zo weinig. Daar is de bitcoin-cli voor in het leven geroepen. Hiermee kun je allerlei commando's afvuren op bitcoind en kun je antwoorden verwachten. Alles is hiermee mogelijk; van het inzien van informatie over het Bitcoin netwerk, tot het aanmaken van complexe multi-sig constructies.

Een handig commando wat een overzicht van de mogelijkheden geeft, is "help".

```bash
bitcoin-cli help
```

Wil je een uitgebreidere uitleg, kun je terecht op websites zoals [Chainquery](https://chainquery.com/bitcoin-cli). Hier wordt de werking van de commando's uitvoerig beschreven.

Een paar handige commando's op een rij.

```bash
# Kijken op welk block we nu zitten
bitcoin-cli getblockcount

# Een kort netwerk overzicht van jouw node
bitcoin-cli getnetworkinfo

# Blockchain statistieken
bitcoin-cli getblockchaininfo
```

Core is continu in verandering. Het kan zijn dat bovenstaande commando's in de toekomst niet meer werken of anders heten.