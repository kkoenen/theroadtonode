# Lightning Terminal

{% hint style="info" %}
Tijd: 20 minuten
{% endhint %}

Lightning Terminal \([LiT](https://github.com/lightninglabs/lightning-terminal)\) geeft de gebruiker een mooi schilletje om bestaande tools van Lightning Labs. De tools zijn [Loop](https://lightning.engineering/loop), [Faraday](https://github.com/lightninglabs/faraday) en [Pool](https://lightning.engineering/pool). Op dit moment is Loop goed te gebruiken door middel van LiT. Ook Pool werkt prima, maar is nog in preview fase. Faraday heeft slechts 1 functionaliteit in LiT, maar meer zal toegevoegd worden.

### LiT use cases voor zover

* Een visueel overzicht van de balans van jouw Lightning kanalen.
* Kanalen balanceren via Loop met behulp van submarine swaps.
* Balanceren doe je met on-chain satoshis met Loop In.
* Je betaalt minder fees als je meerdere kanalen tegelijk balanceert.
* Eenvoudig satoshis versturen naar je on-chain wallet met Loop Out.
* Deelname aan de Lightning Network liquiditeit markt met Pool.
* Downloadbaar overzicht van channels in CSV formaat met Faraday.

Een demo video kun je [hier](https://lightning.engineering/static/terminal-2c00f93b062f44e5c2d1db067f7ee8cd.mp4) vinden.

### Benodigdheden

* [Faraday](https://docs.theroadtonode.com/lightning-extensies/faraday)
* [Golang](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren#golang)
* [Loop](https://docs.theroadtonode.com/lightning-extensies/loop)
* [NodeJS](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren#nodejs)
* [Pool](https://docs.theroadtonode.com/lightning-extensies/pool)
* [Yarn](https://docs.theroadtonode.com/raspberry-pi/algemene-dependencies-installeren#yarn)

## LND configureren voor LiT

Als je LND hebt geïnstalleerd zonder speciale installatie tags, dan moet je LND even opnieuw installeren. Als je de gids gevolgd hebt, hoef je je geen zorgen te maken en kun je deze paragraaf overslaan.

Aangezien we LND opnieuw installeren met nieuwe tags, dienen de macaroon bestanden \(bestanden die authenticatie verzorgen\) verwijderd te worden. Na het opnieuw opstarten van de verse installatie, zullen de bestanden opnieuw aangemaakt worden.

{% hint style="info" %}
Je channels en wallet blijven bestaan bij een herinstallatie van LND. Net als bij een update.

Heb je Thunderhub of andere tools draaien die op LND leunen? Dan dien je die na het opnieuw opstarten van LND ook opnieuw te starten.
{% endhint %}

Ga naar de bronbestanden map van LND om LND opnieuw te installeren met de juiste installatie tags.

```bash
cd ~/lnd
```

Installeer LND met de juiste tags.

```bash
make install tags="autopilotrpc signrpc walletrpc chainrpc invoicesrpc routerrpc watchtowerrpc monitoring"
```

Check voor macaroon bestanden.

```bash
ls -la ~/.lnd/data/chain/bitcoin/mainnet
```

De macaroon bestanden die je moet verwijderen zijn. `admin.macaroon`, `invoice.macaroon`, `readonly.macaroon` `router.macaroon` .

Verwijder de macaroon bestanden.

```bash
rm -i ~/.lnd/data/chain/bitcoin/mainnet/*.macaroon
```

Beantwoord de vragen met `yes` om elk bestand te verwijderen.

Start LND opnieuw. Hiermee worden ook nieuwe macaroons aangemaakt.

```bash
sudo systemctl restart lnd
```

Unlock je wallet met je wachtwoord.

```bash
lncli unlock
```

## Installatie

Navigeer terug naar je home directory.

```bash
cd ~
```

Download de broncode van Lightning Terminal.

```bash
git clone https://github.com/lightninglabs/lightning-terminal
```

Duik de code in.

```bash
cd lightning-terminal
```

Pak de laatste versie/tag/release.

```bash
git checkout v0.6.2-alpha
```

Installeer Lightning Terminal. Dit kan ongeveer een kwartier duren.

```bash
make install
```

## Configuratie

Het configureren van LiT gaat - zoals vele andere zaken in deze guide - via een configuratiebestand. In het bestand staan alle instellingen en tijdens het opstarten van LiT zullen de instellingen toegepast worden.

Maak de `.lit` map aan in je home directory.

```bash
mkdir ~/.lit
```

Maak het configuratiebestand aan.

```bash
nano ~/.lit/lit.conf
```

Kopieer het volgende in het configuratiebestand.

```toml
# Lightning Terminal applicatie instellingen
uipassword=BEDENK_IETS
network=mainnet
httpslisten=0.0.0.0:8443

# Remote LND instellingen
remote.lnd.rpcserver=127.0.0.1:10009
remote.lnd.macaroonpath=/home/ubuntu/.lnd/data/chain/bitcoin/mainnet/admin.macaroon
remote.lnd.tlscertpath=/home/ubuntu/.lnd/tls.cert

# Remote Faraday instellingen
faraday-mode=remote
remote.faraday.rpcserver=127.0.0.1:8465
remote.faraday.macaroonpath=/home/ubuntu/.faraday/mainnet/faraday.macaroon
remote.faraday.tlscertpath=/home/ubuntu/.faraday/mainnet/tls.cert

# Remote Loop instellingen
loop-mode=remote
remote.loop.rpcserver=127.0.0.1:11010
remote.loop.macaroonpath=/home/ubuntu/.loop/mainnet/loop.macaroon
remote.loop.tlscertpath=/home/ubuntu/.loop/mainnet/tls.cert

# Remote Pool instellingen
pool-mode=remote
remote.pool.rpcserver=127.0.0.1:12010
remote.pool.macaroonpath=/home/ubuntu/.pool/mainnet/pool.macaroon
remote.pool.tlscertpath=/home/ubuntu/.pool/mainnet/tls.cert
```

Hierboven staat `BEDENK_IETS` bij het wachtwoord. Pas dit aan naar iets wat je zelf prettig vindt. Sla de wijzigingen op met `Ctrl + X` en bevestig met `Y`.

## Firewall

We kunnen LiT benaderen over port 8443, vandaar dat we die open zetten.

```bash
sudo ufw allow 8443 comment "Port voor Lightning Terminal"
```

## Automatiseren

Hoe laat je Lightning Terminal automatisch opstarten? Daarvoor maken we weer een Lightning Terminal service bestand aan.

### LiT service aanmaken

```bash
sudo nano /etc/systemd/system/lit.service
```

Plak er dit in.

```toml
[Unit]
Description=Lightning Terminal
Requires=bitcoind.service
Requires=lnd.service
Requires=faraday.service
Requires=loop.service
Requires=pool.service
After=bitcoind.service
After=lnd.service
After=faraday.service
After=loop.service
After=pool.service

[Service]
User=ubuntu
ExecStart=/home/ubuntu/go/bin/litd
PIDFile=/home/ubuntu/.lit/lit.pid
Restart=always
TimeoutSec=120
RestartSec=30

[Install]
WantedBy=multi-user.target
```

Sla de wijzigingen op met `Ctrl + X` en bevestig met `Y`.

Breng het systeem op de hoogte van de nieuwe service.

```bash
sudo systemctl enable lit
```

Start de LiT service als volgt.

```bash
sudo systemctl start lit
```

Wil je zien of de service is opgestart, voer dan dit uit.

```bash
systemctl status lit
```

Wil je een overzicht van de status over meerdere sessies, gebruik dan dit commando.

```bash
sudo journalctl -f -u lit
```

## Lightning Terminal gebruiken

Open `https://IP VAN JE PI:8443` in de browser om in te loggen in Lightning Terminal en aan de slag te gaan met de tool. **Vergeet HTTPS er niet voor te zetten.**

### Walkthrough

Aan de slag met het LiT dashboard? Volg dan [deze walkthrough](https://github.com/lightninglabs/lightning-terminal/blob/master/doc/WALKTHROUGH.md) van Lightning Labs.

## Updaten

Ga naar de applicatie directory.

```bash
cd ~/lightning-terminal
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
git checkout -f <OUTPUT VAN DE VORIGE STAP> #bijvoorbeeld v0.6.2-alpha
```

Installeer Lightning Terminal.

```bash
make install
```

Herstart de LiT service.

```bash
sudo systemctl restart lit
```
