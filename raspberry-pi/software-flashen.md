# Software flashen

{% hint style="info" %}
Tijd: 15 minuten
{% endhint %}

Het is tijd om iets met een beeldscherm te doen. De Pi moet een besturingssysteem hebben, namelijk [Ubuntu Server 20 LTS](https://ubuntu.com/download/raspberry-pi). [Raspberry Pi OS Lite](https://downloads.raspberrypi.org/raspios\_lite\_armhf\_latest) is ook nodig voor het eenmalig instellen van de Pi. Om de besturingssystemen op zowel de SSD als de microSD te krijgen, gebruiken we het programma [Balena Etcher](https://www.balena.io/etcher/). Gebruik je Windows dan zou je ook [Rufus](https://github.com/pbatard/rufus/releases/download/v3.17/rufus-3.17.exe) kunnen gebruiken.

## Raspberry Pi OS

We beginnen met het flashen van Raspberry Pi OS naar de microSD. Open Balena Etcher, selecteer de Raspberry Pi OS image en microSD en druk op de knop "Flash!".

![Raspberry Pi OS flashen naar de microSD](../.gitbook/assets/screenshot-2020-10-31-at-12.20.41.png)

Zodra het klaar is zal de microSD uitgeworpen worden. Haal hem uit je computer en steek hem er opnieuw in zodat je computer de microSD weer ziet. [Open de Terminal](https://support.apple.com/nl-nl/guide/terminal/apd5265185d-f365-44cb-8b09-71a064a42125/mac) en typ het volgende commando waarna je op enter drukt.

```bash
touch /Volumes/boot/ssh
```

Zit je [op Windows](https://arjanlobbezoo.nl/windows-10-programma-administrator-mode-openen/), dan zou iets in de trant van het volgende moeten werken. In dit voorbeeld zou je de D-schijf moeten aanpassen naar wat voor jou van toepassing is.

```bash
cd D:\
```

```bash
type ssh
```

Met bovenstaande commando's wordt een bestand aangemaakt op de microSD. Het bestand heet "ssh" en als de Pi opstart met dit bestand aan boord, maakt de Pi het mogelijk om zich te laten aansturen van buitenaf. We gaan namelijk met behulp van SSH de Pi aansturen vanaf onze computer. Werp tot slot de microSD uit en stop hem in de Pi.

## SSD

Herhaal het bovenstaande voor de SSD, maar gebruik dan Ubuntu en laat het `ssh` bestand zitten. Dat zit standaard al aan boord van Ubuntu.