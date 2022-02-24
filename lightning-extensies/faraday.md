# Faraday

{% hint style="info" %}
Tijd: 5 minuten
{% endhint %}

Faraday is een [boekhoud tool](https://lightning.engineering/posts/2020-09-15-faraday-accounting) voor Lightning en on-chain. Handig, want je bent altijd wel wat sats kwijt aan dingen als transactiekosten die vaak onopgemerkt gaan. Het biedt onder andere de mogelijkheid om een uitdraai te maken naar CSV formaat.

## Benodigdheden

* [Golang](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren#golang) **\(update naar de nieuwste versie voordat je begint\)**

## Installatie

Navigeer naar je home directory.

```bash
cd ~
```

Download de broncode.

```bash
git clone https://github.com/lightninglabs/faraday
```

Ga naar de faraday map.

```bash
cd faraday
```

Pak de laatste versie/tag/release.

```bash
git checkout v0.2.7-alpha
```

Installeer de faraday software.

```bash
make && make install
```

Er zijn twee tools geinstalleerd: `faraday` en `frcli`. Faraday is het programma zelf en frcli is een manier om met Faraday te communiceren. Test beiden of installatie gelukt is.

```bash
faraday --version

frcli --version
```

Je kunt nog een stapje verder gaan door Faraday op te starten met de juiste instellingen. In de tekst hieronder moet je op de plekken "VUL\_USERNAME\_IN" en "VUL\_PASSWORD\_IN" de gegevens invullen die je tijdens de [configuratie](https://docs.theroadtonode.com/bitcoin-core/configuratie-en-starten#authenticatie) van Core hebt aangemaakt.

```bash
faraday --lnd.macaroondir=/home/ubuntu/.lnd/data/chain/bitcoin/mainnet --lnd.tlscertpath=/home/ubuntu/.lnd/tls.cert --lnd.rpcserver=127.0.0.1:10009 --connect_bitcoin --bitcoin.host=127.0.0.1:8332 --bitcoin.user=VUL_USERNAME_IN --bitcoin.password=VUL_PASSWORD_IN
```

Zodra je te zien krijgt dat alles werkt, kun je faraday weer afsluiten met `Ctrl + C`.

## Firewall

Je kunt via gRPC Faraday benaderen op port 8465.

```bash
sudo ufw allow 8465 comment "Port voor Faraday"
```

## Automatiseren

Het automatiseren van Faraday is niet heel netjes te doen. Dit heeft te maken met het feit dat Faraday niet gebruik maakt van een configuratie bestand waar de instellingen in staan. In plaats daarvan moet je alle instellingen tijdens het opstarten meegeven.

```bash
sudo nano /etc/systemd/system/faraday.service
```

Plak dit erin.

```toml
[Unit]
Description=Faraday
Requires=bitcoind.service
Requires=lnd.service
After=bitcoind.service
After=lnd.service

[Service]
User=ubuntu
ExecStart=/home/ubuntu/go/bin/faraday --lnd.macaroondir=/home/ubuntu/.lnd/data/chain/bitcoin/mainnet --lnd.tlscertpath=/home/ubuntu/.lnd/tls.cert --lnd.rpcserver=127.0.0.1:10009 --connect_bitcoin --bitcoin.host=127.0.0.1:8332 --bitcoin.user=VUL_USERNAME_IN --bitcoin.password=VUL_PASSWORD_IN
Restart=always
TimeoutSec=120
RestartSec=30

[Install]
WantedBy=multi-user.target
```

Sla de wijzigingen op met `Ctrl + X` en bevestig met `Y`.

Breng het systeem op de hoogte van de nieuwe service.

```bash
sudo systemctl enable faraday
```

Start de Faraday service als volgt.

```bash
sudo systemctl start faraday
```

Wil je zien of de service is opgestart, voer dan dit uit.

```bash
systemctl status faraday
```

Wil je een overzicht van de status over meerdere sessies, gebruik dan dit commando.

```bash
sudo journalctl -f -u faraday
```
