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
  * [Ubuntu Server 24.04 LTS ISO](https://ubuntu.com/download/server) (*LTS = Long Term Support*: stabil und 5 Jahre mit Updates versorgt).
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

> 💡 **Erklärung:** Ubuntu nutzt das Werkzeug Netplan zur Netzwerksteuerung. Die Konfiguration erfolgt über Textdateien im YAML-Format.

> ⚠️ **WICHTIG bei YAML:** Rücke Text niemals mit der Tabulatortaste ein, sondern nutze ausschließlich Leerzeichen! YAML reagiert extrem empfindlich auf falsche Einrückungen.

1. Ermittle den Namen deiner WLAN-Schnittstelle:

```bash
ip link

```

Suche nach einem Eintrag, der mit `w` beginnt, z. B. `wlan0` oder `wlp2s0`.

2. Bearbeite die Netplan-Datei:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml

```

3. Passe den Inhalt wie folgt an (ersetze `wlp2s0`, `Dein-WLAN-Name` und `Dein-WLAN-Passwort`):

```yaml
network:
  version: 2
  renderer: networkd
  wifis:
    wlp2s0:
      optional: true
      access-points:
        "Dein-WLAN-Name":
          password: "Dein-WLAN-Passwort"
      dhcp4: true

```

4. Speichere die Datei (`Strg` + `O` -> `Enter` -> `Strg` + `X`).
5. Überprüfe die Datei auf Syntaxfehler und wende sie an:

```bash
sudo netplan apply

```

> 🚨 **Warnsignale:** Erhältst du beim Ausführen von `netplan apply` die Fehlermeldung `line X column Y: mapping values are not allowed here`, hast du Tabulatoren oder falsche Leerzeichen verwendet.

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

## 5. Nextcloud Installation & Datenbank-Setup

*⏱️ Geschätzte Dauer: 45–60 Minuten*

Wir installieren nun den sogenannten **LEMP-Stack**:

* **L**inux (Betriebssystem)
* **E**ngine-X / Nginx (Webserver)
* **M**ariaDB (Datenbank)
* **P**HP (Programmiersprache für Nextcloud)

---

### Schritt 5.1: Software-Abhängigkeiten installieren

> 💡 **Erklärung:** Nextcloud benötigt den Nginx-Webserver, die MariaDB-Datenbank sowie verschiedene PHP-Module zur Verarbeitung von Bildern, Dateien und Verschlüsselungen.

Führe diesen zusammengefassten Befehl aus:

```bash
sudo apt install -y nginx mariadb-server php-fpm php-mysql php-curl php-gd php-json php-common php-xml php-mbstring php-gmp php-bcmath php-imagick php-xmlrpc php-zip php-intl php-cli php-tokyocabinet php-redis redis-server unzip

```

---

### Schritt 5.2: MariaDB-Datenbank einrichten

> 💡 **Erklärung:** Nextcloud speichert Benutzerdaten, Pfade, Passwörter und Einstellungen in einer Datenbank.

1. Sichere die Grundinstallation von MariaDB ab:

```bash
sudo mysql_secure_installation

```

Beantworte die Fragen im Dialog wie folgt:

* **Enter current password for root:** `Enter` drücken (keins gesetzt).
* **Switch to unix_socket authentication:** `n`
* **Change the root password?:** `y` -> Vergib ein starkes Root-Passwort für die Datenbank!
* **Remove anonymous users?:** `y`
* **Disallow root login remotely?:** `y`
* **Remove test database and access to it?:** `y`
* **Reload privilege tables now?:** `y`

2. Erstelle die Nextcloud-Datenbank:

Öffne die Datenbank-Konsole:

```bash
sudo mysql -u root -p

```

*(Gib dein eben vergebenes Datenbank-Root-Passwort ein).*

Führe nun folgende Befehle exakt aus. Ersetze `SicheresPasswort123!` durch dein eigenes sicheres Passwort!

```sql
CREATE DATABASE nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
CREATE USER 'nextclouduser'@'localhost' IDENTIFIED BY 'SicheresPasswort123!';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

```

---

### Schritt 5.3: Nextcloud herunterladen & entpacken

> 💡 **Erklärung:** Wir laden das offizielle Nextcloud-Paket herunter, entpacken es und verschieben es in das Standardverzeichnis des Webservers.

1. Wechsle in den temporären Ordner und lade das Paket herunter:

```bash
cd /tmp
wget [https://download.nextcloud.com/server/releases/latest.zip](https://download.nextcloud.com/server/releases/latest.zip)

```

2. Entpacke die Archiv-Datei:

```bash
unzip latest.zip

```

3. Verschiebe den entpackten Ordner in das Web-Verzeichnis:

```bash
sudo mv nextcloud /var/www/

```

4. Übergib die Berechtigungen an den Webserver-Benutzer `www-data`:

```bash
sudo chown -R www-data:www-data /var/www/nextcloud

```

---

### Schritt 5.4: Nginx-Webserver konfigurieren

> 💡 **Erklärung:** Nginx muss wissen, unter welcher Adresse und mit welchen PHP-Einstellungen Nextcloud ausgeliefert werden soll.

1. Erstelle eine Konfigurationsdatei für Nextcloud:

```bash
sudo nano /etc/nginx/sites-available/nextcloud

```

2. Füge folgenden Inhalt ein. Passe die Zeile `server_name` an die IP-Adresse deines Laptops an!

```nginx
server {
    listen 80;
    server_name 192.168.178.45; # <--- DEINE LAPTOP IP EINTRAGEN

    root /var/www/nextcloud;
    index index.php index.html;

    client_max_body_size 512M; # Maximale Dateiupload-Größe
    fastcgi_buffers 64 4K;

    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Robots-Tag none;
    add_header X-Download-Options noopen;
    add_header X-Frame-Options "SAMEORIGIN";

    location / {
        rewrite ^ /index.php;
    }

    location ~ ^/(?:build|tests|config|lib|3rdparty|templates|data)/ {
        deny all;
    }

    location ~ ^/(?:\.|autotest|occ|issue|credencials|suitemeta) {
        deny all;
    }

    location ~ \.php(?:$|/) {
        rewrite ^/(?!index|remote|public|cron|core/ajax/update|status|ocs/v[12]|updater/.+|oc[ms]-provider/.+).+$ /index.php$request_uri;

        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        set $path_info$fastcgi_path_info;
        try_files $fastcgi_script_name =404;

        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $path_info;
        fastcgi_param HTTPS off;

        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
        fastcgi_intercept_errors on;
        fastcgi_request_buffering off;
    }

    location ~ \.(?:css|js|woff2?|svg|gif|map)$ {
        try_files $uri /index.php$request_uri;
        add_header Cache-Control "public, max-age=15778463";
    }

    location ~ \.(?:png|html|ttl|ico)$ {
        try_files $uri /index.php$request_uri;
    }
}

```

3. Aktiviere die neue Konfiguration und deaktiviere die Standard-Beispielseite:

```bash
sudo ln -s /etc/nginx/sites-available/nextcloud /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default

```

4. Prüfe die Datei auf Syntaxfehler:

```bash
sudo nginx -t

```

5. Wenn `syntax is ok` erscheint, starte den Webserver neu:

```bash
sudo systemctl restart nginx

```

> 🚨 **Warnsignale:** Meldet `nginx -t` einen Fehler bezüglich `php8.3-fpm.sock`, prüfe mit `ls /run/php/`, welche PHP-Version auf deinem Server installiert wurde und passe die Pfad-Zeile in der Nginx-Datei entsprechend an.

---

### Schritt 5.5: Web-Installation im Browser abschließen

1. Öffne den Browser auf deinem Haupt-PC und gib die IP des Laptops ein: `http://192.168.178.45`.
2. Fülle die Eingabefelder im Installationsmenü aus:
* **Administrator-Konto erstellen:** Erstelle hier deinen Wunschnamen und ein Passwort für das Nextcloud-Admin-Konto.
* **Speicher & Datenbank:** Klicke auf **MySQL/MariaDB**.
* **Datenbank-Benutzer:** `nextclouduser`
* **Datenbank-Passwort:** Das in Schritt 5.2 gewählte Passwort (`SicheresPasswort123!`).
* **Datenbank-Name:** `nextcloud`
* **Host:** `localhost`


3. Klicke auf **Einrichtung abschließen**.

> 💡 **Hinweis:** Der Erstinstallationprozess kann auf 12 Jahre alter Hardware bis zu 3 Minuten dauern. Bitte das Browser-Fenster nicht schließen oder aktualisieren!

---

### 🔍 Validierung von Abschnitt 5

Nach Abschluss wirst du automatisch zum Dashboard deiner neuen Nextcloud weitergeleitet. Das System läuft lokal!

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
