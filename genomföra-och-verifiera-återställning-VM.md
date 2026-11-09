# Genomföra och verifiera återställning i VM

Mål: Praktiskt skapa en säkerhetskopia av en viktig katalog på din Ubuntu VM, simulera en dataförlust genom att radera katalogen, samt genomföra och verifiera återställningen enligt en strukturerad rutin.


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




**Dokumentation av rutinen**
Teknisk rutin för Backup och Återställning:

Ansvarig: Systemadministratör / IT-ansvarig (eller din studentprofil).

Kontroll: Säkerhetskopiering sker automatiskt/schemalagt. Kontroll av backup-filernas integritet samt test-återställning utförs månadsvis.

Plats för dokumentation: IT-avdelningens interna wiki / systemdokumentation på /docs/routines/backup.md.



Instruktion för återställning från arkiv (3–5 steg):

Identifiera backup-fil: Kontrollera vilken säkerhetskopia som ska återställas i /mnt/backup/ (t.ex. sorterat på datum).

Kontrollera utrymme: Säkerställ att det finns tillräckligt med diskutrymme på måldisken med df -h.

Kör återställning: Packa upp valt arkiv till rotkatalogen med kommandot:
        
        sudo tar -xvzf /mnt/backup/<FILNAMN>.tar.gz -C /

Verifiera filer: Kontrollera att filerna återställts till /home/student/viktig_data/ och att innehållet stämmer (cat).

Logga åtgärden: Dokumentera datum, tid och orsak till återställningen i incidentloggen.
