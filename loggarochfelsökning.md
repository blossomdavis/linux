Här är en strukturerad sammanfattning av alla dina resurser, anpassad för dig som student. Jag har delat upp det i fyra logiska delar så att det blir lättare att plugga in!

### 1. Grundläggande om loggar i Linux (rsyslog & logrotate)

Loggar är systemets "dagbok". Nästan allt som händer i Linux (inloggningar, applikationsfel, nätverkshändelser) sparas som text så att du i efterhand kan ta reda på **vad** som hände och **varför**.

* **Rsyslog (Hjärtat i loggningen):** Detta är en bakgrundstjänst (daemon) som samlar in loggmeddelanden från hela systemet och skickar dem till rätt fil. Den styrs av konfigurationsfilen `/etc/rsyslog.conf` och jobbar utifrån regler (t.ex. "om det är ett säkerhetsfel, spara det här").
* **Loggrotation (logrotate):** Loggar växer snabbt och kan fylla upp hela hårddisken. `logrotate` löser detta genom att automatiskt zippa (komprimera) och arkivera gamla loggar, och till slut radera de allra äldsta, vilket sparar utrymme men bevarar viktig historik.

### 2. Vart loggarna finns och hur du läser dem

Nästintill alla loggar i Linux sparas i mappen `/var/log`. För att hitta rätt måste du veta vilken typ av problem du letar efter:

**Viktiga loggfiler:**

* `/var/log/syslog` – Den generella systemloggen (Hittar du det inte någon annanstans, leta här).
* `/var/log/auth.log` – Säkerhet och autentisering (t.ex. lyckade/misslyckade lösenordsförsök och `sudo`-kommandon).
* `/var/log/kern.log` – Loggar direkt från operativsystemets kärna (hårdvarufel etc.).
* `/var/log/dameon.log` – Loggar från bakgrundstjänster (t.ex. Bluetooth, SSH).

**Kommandon för att läsa textloggar:**

* `cat [filnamn]` – Skriver ut hela filens innehåll direkt i terminalen.
* `less [filnamn]` – Öppnar filen så att du kan scrolla och söka (tryck `q` för att avsluta).
* `tail -f [filnamn]` – **Viktigast!** Visar slutet på en fil i realtid. Perfekt när du felsöker och vill se nya fel dyka upp direkt.
* `grep "sökord" [filnamn]` – Söker efter specifika ord (t.ex. "error") i en loggfil.

*(Notera: Vissa loggar, som inloggningshistorik i `/var/log/wtmp`, är binära och måste läsas med särskilda kommandon som `who` eller `last`).*

### 3. Felsökning (Debugging) i Linux

När något går fel räcker det inte alltid att bara läsa loggar. Här är de vanligaste verktygen för att felsöka systemet i realtid:

* **Processer & CPU:**
* `top` – Visar alla processer i realtid (lite som Aktivitetshanteraren i Windows).
* `ps aux` – Listar alla processer som körs just nu.
* `strace -p [PID]` – Avancerat verktyg som visar exakt vilka systemanrop en process gör (bra om ett program hänger sig).


* **Nätverk:**
* `ss -tuln` eller `netstat -tuln` – Visar vilka portar som är öppna och lyssnar på trafik.
* `ping [ip/domän]` – Kollar om du har nätverkskontakt med en annan maskin.
* `sudo ufw status` – Visar brandväggens regler.


* **Disk och Lagring:**
* `df -h` – Visar hur mycket ledigt utrymme som finns på hårddisken (i läsbart format).
* `du -sh [mapp]` – Visar hur stor plats en specifik mapp tar.


* **Minne (RAM):**
* `free -h` – Visar hur mycket RAM-minne som används och är ledigt.



### 4. Logganalys med `journalctl` (Systemd)

Moderna Linux-system (som Ubuntu, Fedora och Debian) använder ett system som heter `systemd`, som hanterar loggar via verktyget `journalctl`. Detta är oftast mycket smidigare än att läsa råa filer i `/var/log` eftersom allt är centrerat.

**De mest användbara `journalctl`-kommandona:**

* `journalctl` – Visar *alla* systemloggar (kan bli väldigt mycket data).
* `journalctl -f` – Följer loggarna i realtid (precis som `tail -f`).
* `journalctl -u [tjänst]` – Filtrerar loggarna för ett specifikt program. (T.ex. `journalctl -u sshd` för att bara se loggar relaterade till SSH-anslutningar).
* `journalctl --since "1 hour ago"` – Visar enbart loggar från den senaste timmen (du kan även använda `--since yesterday` eller specifika datum).
* `journalctl _UID=1000` – Visar endast loggar skapade av en specifik användare (baserat på deras användar-ID).
* `sudo journalctl --vacuum-size=500M` – Raderar äldre loggar så att de totalt bara tar upp max 500 megabyte på disken (bra om systemet börjar få slut på plats).

---

**Tips för dina studier:**
Om du ska felsöka något i Linux framöver; börja alltid med att kolla statusen på tjänsten (fungerar den?), därefter tittar du i loggarna via `journalctl -u [tjänstnamn]` för att hitta felmeddelanden, och avslutningsvis använder du nätverks/disk-kommandona för att hitta den exakta flaskhalsen!