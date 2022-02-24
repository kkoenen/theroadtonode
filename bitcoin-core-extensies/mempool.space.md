# Mempool.space

{% hint style="info" %}
Tijd: 20 minuten.
{% endhint %}

Met mempool.space kun je zelf de mempool van bitcoin in de gaten houden. Dat is handig om te bepalen hoeveel fee je moet betalen of om te zien of je transactie er al doorheen is. Door het zelf te hosten geef je geen data uit handen aan derden.

## Voorbereiding

1. [Electrs](https://docs.theroadtonode.com/bitcoin-core-extensies/electrs)
2. [NodeJS](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren#nodejs)

## Installatie

Haal de source code binnen.

```bash
git clone https://github.com/mempool/mempool
```

Ga de map in met `cd mempool`. Zet daarna de laatste release in een tijdelijke variabele genaamd `latestrelease` in je terminal.

```bash
latestrelease=$(curl -s https://api.github.com/repos/mempool/mempool/releases/latest|grep tag_name|head -1|cut -d '"' -f4)
```

Pak daarna de laatste versie met `git checkout $latestrelease`.

## Maria DB

Mempool.space heeft een database nodig om wat data in op te slaan. Daarom gebruikt het Maria DB.

```bash
sudo apt install mariadb-server mariadb-client -y
```

Log in bij Maria DB.

```bash
sudo mariadb -u root
```

Eenmaal in Maria DB kun je SQL code uitvoeren. Mempool.space heeft een database nodig en maak hem daarom aan. Voer heel het commando uit, dus ook met de `;`.

```sql
create database mempool;
```

Daarna maak je een gebruiker aan die toegang heeft tot die specifieke database.

```sql
grant all privileges on mempool.* to 'mempool'@'%' identified by 'mempool';
```

Tot slot kan je de database connectie sluiten met `quit;`.

## Backend

De tool bestaat uit twee onderdelen die los van elkaar draaien: een backend en een frontend. De frontend zie je in je browser en de backend verzocht de data voor de frontend. Ga de backend map in met `cd backend`. Installeer dan de backend afhankelijkheden met `npm install --prod` waarna je een build maakt `npm run build`.

De backend heeft een configuratie nodig en daarvoor hebben we een JSON bestand. Kopieer de voorbeeld configuratie naar een bruikbare.

```bash
cp mempool-config.sample.json mempool-config.json
```

Open het bestand `nano mempool-config.json`. Als het goed is lijkt het bestand op hetgeen dat hieronder staat. Hij is best groot en let goed op dat je de juist dingen wijzigt:

* `DIT_WEET_JE_A` en `DIT_WEET_JE_C`: [De user](https://docs.theroadtonode.com/bitcoin-core/configuratie-en-starten#authenticatie) die je hebt aangemaakt.
* `DIT_WEET_JE_B` en `DIT_WEET_JE_D`: [Het wachtwoord](https://docs.theroadtonode.com/bitcoin-core/configuratie-en-starten#authenticatie) dat je je hebt gekregen.

```json
{
  "MEMPOOL": {
    "NETWORK": "mainnet",
    "BACKEND": "electrum",
    "HTTP_PORT": 8999,
    "SPAWN_CLUSTER_PROCS": 0,
    "API_URL_PREFIX": "/api/v1/",
    "POLL_RATE_MS": 2000,
    "CACHE_DIR": "./cache",
    "CLEAR_PROTECTION_MINUTES": 20,
    "RECOMMENDED_FEE_PERCENTILE": 50,
    "BLOCK_WEIGHT_UNITS": 4000000,
    "INITIAL_BLOCKS_AMOUNT": 8,
    "MEMPOOL_BLOCKS_AMOUNT": 8,
    "PRICE_FEED_UPDATE_INTERVAL": 3600,
    "USE_SECOND_NODE_FOR_MINFEE": false,
    "EXTERNAL_ASSETS": []
  },
  "CORE_RPC": {
    "HOST": "127.0.0.1",
    "PORT": 8332,
    "USERNAME": "DIT_WEET_JE_A",
    "PASSWORD": "DIT_WEET_JE_B"
  },
  "ELECTRUM": {
    "HOST": "127.0.0.1",
    "PORT": 50001,
    "TLS_ENABLED": true
  },
  "ESPLORA": {
    "REST_API_URL": "http://127.0.0.1:3000"
  },
  "SECOND_CORE_RPC": {
    "HOST": "127.0.0.1",
    "PORT": 8332,
    "USERNAME": "DIT_WEET_JE_C",
    "PASSWORD": "DIT_WEET_JE_D"
  },
  "DATABASE": {
    "ENABLED": true,
    "HOST": "127.0.0.1",
    "PORT": 3306,
    "DATABASE": "mempool",
    "USERNAME": "mempool",
    "PASSWORD": "mempool"
  },
  "SYSLOG": {
    "ENABLED": true,
    "HOST": "127.0.0.1",
    "PORT": 514,
    "MIN_PRIORITY": "info",
    "FACILITY": "local7"
  },
  "STATISTICS": {
    "ENABLED": true,
    "TX_PER_SECOND_SAMPLE_PERIOD": 150
  },
  "BISQ": {
    "ENABLED": false,
    "DATA_PATH": "/bisq/statsnode-data/btc_mainnet/db"
  }
}
```

## Frontend

Nu de backend geregeld is kunnen we ons focussen op de frontend. Dat begint met naar de juiste map te navigeren.

```bash
cd ../frontend
```

Ook hier moet je weer de afhankelijkheden installeren met `npm install --prod`. Daarna kun je de voorbeeld configuratie kopieren, maar je hoeft hem niet aan te passen.

```bash
cp mempool-frontend-config.sample.json mempool-frontend-config.json
```

{% hint style="info" %}
Het kan voor komen dat de onderstaande build faalt. Probeer het dan later nog een keer.
{% endhint %}

De backend kun je builden met `npm run build` (kan vrij lang duren) en tot slot de output van de build verplaatsen naar een andere map. Nginx zal verkeer doorsturen naar die map.

```bash
sudo rsync -av --delete dist/ /var/www/
```

## Server

De website is alleen beschikbaar via Nginx.

```bash
sudo apt install -y nginx
```

Ga na de installatie van Nginx terug naar de root van het project met `cd ~/mempool`. Installeer vanaf daar de Nginx configuratie van mempool.

```bash
sudo cp nginx.conf nginx-mempool.conf /etc/nginx/
```

Pas daarna twee regels van de configuratie aan met `sudo nano /etc/nginx/nginx.conf`. Een scherm zal zich openen waain je in de bovenste regel `user nobody;` ziet staan. Pas het aan naar `user ubuntu;`. Scroll door naar onder waar je `listen 127.0.0.1:80;` ziet. Dat moet `listen 0.0.0.0:80;` worden. Sla het op met `Ctrl + X` en bevestigen met `Y`. Je kunt de configuratie controleren met `sudo nginx -t` waarvan de output moet lijken op:

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Om het Nginx verhaal af te ronden herstart je Nginx met `sudo systemctl restart nginx`.

## Firewall

Het bereiken van mempool.space loopt via het normale internetverkeer. Dat is poort 80. Nginx handelt verder af waar het verkeer naartoe gaat.

```bash
sudo ufw allow 80 comment "Port voor normaal internetverkeer"
```

## Automatiseren

De frontend van mempool.space staat vrolijk in een mapje waar je niet naar om hoeft te kijken. Met het is wel van belang dat de backend op de achtergrond draait. Hier maken we een service voor aan.

```bash
sudo nano /etc/systemd/system/mempool.service
```

Zet daar de volgende tekst in.

```toml
[Unit]
Description=Mempool
Requires=bitcoind.service
Requires=electrs.service
After=bitcoind.service
After=electrs.service

[Service]
WorkingDirectory=/home/ubuntu/mempool/backend
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

Sla het op met `Ctrl + X` en bevestigen met `Y`.

Enable en start de service met deze twee commando's:

```bash
sudo systemctl enable mempool

sudo systemctl start mempool
```

Met `sudo systemctl status mempool` kun je zien of alles goed draait en krijg je een output die lijkt op het onderstaande.

```bash
● mempool.service - Mempool
     Loaded: loaded (/etc/systemd/system/mempool.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-01-31 09:59:52 UTC; 25s ago
   Main PID: 209191 (npm run start)
      Tasks: 27 (limit: 4203)
     CGroup: /system.slice/mempool.service
             ├─209191 npm run start
             ├─209202 sh -c node --max-old-space-size=2048 dist/index.js
             └─209203 node --max-old-space-size=2048 dist/index.js

Jan 31 10:00:15 ubuntu npm[209203]: Jan 31 10:00:15 [209203] DEBUG: Fetched transaction 14 / 19
Jan 31 10:00:15 ubuntu npm[209203]: Jan 31 10:00:15 [209203] DEBUG: Fetched transaction 15 / 19
Jan 31 10:00:15 ubuntu npm[209203]: Jan 31 10:00:15 [209203] DEBUG: Fetched transaction 16 / 19
```
