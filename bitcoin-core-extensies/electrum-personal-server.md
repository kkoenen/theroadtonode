# Electrum Personal Server

{% hint style="info" %}
Tijd: 30 minuten
{% endhint %}

Als je de wallet software van [Electrum](https://electrum.org/#home) gebruikt is het een logische stap om ervoor te zorgen dat Electrum babbelt met je eigen node. Dit kan niet rechtstreeks maar moet verlopen via een Electrum Server. De Electrum wallet software connect namelijk normaal gesproken met Electrum Servers waarvan je niet weet wie die in de gaten houdt. De wallet houdt namelijk de adressen in de gaten waar je Bitcoin op hebt ontvangen en zo kunnen verschillende adressen terug te herleiden zijn naar jouw IP. Dat wil je natuurlijk niet. De oplossing: draai je eigen Electrum Server. Er zijn verschillende forks beschikbaar zoals [ElectrumX](https://github.com/spesmilo/electrumx), [ElectRS](https://github.com/romanz/electrs) en Electrum Personal Server. Dit document beschrijft de installatie van [Electrum Personal Server](https://github.com/chris-belcher/electrum-personal-server) omdat dit momenteel de meest lightweight implementatie is. In TRTN is ook een handleiding opgenomen voor het installeren voor [ElectrumX](https://docs.theroadtonode.com/bitcoin-core-extensies/electrum-x). Je hebt niet beide nodig dus bedenk vooraf welke fork je nodig hebt. Een voordeel van EPS is dat deze vrij licht en simpel is. Een nadeel is dat er maar 1 connectie tegelijk mogelijk is. Dat kan een nadeel zijn wanneer je op een later moment misschien met zowel een desktop client als Blue Wallet tegelijk wilt verbinden.

## Voorbereidingen

SSH in je Pi en voer het installatie commando uit voor de nodige tools.

```bash
sudo apt install -y python3 python3-pip
sudo pip3 install setuptools
```

Check dat er in `bitcoin.conf` een regel staat `disablewallet = 0`.

```bash
nano /home/ubuntu/.bitcoin/bitcoin.conf
```

Als dat nog niet het geval is voeg je deze toe, sla je het bestand op \(met `Ctrl + X`\) en herstart je de bitcoind service.

```bash
sudo systemctl restart bitcoind
```

EPS heeft een wallet nodig in Bitcoin Core. Maak deze aan als deze nog niet aanwezig is.

```bash
bitcoin-cli createwallet electrumpersonalserver true
```

Maak tot slot de volgende map in je homedir aan en ga erin.

```bash
cd ~
mkdir .eps
cd .eps
```

## Installatie

Nu kunnen we verder gaan met het downloaden van de source en het installeren van Electrum Personal Server. Zorg er voor dat je de meest recente versie van EPS hebt.

Download vanaf de Github de [nieuwste release](https://github.com/chris-belcher/electrum-personal-server/releases) van Electrum Personal Server. Kopiëer de link van Source code \(tar.gz\), pak deze uit en gooi de download weg.

```bash
wget https://github.com/chris-belcher/electrum-personal-server/archive/eps-v0.2.2.tar.gz
tar -xvf eps-v0.2.2.tar.gz
rm eps-v0.2.2.tar.gz
```

Maak een kopie van het configuratie bestand en pas deze aan.

```bash
cp electrum-personal-server-eps-v0.2.2/config.ini_sample config.cfg
nano config.cfg
```

Deze config-file bevat veel commentaar en tips wat je waar moet invullen. Kijk in ieder geval even naar de volgende secties:

* `[master-public-keys]` — zoek in Electrum je xpub op via het menu `Wallet` &gt; `Information`
* `[bitcoin-rpc]` — `datadir = /home/ubuntu/.bitcoin`
* `[electrum-server]` — kies hier voor `host = 0.0.0.0` \(ipv 127.0.0.1\)
* `wallet_filename` — maak daar `wallet_filename = electrumpersonalserver` van als je eerder hier een speciale wallet voor hebt aangemaakt. Of vul hier `wallet.dat` in als je gebruikt maakt van de default wallet van Bitcoin Core zodat het voor EPS duidelijk is welke walletfile hij moet gebruiken. Mocht je namelijk later ook `Specter` willen gebruiken dan maakt die ook wallets aan en als je dan geen `wallet_filename` hebt opgegeven start EPS niet.

{% hint style="info" %}
let bij het laatste commando in onderstaand blokje op de punt \(.\) aan het einde van de regel
{% endhint %}

```bash
cd electrum-personal-server-eps-v0.2.2
pip3 install wheel
pip3 install use .
```

## Opstarten

Electrum Personal Server is nu geïnstalleerd \(in ~/.local/bin\) en klaar om voor het eerst te worden gestart. Deze roep je aan door ook het pad naar de config-file mee te geven. Dit kan ~10 minuten duren. Daarna zal het programma stoppen.

```bash
~/.local/bin/electrum-personal-server ~/.eps/config.cfg
```

Nu moet EPS de blockchain scannen op zoek naar adressen en transacties horend bij de xpubs die je in de `config.cfg` hebt opgenomen. Als er wordt gevraagd naar de startdatum is het handig dat je de datum opgeeft van de eerste transactie naar een adres horend bij die wallet. Dan hoeft EPS geen blokken te scannen waar toch geen transacties in staan die horen bij jouw xpub.

```bash
~/.local/bin/electrum-personal-server --rescan ~/.eps/config.cfg
```

Je kunt de voorgang monitoren door de log-file van bitcoin in de gaten te houden via een tweede Putty venster:

```bash
sudo tail -f -n 200 /home/ubuntu/.bitcoin/debug.log
```

Als deze klaar is start je EPS nog een keer.

```bash
~/.local/bin/electrum-personal-server ~/.eps/config.cfg
```

## Automatiseren

Nu moeten we alleen nog zorgen de EPS als een Service gaat draaien. Je hebt als het goed is twee vensters open. Eentje met de output van de bitcoin log en eentje waarin EPS draait.

```bash
sudo nano /etc/systemd/system/eps.service
```

```toml
[Unit]
Description=Electrum Personal Server
Requires=bitcoind.service
After=bitcoind.service

[Service]
ExecStart=/usr/bin/python3 /home/ubuntu/.local/bin/electrum-personal-server /home/ubuntu/.eps/config.cfg
User=ubuntu
Group=ubuntu
Type=simple
KillMode=process
TimeoutSec=60
Restart=always
RestartSec=60

[Install]
WantedBy=multi-user.target
```

Sluit het bestand af en sla de wijzigingen op. Vervolgens de service inschakelen en starten.

```bash
sudo systemctl enable eps
sudo systemctl start eps
```

Je kunt de voortgang van het opstarten van EPS volgen door het log-bestand te bekijken:

```bash
sudo tail -f -n 200 /tmp/electrumpersonalserver.log
```

## Firewall

Ook hier dient de firewall geüpdate te worden. De port waarover Electrum Personal Server zich toont is 50002.

```bash
sudo ufw allow 50002
```

## Electrum Wallet

1. Start nu Electrum Wallet op je PC met `"C:\Program Files (x86)\Electrum\electrum-4.1.5.exe" --server IP-ADRES VAN PI:50002:s --oneserver`
2. Kies in het Tools-menu voor Network
3. Open de Server-tab
4. Haal het vinkje weg bij “select server automatically”
5. Vul bij server het IP-adres in van je RPi4
6. Klik op Close

Als alles goed is gegaan heb je rechtsonder in het venster van Electrum een groene cirkel die aangeeft verbonden te zijn. Je kunt ook nog

1. Kies in het View-menu voor Show Console
2. Klik op de Console tab

Hier zie je nu dat je bent verbonden met een EPS en jouw Bitcoin Node!

## Updaten

Stop de service.

```bash
sudo systemctl stop eps
```

Download vanaf de Github de tarball van de [nieuwste release](https://github.com/chris-belcher/electrum-personal-server/releases) van Electrum Personal Server. Kopiëer de link van source code \(tar.gz\), pak deze uit en gooi de download weg. **Vervang hier natuurlijk de versienummers met de meest recente.**

```bash
cd ~/.eps

wget https://github.com/chris-belcher/electrum-personal-server/archive/eps-v0.2.2.tar.gz

tar -xvf eps-v0.2.2.tar.gz

rm eps-v0.2.2.tar.gz

cd electrum-personal-server-eps-v0.2.2

pip3 install use .
```

Start de service weer.

```bash
sudo systemctl start eps
```