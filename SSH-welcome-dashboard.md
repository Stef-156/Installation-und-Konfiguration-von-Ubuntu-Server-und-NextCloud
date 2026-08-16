## Automatisches System-Dashboard beim Login
Um beim Verbinden über SSH sofort einen Überblick über den Serverstatus zu erhalten, wurde Fastfetch auf dem Ubuntu-System installiert. Der Befehl wurde in die .bashrc integriert, sodass bei jedem SSH-Login automatisch ein kompaktes Dashboard mit Hardware-Daten (CPU, RAM, Festplatte) und dem Betriebssystem-Logo angezeigt wird.
## Einrichtungsschritte & Befehle:

   1. Fastfetch installieren:
   
   sudo apt update && sudo apt install -y fastfetch
   
   2. Autostart in der .bashrc einrichten:
   
   nano ~/.bashrc
   
   Bewege den Cursor ganz nach unten, füge in einer neuen Zeile das Wort fastfetch hinzu, speichere mit Strg + O (Enter) und beende mit Strg + X.
   3. Verbindung testen:
   
   exit
   
   Nach dem nächsten SSH-Login startet das Dashboard automatisch.
