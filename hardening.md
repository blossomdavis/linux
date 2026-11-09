# Linux Hardening

Mål: Lär dig säkra en Linux-server genom att inaktivera oanvända filsystem, begränsa användarbehörigheter, konfigurera brandväggsregler samt genomföra en automatiserad säkerhetsgranskning.

Efter övningen kommer vi att titta på några av era konfigurationer, så behåll VM på efteråt.

Del 1: Inaktivera oanvända filsystemmoduler

Genom att inaktivera moduler för ovanliga filsystem (t.ex. cramfs, freevxfs, jffs2, hfs, hfsplus, squashfs) minskas serverns attackyta. Detta berörs också i CIS benchmark "1.1.1 Configure Filesystem Kernel Modules, sida 22". Här kan man välja om man vill följa CIS exakt, med olika filer per modul, för att det underlättar i en komplex miljö. Men man kan också lägga alla spärrar i samma fil som nedan, en per rad.

Vill ni så kan ni välja ut flera moduler som nämns i CIS benchmark, och inaktivera dessa också. Undersök även vad de används för.

    Skapa en konfigurationsfil för modulspärr:

    sudo nano /etc/modprobe.d/disable-filesystems.conf

    Lägg till regler som förhindrar laddning av modulerna (exempelvis cramfs och freevxfs):

    blacklist cramfs
    install cramfs /bin/false 
    blacklist freevxfs
    install freevxfs /bin/false
    blacklist hfs
    install hfs /bin/false
    blacklist hfsplus
    install hfsplus /bin/false

    blacklist "modulename" med /bin/false vs /bin/true, starten av modulen kommer stoppas oavsätt, men med /bin/true så luras andra system att modulen är igång, och man slipper felkoder i loggar. Men det är tydligt i loggar att modulen är disabled.

    Verifiera att modulen inte kan laddas:

    sudo modprobe -n -v cramfs

    Förväntat resultat: Utlinjen visar install /bin/false, vilket innebär att modulen inte laddas.

Del 2: Konto- och åtkomsthantering (Account & Access Management)
A. Säkra SSH-inloggning

Se till att du har ett annat konto än "root-kontot" skapat, innan du gör nedan steg. SSH och säkerhet berörs också i CIS Benchmark 5.1.1, sida 558. Där ni kan kolla på extra delar om ni blir klar tidigt.

    Öppna SSH-konfigurationen:

    sudo nano /etc/ssh/sshd_config

    Ändra/verifiera följande inställningar:

    PermitRootLogin no
    PasswordAuthentication no   # (Sätt till yes endast om du inte använder SSH-nycklar under labben)
    MaxAuthTries 3

    Om ni blir klara snabbt kan ni välja att söka upp hur ni använder SSH nycklarLänkar till en extern sida. istället för lösenord, och implementera detta (samt ändra "PasswordAuthentication" till no när ni har verifierat att nycklarna funkar)

    Starta om SSH-tjänsten:

    sudo systemctl restart sshd

B. Säkra lösenordspolicy och kontolåsning

    Kontrollera och ställ in maximal livslängd för lösenord i /etc/login.defs:

    PASS_MAX_DAYS   90
    PASS_MIN_DAYS   7
    PASS_WARN_AGE   14

C. Hantera Sudo-behörigheter

    Kontrollera vilka användare som har sudo-rättigheter:

    getent group sudo

    Ta bort obehöriga användare från sudo-gruppen om det behövs:

    sudo deluser <användarnamn> sudo

Del 3: Grundläggande UFW Brandväggsregler

UFW berörs också i CIS Benchmarks 4.1.1, sida 545, om ni blir klar snabbt så kan ni kolla igenom den och labba med fler delar som nämns i CIS.

    Kontrollera status för UFW:

    sudo ufw status verbose

    Ställ in standardpolicy (Blockera inkommande, tillåt utgående):

    sudo ufw default deny incoming
    sudo ufw default allow outgoing

    Tillåt endast nödvändiga portar (SSH & HTTP/HTTPS):

    # Tillåt SSH (port 22)
    sudo ufw allow 22/tcp

    # Tillåt webbtrafik (Port 80 & 443)
    sudo ufw allow 80/tcp
    sudo ufw allow 443/tcp

    Aktivera brandväggen:

    sudo ufw enable

    Verifiera aktiva regler:

    sudo ufw status numbered

Del 4: Snabbguide – Säkerhetsgranskning med Lynis

Lynis är ett öppet verktyg för säkerhetsgranskning och hardening-analys på Unix/Linux-system.

    Installera Lynis:

    sudo apt update && sudo apt install lynis -y

    Kör en komplett säkerhetsaudit:

    sudo lynis audit system

    Tolka resultatet:

        Hardening Index: Ger en sammanlagd poäng (t.ex. 65/100).

        Warnings (Varningar): Kritiska punkter som bör åtgärdas direkt.

        Suggestions (Förslag): Rekommenderade åtgärder för att öka säkerheten ytterligare.

    Visa endast varningar och förslag från senast körda audit:

    sudo grep -E "Warning|Suggestion" /var/log/lynis.log

