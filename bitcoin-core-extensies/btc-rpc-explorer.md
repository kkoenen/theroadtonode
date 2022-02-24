# BTC RPC Explorer

{% hint style="info" %}
Tijd: 20 minuten
{% endhint %}

Een eigen block explorer helpt je in het waarborgen van je privacy, omdat je geen publieke block explorer meer hoeft te raadplegen. Zo kun je ongestoord kijken of jouw transacties nog in de mempool zitten of al gevalideerd zijn.

De Block Explorer die we gaan gebruiken is [BTC RPC Explorer](https://github.com/janoside/btc-rpc-explorer) van Dan Janosik.

{% hint style="info" %}
Let op: dit onderdeel is afhankelijk van de [NodeJS installatie](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren#nodejs). Je kunt niet verder als je NodeJS niet geïnstalleerd hebt op de Raspberry Pi.
{% endhint %}

## Voorbereiding

Er zijn een aantal voorwaarden waaraan je moet voldoen om deze block explorer te kunnen draaien:

1. Je moet een full node hebben draaien waarvan de blockchain compleet is.
2. Je node moet ook een index hebben van alle transacties.
3. Je moet een recente versie van NodeJS hebben draaien.

### Full node

Je bent bezig met de road to node. Als je het in de juiste volgorde aan het doen bent dan heb je [Bitcoin Core](https://docs.theroadtonode.com/bitcoin-core/installatie) inmiddels geïnstalleerd. Zo niet doe dat dan eerst.

### Transactieindex

Login op je Pi en open het bitcoin configuratiebestand.

```bash
nano /home/ubuntu/.bitcoin/bitcoin.conf
```

Controleer of de regel `txindex=1` erin voorkomt. Zo niet, voeg deze dan toe en sla je wijzigingen op met `Ctrl + X` gevolgd door `Y`.

Herstart vervolgens `bitcoind`.

```bash
sudo systemctl restart bitcoind
```

## Firewall

Ook hier dient de firewall geüpdate te worden. De port waarover BTC RPC Explorer zich toont is 3002.

```bash
sudo ufw allow 3002 comment "Port voor BTC-RPC-Explorer"
```

## Installatie

Zorg dat je in de home directory bent.

```bash
cd ~
```

Haal de broncode binnen.

```bash
git clone https://github.com/janoside/btc-rpc-explorer
```

Ga de BTC RPC Explorer map in.

```bash
cd btc-rpc-explorer
```

Pak de nieuwste release.

```bash
git checkout v3.3.0
```

Installeer BTC RPC Explorer en maak daarna het configuratie bestand `.env` aan.

```bash
npm install

nano .env
```

Plak daar de volgende regels in.

```bash
BTCEXP_HOST=IP-ADRES VAN PI
BTCEXP_PORT=3002

BTCEXP_BITCOIND_HOST=127.0.0.1
BTCEXP_BITCOIND_PORT=8332
BTCEXP_BITCOIND_COOKIE=/home/ubuntu/.bitcoin/.cookie
BTCEXP_BITCOIND_RPC_TIMEOUT=5000

BTCEXP_PRIVACY_MODE=true
```

Pas de tekst `IP-ADRES VAN PI` aan naar wat voor jou van toepassing is. Vervang het dus met iets dat lijkt op `192.168.1.6`. Sla het bestand op met `Ctrl + X` gevolgd door `Y`.

{% hint style="info" %}
Open het bestand `.env-sample` om te zien welke opties er nog meer zijn.
{% endhint %}

## Automatiseren

Zorg er nu voor dat de BTC-RPC-Explorer automatisch start en draait als een service wanneer je Pi opnieuw opstart.

```bash
sudo nano /etc/systemd/system/btc-rpc-explorer.service
```

De inhoud van het bestand moet er zo uit zien. Met name het pad naar de `WorkingDirectory` is belangrijk.

```toml
[Unit]
Description=BTC-RPC-Explorer
Requires=bitcoind.service
After=bitcoind.service

[Service]
WorkingDirectory=/home/ubuntu/btc-rpc-explorer
ExecStart=npm run start
User=ubuntu
Group=ubuntu
Type=simple
Restart=on-failure
TimeoutSec=120
RestartSec=30

[Install]
WantedBy=multi-user.target
```

Sla het bestand op met `Ctrl + X` gevolgd door `Y`.

Met de volgende twee commando's activeer en start je de service.

```bash
sudo systemctl enable btc-rpc-explorer

sudo systemctl start btc-rpc-explorer
```

Open nu in Firefox op je PC een tabblad naar `http://IP-ADRES VAN PI:3002` om te zien of het werkt. Bijvoorbeeld `http://192.168.1.6:3002`.

## Updaten

Als er een nieuwe versie beschikbaar is voor BTC RPC Explorer kun je eenvoudig updaten door de nieuwe source met Git op te halen en deze te installeren. Wel eerst even de service stoppen en naderhand weer starten zoals hieronder staat aangegeven.

```
sudo systemctl stop btc-rpc-explorer
```

Ga de BTC RPC Explorer map in.

```
cd ~/btc-rpc-explorer
```

Update de repository met de laatste wijzigingen via Git.

```bash
git fetch --all
```

Toon de laatste versie/tag/release.

```
git describe --tags `git rev-list --tags --max-count=1`
```

Haal de wijzigingen op van de laatste versie.

```bash
git checkout -f <OUTPUT VAN DE VORIGE STAP> #voorbeeld: v3.3.0
```

Installeer via NPM.

```bash
npm install
```

Start de service weer op nadat installeren klaar is.

```
sudo systemctl start btc-rpc-explorer
```

## Tor

De service kun je ook beschikbaar maken via tor. Allereerst passen we de tor configuratie aan om een nieuwe hidden service te maken.

```bash
sudo nano /etc/tor/torrc
```

In het bestand dat zich opent voeg je onderaan de volgende drie regels toe.

```bash
HiddenServiceDir /var/lib/tor/btc-rpc-explorer
HiddenServiceVersion 3
HiddenServicePort 3002 127.0.0.1:3002
```

Nadat tor is geconfigureerd moeten we de juiste mappen aanmaken en rechten toekennen.

```bash
sudo mkdir /var/lib/tor/btc-rpc-explorer
sudo chown -R debian-tor:debian-tor /var/lib/tor/btc-rpc-explorer
sudo chmod 700 /var/lib/tor/btc-rpc-explorer
```

Herstart tor met:

```bash
sudo systemctl restart tor
```

Het onion-adres vind je met het volgende commando:

```bash
sudo cat /var/lib/tor/btc-rpc-explorer/hostname
```

Vul deze (met portnummer) in in je tor browser. De BTC RPC Explorer homepage zou moeten verschijnen.

## Koppeling met Electrum Server

Als je de Electrum Server (Electrs, Electrum X, of Electrum Personal Server) guide gevolgd hebt, kun je BTC RPC Explorer meteen hierop aansluiten voor verbeterde privacy. Pas het configuratie bestand van BTC RPC Explorer aan.

```bash
nano ~/btc-rpc-explorer/.env
```

Voeg onderaan de volgende twee regels toe:

```toml
BTCEXP_ADDRESS_API=electrum
BTCEXP_ELECTRUM_SERVERS=tcp://127.0.0.1:50001
```

Als je er zeker van wil zijn dat de explorer enkel gebruik maakt van jouw eigen backend, kun je ook de service aanpassen en meegeven dat hij enkel mag draaien als de Electrum Server draait. In het onderstaande voorbeeld staat Electrs als voorbeeld, maar pas dit aan wat voor jou van toepassing is.

```bash
sudo nano /etc/systemd/system/btc-rpc-explorer.service
```

De aangepaste service ziet er dan zo uit:

```toml
[Unit]
Description=BTC-RPC-Explorer
Requires=electrs.service
After=electrs.service

[Service]
WorkingDirectory=/home/ubuntu/btc-rpc-explorer
ExecStart=npm run start
User=ubuntu
Group=ubuntu
Type=simple
Restart=on-failure
TimeoutSec=120
RestartSec=30

[Install]
WantedBy=multi-user.target
```

Op deze manier wacht Electrs op Bitcoin Core en wacht BTC RPC Explorer op Electrs.

Na het aanpassen van services moet systemctl even opnieuw geladen worden met `systemctl daemon-reload`. Hier is je wachtwoord voor nodig.

Herstart de tot slot de explorer service om de nieuwe configuratie van kracht te laten zijn en gebruik te maken van je eigen backend.

```bash
sudo systemctl restart btc-rpc-explorer
```
