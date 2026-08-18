## 📋 Inhaltsverzeichnis

- [1. Übersicht \& Voraussetzungen](#1-übersicht--voraussetzungen)
- [2. Ubuntu Server Installation](#2-ubuntu-server-installation)
- [3. Hardwarekonfiguration (Laptop-Spezifika)](#3-hardwarekonfiguration-laptop-spezifika)
- [4. Grundlegende System- & Sicherheitskonfiguration](#4-grundlegende-system---sicherheitskonfiguration)
- [5. Docker Installation & Nextcloud](#5-docker-installation--nextcloud)
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
* Hinweis zum USB-Stick: Nach dem Flashen des Ubuntu-Images fordert Windows zum Formatieren auf, da es das Linux-Dateisystem nicht lesen kann. Der Stick ist voll funktionsfähig und nicht beschädigt.

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
   * **WICHTIG:** Deaktiviere die Option `Set up this disk as an LVM group` (Häkchen entfernen mit leertaste), um das System für Einsteiger einfach zu halten.
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

```

Gib deinen Benutzernamen (z. B. `cloudadmin`) und dein Passwort ein.

> 💡 **Hinweis:** Bei der Passworteingabe unter Linux werden aus Sicherheitsgründen keine Sterne oder Punkte angezeigt. Tippe das Passwort blind ein und drücke `Enter`.

Siehst du danach eine Eingabeaufforderung wie `cloudadmin@nextcloud-server:~$`, war die Installation erfolgreich!

---

## 3. Hardwarekonfiguration (Laptop-Spezifika)

*⏱️ Geschätzte Dauer: 20–30 Minuten*

Ein Laptop ist als mobiles Gerät gebaut. Wenn man den Deckel zuklappt, geht er normalerweise in den Standby-Modus (Suspend) und schaltet das WLAN ab. Für einen Server ist das fatal. Das beheben wir jetzt.

---

### Schritt 3.1: Schlafmodus beim Zuklappen deaktivieren

> 💡 **Erklärung:** Der System-Dienst `systemd-logind` verarbeitet Hardware-Ereignisse wie den Deckelsensor. Wir konfigurieren ihn so, dass das Schließen des Laptops ignoriert wird.

1. Öffne die Konfigurationsdatei im Texteditor `nano`:

```bash
sudo nano /etc/systemd/logind.conf

```

*(Das Wort `sudo` führt den Befehl mit Administrator-Rechten aus. Du wirst nach deinem Passwort gefragt).*

2. Suche mit den Pfeiltasten nach folgenden Zeilen (das `#` bedeutet, dass die Zeile inaktiv/auskommentiert ist):

```ini
#HandleLidSwitch=suspend
#HandleLidSwitchExternalPower=suspend
#HandleLidSwitchDocked=ignore

```

3. Entferne das `#` am Anfang dieser drei Zeilen und ändere die Werte jeweils auf `ignore`:

```ini
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore

```

4. **Speichern in Nano:**
* Drücke `Strg` + `O` (Speichern) -> `Enter` drücken.
* Drücke `Strg` + `X` (Editor beenden).


5. Wende die Änderungen ohne Neustart an:

```bash
sudo systemctl restart systemd-logind

```

> 🚨 **Warnsignale:** Falls Nano meldet "Permission denied", hast du vergessen, `sudo` vor den Befehl zu schreiben.

---

### Schritt 3.2: WLAN-Verbindung einrichten (Netplan)

# Ubuntu Server: WLAN über Netplan einrichten (Schritt-für-Schritt)

Diese Anleitung zeigt Schritt für Schritt, wie man bei einer frischen Installation von **Ubuntu Server** das WLAN über **Netplan** einrichtet – komplett ohne grafische Oberfläche und garantiert ohne Frust mit den Abständen.

---

## 🛠️ Schritt 1: Namen der WLAN-Karte herausfinden

Bevor wir konfigurieren, müssen wir wissen, wie Ubuntu deine WLAN-Hardware nennt. 

Gib folgenden Befehl in das Terminal ein:
```bash
ls /sys/class/net
```
Du siehst eine kurze Liste. Suche nach dem Namen, der mit **`wl`** beginnt (z. B. `wlp2s0`, `wlp3s0` oder `wlo1`). 
* *Schreibe dir diesen Namen exakt auf!*

---

## 📄 Schritt 2: Die Konfigurationsdatei leeren

Netplan reagiert extrem empfindlich auf unsichtbare Formatierungsfehler oder alte Einträge. Um Tippfehler und falsche Abstände zu vermeiden, machen wir die Datei zuerst komplett leer:

```bash
sudo truncate -s 0 /etc/netplan/*.yaml
```
*(Falls nach deinem Passwort gefragt wird, tippe es blind ein und drücke Enter).*

---

## 📝 Schritt 3: Die Datei im Editor öffnen

Öffne die leere Netzwerkerweiterung mit dem Texteditor `nano`:

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

---

## 📐 Schritt 4: Die Vorlage exakt abtippen

Kopiere oder tippe den folgenden Block in die Datei. 

⚠️ **ACHTUNG (SEHR WICHTIG):** 
* Benutze **NIEMALS die Tabulator-Taste (Tab)** für die Abstände!
* Nutze **NUR normale Leerzeichen** (Leertaste). Die Einrückungen müssen mathematisch perfekt sein.

In den Klammern steht, wie oft du am Zeilenanfang die Leertaste drücken musst:

```yaml
network:                                      (0 Leerzeichen)
  version: 2                                  (2 Leerzeichen)
  renderer: networkd                          (2 Leerzeichen)
  wifis:                                      (2 Leerzeichen)
    wlp3s0:                                   (4 Leerzeichen) <-- Hier deinen Kartennamen aus Schritt 1 eintragen
      optional: true                          (6 Leerzeichen)
      access-points:                          (6 Leerzeichen)
        "DEIN_WLAN_NAME":                     (8 Leerzeichen) <-- Die "" stehen lassen!
          password: "DEIN_WLAN_PASSWORT"      (10 Leerzeichen) <-- Die "" stehen lassen!
      dhcp4: true                             (6 Leerzeichen)
```

### So speicherst und schließt du den Editor:
1. Drücke **`Strg` + `O`** (Buchstabe O für Open/Ausschreiben).
2. Drücke **`Enter`**, um den Dateinamen zu bestätigen.
3. Drücke **`Strg` + `X`**, um den Editor zu schließen.

---

## 🚀 Schritt 5: WLAN aktivieren & testen

Jetzt aktivieren wir die neue Netzwerkkonfiguration:

```bash
sudo netplan apply
```
*Wenn der Bildschirm danach einfach ruhig bleibt und eine leere Zeile anzeigt, war die Konfiguration fehlerfrei!*

### Der finale Internet-Test:
```bash
ping -c 3 google.com
```
Wenn auf dem Bildschirm erfolgreiche Zeilen wie `64 bytes from...` erscheinen, steht deine Funkverbindung und dein Ubuntu Server ist online!

---

## 🔍 Fehlerbehebung (Troubleshooting)

### 1. Fehler: "inconsistent indentation"
Du hast aus Versehen die Tab-Taste benutzt oder dich bei den Leerzeichen vertan. Öffne die Datei erneut mit `sudo nano` und zähle die Leerzeichen am Zeilenanfang noch einmal genau nach.

### 2. Fehler: "Network is unreachable" beim Ping
Die Datei ist korrekt, aber die Verbindung zum Router schlägt fehl. 
* Überprüfe, ob du deinen WLAN-Namen (SSID) oder dein Passwort in den Anführungszeichen `" "` falsch geschrieben hast (Groß-/Kleinschreibung beachten!).
* Manchmal schläft die WLAN-Karte noch. Wecke sie mit diesem Befehl auf:
  ```bash
  sudo rfkill unblock wifi
  ```


---

### Schritt 3.3: WLAN-Stromsparmodus deaktivieren

> 💡 **Erklärung:** Ältere WLAN-Chipsätze schalten sich bei Inaktivität nach einigen Minuten ab, um Strom zu sparen. Dadurch bricht der Server-Zugriff ab. Wir schalten diese Funktion dauerhaft aus.

1. Erstelle ein automatisches Start-Skript:

```bash
sudo nano /etc/rc.local

```

2. Füge folgenden Inhalt ein (ersetze `wlp2s0` durch deinen WLAN-Namen aus Schritt 3.2):

```bash
#!/bin/sh -e
iw dev wlp2s0 set power_save off
exit 0

```

3. Speichere und mache das Skript ausführbar:

```bash
sudo chmod +x /etc/rc.local

```

---

### 🔍 Validierung von Abschnitt 3

1. Ziehe das LAN-Kabel ab.
2. Klappe den Laptop-Deckel komplett zu.
3. Warte 3 Minuten.
4. Führe folgenden Befehl aus (falls du noch lokal getippt hast) oder prüfe am Router, ob das Gerät online ist:

```bash
ip a show dev wlp2s0

```

Wenn in den Zeilen eine Adresse wie `inet 192.168.178.X` erscheint und der Laptop trotz geschlossenem Deckel läuft, war die Hardware-Konfiguration erfolgreich!

---

## 4. Grundlegende System- & Sicherheitskonfiguration

*⏱️ Geschätzte Dauer: 20–30 Minuten*

In diesem Schritt aktualisieren wir das System, sichern eine feste IP-Adresse und richten eine Firewall sowie den SSH-Fernzugriff ein, damit wir den Laptop fortan ohne Monitor und Tastatur steuern können.

---

### Schritt 4.1: System-Updates installieren

> 💡 **Erklärung:** Vor der Installation neuer Programme muss die Paketliste aktualisiert und bestehende Software auf den neuesten Stand gebracht werden.

```bash
sudo apt update && sudo apt upgrade -y

```

* `apt update`: Lädt die neueste Liste verfügbarer Softwarepakete herunter.
* `apt upgrade -y`: Installiert alle verfügbaren Sicherheitsupdates.

---

### Schritt 4.2: Feste IP-Adresse im Router reservieren

> 💡 **Erklärung:** Wenn der Router deinem Server jeden Tag eine neue IP-Adresse zuweist, müsstest du deine Apps täglich neu konfigurieren. Es ist am einfachsten, die IP-Adresse direkt im Router einzufrieren.

1. Öffne die Benutzeroberfläche deines Routers an deinem Haupt-PC (z. B. `192.168.178.1` bei einer Fritz!Box).
2. Gehe zu den Heimnetz-Einstellungen und suche nach dem Gerät `nextcloud-server`.
3. Aktiviere die Option **"Diesem Netzwerkgerät immer die gleiche IPv4-Adresse zuweisen"**.
4. Notiere dir die IP-Adresse des Servers (z. B. `192.168.178.45`).

---

### Schritt 4.3: SSH-Zugriff vom Haupt-PC testen

> 💡 **Erklärung:** SSH (Secure Shell) ermöglicht es dir, das Terminal des Laptops verschlüsselt über das Netzwerk von deinem Haupt-PC aus zu steuern.

1. Gehe zu deinem Haupt-PC.
2. Öffne die PowerShell (Windows) oder das Terminal (Mac/Linux).
3. Tippe folgenden Befehl ein (ersetze `cloudadmin` und die IP durch deine Daten):

```bash
ssh cloudadmin@192.168.178.45

```

4. Bestätige die erste Verbindung mit der Eingabe von `yes` und gib dein SSH-Passwort ein.

5. Bei der ersten SSH-Verbindung mit einem neuen Gerät zeigt Git/SSH diese Sicherheitsmeldung an, da der Schlüssel des Zielservers noch unbekannt ist.
   Nach der Überprüfung der IP-Adresse im eigenen Netzwerk kann die Meldung sicher durch die Eingabe von yes bestätigt werden.

> 💡 Ab jetzt kannst du die Befehle bequem von deinem Haupt-PC kopieren und ins Terminal einfügen!

---

### Schritt 4.4: Basis-Firewall (UFW) einrichten

> 💡 **Erklärung:** Eine Firewall blockiert unerwünschte Netzwerkanfragen. Wir erlauben explizit nur SSH (Fernsteuerung) sowie HTTP/HTTPS (Webseitenzugriff).

1. Erlaube SSH (WICHTIG, sonst sperrst du dich aus!):

```bash
sudo ufw allow ssh

```

2. Erlaube Datenverkehr für den Webserver:

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

```

3. Aktiviere die Firewall:

```bash
sudo ufw enable

```
(4.) Beim "Aussperren":

```bash
sudo ufw allow ssh

```
```bash
sudo ufw reload

```
---

### 🔍 Validierung von Abschnitt 4

Prüfe den Status der Firewall mit folgendem Befehl:

```bash
sudo ufw status verbose

```

**Erwartetes Ergebnis:**

```text
Status: active

To                         Action      From
--                         ------      ----
22/tcp (SSH)               ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere

```

---

## 5. Docker Installation & Nextcloud

*⏱️ Geschätzte Dauer: 45–60 Minuten*

Aus Zeitgründen setzen wir statt eines klassischen LEMP-Stacks (Linux, Nginx, MariaDB und PHP für Nextcloud) auf Docker, durch dessen Einsatz die aufwendige manuelle Konfiguration entfällt. 
Das System ist sofort einsatzbereit, läuft überall identisch und hält das Host-Betriebssystem dauerhaft sauber.

---

# Lokale Nextcloud-Infrastruktur mit Docker auf Ubuntu Server

Diese Anleitung beschreibt die Schritt-für-Schritt-Installation von **Nextcloud AIO (All-in-One)** über Docker auf einem lokalen Ubuntu Server. Das Setup ist für den Betrieb im heimischen WLAN optimiert und ermöglicht es, private Speicherbereiche für Gäste/Freunde einzurichten.

---

## 📋 Voraussetzungen
* Ein installierter **Ubuntu Server** im lokalen Netzwerk.
* Ein PC/Laptop im selben WLAN für die Einrichtung über den Webbrowser.
* Sudo-Rechte auf dem Ubuntu Server.

---

## 🛠️ Schritt 1: IP-Adresse des Ubuntu-Servers ermitteln

Bevor die Installation startet, muss die lokale Netzwerkadresse des Servers ermittelt werden.

1. Gib im Terminal des Ubuntu-Servers folgenden Befehl ein:
   ```bash
   ip a
   ```
2. Suche nach der primären Netzwerkschnittstelle (z. B. `eth0` oder `enp3s0`). Notiere die IPv4-Adresse (z. B. `192.168.178.50`).
3. **Empfehlung:** Stelle im Router (z. B. Fritz!Box) ein, dass der Ubuntu-Server über DHCP *immer* die gleiche IP-Adresse zugewiesen bekommt.

---

## 🐳 Schritt 2: Docker auf Ubuntu installieren

Dieser Block aktualisiert das System und installiert die offizielle, stabile Version der Docker-Engine direkt aus dem Docker-Repository.

```bash
# System aktualisieren
sudo apt update && sudo apt upgrade -y
sudo apt install -y ca-certificates curl gnupg

# Docker GPG-Schlüssel hinzufügen
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://docker.com | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Repository zu den Apt-Quellen hinzufügen
echo \
  "deb [arch=\$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://docker.com \
  \((. /etc/os-release && echo "\)VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Docker Pakete installieren
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

---

## 🚀 Schritt 3: Speicherordner erstellen & Nextcloud AIO starten

1. Erstelle im Home-Verzeichnis einen permanenten Ordner, in dem die Daten der Cloud-Nutzer gespeichert werden:
   ```bash
   mkdir -p ~/nextcloud_daten
   ```

2. Starte den offiziellen Nextcloud AIO-Mastercontainer. Die Umgebungsvariable `AIO_DISABLE_REVERSE_PROXY_GUARD=true` erlaubt die spätere Initialisierung über die lokale IP-Adresse ohne öffentliche Domain:
   ```bash
   sudo docker run \
     --init \
     --sig-proxy=false \
     --name nextcloud-aio-mastercontainer \
     --restart always \
     --publish 80:80 \
     --publish 8080:8080 \
     --publish 8443:8443 \
     --volume nextcloud_aio_mastercontainer:/mnt/docker-aio-config \
     --volume /var/run/docker.sock:/var/run/docker.sock:ro \
     -e NEXTCLOUD_DATADIR="/home/\$USER/nextcloud_daten" \
     -e AIO_DISABLE_REVERSE_PROXY_GUARD=true \
     -d nextcloud/all-in-one:latest
   ```

---

## 🌐 Schritt 4: Einrichtung im Webbrowser (Admin-Setup)

1. Öffne den Browser auf einem PC/Laptop im selben Netzwerk.
2. Rufe das AIO-Dashboard über die Server-IP auf Port **8080** via **HTTPS** auf:
   *Beispiel:* `https://192.168.178.50:8080`
3. Ignoriere die Sicherheitswarnung des Browsers (auf *Erweitert* -> *Risiko akzeptieren und weiter* klicken), da im lokalen Netz standardmäßig kein SSL-Zertifikat vorliegt.
4. **Kopiere das angezeigte AIO-Passwort** und melde dich an.
5. Gib bei der Domain-Abfrage die lokale **IP-Adresse des Servers** ein.
6. Scrolle nach unten und klicke auf **"Containers starten"** (Download and start containers). Docker lädt nun Nextcloud, PostgreSQL, Redis und Apache im Hintergrund. Dieser Vorgang dauert ca. 3–5 Minuten.
7. Sobald alle Statusanzeigen grün leuchten, kopiere die generierten **Admin-Zugangsdaten** (Benutzername: `admin` + Passwort).

---

## 👥 Schritt 5: Account für Gäste / Freunde anlegen

1. Klicke im Dashboard auf **"Öffne deine Nextcloud"** (Open your Nextcloud) und logge dich mit den Admin-Daten aus Schritt 4 ein.
2. Klicke oben rechts auf das Admin-Profilbild und wähle den Menüpunkt **Benutzer**.
3. Klicke oben links auf **+ Neuer Benutzer**.
4. Definiere die Zugangsdaten für den Gast:
   * **Benutzername:** z. B. `gast`
   * **Passwort:** Ein sicheres Kennwort eintragen.
   * **Kontingent:** Setze ein Speicherlimit (z. B. `20 GB`), um ein unkontrolliertes Vollaufen der Server-Festplatte zu verhindern.
5. Bestätige die Erstellung mit dem blauen Haken am Ende der Zeile.

---

## 📱 Schritt 6: Smartphone des Gastes verbinden

Sobald der Gast im lokalen WLAN angemeldet ist, kann er sein Gerät wie folgt koppeln:

1. Installiere die offizielle und kostenlose **Nextcloud App** aus dem Google Play Store oder Apple App Store.
2. Öffne die App und tippe auf **Anmelden**.
3. Gib als Server-Adresse die IP-Adresse des Ubuntu-Servers mit `http://` ein (die Nutzung von unverschlüsseltem HTTP vereinfacht die lokale Verbindung in der App ohne eigene Zertifikatskette):
   *Beispiel:* `http://192.168.178.50`
4. Melde dich mit dem in Schritt 5 erstellten Benutzernamen (`gast`) und dem dazugehörigen Passwort an.
5. Die App ist nun einsatzbereit. Der automatische Foto-Upload kann optional in den App-Einstellungen aktiviert werden.



---

## 6. Fernzugriff & Verschlüsselung (TLS/SSL)

*⏱️ Geschätzte Dauer: 30–45 Minuten*

Damit du und deine Freunde auch unterwegs von außerhalb des heimischen WLANs sicher auf die Cloud zugreifen können, gibt es zwei Wege. Wähle eine der beiden Optionen:

---

### Option A: Cloudflare Tunnel (Empfohlen – Keine Portfreigabe nötig)

> 💡 **Erklärung:** Ein Cloudflare Tunnel baut eine ausgehende, verschlüsselte Verbindung vom Laptop zu Cloudflare auf. Du musst keine Ports im heimischen Router öffnen und schützt deinen Anschluss vor DDoS-Angriffen.

* **Voraussetzung:** Eine eigene Domain (gibt es für ca. 1–2 €/Jahr) und ein kostenloses Cloudflare-Konto.

1. Installiere den Cloudflare-Dienst auf dem Server:

```bash
sudo mkdir -p /usr/local/etc/cloudflared
curl -L --output cloudflared [https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64](https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64)
sudo chmod +x cloudflared
sudo mv cloudflared /usr/local/bin/

```

2. Verbinde den Dienst mit deinem Cloudflare-Account:

```bash
cloudflared tunnel login

```

3. Kopiere den ausgegebenen Link in deinen Browser und autorisiere deine Domain.
4. Erstelle den Tunnel und folge den Anweisungen im Dashboard unter **Zero Trust -> Access -> Tunnels**, um deine Subdomain (z. B. `cloud.deinedomain.de`) auf `http://localhost:80` deines Laptops zu leiten.

---

### Option B: Klassisch über DynDNS & Let's Encrypt SSL

> 💡 **Erklärung:** Wenn du keine eigene Domain kaufen möchtest, verknüpfst du deinen Anschluss mit einer kostenlosen Subdomain (z. B. von DuckDNS) und öffnest die Ports im Router.

#### 1. Portfreigabe im Router einrichten

Erstelle in den Einstellungen deines Routers zwei Freigaben für deinen Server:

* **Port 80** (HTTP)
* **Port 443** (HTTPS)

#### 2. Kostenloses SSL-Zertifikat installieren

Installiere das Tool Certbot, welches automatisch kostenlose Zertifikate von Let's Encrypt bezieht:

```bash
sudo apt install -y certbot python3-certbot-nginx

```

Zertifikat anfordern (ersetze die Beispiel-Domain durch deine DuckDNS-Domain):

```bash
sudo certbot --nginx -d meine-cloud.duckdns.org

```

Folge den Anweisungen im Menü. Certbot konfiguriert Nginx automatisch für eine verschlüsselte HTTPS-Verbindung.

#### 3. Domain in Nextcloud als vertrauenswürdig eintragen

Damit Nextcloud Anfragen über die neue Adresse akzeptiert, muss diese in die Konfigurationsdatei aufgenommen werden.

1. Öffne die Nextcloud-Konfiguration:

```bash
sudo nano /var/www/nextcloud/config/config.php

```

2. Suche nach dem Block `trusted_domains` und füge deine Domain als zweite Zeile ein:

```php
  'trusted_domains' => 
  array (
    0 => '192.168.178.45',
    1 => 'meine-cloud.duckdns.org',
  ),

```

3. Speichere die Datei (`Strg` + `O` -> `Enter` -> `Strg` + `X`).

---

### 🔍 Validierung von Abschnitt 6

Schalte die WLAN-Verbindung an deinem Smartphone aus (nutze das mobile Datennetz) und rufe deine Domain (`https://meine-cloud.duckdns.org` bzw. deine Cloudflare-Domain) im Browser auf.

**Erfolg:** Nextcloud lädt verschlüsselt (mit einem Schloss-Symbol in der Browserleiste) über das Internet!

---

## 7. Wartung & Troubleshooting

*⏱️ Geschätzte Dauer: Fortlaufend / Nach Bedarf*

### 🛠️ Regelmäßige Systempflege

Führe einmal im Monat folgende Befehle aus, um dein Linux-System sicher zu halten:

```bash
sudo apt update && sudo apt upgrade -y

```

---

### 🚨 Häufige Fehler & Lösungen

#### Fehler 1: "Internal Server Error" (500) beim Aufruf von Nextcloud

* **Ursache:** Falsche Dateiberechtigungen im Nextcloud-Ordner.
* **Lösung:** Setze die Besitzerrechte für den Webserver-User zurück:

```bash
sudo chown -R www-data:www-data /var/www/nextcloud

```

#### Fehler 2: Nextcloud meldet Performance-Warnungen bezüglich "Memory Cache"

* **Ursache:** Der Arbeitsspeicher-Zwischenspeicher (Redis) ist installiert, aber noch nicht in Nextcloud eingetragen.
* **Lösung:** Öffne die `config.php`:

```bash
sudo nano /var/www/nextcloud/config/config.php

```

Füge folgende Zeilen vor der schließenden Klammer `);` ein:

```php
  'memcache.local' => '\OC\Memcache\Redis',
  'memcache.locking' => '\OC\Memcache\Redis',
  'redis' => array(
     'host' => 'localhost',
     'port' => 6379,
  ),

```

#### Fehler 3: Nginx lässt sich nach Änderung nicht neu starten

* **Lösung:** Prüfe das Log-File auf den genauen Zeilenfehler:

```bash
sudo nginx -t
sudo tail -n 20 /var/log/nginx/error.log

```

---

## 🔗 Weiterführende Links & Quellen

* [Ubuntu Server Official Documentation](https://www.google.com/search?q=https://canonical.com/ubuntu/server/docs)
* [Nextcloud Administration Manual](https://docs.nextcloud.com/server/latest/admin_manual/)
* [Let's Encrypt / Certbot Documentation](https://certbot.eff.org/)

---

*Erstellt für die GitHub-Referenz-Dokumentation. Viel Spaß mit deinem eigenen Cloud-Server!*

```

```
