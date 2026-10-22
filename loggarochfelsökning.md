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


Övning & mini-labb om felsökning och logganalys

Del 1: Orientering bland loggfiler
Öppna en terminal och gå till katalogen där Linux lagrar sina traditionella system loggfiler ( /var/log ), och kolla de olika loggfilerna du kan se där.

Identifiera viktiga loggar:

Lista innehållet i katalogen. 
Hitta nedan filer

    syslog (messages beroende på distribution)
    auth.log (eller secure).


Läs loggar i realtid:

    Använd kommandot " tail -f syslog " (eller på en valfri loggfil i katalogen).

    Öppna ett nytt terminalfönster och gör något i systemet, t.ex. kör ett sudo-kommando (testa sudo apt update).

    Observera vad som händer i syslog loggen i din andra terminal.

    Sluta läs filen med Ctrl + C.

Du kan utforska andra logg-filer på samma sätt, se andra kataloger för loggar i Ubuntu HÄRLinks to an external site..

Del 2: Modern logganalys med journalctl
Eftersom moderna Linux-system använder systemdLinks to an external site. (linux service manager) skrivs mycket av logginformationen till den binära journalen (systemd-journaldLinks to an external site.).

Grundläggande visning:

    Kör kommandot " journalctl " för att se "ALLT" som loggats

För att filtrera loggvisning och loggarna testa kör:

    Kör kommandot " journalctl -b " för att se loggar sedan systemet startades.
    Kör kommandot " journalctl --since "1 hour ago" "
    Kör kommandot " journalctl -u nginx " för att filtrera loggarna på nginx servicen/tjänsten. ()
    Eller " journalctl -u NetworkManager  " för att filtrera på tjänsten som hanterar nätverksanslutningar.
    Du kan också filtrera på ord, testa " journalctl -g "Warning" "

Felprioritering:
Visa endast loggmeddelanden som har prioritetsnivå "Error" eller högre.

    Kör kommandot " journalctl -p err "
    Testa att kombinera kommands som vi gått igenom med varandra.

Fråga 1: Hur kan vi se loggmeddelanden med Error eller högre, inom den senaste timmen?

 

Del 3: Praktiskt felsökningsscenario (Mini-Lab)

Scenario: En användare rapporterar att en tjänst eller applikation inte fungerar som den ska.

    Skapa en felexempel/test-händelse:

    Generera ett eget loggmeddelande i systemet med kommandot:
    " logger -p daemon.err "FELSÖKNINGSTEST: Tjänsten DatacenterApp kraschade på grund av nätverksfel." "

    Hitta "felet"/test-händelsen:

    Använd journalctl eller leta i /var/log för att hitta meddelandet du nyss skapade.

Fråga 2: Vilket exakt journalctl-kommando/sökning använde du för att snabbt hitta texten "FELSÖKNINGSTEST"?