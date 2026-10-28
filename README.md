# Lenovo ThinkCentre M715q Gen 2 – NVMe Boot Freeze & Error 1962 bei Home Assistant Installation

## 1. Problembeschreibung

Beim Einrichten eines **Lenovo ThinkCentre M715q Gen 2 (Typ 10VM)** für Home Assistant OS traten Probleme in Verbindung mit der eingebauten M.2-NVMe-SSD auf.

Folgende Symptome konnten beobachtet werden:

- Mit eingebauter M.2-NVMe-SSD blieb der Rechner bereits beim **Lenovo-Logo** hängen.
- Ohne bootfähiges Betriebssystem erschien `Error 1962: No operating system found`.
- Die verwendete NVMe-SSD funktionierte in einem anderen Mini-PC problemlos.
- Ohne eingebaute NVMe-SSD ließ sich das ThinkCentre problemlos über ein Linux-Live-System starten.

Die Fehlersuche deutete damit auf ein Problem bei der Initialisierung der NVMe-SSD durch das BIOS/UEFI des ThinkCentre hin.

---

## 2. Fehlersuche

### 2.1 BIOS-Einstellungen prüfen

Zunächst wurden die relevanten BIOS-Einstellungen überprüft und verschiedene Boot-Konfigurationen getestet.

Unter anderem:

- **Secure Boot:** Deaktiviert
- **Boot Mode & Priority:** UEFI bevorzugt
- **USB HDD:** In die Boot Sequence aufgenommen

Zusätzlich wurde über `F12` versucht, ein angeschlossenes Boot-Medium manuell auszuwählen.

**Ergebnis:**  
Das Verhalten mit eingebauter NVMe-SSD änderte sich dadurch nicht.

### 2.2 M.2-NVMe-SSD prüfen

Um einen Defekt der SSD auszuschließen, wurde diese aus dem ThinkCentre ausgebaut und in einem **NiPoGi Mini-PC** getestet.

Dort wurde die SSD problemlos erkannt und konnte verwendet werden.

**Ergebnis:**  
Ein Hardwaredefekt der M.2-NVMe-SSD konnte damit ausgeschlossen werden.

### 2.3 NVMe-SSD erneut im ThinkCentre testen

Die im Zweitsystem erfolgreich getestete NVMe-SSD wurde anschließend wieder in das ThinkCentre eingebaut.

**Ergebnis:**  
Das ThinkCentre blieb erneut bereits beim Lenovo-Logo hängen.

### 2.4 ThinkCentre ohne NVMe-SSD testen

Anschließend wurde die NVMe-SSD aus dem ThinkCentre entfernt.

Mit einem Linux-Live-USB-Stick ließ sich der Rechner anschließend problemlos starten.

**Ergebnis:**  
Sowohl das ThinkCentre ohne die betreffende NVMe-SSD als auch die NVMe-SSD in einem anderen System funktionierten ordnungsgemäß.

---

## 3. Ursache

Die durchgeführten Tests deuteten auf ein Problem bei der Initialisierung der verwendeten M.2-NVMe-SSD durch die installierte BIOS-/UEFI-Version des ThinkCentre hin.

Bei der weiteren Recherche wurde ein vergleichbares Verhalten beim **ThinkCentre M715q Gen 2** gefunden, bei dem ein Downgrade der BIOS-Version als Lösung beschrieben wurde.

> **Hinweis:**  
> Die beschriebene Ursache basiert auf dem reproduzierbaren Verhalten des eigenen Systems sowie einem vergleichbaren Community-Bericht. Daraus lässt sich nicht ableiten, dass grundsätzlich alle neueren BIOS-Versionen oder alle NVMe-SSDs beim M715q Gen 2 betroffen sind.

---

## 4. Lösung: BIOS-Downgrade auf M1XKT49A

Im betroffenen System konnte das Problem durch ein Downgrade auf die BIOS-Version **M1XKT49A** behoben werden.

Verwendetes Lenovo-BIOS-Image:

`m1xj949usa.iso`

> **Achtung:**  
> Ein BIOS-Update oder -Downgrade verändert die Firmware des Geräts. Ein unterbrochener Flash-Vorgang oder eine ungeeignete Firmware kann dazu führen, dass das System nicht mehr startet. Vor der Durchführung muss sichergestellt werden, dass die Firmware zum verwendeten Gerätemodell passt.

### Voraussetzungen

Benötigt werden:

- USB-Stick
- Ventoy
- passendes Lenovo-BIOS-Image für das ThinkCentre M715q Gen 2

### BIOS-Bootmedium vorbereiten

1. **Ventoy** auf dem USB-Stick installieren.
2. Das passende BIOS-ISO von Lenovo herunterladen.
3. Die ISO-Datei auf den Ventoy-USB-Stick kopieren.

### BIOS-Downgrade durchführen

1. ThinkCentre vollständig ausschalten.
2. M.2-NVMe-SSD ausbauen.
3. Ventoy-USB-Stick anschließen.
4. ThinkCentre starten.
5. Im Ventoy-Menü das Lenovo-BIOS-Image auswählen.
6. Lenovo Flash Utility starten.
7. Die Warnung

   `BIOS ROM file is older than (or same as) current BIOS ROM image`

   mit `Y` bestätigen.
8. Abfragen zur Änderung von Seriennummer, Machine Type und Model mit `N` beantworten, damit die vorhandenen Gerätedaten unverändert bleiben.
9. Flash-Vorgang vollständig abschließen lassen.
10. Gerät anschließend ausschalten.

> **Wichtig:**  
> Das ThinkCentre während des Flash-Vorgangs nicht ausschalten oder von der Stromversorgung trennen.

### BIOS anschließend konfigurieren

1. ThinkCentre starten und mit `F1` das BIOS-Setup öffnen.
2. Prüfen, ob **M1XKT49A** als BIOS-Version angezeigt wird.
3. Mit `F9` die **Setup Defaults** laden.
4. **Secure Boot** deaktivieren.
5. Änderungen mit `F10` speichern.
6. ThinkCentre ausschalten.

---

## 5. Home Assistant OS installieren

Nach dem erfolgreichen BIOS-Downgrade kann die M.2-NVMe-SSD wieder eingebaut und Home Assistant OS installiert werden.

Verwendet wird **Methode 1 der offiziellen Home-Assistant-Anleitung für Generic x86-64**. Dabei wird ein Linux-Live-System gestartet und das Home-Assistant-OS-Image von dort direkt auf die interne NVMe-SSD geschrieben.

### Linux-Live-USB erstellen

Benötigt werden:

- USB-Stick
- aktuelles Ubuntu-Desktop-Image
- Rufus

1. Das aktuelle Ubuntu-Desktop-Image herunterladen.
2. **Rufus** unter Windows starten.
3. Den gewünschten USB-Stick auswählen.
4. Das Ubuntu-Image als Boot-Abbild auswählen.
5. Den bootfähigen USB-Stick erstellen.

> **Hinweis:**  
> Beim Erstellen des Bootmediums werden vorhandene Daten auf dem USB-Stick gelöscht.

### Linux-Live-System starten

1. M.2-NVMe-SSD in das ThinkCentre einbauen.
2. Linux-Live-USB-Stick anschließen.
3. ThinkCentre einschalten.
4. Bei Bedarf mit `F12` das Boot-Menü öffnen.
5. USB-Stick als Boot-Medium auswählen.
6. Ubuntu als Live-System starten, ohne es auf der internen SSD zu installieren.

### Home Assistant OS auf die NVMe-SSD schreiben

Nach dem Start des Linux-Live-Systems wird das aktuelle **Home Assistant OS Generic x86-64 Image** entsprechend der offiziellen Home-Assistant-Anleitung direkt auf die interne NVMe-SSD geschrieben.

> **Achtung:**  
> Beim Schreiben des HAOS-Images werden sämtliche vorhandenen Daten und Partitionen auf dem ausgewählten Ziellaufwerk gelöscht. Vor dem Start unbedingt sicherstellen, dass die interne NVMe-SSD als korrektes Ziellaufwerk ausgewählt wurde.

Der jeweils aktuelle Installationsablauf ist in der offiziellen [Home Assistant – Generic x86-64 Installation](https://www.home-assistant.io/installation/generic-x86-64/) Dokumentation beschrieben.

### Erster Start

Nach dem erfolgreichen Schreiben des HAOS-Images:

1. Linux-Live-System herunterfahren.
2. USB-Stick entfernen.
3. ThinkCentre neu starten.
4. Das System von der internen M.2-NVMe-SSD starten lassen.
5. Warten, bis Home Assistant OS vollständig gestartet wurde.
6. Die Ersteinrichtung von Home Assistant über einen anderen Rechner im Netzwerk durchführen.

---

## 6. Ergebnis

Nach dem Downgrade auf **M1XKT49A** konnte das ThinkCentre mit eingebauter M.2-NVMe-SSD wieder ordnungsgemäß gestartet werden.

Anschließend konnte Home Assistant OS über ein Linux-Live-System direkt auf die interne NVMe-SSD geschrieben und davon gestartet werden.

**Vor dem BIOS-Downgrade:**

`NVMe eingebaut → Lenovo-Logo → System hängt`

**Nach dem BIOS-Downgrade:**

`NVMe eingebaut → Linux-Live-System startet → HAOS auf NVMe schreiben → HAOS startet von NVMe`

---

## 7. Quellen & weiterführende Links

- [Home Assistant – Generic x86-64 Installation](https://www.home-assistant.io/installation/generic-x86-64/)
- [Rufus](https://rufus.ie/)
- [Ubuntu Desktop](https://ubuntu.com/download/desktop)
- [Lenovo – BIOS Update für ThinkCentre M715q Gen 2](https://support.lenovo.com/fr/fr/downloads/ds504437-flash-bios-update-thinkcentre-m715q-2nd-gen)
- [Ventoy](https://www.ventoy.net/)
- [Community-Bericht zum NVMe-Boot-Problem](https://www.reddit.com/r/MiniPCs/comments/1r7ddln/thinkcentre_m715q_gen2_nvme_boot_freeze_solution/)

---

## 8. Navigation

- [⬅️ Zurück zur Übersicht](../README.md)