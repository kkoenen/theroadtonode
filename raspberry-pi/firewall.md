# Firewall

Een Bitcoin node zal in beginsel geen of niet veel bitcoin bevatten. Een Bitcoin node wordt door de meesten enkel gebruikt om transacties te controleren. Desalniettemin zijn er genoeg uitzonderingen te bedenken waarom je wél bitcoin op je node hebt staan. Lightning is zo'n uitzondering. Een Lightning node moet continu verbonden zijn met het internet en funds online hebben. De funds moeten uitgeefbaar zijn en daar zijn private keys voor nodig. Een Lightning node is per definitie ["hot"](https://www.investopedia.com/terms/h/hot-wallet.asp).

Reden genoeg voor een extra beveiligingslaag op je node. Een tool genaamd Uncomplicated Firewall \(of UFW in het kort\) maakt je node een stukje veiliger. In de basis houdt UFW al het inkomende verkeer naar je node toe, tegen. Maar je kunt ook enkele ports open zetten waar verkeer wél over is toegestaan. Je zal UFW vaak tegenkomen in de guide. In dit hoofdstuk vind je de volgende zaken:

1. Regel instellen voor SSH
2. UFW activeren

Het instellen van een regel waarmee je verkeer toestaat op een specifieke port, doe je op de manier die hier onder staat. Hier wordt aan het `ufw` programma de opdracht `allow` meegegeven en een portnummer. **Voer dit commando ook uit, anders kun je je Pi niet meer in!**

```bash
sudo ufw allow 22 comment "Port voor SSH"
```

Tot slot activeer je de UFW als volgt.

```bash
sudo ufw enable
```

Wil je een mooi overzicht van alle ports die je open hebt staan, type dan:

```bash
sudo ufw status
```

