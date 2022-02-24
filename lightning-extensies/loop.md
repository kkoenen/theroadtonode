# Loop

{% hint style="info" %}
Tijd: 5 minuten
{% endhint %}

Om kanalen te kunnen balanceren heb je verschillende tools, Loop is zo'n tool.

## Benodigdheden

* [Golang](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren#golang) **\(update naar de nieuwste versie voordat je begint\)**

## Installatie

Ga eerst naar je home directory.

```bash
cd ~
```

Download de broncode.

```bash
git clone https://github.com/lightninglabs/loop
```

Ga de map in.

```bash
cd loop
```

Pak de laatste versie/tag/release.

```bash
git checkout v0.17.0-beta
```

Ga naar `loop/cmd`.

```bash
cd loop/cmd
```

Installeer de `loopd` software.

```bash
go install ./...
```

Test of het gelukt is door Loop tijdelijk op te starten.

```bash
loopd
```

In de output zul je lezen dat Loop verbonden is met je LND en met `Ctrl + C` kun je `loopd` weer stop zetten.

Naast het Loop programma is er ook een CLI geinstalleerd. Je kunt deze controleren met `loop --version`.

## Firewall

Je kunt van buitenaf Loop benaderen op twee verschillende poorten.

```bash
sudo ufw allow 8081 comment "Port voor REST API van Loop"

sudo ufw allow 11010 comment "Port voor RPC van Loop"
```

## Automatiseren

Wil je Loop in de achtergrond draaien dan heb je weer een service nodig.

```bash
sudo nano /etc/systemd/system/loop.service
```

Plak dit erin.

```toml
[Unit]
Description=Loop
Requires=bitcoind.service
Requires=lnd.service
After=bitcoind.service
After=lnd.service

[Service]
User=ubuntu
ExecStart=/home/ubuntu/go/bin/loopd
Restart=always
TimeoutSec=120
RestartSec=30

[Install]
WantedBy=multi-user.target
```

Sla de wijzigingen op met `Ctrl + X` en bevestig met `Y`.

Breng het systeem op de hoogte van de nieuwe service.

```bash
sudo systemctl enable loop
```

Start de loop service als volgt.

```bash
sudo systemctl start loop
```

Wil je zien of de service is opgestart, voer dan dit uit.

```bash
systemctl status loop
```

Wil je een overzicht van de status over meerdere sessies, gebruik dan dit commando.

```bash
sudo journalctl -f -u loop
```

## Updaten

Ga naar de applicatie directory.

```bash
cd ~/loop
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
git checkout <OUTPUT VAN DE VORIGE STAP> #bijvoorbeeld v0.17.0-beta
```

Ga naar `cmd`.

```bash
cd cmd
```

Installeer de `loopd` software.

```bash
go install ./...
```

Herstart de services van Loop en LiT (als je dat hebt draaien).

```bash
sudo systemctl restart loop

sudo systemctl restart lit
```