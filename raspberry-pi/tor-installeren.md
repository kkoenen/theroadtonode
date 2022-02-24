# Tor installatie

{% hint style="info" %}
Tijd: 5 minuten
{% endhint %}

Het [Tor netwerk](https://nl.wikipedia.org/wiki/Tor_%28netwerk%29) zorgt voor een extra laagje privacy bij het gebruik van Bitcoin. Het gebruik van tor heeft nog een bijkomend voordeel; je node is bereikbaar van buitenaf. Wat bedoelen we hiermee? Neem dit voorbeeld:

> Je hebt een mobiele wallet. Aangezien je de volledige Bitcoin blockchain niet op je mobiel hebt staan, moet je aan een externe bron informatie opvragen omtrent jouw wallet. Hiermee geef je informatie over jezelf vrij. Verbind je daarentegen je mobiele wallet met je eigen Bitcoin node, behoud je controle over dit soort informatie. Het probleem is dat jouw node thuis draait, dus de mobiele wallet moet verbinding maken met de computer thuis. Dit is lastig omdat internet service providers \(ISPs\), zoals Ziggo of KPN, het IP-adres van jouw huis iedere x-aantal dagen kunnen veranderen. Er zijn manieren om dit te omzeilen. ~~Voor een ietwat geavanceerdere oplossing, zie het hoofdstuk VPN via DuckDNS.~~ Een andere manier is tor. Door zogenaamde [hidden services](https://youtu.be/lVcbq_a5N9I) op te zetten, kun je alsnog van buitenaf met jouw node communiceren.

SSH in je Pi en voer het onderstaande uit om Tor te installeren.

```bash
sudo apt install tor -y
```

De juiste rechten moeten toegekend worden aan onze gebruiker genaamd pi.

```bash
sudo usermod -a -G debian-tor ubuntu
```

Om de `usermod` van kracht te laten zijn, log je uit door "exit" te typen en op enter te drukken. **Vergeet dit niet!** SSH daarna weer je Pi in.

Tot slot kunnen we Tor activeren en opstarten.

```bash
sudo systemctl enable tor
```

```bash
sudo systemctl start tor
```

Tor is nu geïnstalleerd en opgestart. We zullen meerdere keren in deze guide terug komen op de configuratie van Tor, maar dat kom je vanzelf tegen in andere hoofdstukken.

