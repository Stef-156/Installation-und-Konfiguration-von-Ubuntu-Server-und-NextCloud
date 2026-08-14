# Installation-und-Konfiguration-von-Ubuntu-Server-und-NextCloud

# ☁️ Ultramontaner Nextcloud-Server: Vom Alt-Laptop zur eigenen Cloud

> **Ein narrensicherer Leitfaden für Einsteiger:** Wie du aus einem alten Windows-Laptop deinen eigenen privaten Cloud-Speicher für dich und deine Freunde baust – inklusive Zuklapp-Sperre, WLAN-Stabilisierung, automatischer Datenbankeinrichtung und sicherem Fernzugriff.

---

## 📋 Inhaltsverzeichnis
- [1. Übersicht \& Voraussetzungen](#1-übersicht--voraussetzungen)
- [2. Ubuntu Server Installation](#2-ubuntu-server-installation)
- [3. Hardwarekonfiguration (Laptop-Spezifika)](#3-hardwarekonfiguration-laptop-spezifika)
- [4. Grundlegende System- \& Sicherheitskonfiguration](#4-grundlegende-system--sicherheitskonfiguration)
- [5. Nextcloud Installation \& Datenbank-Setup](#5-nextcloud-installation--datenbank-setup)
- [6. Fernzugriff \& Verschlüsselung (TLS/SSL)](#6-fernzugriff--verschlüsselung-tlsssl)
- [7. Wartung \& Troubleshooting](#7-wartung--troubleshooting)

---

## 1. Übersicht & Voraussetzungen

### 🎯 Ziel dieses Leitfadens
Wir verwandeln einen ca. 12 Jahre alten Laptop (z. B. Intel Core i3/i5 der 2. oder 3. Generation, 4–8 GB RAM) in einen rund um die Uhr laufenden, sicheren Nextcloud-Server. Am Ende hast du deinen eigenen Online-Speicher für Dateien, Fotos und Dokumente, auf den du und deine Freunde per Browser oder Smartphone-App zugreifen könnt.

### ⏱️ Geschätzte Gesamtdauer
* **Ersteinrichtung:** ca. 2,5 bis 3,5 Stunden

### 🎒 Was du benötigst
* **Hardware:**
  * Den alten Laptop inklusive Ladekabel.
  * Einen USB-Stick mit mindestens 4 GB Speicher (**Achtung:** Der Inhalt wird gelöscht!).
  * Einen zweiten PC (Windows/Mac/Linux) zum Erstellen des Boot-Sticks.
  * *Empfohlen:* Ein Ethernet-Kabel (LAN) für die Erstinstallation.
* **Software (auf dem Haupt-PC herunterzuladen):**
  * [Ubuntu Server 26.04 LTS ISO](https://ubuntu.com/download/server) (*LTS = Long Term Support*: stabil und 5 Jahre mit Updates versorgt).
  * [BalenaEtcher](https://etcher.balena.io/) (zum Schreiben der ISO-Datei auf den USB-Stick).

---

## 2. Ubuntu Server Installation

*⏱️ Geschätzte Dauer: 30–45 Minuten*

In diesem Abschnitt löschen wir Windows und installieren **Ubuntu Server**. Im Gegensatz zu Windows oder Ubuntu Desktop hat Ubuntu Server keine grafische Benutzeroberfläche (keine Maus, keine bunten Fenster). Das spart enorm viel Arbeitsspeicher (RAM) und Prozessorleistung – perfekt für 12 Jahre alte Hardware.

---

### Schritt 2.1: Boot-USB-Stick erstellen

> 💡 **Erklärung:** Ein Betriebssystem wird als sogenannte "ISO-Datei" heruntergeladen. Damit der alte Laptop von dieser Datei starten kann, müssen wir sie bootfähig auf einen USB-Stick schreiben ("flashen").

1. Stecke deinen USB-Stick in deinen Haupt-PC.
2. Starte **BalenaEtcher**.
3. Klicke auf **Flash from file** und wähle die heruntergeladene `ubuntu-24.04-live-server-amd64.iso` aus.
4. Klicke auf **Select target** und wähle deinen USB-Stick aus.
5. Klicke auf **Flash!** und warte den Vorgang ab.

> ⚠️ **WARNUNG:** Alle auf dem USB-Stick befindlichen Daten werden dabei unwiderruflich gelöscht!

* **Validierung:** Nach Abschluss meldet BalenaEtcher "Flash Complete!". Der USB-Stick ist bereit.
* Windows-Hinweis: Falls Windows beim Einstecken des geflashten Laufwerks meint, es müsse erst formatiert werden: Keine Sorge, das Zeug ist drauf! (Ignoriere die Formatierungsaufforderung von Windows, das Dateisystem ist für Linux/Raspberry Pi/etc. formatiert und wird von Windows nur nicht erkannt).

---

### Schritt 2.2: Vom USB-Stick booten

> 💡 **Erklärung:** Standardmäßig startet der Laptop das eingebaute Windows. Wir müssen dem Laptop sagen, dass er stattdessen vom USB-Stick lesen soll.

1. Schalte den alten Laptop aus.
2. Stecke den USB-Stick ein (und verbinde idealerweise das LAN-Kabel mit dem Router).
3. Schalte den Laptop ein und drücke sofort wiederholt die **Boot-Menü-Taste** deines Herstellers:
   * **Lenovo:** `F12` oder Novo-Taste
   * **HP:** `F9` oder `Esc`
   * **Dell:** `F12`
   * **Acer/ASUS:** `F12` oder `F8`
4. Wähle im Menü deinen USB-Stick (z. B. *USB Storage*, *UEFI: SanDisk*) aus und drücke `Enter`.

> 🚨 **Warnsignale & Fehler:** Falls Windows normal startet, warst du beim Drücken der Taste zu langsam oder hast die falsche Taste erwischt. Schalte den Laptop erneut aus und versuche es noch einmal.

---

### Schritt 2.3: Der Installations-Assistent

> 💡 **Erklärung:** Der Assistent führt dich durch die Grundeinstellungen des Systems. Du steuerst ausschließlich mit der Tastatur:
> * `Pfeiltasten`: Navigieren
> * `Leertaste`: Auswählen / Häkchen setzen
> * `Enter`: Bestätigen

#### Detaillierte Schritte:
1. **Language:** Wähle `English` (Empfohlen, da Fehlersuche im Internet auf Englisch deutlich einfacher ist).
2. **Keyboard Configuration:** Wähle unter Layout `German` aus.
3. **Type of Install:** Wähle `Ubuntu Server (default)`.
4. **Network Connections:** Wenn LAN angeschlossen ist, bezieht das System automatisch eine IP-Adresse. WLAN richten wir später ein.
5. **Configure Proxy & Mirror:** Standardwerte beibehalten (`Enter` drücken).
6. **Storage Configuration (Festplatte):**
   * Wähle `Use an entire disk`.
   * **WICHTIG:** Deaktiviere die Option `Set up this disk as an LVM group` (Häkchen entfernen), um das System für Einsteiger einfach zu halten.
   * Wähle die Festplatte aus und bestätige die Warnung, dass alle Daten überschrieben werden (*Continue*).
7. **Profile Configuration:**
   * **Your name:** Dein Name (z. B. `Max Mustermann`).
   * **Your server's name:** Der Name des Laptops im Netz (z. B. `nextcloud-server`).
   * **Pick a username:** Dein Admin-Benutzername (z. B. `cloudadmin` – nur Kleinbuchstaben, keine Leerzeichen!).
   * **Choose a password:** Ein sicheres Passwort wählen und wiederholen.
8. **SSH Setup:**
   * **WICHTIG:** Setze mit der Leertaste ein Häkchen bei `[X] Install OpenSSH server`. Das erlaubt uns später die Steuerung aus der Ferne.
9. **Featured Server Snaps:** Keinen Dienst auswählen! Wir installieren Nextcloud manuell, um die volle Kontrolle zu behalten.
10. **Installation:** Warte, bis unten `Reboot Now` erscheint. Ziehe den USB-Stick heraus und drücke `Enter`.

---

### 🔍 Validierung von Abschnitt 2
Nach dem Neustart bleibt der Bildschirm bei einer Textzeile stehen:

```text
nextcloud-server login:
