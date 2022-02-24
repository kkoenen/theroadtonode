# Thunderhub

{% hint style="info" %}
Tijd: 20 minuten
{% endhint %}

Thunderhub is net als Ride The Lightning een beheertool voor jouw node. Bezoek [de website](https://www.thunderhub.io/) om alle features te ontdekken.

### Benodigdheden

* [NodeJS](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren#nodejs)

## Broncode

Download de broncode van Thunderhub.

```bash
git clone https://github.com/apotdevin/thunderhub
```

Ga naar de code.

```bash
cd thunderhub
```

Pak de laatste versie/tag/release.

```bash
git checkout v0.13.6
```

Haal alle benodigde software dependencies binnen.

```bash
npm install
```

## Configuratie

Maak het bestand `.env.local`:

```bash
nano .env.local
```

Plak het volgende erin:

```toml
ACCOUNT_CONFIG_PATH='/home/ubuntu/.thunderhub/config.yaml'
PORT=4000
```

Sla het op met `Ctrl + X` en bevestig met `Y`. Dit is een minimale setup qua configuratie. Meer parameters die je kunt gebruiken vind je in het `.env` bestand.

Nu gaan we terug naar je home directory en maken daar een map aan met de naam .thunderhub. In deze map maken we een config bestand aan voor Thunderhub.

```bash
mkdir ~/.thunderhub && nano ~/.thunderhub/config.yaml
```

Plak dit erin:

```yaml
masterPassword: 'password'
accounts:
  - name: '<kies_een_naam>'
    serverUrl: '127.0.0.1:10009'
    lndDir: '/home/ubuntu/.lnd'
```

Het `masterPassword` kun je naar wens aanpassen en heb je nodig om in te loggen in Thunderhub in je browser straks. Nadat je Thunderhub voor de eerste keer hebt opgestart, wordt dit wachtwoord herschreven met een hashed waarde. Sla het op met `Ctrl + X` en bevestig met `Y`.

## Installatie

We gaan weer terug naar de map met de Thunderhub software als je daar nog niet was:

```bash
cd ~/thunderhub
```

Installeer Thunderhub:

```bash
npm run build
```

## Firewall

Zet port 4000 open.

```bash
sudo ufw allow 4000 comment "Port voor Thunderhub"
```

Mocht je Thunderhub van buiten je netwerk willen gebruiken, moet je port 4000 op je router opengooien en verkeer doorsturen naar je Pi.

## Automatiseren

Hoe laat je Thunderhub automatisch opstarten? Daarvoor maken we een Thunderhub service bestand aan:

```bash
sudo nano /etc/systemd/system/thunderhub.service
```

Plak er dit in.

```toml
[Unit]
Description=Thunderhub
Requires=lnd.service
After=lnd.service

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/thunderhub
ExecStart=npm start
Restart=always
TimeoutSec=120
RestartSec=30

[Install]
WantedBy=multi-user.target
```

Sla het weer op met `Ctrl + X` en bevestig met `Y`. De applicatie wordt gestart op poort 4000. Standaard is dit poort 3000, maar deze poort wordt ook gebruikt voor de [Ride The Lightning](ride-the-lightning.md) applicatie.

Het systeem moet op de hoogte gesteld worden van de nieuwe service en kan daarna gestart worden.

```bash
sudo systemctl enable thunderhub
```

```bash
sudo systemctl start thunderhub
```

Wil je zien of alles goed is opgestart, voer dan dit uit:

```bash
systemctl status thunderhub
```

Wil je een overzicht van de status over meerdere sessie, gebruik dan dit:

```bash
sudo journalctl -f -u thunderhub
```

## Gebruiken

Ga naar `http://[het ip adres van je Pi]:4000` in je browser om Thunderhub te openen.  
Gebruik het wachtwoord `password` om in te loggen tenzij je een ander wachtwoord hebt ingevuld in het `config.yaml` bestand.

## Updaten

{% hint style="warning" %}
Bij het upgraden van een Thuderhub van voor v0.13.0 naar v0.13.0+, zijn wijzigingen aan het `.env.local` bestand nodig. Neem even een kijkje bij de [paragraaf "configuratie"](#configuratie).
{% endhint %}

Ga naar de applicatie directory.

```bash
cd ~/thunderhub
```

Update de repository met de laatste wijzigingen via Git.

```bash
git fetch --all
```

Toon de laatste versie/tag/release.

```bash
git describe --tags `git rev-list --tags --max-count=1`
```

Haal de wijzigingen op van de laatste versie.

```bash
git checkout -f <OUTPUT VAN DE VORIGE STAP> # Bijvoorbeeld v0.13.6
```

Installeer de software.

```bash
npm install

npm run build
```

Herstart de Thunderhub service.

```bash
sudo systemctl restart thunderhub
```

Thunderhub is nu bijgewerkt!
