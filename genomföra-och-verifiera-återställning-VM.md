Övning 2 - Genomföra och verifiera återställning i VM

Mål: Praktiskt skapa en säkerhetskopia av en viktig katalog på din Ubuntu VM, simulera en dataförlust genom att radera katalogen, samt genomföra och verifiera återställningen enligt en strukturerad rutin (kursmål 10 och 12).
Huvudmoment och instruktioner

    Skapa testdata

        Skapa en eller flera kataloger, tex /home/student/viktig_data/ och lägg till tre testfiler med textinnehåll.

    Genomför säkerhetskopiering (tar eller rsync)

        Skapa ett komprimerat arkiv av katalogen och spara det på vår separata backup-disk (/mnt/backup/)

        sudo tar -cvzf /mnt/backup/viktig_data_$(date +%Y%m%m).tar.gz /home/student/viktig_data/

    Simulera incident (Dataförlust)

        Radera ursprungskatalogen permanent:

        rm -rf /home/student/viktig_data/

    Återställ data & Verifiera integritet

        Återställ arkivet till sin ursprungliga plats.

        Verifiera att alla tre filer finns kvar och att innehållet i dem stämmer överens med originalfilerna.

    Dokumentera rutinen

        Dokumentera den tekniska rutinen för backup och återställning, så att arbetet kan utföras på samma sätt varje gång. Beskriv kort vem i verksamheten som ansvarar, hur backupen kontrolleras och var dokumentationen ska finnas.

        Skriv en enkel instruktion för återställning från arkivet, till exempel i 3–5 steg:
