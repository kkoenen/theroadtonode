# Electrum X

{% hint style="info" %}
Tijd: 15 minuten en 7 dagen wachten op synchronisatie/indexering
{% endhint %}

Met behulp van Electrum X kun je gebruik maken van het [Electrum protocol](https://electrumx-spesmilo.readthedocs.io/en/latest/protocol-basics.html). Dit protocol laat wallets en andere software met nodes communiceren. Op andere plekken in de guide kun je tools en wallets die via het Electrum protocol communiceren direct aansluiten op je node. Een functionaliteit die de privacy bevordert. Zo kun je de [BTC RPC Explorer](https://docs.theroadtonode.com/bitcoin-core-extensies/btc-rpc-explorer#koppeling-met-electrum-x) ook aansluiten op je eigen Electrum X server. Als je dan in het vervolg informatie over je transacties of adressen van jezelf opzoekt, zal niemand dat kunnen zien. Zo kan niemand transacties aan jou linken en geniet je meer privacy.

Het kan zo'n 7 dagen duren voordat Electrum X volledig geïndexeerd en bruikbaar is. Tot die tijd kan je er niks mee.

## Installatie

Zorg dat je in de home directory zit.

```bash
cd ~
```

Installeer de volgende packages op je Raspberry.

```bash
sudo apt install python3-pip build-essential libc6-dev libncurses5-dev libncursesw5-dev libreadline-dev libleveldb-dev
```

Door middel van `pip` kun je een Python-specifieke package installeren die nodig is voor Electrum X.

```bash
sudo pip3 install plyvel
```

Clone de broncode voor Electrum X.

```bash
git clone https://github.com/spesmilo/electrumx
```

Navigeer de zojuist geclonede map in.

```bash
cd electrumx
```

Pak de laatste versie.

```bash
git checkout 1.16.0
```

Installeer tot slot Electrum X.

```bash
sudo python3 setup.py install
```

## Configuratie

Net als met alle andere tools in deze guide kun je ook Electrum X op een bepaalde manier configureren. Allereerst zorgen we voor een locatie waar de database van Electrum X opgeslagen wordt. Daarna maken we met behulp van `openssl` sleutels aan. Tot slot stellen we Electrum X in met behulp van een configuratie bestand.

Maak een `.electrumx` map in de home directory met daarin weer een `db` map.

```bash
mkdir ~/.electrumx

mkdir ~/.electrumx/db
```

Ga de map in.

```bash
cd ~/.electrumx
```

Voer de volgende drie openssl commando's uit voor het aanmaken van een een stel sleutels en certificaten. Tijdens dit proces zul je wat informatie moeten invoeren, maar dat zie je vanzelf.

```bash
openssl genrsa -out server.key 2048
openssl req -new -key server.key -out server.csr
openssl x509 -req -days 1825 -in server.csr -signkey server.key -out server.crt
```

De laatste stap van het configureren betreft het aanmaken van het configuratie bestand. Maak een bestand aan.

```bash
nano ~/.electrumx/electrumx.conf
```

Zet daar de volgende tekst in.

```bash
NET=mainnet
COIN=Bitcoin
CACHE_MB=500
DB_ENGINE=leveldb
DB_DIRECTORY=/home/ubuntu/.electrumx/db
DAEMON_URL=http://USERNAME:PASSWORD@127.0.0.1
SSL_CERTFILE=/home/ubuntu/.electrumx/server.crt
SSL_KEYFILE=/home/ubuntu/.electrumx/server.key
SERVICES=tcp://:50001,ssl://:50002,wss://:50004,rpc://
```

Voordat je het opslaat moet je een tweetal aanpassingen maken. Vul op de plekken `USERNAME` en `PASSWORD` de gebruikersnaam-wachtwoord combinatie die je hebt aangemaakt in het [authenticatie deel van de Bitcoin Core configuratie](https://docs.theroadtonode.com/bitcoin-core/configuratie-en-starten#authenticatie). Zodra alles goed staat kun je het bestand opslaan met `Ctrl + X` en bevestigen met `Y`.

## Firewall en router

In principe kun je een lokale tool zoals [BTC RPC Explorer](https://docs.theroadtonode.com/bitcoin-core-extensies/btc-rpc-explorer#koppeling-met-electrum-x) direct aansluiten op Electrum X. Hier hoef je geen ports voor open te zetten, aangezien lokale connecties \(dus van de Pi naar de Pi\) altijd worden toegestaan. Wil je echter gebruik maken van Electrum X in een app zoals BlueWallet, dan dien je de volgende commando's uit te voeren om twee ports open te zetten.

```bash
sudo ufw allow 50001
sudo ufw allow 50002
```

Wil je van buiten je netwerk Electrum X als backend gebruiken, dan moet je dezelfde ports open gooien op je router en doorlussen naar je Pi.

## Automatiseren

Electrum X wil je altijd op de achtergrond hebben draaien. Daarom gaan we het opstarten automatiseren middels een service. Maak een service aan.

```bash
sudo nano /etc/systemd/system/electrumx.service
```

Zet daar de volgende tekst in.

```toml
[Unit]
Description=Electrum X Server
Requires=bitcoind.service
After=network.target

[Service]
User=ubuntu
EnvironmentFile=/home/ubuntu/.electrumx/electrumx.conf
ExecStart=/usr/local/bin/electrumx_server
Restart=always
TimeoutSec=120
RestartSec=30

[Install]
WantedBy=multi-user.target
```

Sla het op met `Ctrl + X` en bevestigen met `Y`.

Enable en start de service met deze twee commando's:

```bash
sudo systemctl enable electrumx

sudo systemctl start electrumx
```

Na ongeveer een minuutje opstarten kun je aan Electrum X wat informatie opvragen.

```bash
electrumx_rpc getinfo
```

Als je output lijkt op het onderstaande ben je klaar!

```bash
{
    "coin": "Bitcoin",
    "daemon": "127.0.0.1:8332/",
    "daemon height": 662952,
    "db height": 662952,
    "db_flush_count": 5797,
    "groups": 0,
    "history cache": "0 lookups 0 hits 0 entries",
    "merkle cache": "0 lookups 0 hits 0 entries",
    "peers": {
        "bad": 14,
        "good": 83,
        "never": 14,
        "stale": 7,
        "total": 118
    },
    "pid": 12650,
    "request counts": {
        "blockchain.block.header": 1,
        "blockchain.headers.subscribe": 560,
        "getinfo": 1,
        "server.features": 560,
        "server.peers.subscribe": 560,
        "server.version": 560
    },
    "request total": 2242,
    "sessions": {
        "count": 1,
        "count with subs": 0,
        "errors": 0,
        "logged": 0,
        "pending requests": 1,
        "subs": 0
    },
    "tx hashes cache": "0 lookups 0 hits 0 entries",
    "txs sent": 0,
    "uptime": "21d 01h 08m",
    "version": "ElectrumX 1.16.0"
}
```

## Updaten

Stop de Electrum X service.

```bash
sudo systemctl stop electrumx
```

Ga naar de applicatie directory.

```bash
cd ~/electrumx
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
git checkout <OUTPUT VAN DE VORIGE STAP> # bijvoorbeeld 1.16.0
```

Installeer de software.

```bash
sudo python3 setup.py install
```

Start de Electrum X service.

```bash
sudo systemctl start electrumx
```

Electrum X is nu bijgewerkt!

