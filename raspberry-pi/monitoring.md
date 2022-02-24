# Monitoring

{% hint style="info" %}
Tijd: 5 minuten
{% endhint %}

Wil je weten hoe veel resources je Pi verbruikt, kun je dat bekijken met `htop`. Door in je Pi te SSH'en en simpel `htop` te typen, verschijnt het monitoringsvenster. Htop zit standaard geïnstalleerd op je Pi, dus je hoeft niets te installeren.

![Het htop venster](../.gitbook/assets/htop.png)

Wat zie je op dit scherm? Bovenaan staan de vier cores van de processor van de Pi. Daaronder staat het geheugengebruik. Je kunt met `Ctrl + C` of `q` het scherm verlaten.

Je Pi wordt ook warm, vandaar de heatsink. Wil je weten hoe warm de Pi wordt, voer dan het volgende uit:

```bash
cat /sys/class/thermal/thermal_zone0/temp
```

Wel even door 1000 delen.