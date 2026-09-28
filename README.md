# FermenterControl – Home Assistant

Öffentliche Home-Assistant-Ressourcen für **FermenterControl** von STAMM INNOVATION.

Dieses Repository enthält ausschließlich Home-Assistant-Dateien. Der FermenterControl-Anwendungscode und die Controller-Firmware bleiben getrennt davon.

## Alarmbenachrichtigungen

Die Blueprint erzeugt pro Fermenter eine Home-Assistant-Automation für:

- aktive Fermenter-Alarme,
- konkrete Alarmtexte aus FermenterControl,
- Controller-Offlinezustand mit konfigurierbarer Verzögerung,
- optionale Meldung nach Alarmbehebung,
- optionale Meldung, wenn der Controller wieder online ist.

### Direkt in Home Assistant importieren

[Blueprint in Home Assistant importieren](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fstamminnovation%2Ffermentorcontrol-home-assistant%2Fblob%2Fmain%2Fblueprints%2Ffermentorcontrol-alarm-notifications.yaml)

Alternativ in Home Assistant:

1. **Einstellungen → Automationen & Szenen → Blueprints**
2. **Blueprint importieren**
3. Folgende URL einfügen:

```text
https://github.com/stamminnovation/fermentorcontrol-home-assistant/blob/main/blueprints/fermentorcontrol-alarm-notifications.yaml
```

4. **Vorschau**
5. **Importieren**
6. Aus der Blueprint für jeden Fermenter eine Automation anlegen.

## Einrichtung einer Automation

Für einen Fermenter werden typischerweise diese durch MQTT Discovery angelegten Entities ausgewählt:

```text
Alarm aktiv:
binary_sensor.fermentor_f01_alarm_active

Alarmtext:
sensor.fermentor_f01_alarms

Controller online:
binary_sensor.fermentor_f01_online
```

Zusätzlich wird ein `notify`-Ziel ausgewählt, beispielsweise eine Notify-Entity der Home-Assistant-Companion-App.

Die Standard-Offline-Verzögerung beträgt **2 Minuten**.

## Voraussetzungen

- FermenterControl mit aktiviertem External MQTT
- Home Assistant mit demselben MQTT-Broker
- Home Assistant MQTT Discovery in FermenterControl aktiviert
- für detaillierte Prozessalarme ein Controller-Firmwarestand mit MQTT-Alarmtelemetrie

FermenterControl übermittelt die Alarmzustände aus denselben Alarmmodulen, die auch für Controller-Display und Controller-Webinterface verwendet werden. Home Assistant definiert daher keine eigenen Temperatur- oder Sensoralarmschwellen.

## Blueprint

Quelldatei:

```text
blueprints/fermentorcontrol-alarm-notifications.yaml
```

GitHub:

https://github.com/stamminnovation/fermentorcontrol-home-assistant/blob/main/blueprints/fermentorcontrol-alarm-notifications.yaml

## Aktualisierungen

Wenn die Blueprint in diesem Repository aktualisiert wurde:

1. **Einstellungen → Automationen & Szenen → Blueprints**
2. Menü der FermenterControl-Blueprint öffnen
3. **Blueprint erneut importieren**

Bestehende Automationen verwenden anschließend die aktualisierte Blueprint nach dem Neuladen der Automationen.

## Projekt

FermenterControl  
STAMM INNOVATION
