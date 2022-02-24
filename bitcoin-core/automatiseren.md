# Automatiseren

{% hint style="info" %}
Tijd: 5 minuten
{% endhint %}

Het mooiste is als bitcoind automatisch opstart, in plaats van dat je het handmatig moet doen. Dit kan handig zijn na het opnieuw opstarten van je Pi of als bitcoind crasht. Linux heeft daar systemd services voor. Zorg voordat je aan de slag gaat met de automatisering, dat Bitcoin Core \(bitcoind\) uit staat. Doe dit met:

```bash
bitcoin-cli stop
```

Zodra bitcoind uit staat kunnen we een service aanmaken.

```bash
sudo nano /etc/systemd/system/bitcoind.service
```

Plak er dit in.

```toml
[Unit]
Description=Bitcoin Daemon
After=network.target

[Service]
User=ubuntu
PIDFile=/home/ubuntu/.bitcoin/bitcoind.pid
ExecStart=/usr/local/bin/bitcoind
Restart=always
TimeoutSec=120
RestartSec=30

[Install]
WantedBy=multi-user.target
```

Sla het weer op met `Ctrl + X` en bevestig met `Y`.

Het systeem moet op de hoogte gesteld worden van de nieuwe service en kan daarna gestart worden.

```bash
sudo systemctl enable bitcoind
```

```bash
sudo systemctl start bitcoind
```

Wil je zien of alles goed is opgestart, voer dan onderstaande uit. Let op: het kan even een minuutje duren voordat je te zien krijgt dat alles naar behoren werkt.

```bash
systemctl status bitcoind
```

Het zou kunnen dat het niet in een keer werkt. Je output zal dan hierop lijken:

```bash
● bitcoind.service - Bitcoin Daemon
     Loaded: loaded (/etc/systemd/system/bitcoind.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-01-14 12:20:53 UTC; 2s ago
   Main PID: 40483 (bitcoind)
      Tasks: 12 (limit: 4203)
     CGroup: /system.slice/bitcoind.service
             └─40483 /usr/local/bin/bitcoind

Jan 14 12:20:53 ubuntu systemd[1]: Started Bitcoin Daemon.
Jan 14 12:20:53 ubuntu bitcoind[40482]: Bitcoin Core starting
```

Probeer het commando een paar keer uit te voeren, want het duurt hooguit een minuutje voordat bitcoind goed is opgestart middels de service.

Wil je een overzicht van de status over meerdere sessie, gebruik dan dit:

```bash
sudo journalctl -f -u bitcoind
```