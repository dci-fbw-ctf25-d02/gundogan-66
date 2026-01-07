# Dateisystem-Berechtigungen und Paketverwaltung

- Wenn du den Befehl `ls -l` in deinem Terminal ausführst, siehst du diese Berechtigungen ganz links in der Ausgabe.
- Eine der Zeilen könnte so aussehen:

```bash
drwxrwxrwx 11 aretas sudo        4096 Jan  5 15:27  Documents
```

<br><br><br>

## Das grundlegende Berechtigungssystem

- Dateien und Ordner auf deinem Computer sind wie Häuser.
- Das Berechtigungssystem ist ein Regelwerk, das festlegt:
  - wer das Haus **betreten** darf,
  - wer Dinge drinnen **verändern** darf, und
  - wer **Schlüsselkopien** für andere machen darf.
- In Unix-artigen Systemen (wie Linux oder macOS) gibt es **drei "Personenebenen"**, für die diese Regeln gelten:
  - **der Besitzer** der Datei,
  - **die Gruppe**, die mit der Datei verbunden ist, und
  - alle anderen Benutzer (others).
- Wir werden Gruppen und Benutzer behandeln, wenn wir später im Kurs über virtuelle Maschinen sprechen.
  <br><br><br>

### Der Besitzer (Owner)

- Das ist die Person, der die Datei oder der Ordner gehört – in der Regel diejenige, die sie erstellt hat.
- Stell dir sie als den "Eigentümer" des Hauses vor.
- Der Besitzer hat in der Regel die meisten Rechte und kann entscheiden, wer sonst noch Zugang bekommt.
- In der Befehlszeile wird der Besitzer mit `u` ("user") abgekürzt.

<br><br><br>

### Die Gruppe (Group)

- Jede Datei gehört auch zu einer bestimmten Gruppe.
- Stell dir das als eine Familie, ein Team oder einen Verein vor.
- Alle Benutzer, die Mitglieder dieser Gruppe sind, haben die gleichen Gruppenberechtigungen für die Datei.
- Das ist nützlich, wenn du mit anderen an einem Projekt arbeitest.
- Anstatt jedem Benutzer einzeln Rechte zu geben, gibst du der Gruppe die Rechte.
- In der Befehlszeile wird die Gruppe mit `g` abgekürzt.

<br><br><br>

### Andere (Others)

- Das ist im Grunde der Rest der Welt.
- Jeder Benutzer im System, der weder Besitzer der Datei ist noch Mitglied der Dateigruppe, fällt in diese Kategorie.
- Die Berechtigungen für "Andere" sind normalerweise am stärksten eingeschränkt, um die Datei vor unbefugtem Zugriff zu schützen.
- In der Befehlszeile werden Andere mit `o` abgekürzt.

<br><br><br>

### Die drei grundlegenden Berechtigungen (r, w, x)

Für jede der drei Ebenen (Besitzer, Gruppe, Andere) kannst du drei grundlegende Rechte erteilen, die durch die Buchstaben `r`, `w` und `x` dargestellt werden.

- **`r` (read):** Das Recht, den Inhalt einer Datei zu lesen oder den Inhalt eines Verzeichnisses aufzulisten.
- **`w` (write):** Das Recht, eine Datei zu ändern oder Dateien in einem Ordner zu erstellen, zu löschen oder umzubenennen.
- **`x` (execute):** Das Recht, eine Datei als Programm oder Skript auszuführen oder ein Verzeichnis zu betreten ("cd" hinein).

- Die Bedeutung dieser Rechte unterscheidet sich leicht, je nachdem, ob es sich um eine Datei oder einen Ordner handelt.
- Hier ist eine Tabelle zur Verdeutlichung:

| Berechtigung      | Bedeutung für eine **Datei**                                  | Bedeutung für ein **Verzeichnis (Ordner)**                                                                                                       |
| :---------------- | :------------------------------------------------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------- |
| **`r` (read)**    | Du darfst den Inhalt anzeigen/lesen (z.B. mit `cat`, `less`). | Du darfst den Inhalt auflisten (Datei-/Unterordnernamen sehen, z.B. mit `ls`).                                                                   |
| **`w` (write)**   | Du darfst die Datei ändern, überschreiben oder leeren.        | Du darfst Dateien/Ordner im Verzeichnis erstellen, löschen oder umbenennen. **Hinweis:** Dies gilt für das Verzeichnis, nicht für seine Dateien! |
| **`x` (execute)** | Du darfst die Datei als Programm oder Skript ausführen.       | Du darfst das Verzeichnis betreten (`cd`) und auf seinen Inhalt zugreifen (sofern du die entsprechenden Berechtigungen für diese Dateien hast).  |

Wenn du den Befehl `ls -l` in deinem Terminal ausführst, siehst du diese Berechtigungen ganz links in der Ausgabe. Eine Zeile könnte so aussehen:

```bash
drwxrwxrwx 11 aretas sudo        4096 Jan  5 15:27  Documents
```

- Lass uns `-rwxr-xr--` aufschlüsseln:

- **Erstes Zeichen (`-`):** Dateityp. `-` bedeutet eine reguläre Datei, `d` würde Verzeichnis bedeuten.
- **Zeichen 2-4 (`rwx`):** Berechtigungen für **Besitzer**. Hier darf der Besitzer lesen, schreiben und ausführen.
- **Zeichen 5-7 (`r-x`):** Berechtigungen für **Gruppe**. Hier darf die Gruppe lesen und ausführen, aber **nicht** schreiben (`-` bedeutet, dass die Berechtigung fehlt).
- **Zeichen 8-10 (`r--`):** Berechtigungen für **Andere**. Hier dürfen Andere nur lesen, nicht schreiben oder ausführen.
- Insgesamt bedeutet `-rwxr-xr--`, dass der Besitzer volle Rechte hat, die Gruppe lesen und ausführen darf, und Andere nur lesen dürfen.
- Manchmal steht auch ein `l` am Anfang, was bedeutet, dass es sich um einen symbolischen Link handelt (eine Art Verknüpfung zu einer anderen Datei oder einem anderen Ordner).

<br><br><br>

## Tools zur Verwaltung von Berechtigungen

- Um Besitzer und Berechtigungen zu ändern, gibt es drei wichtige Befehlszeilen-Tools: `chmod`, `chown` und `chgrp`.

<br><br><br>

### `chmod`: Zugriffsrechte ändern

- `chmod` steht für "change mode" und wird verwendet, um `rwx`-Berechtigungen für eine Datei oder einen Ordner zu ändern.
- Es gibt zwei Methoden, um `chmod` zu verwenden:
  - symbolische und
  - numerische (oktale) Methoden.

#### Symbolische Methode

- Diese ist intuitiv und leicht zu lesen.
- Die Struktur ist: `chmod [Wer][Aktion][Berechtigung] [Dateiname]`.

- **Wer?**
  - `u` (user/Besitzer)
  - `g` (group/Gruppe)
  - `o` (others/Andere)
  - `a` (all — entspricht `u`, `g` und `o` zusammen)
- **Aktion?**
  - `+` (ein Recht hinzufügen)
  - `-` (ein Recht entfernen)
  - `=` (Rechte genau wie angegeben setzen, vorherige Einstellung überschreiben)
- **Berechtigung?**
  - `r` (read/Lesen)
  - `w` (write/Schreiben)
  - `x` (execute/Ausführen)

**Beispiele:**

- Wenn du ein Skript für dich selbst ausführbar machen möchtest:

```bash
chmod u+x mein_skript.sh
```

(Fügt die Ausführungsberechtigung für den **U**ser/Benutzer hinzu)

- Wenn du die Schreibberechtigung für deine Gruppe entfernen möchtest:

  ```bash
  chmod g-w projektdatei.txt
  ```

  (Entfernt **W**rite/Schreiben für **G**roup/Gruppe)

#### Numerische (Oktale) Methode

Beliebt bei erfahrenen Benutzern wegen Geschwindigkeit und Kürze. Jedes Recht hat einen numerischen Wert:

- `r` (read/Lesen) = **4**
- `w` (write/Schreiben) = **2**
- `x` (execute/Ausführen) = **1**
- Kein Recht = **0**

Für jede Ebene (Besitzer, Gruppe, Andere) addierst du die Werte, um die Berechtigungen festzulegen.

| Berechtigung | Summe         | Bedeutung                                       |
| :----------- | :------------ | :---------------------------------------------- |
| `---`        | 0+0+0 = **0** | Keine Rechte                                    |
| `--x`        | 0+0+1 = **1** | Nur ausführen                                   |
| `-w-`        | 0+2+0 = **2** | Nur schreiben                                   |
| `-wx`        | 0+2+1 = **3** | Schreiben und ausführen                         |
| `r--`        | 4+0+0 = **4** | Nur lesen                                       |
| `r-x`        | 4+0+1 = **5** | Lesen und ausführen                             |
| `rw-`        | 4+2+0 = **6** | Lesen und schreiben                             |
| `rwx`        | 4+2+1 = **7** | Lesen, schreiben und ausführen (voller Zugriff) |

Du verwendest eine dreistellige Zahl: erste Ziffer für Besitzer, zweite für Gruppe, dritte für Andere.

**Beispiele:**

- Gib einer Textdatei typische Rechte: Besitzer kann lesen und schreiben, Gruppe und Andere nur lesen.

  - Besitzer: `rw-` = 4 + 2 = **6**
  - Gruppe: `r--` = **4**
  - Andere: `r--` = **4**

  - Der Befehl:
    ```bash
    chmod 644 wichtige_notiz.txt
    ```

<br><br><br>

### `chown`: Besitzer ändern

- `chown` steht für "change owner" und tut genau das: ändert den Besitzer und/oder die Gruppe einer Datei oder eines Ordners.
- Dieser Befehl ist besonders wichtig für Systemadministratoren (`root`), da normale Benutzer normalerweise nicht beliebig den Besitz ihrer Dateien auf andere übertragen können.

- Syntax: `chown [neuer_besitzer] [Dateiname]`

#### Beispiel:

- Die Datei `bericht.docx` gehört derzeit dem Benutzer `peter`.
- Du (als `root`) möchtest sie dem Benutzer `anna` zuweisen.

```bash
chown anna bericht.docx
```

#### Beispiel:

- Der Ordner `/var/www/projekt_alpha` muss dem Benutzer `www-data` und der Gruppe `www-data` gehören.

  ```bash
  chown www-data:www-data /var/www/projekt_alpha
  ```

- Wenn du möchtest, dass die Änderung für alle Dateien und Unterordner innerhalb eines Ordners gilt, verwende die `-R`-Option (rekursiv).

#### Beispiel:

- Weise den gesamten Projektordner und alle seine Inhalte dem Benutzer `max` und der Gruppe `developers` zu:

```bash
chown -R max:developers /home/max/projekte/super_projekt
```

<br><br><br>

## Paketverwaltung in Linux-Systemen

- Stell dir die Paketverwaltung als einen riesigen App-Store für dein Linux-System vor.
- Anstatt Software manuell von Webseiten herunterzuladen und selbst zu installieren, hast du ein zentrales Tool, das alles für dich erledigt.
- Dieses Tool nennt sich `Paketmanager`.

- Ein **"Paket"** ist mehr als nur das Programm selbst.
- Es ist ein sorgfältig gepacktes Bündel, das Folgendes enthält:

  - Die eigentliche Software (die ausführbaren Dateien).
  - Informationen darüber, welche anderen Pakete ("Abhängigkeiten") benötigt werden, damit die Software funktioniert.
  - Konfigurationsdateien.
  - Anweisungen für den Paketmanager, wie alles ordnungsgemäß installiert, aktualisiert oder entfernt werden soll.

- Die drei bekanntesten Paketmanager-Familien in der Linux-Welt sind `apt`, `yum` und `dnf`.
- Welchen du verwendest, hängt von deiner Distribution ab.
- Da wir in diesem Kurs hauptsächlich mit Debian-basierten Systemen arbeiten, wird `apt` unser Hauptfokus sein.

<br><br><br>

### Apt (Advanced Package Tool) Paketmanager

- `apt` wird hauptsächlich auf Debian-basierten Distributionen verwendet, wie:

  - **Debian**
  - **Ubuntu** (und seine Varianten wie Kubuntu, Xubuntu usw.)
  - **Linux Mint**
  - **Raspberry Pi OS**

- `apt` ist bekannt für seine Stabilität und die riesige Auswahl an verfügbarer Software in seinen Repositories.

#### Grundlagen: Pakete installieren und entfernen

- Um Softwarepakete zu installieren oder zu entfernen, verwendest du bestimmte Befehle mit deinem Paketmanager.
- Diese Befehle erfordern fast immer `sudo`, da du Systemdateien änderst und Administratorrechte benötigst.

##### ➕ Ein Paket installieren

- Der grundlegende Installationsbefehl ist fast immer `install` gefolgt vom Paketnamen.
- Der Paketmanager lädt es automatisch herunter und installiert es zusammen mit allen benötigten Abhängigkeiten.

| Paketmanager | Befehl                         | Beispiel (installiert den Prozessmanager `htop`) |
| :----------- | :----------------------------- | :----------------------------------------------- |
| **apt**      | `sudo apt install [Paketname]` | `sudo apt install htop`                          |

- Bevor die Installation beginnt, zeigt der Paketmanager an, welche Pakete (das gewünschte und seine Abhängigkeiten) installiert werden und wie viel Speicherplatz benötigt wird.
- Du musst normalerweise mit `y` (yes/ja) bestätigen.

##### ➖ Ein Paket entfernen

- Wenn du Software nicht mehr benötigst, kannst du sie ordentlich deinstallieren; der Paketmanager stellt sicher, dass nichts kaputt geht, was andere Programme noch brauchen.

| Paketmanager | Befehl                        | Beispiel (entfernt den Prozessmanager `htop`) |
| :----------- | :---------------------------- | :-------------------------------------------- |
| **apt**      | `sudo apt remove [Paketname]` | `sudo apt remove htop`                        |

**Wichtiger Hinweis zu `apt`:**

- `apt remove`: Entfernt nur das Programmpaket. Systemweite Konfigurationsdateien werden behalten. Das ist nützlich, wenn du es später vielleicht wieder installierst.
- `apt purge`: Entfernt das Programmpaket **und** alle seine systemweiten Konfigurationsdateien. Das ist eine radikalere Deinstallation, die das System sauberer hinterlässt, wenn du die Software nie wieder möchtest.

<br><br><br>

#### 🔄 Softwarepakete aktualisieren

- Dein System auf dem neuesten Stand zu halten ist entscheidend.
- Updates beheben Sicherheitslücken, korrigieren Fehler und fügen manchmal Funktionen hinzu.
- Der Prozess variiert leicht zwischen Paketmanagern, folgt aber einer ähnlichen Logik.

- Der Update-Prozess mit `apt` ist in zwei Schritte unterteilt:

1.  **Schritt 1: Einkaufsliste aktualisieren.**

- `sudo apt update`
- Dein System prüft bei den Repositories: "Was gibt es Neues seit meinem letzten Besuch?"
- Es lädt eine frische Liste aller verfügbaren Pakete und Versionen herunter. **Es wird noch keine Software tatsächlich aktualisiert!**

2.  **Schritt 2: Einkaufen gehen.**

- `sudo apt upgrade`
- Jetzt, da dein System die neue Liste hat, vergleichst du sie mit dem, was du installiert hast.
- Der upgrade-Befehl sagt: "OK, jetzt lade neue Versionen für alle meine installierten Pakete herunter und installiere sie."

```bash
├── index.html
├── script.js
└── styles.css
```
