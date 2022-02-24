# Specter

{% hint style="info" %}
Tijd: 5 minuten
{% endhint %}

[Specter](https://github.com/cryptoadvance/specter-desktop) is in het leven geroepen om het opzetten van een multisig constructie makkelijker te maken. Het biedt een overzichtelijke tool welke je kunt inzien in je browser. Om dit onderdeel van de guide enigszins simpel te houden, moet je voor het gebruik van Specter fysiek toegang hebben tot je node. Om Specter te gebruiken en een [multisig](https://youtu.be/yeLqe_gg2u0) op te zetten, moet je namelijk jouw hardware wallets aansluiten op de Pi. Een duidelijke uitleg over het gebruik van Specter is onderaan de pagina te vinden. In dit onderdeel gaan we in op de installatie en het openen van Specter.

SSH in je Pi en voer het installatie commando uit voor de nodige tools.

```bash
sudo apt install libusb-1.0-0-dev libudev-dev python3-pip -y
```

Daarna installeer je Specter.

```bash
pip3 install cryptoadvance.specter
```

En tot slot start je Specter met:

```bash
python3 -m cryptoadvance.specter server --host 0.0.0.0 --port 25441
```

De tool zal nu in de achtergrond draaien op poortnummer `:25441`. Open je browser en ga naar `het ip van je pi:poortnummer`. Het zal iets weg hebben van: `192.168.1.6:25441`.

Als je een firewall gebruik is het wel van belang dat je de bijbehorende port openzet.

```bash
sudo ufw allow 25441 comment "Port voor Specter"
```

## Automatiseren

Net als bij alle andere tools in deze guide zullen we Specter in de achtergrond laten draaien. Zo hoef je niet iedere keer Specter handmatig op te starten voordat je er gebruik van kan maken.

Maak een service aan.

```bash
sudo nano /etc/systemd/system/specter.service
```

Plak er het volgende in.

```toml
[Unit]
Description=Specter
Requires=bitcoind.service
After=bitcoind.service

[Service]
ExecStart=python3 -m cryptoadvance.specter server --host 0.0.0.0 --port 25441
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

Activeer de service.

```bash
sudo systemctl enable specter
```

En start de service op.

```bash
sudo systemctl start specter
```

Ga naar `http://IP-ADRES VAN PI:25441` om te zien of het werkt. Bijvoorbeeld `http://192.168.1.6:25441`.

## Updaten

Af en toe komt er een nieuwe versie van Specter uit. Updaten is best gemakkelijk. Eerst gooi je de service uit.

```bash
sudo systemctl stop specter
```

Het updaten doe je als volgt.

```bash
pip3 install cryptoadvance.specter --upgrade
```

Tot slot start je de service weer met het `systemctl` commando.

```bash
sudo systemctl start specter
```

Zodra je weer naar de Specter website toe gaat zoals eerder beschreven, zal je links onderin het versienummer zien staan.

## Gebruik van Specter

{% embed url="https://www.youtube.com/watch?v=ZQvCncdFMPo" caption="Een uitgebreide video over het gebruik van Specter" %}

