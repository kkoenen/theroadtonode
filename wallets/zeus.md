# Zeus

{% hint style="info" %}
Tijd: 10 minuten
{% endhint %}

Er is een [Zeus: Bitcoin/Lightning Wallet](https://play.google.com/store/apps/details?id=app.zeusln.zeus)-app beschikbaar in de Play Store maar de [alpha-release](https://github.com/ZeusLN/zeus/releases) van het project zijn ook prima bruikbaar. Hij heeft een mooie interface en je kunt hem aan je eigen node hangen via Tor. Omdat deze app gebruik maakt van de REST-api en niet van RPC-calls (zoals bijv. Zap) voelt deze app wat vlotter en heeft deze ook wat meer functionaliteiten.

## Voorbereiding

Omdat we een headless server draaien, hebben we geen QR-code die we straks kunnen scannen dus moeten we een connectiestring plakken. Deze connectiestring maken we met [lndconnect](https://docs.theroadtonode.com/lightning-extensies/lnd-connect) op je Pi. SSH je Pi in en typ:

```bash
lndconnect --host=xxx.onion --port=8080 -j
```

Waar **xxx.onion** staat, vul je natuurlijk [jouw onion-adres](https://docs.theroadtonode.com/lightning-extensies/lnd-connect) in voor de **REST** API van LND. Er zal een lap tekst verschijnen dat iets weg heeft van het volgende:

```bash
lndconnect://xxx.onion:8080?cert=heel_veel_tekens&macaroon=heel_veel_tekens
```

Kopieer die lap tekst vanuit SSH en plak die bijvoorbeeld in een Google Docs document \(want we willen die tekst straks op onze telefoon hebben\).

## Zeus

1. Switch op je telefoon naar dit Google Docs document en kopieer de gehele string \(`lndconnect://xxx.onion:8080?cert=heel_veel_tekens&macaroon=heel_veel_tekens`\).
2. Open Zeus
3. `GET STARTED`
4. `Connect a node >`
5. `+ ADD A NEW NODE`
6. `IMPORT`
7. Vul de alias van je node in bij `Nickname (optional)`
8. `SAVE NODE CONFIG`

Je bent nu verbonden met je eigen Node.