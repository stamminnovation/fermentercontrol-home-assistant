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


## Messdaten exportieren

Home Assistant speichert die FermenterControl-Entities über den Recorder. Im **Verlauf / History**-Panel können gewünschte Fermenter-Entities und ein Zeitraum gewählt und anschließend über **Download data** als CSV exportiert werden.

Home Assistant bewahrt detaillierte Recorder-Daten standardmäßig 10 Tage auf. Sensoren mit `state_class: measurement` erhalten zusätzlich Langzeitstatistiken, die stündlich aggregiert werden. FermenterControl kennzeichnet Temperatur, Solltemperatur, Dichte, Dichteänderung und Reglerausgang entsprechend.

Für längere Vollauflösung liegt unter:

```text
examples/recorder-fermentorcontrol.yaml
```

eine Beispielkonfiguration mit 90 Tagen Aufbewahrung.

Wichtig: Wer bereits einen `recorder:`-Block in `configuration.yaml` verwendet, darf keinen zweiten Block anlegen, sondern muss die Werte in die vorhandene Konfiguration übernehmen.

## Profilsteuerung

Aktuelle FermenterControl-Versionen können die auf dem Controller gespeicherte Profilbibliothek vollständig synchronisieren. Home Assistant erhält dadurch für die Profilauswahl keine numerische ID-Eingabe mehr, sondern ein dynamisches Select mit Profilname und stabiler ID.

Das eigentliche Bearbeiten von Profilen bleibt in FermenterControl bzw. in der generischen External-MQTT-Schnittstelle. Home Assistant ist bewusst auf Auswahl, Start und Stop des Profils fokussiert.


## Zentrales Profilarchiv in Home Assistant

FermenterControl kann das zentrale Profilarchiv per MQTT Discovery als eigenes Home-Assistant-Gerät bereitstellen.

Gerät:

```text
FermenterControl Profilarchiv
```

Typische Entities:

```text
sensor.fermentorcontrol_profile_archive_count
sensor.fermentorcontrol_profile_archive_selected
sensor.fermentorcontrol_profile_archive_target
binary_sensor.fermentorcontrol_profile_archive_target_writable
select.fermentorcontrol_profile_archive_profile
select.fermentorcontrol_profile_archive_target
button.fermentorcontrol_profile_archive_distribute
button.fermentorcontrol_profile_archive_delete
button.fermentorcontrol_profile_archive_refresh
sensor.fermentorcontrol_profile_archive_command_result
```

Außerdem kann jedes Fermenter-Gerät einen Button

```text
button.fermentor_<id>_profile_archive
```

erhalten. Dieser übernimmt das aktuell ausgewählte Controllerprofil in das zentrale Archiv.

Voraussetzungen in **FermenterControl → External MQTT → Externe Steuerungsrechte**:

- **Profile anzeigen** für das Archiv-Gerät
- **Profile anlegen** für Archivieren und Verteilen
- **Profile löschen** nur dann, wenn Löschen aus Home Assistant erlaubt sein soll

Das automatisch generierte FermenterControl-Dashboard enthält einen eigenen Tab **Profilarchiv**. Destruktive Aktionen werden dort mit einer Bestätigungsabfrage versehen.

Der vollständige Mehrschritt-Editor bleibt in FermenterControl; Home Assistant dient für schnelle Auswahl, Archivierung und Verteilung.


## Archivprofile direkt in Home Assistant bearbeiten

Bei aktiviertem Recht **Profile bearbeiten** stellt FermenterControl per MQTT Discovery einen transaktionalen Editor für das zentrale Profilarchiv bereit.

Der Editor arbeitet mit einem retained Entwurfszustand:

```text
fermentorcontrol/profile-archive/editor/state
```

Typische Entities:

```text
button.fermentorcontrol_profile_archive_editor_open
binary_sensor.fermentorcontrol_profile_archive_editor_active
binary_sensor.fermentorcontrol_profile_archive_editor_dirty
binary_sensor.fermentorcontrol_profile_archive_editor_conflict

text.fermentorcontrol_profile_archive_editor_name
number.fermentorcontrol_profile_archive_editor_tolerance
select.fermentorcontrol_profile_archive_editor_end_behavior

sensor.fermentorcontrol_profile_archive_editor_step_count
select.fermentorcontrol_profile_archive_editor_step
number.fermentorcontrol_profile_archive_editor_setpoint
number.fermentorcontrol_profile_archive_editor_duration
select.fermentorcontrol_profile_archive_editor_timer
select.fermentorcontrol_profile_archive_editor_advance
number.fermentorcontrol_profile_archive_editor_density_target
number.fermentorcontrol_profile_archive_editor_density_change

button.fermentorcontrol_profile_archive_editor_add_step
button.fermentorcontrol_profile_archive_editor_delete_step
button.fermentorcontrol_profile_archive_editor_save
button.fermentorcontrol_profile_archive_editor_cancel
```

Ablauf:

1. Archivprofil auswählen.
2. **Ausgewähltes Profil bearbeiten** drücken.
3. Felder bzw. Profilschritte ändern.
4. **Profiländerungen speichern** schreibt den vollständigen Entwurf nach Validierung in das zentrale Archiv.
5. **Änderungen verwerfen** beendet den Editor ohne persistente Änderungen.

Der Editierpuffer liegt in FermenterControl. Home Assistant schreibt daher nicht bei jeder einzelnen Feldänderung sofort in MongoDB.

Zusätzlich schützt FermenterControl vor parallelen Änderungen: Wird das Profil nach Öffnen des Editors an anderer Stelle verändert, wird **Bearbeitungskonflikt** aktiv und ein veralteter Entwurf kann nicht gespeichert werden.

Der automatisch generierte Dashboard-Tab **Profilarchiv** enthält den Editor bereits vollständig; es sind keine HACS-Karten oder manuell angelegten Helper erforderlich.


### Neues Profil direkt aus Home Assistant

Mit aktiviertem Recht **Profile anlegen** erscheint im Profilarchiv zusätzlich:

```text
button.fermentorcontrol_profile_archive_editor_new
binary_sensor.fermentorcontrol_profile_archive_editor_new_profile
```

Der Button **Neues Profil anlegen** öffnet einen editierbaren Entwurf mit einem Standardschritt. Erst **Profiländerungen speichern** legt daraus eine neue zentrale Archivvorlage an.

Die beiden hochauflösenden Profilwerte

```text
text.fermentorcontrol_profile_archive_editor_density_target
text.fermentorcontrol_profile_archive_editor_density_change
```

werden absichtlich als MQTT Text statt MQTT Number bereitgestellt, weil Home Assistant für MQTT Number derzeit keine Schrittweite kleiner als `0.001` akzeptiert. FermenterControl benötigt für Dichtebedingungen bis zu vier bzw. fünf Nachkommastellen.

Die Felder akzeptieren Dezimalpunkt und Dezimalkomma.


## Profilabschluss-Benachrichtigung

Für das Ende eines Gärprofils gibt es eine separate Blueprint:

```text
blueprints/fermentorcontrol-profile-complete-notifications.yaml
```

One-Click-Import:

```text
https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fstamminnovation%2Ffermentorcontrol-home-assistant%2Fblob%2Fmain%2Fblueprints%2Ffermentorcontrol-profile-complete-notifications.yaml
```

Die Blueprint verwendet:

```text
binary_sensor.fermentor_<id>_profile_complete
sensor.fermentor_<id>_profile_name
```

und ein frei wählbares `notify`-Ziel.

Die Benachrichtigung wird ausschließlich beim Zustandswechsel

```text
off → on
```

von **Profil abgeschlossen** ausgelöst. Dadurch erzeugt ein nach Home-Assistant-Neustart erneut eingelesener retained MQTT-Zustand keine zweite Abschlussmeldung.

Beispielmeldung:

```text
FermenterControl – Fermenter F01

Profil „Lager Standard“ ist abgeschlossen.
Der letzte Profilschritt wurde beendet.
```

Für jeden Fermenter wird wie bei den Alarmbenachrichtigungen eine eigene Automation aus der Blueprint angelegt.
