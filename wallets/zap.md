# Zap

{% hint style="info" %}
Tijd: 10 minuten
{% endhint %}

Zap is een Bitcoin Lightning wallet voor iOS en Android. De app is uiterst gebruiksvriendelijk en daarom iedereen aan te raden. En het mooiste is: je kunt hem aan je eigen node hangen via Tor. Het enige minpunt is dat de Android-versie van de app je node benadert via de **RPC** API van LND. De iOS-versie ondersteunt wel de **REST** API van LND.

## Android
Download de [Zap: Bitcoin Lightning Wallet](https://play.google.com/store/apps/details?id=zapsolutions.zap)-app uit de Play Store. Deze is nog in ontwikkeling maar daarom niet minder bruikbaar. 

### Voorbereiding
Omdat we een headless server draaien, hebben we geen QR-code die we straks kunnen scannen dus moeten we een connectiestring plakken. Deze connectiestring maken we met [lndconnect](https://docs.theroadtonode.com/lightning-extensies/lnd-connect) op je Pi. SSH je Pi in en typ:

```bash
lndconnect --host=xxx.onion --port=10009 -j
```

Waar **xxx.onion** staat, vul je natuurlijk [jouw onion-adres](https://docs.theroadtonode.com/lightning-extensies/lnd-connect) in voor de **RPC** API van LND. Er zal een lap tekst verschijnen dat iets weg heeft van het volgende:

```bash
lndconnect://xxx.onion:10009?cert=heel_veel_tekens&macaroon=heel_veel_tekens
```

Kopieer die lap tekst vanuit SSH en plak die bijvoorbeeld in een Google Docs document \(want we willen die tekst straks op onze telefoon hebben\).

### Zap

1. Switch op je telefoon naar dit Google Docs document en kopieer de gehele string \(`lndconnect://xxx.onion:10009?cert=heel_veel_tekens&macaroon=heel_veel_tekens`\).
2. Open Zap
3. Tap op Wallet instellen
4. Plak
3. OK

Je bent nu verbonden met je eigen Node.

Via `Beheer...` kun je naam van je wallet aanpassen. Standaard krijgt die de naam van je Tor-adres namelijk.

## iOS
Download de [Zap: Bitcoin Lightning Wallet](https://apps.apple.com/nl/app/zap-bitcoin-lightning-wallet/id1406311960) uit de App Store. Als je daarna de app installeert en voor het eerst opent, wordt je gevraagd een PIN te bedenken. Direct daarna verschijnt een scherm waarmee jij jouw node kunt connecten. Hiervoor moet je [lndconnect](https://docs.theroadtonode.com/lightning-extensies/lnd-connect) installeren op je Pi. SSH je Pi in en typ:

```bash
lndconnect --host=xxx.onion --port=8080 --nocert -j
```

Waar **xxx.onion** staat, vul je natuurlijk [jouw onion-adres](https://docs.theroadtonode.com/lightning/tor-aanpassen#onion-adressen) in voor de **REST** API van LND. Er zal een lap tekst verschijnen dat iets weg heeft van het volgende.

```bash
lndconnect://xxx.onion:8080?macaroon=heel_veel_tekens
```

Kopieer die lap tekst en open de Zap app op je iPhone. Als het goed is zie je een scherm voor je dat lijkt op dit.

![Zap app connectie scherm](../.gitbook/assets/img_0163.png)

Klik op "Paste" en Zap vult alles automatisch voor je in.

![Alles wordt automatisch ingevuld](../.gitbook/assets/img_4ea8cbfa09b2-1.jpeg)

Druk op "Connect" en de connectie komt tot stand.

### Connectie over clearnet

Je hoeft niet te verbinden via tor. Sterker nog, Zap werkt beter via clearnet. Je kunt op dezelfde manier een lndconnect string genereren als met tor.

```bash
lndconnect -j --host=JOUW_IP --port=10009 --adminmacaroonpath=~/.lnd/data/chain/bitcoin/mainnet/admin.macaroon --tlscertpath=~/.lnd/tls.cert
```

Je moet in het bovenstaande commando even `JOUW_IP` vervangen voor het IP van je Pi. Of als je een DDNS gebruikt vervangen met je URL.