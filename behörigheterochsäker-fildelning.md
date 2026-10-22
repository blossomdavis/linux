### Övergripande syfte med modulen

Linux är byggt för att många personer ska kunna använda samma dator eller server samtidigt (ett fleranvändarsystem). För att detta ska vara säkert måste man ha stenkoll på **vem** som får göra **vad**. Detta görs genom att hantera användare, gruppera dem smart, sätta rättigheter på filer och använda säkra metoder för att dela filer över nätverk.

Här är en nedbrytning av de tre huvudområdena du ska plugga på:

---

### 1. Användar- och grupphantering

Istället för att ge rättigheter till en person i taget, använder man grupper i Linux. Det gör systemet mycket lättare att administrera.

* **Användare:** Skapas ofta med kommandot `adduser` och kan ändras i efterhand med `usermod` (till exempel för att lägga till en användare i en specifik grupp).
* **Grupper:** Om du har fem studenter som behöver läsa samma dokument, skapar du en grupp (t.ex. "studenter"), lägger in alla fem där, och ger sedan *gruppen* rättighet till dokumentet.

### 2. Filrättigheter (Det viktigaste att förstå!)

I Linux styrs åtkomsten till filer och mappar i två nivåer: **Standard** och **Special**.

**Standardbehörigheter (rwx):**
Varje fil och mapp har tre typer av grundrättigheter:

* **r (Read):** Får läsa filen/mappen.
* **w (Write):** Får ändra, spara över eller radera filen.
* **x (Execute):** Får köra filen (om det är ett program/skript) eller "gå in i" en mapp.

Dessa tre rättigheter delas ut till tre olika kategorier av personer:

1. **User (Ägaren):** Den som skapade filen.
2. **Group (Gruppen):** Den grupp som filen tillhör.
3. **Others (Alla andra):** Resten av alla användare på hela systemet.

**Specialbehörigheter (Överkursen som gör Linux kraftfullt):**
För att lösa mer komplexa samarbetsproblem används specialrättigheter:

* **SetUID (SUID):** Om ett program har detta, körs programmet tillfälligt med *filägarens* rättigheter, oavsett vem som faktiskt startar det. (Exempel: Kommandot för att byta lösenord behöver tillfälligt få "root"-rättigheter för att spara det nya lösenordet).
* **SetGID (SGID):** Mycket viktigt för samarbete! Om du sätter detta på en mapp kommer alla nya filer som skapas i den mappen automatiskt att tillhöra mappens grupp, istället för den enskilda användarens grupp. Perfekt för delade projektmappar.
* **Sticky Bit:** Används på mappar där *alla* har skrivrättigheter (som mappen `/tmp`). Sticky bit gör så att du bara får radera eller byta namn på de filer **du själv** har skapat i mappen, du kan alltså inte radera andras filer.

### 3. Fildelning och nätverk

När du ska flytta filer mellan din dator och Linux-servern (eller mellan två servrar) måste det göras säkert så att ingen kan "lyssna" på nätverket och sno filerna.

* **SSH (Secure Shell):** Grundprotokollet för att ansluta säkert till en Linux-dator.
* **SCP (Secure Copy) & SFTP (Secure FTP):** Två sätt att överföra filer som bygger på SSH. Allt krypteras. SFTP är ofta smidigare eftersom det fungerar som ett vanligt filhanteringsprogram där du kan bläddra i mappar.
* **Chroot (Nämndes i din intro):** Står för "Change Root". Det är ett säkerhetsknep för SFTP. Det låser in användaren i en specifik mapp (ett "fängelse"). Om användaren loggar in ser det ut som att deras mapp är hela hårddisken, och de kan inte backa ut och titta på systemets eller andra användares filer.

**Tips inför workshopen:**
Se till att du förstår skillnaden mellan **SetGID** och **Sticky Bit**, då detta är klassiska koncept som ofta dyker upp i övningar kring delade mappar!


# Labbövning 1: Användar- och gruppbehörigheter för lokal fildelningsstruktur
Övningen utförs i grupper om 3-4 personer på er individuella Linux-labbserver. Målet är att bygga upp en säker infrastruktur för ett fiktivt företag.

Med två avdelningar: Developers, Research and development (rnd). 
Med tre användare: dev_bob, dev_charles, rnd_alice

Förklaringar: <GROUP> = byt ut mot dina gruppnamn, <USER> = byt ut mot dina användares namn.

 
Del 1: Skapa grupper och användare

1. Skapa grupper
sudo groupadd <GROUP>


2. Skapa användare och tilldela dom alla i rätt grupp

sudo useradd -m -g <GROUP> -s /bin/bash <USER>

Flaggorna: -m skapar hemkatalog, -g sätter primär grupp, -s sätter standard-shell 


3. Sätt tillfälligt lösenord för alla användare

sudo passwd <USER>


4. Verifiera att användarna skapat och att de är tilldelade en grupp

id <USER>

getent group <GROUP>

Del 2: Skapa katalogstruktur och sätt ägandeskap

1. Skapa kataloger för varje grupp under /data

sudo mkdir -p /data/<GROUP>_data


2. Ändra gruppägarskapet för katalogerna (VEM äger gruppen)
chown ägare:grupp sök väg

sudo chown root:<GROUP> /data/<GROUP>_data


3. Sätt rättigheter för båda grupperna & SetGID (VAD man får göra)
7 (ägare/root): rwx (Läs, skriv, exekvera/navigera i mappen)
7 (grupp): rwx (Läs, skriv, exekvera/navigera i mappen)
0 (övriga): --- (Ingen åtkomst alls)


Om SetGID sätts på mappen kommer alla nya filer/underkataloger som skapas i mappen automatiskt att ärva mappens gruppägarskap istället för användarens primära grupp.

sudo chmod 2770 /data/<GROUP>_data

4. Testa åtkomst

För att testa godkänd åtkomst:
sudo su - dev_bob
cd /data/dev_data
touch fil_fran_bob.txt
ls -l

För att testa nekad åtkomst:
sudo su - dev_bob
cd /data/rnd_data


Labbövning 2: Lokal fildelning i Linux
Del 1: Gemensam dropplåda med Sticky Bit

En Sticky Bit gör att alla kan skapa filer i mappen, men endast den som äger filen kan radera eller byta namn på den.

 1. Skapa mappen och sätt Sticky Bit:
sudo mkdir -p /data/public_drop
sudo chmod 1777 /data/public_drop

2.  Testa funktionallitet
Skapa en fil som dev_bob:
sudo su - dev_bob -c "touch /data/public_drop/bobs_fil.txt"


Försök ta bort Bobs fil som rnd_alice:

sudo su - rnd_alice -c "rm /data/public_drop/bobs_fil.txt"

Förväntat resultat: Operation not permitted (Nekad åtkomst).

 
Tilläggsövning 3: Introduktion till specialpermissions och fallgropar (SetUID, SetGID & Sticky Bit)
Introduktion & Fallgropar


I Linux kompletteras standardbehörigheter som ni kan se nedan:
7 = user/owner = rwx 
7 = groups = rwx
0 = others = ---

ibland med tre specialbits, med dessa värden och platser:
1 exempel 1770 - Sticky bit
2 exempel 2770 - SetGID
4 exempel 4770 - SetUID

Dessa kan lösa komplexa problem, men vid felaktigt användande kan de skapa allvarliga säkerhetsrisker och oväntade beteenden.

1. SetUID (Octal: 4) – "Exekvera som filens ägare"

    Säkerhetsfälla: Om du sätter SetUID (chmod 4755) på ett program som ägs av root, körs programmet därefter med root som användare och därmed också roots-behörigheter, oavsett vem som startar det.

    Fallgrop: Om programmet har en sårbarhet kan det ge användaren möjlighet att utföra åtgärder med root-behörighet. Funktioner som kan starta shells eller köra kommandon kan öka risken, men innebär inte automatiskt att användaren får root-åtkomst. SetUID ignoreras normalt för skript på Linux.

 

2. SetGID (Octal: 2) – Mappar/kataloger jämfört med körbara filer

    På mappar/kataloger: Nya filer och underkataloger ärver katalogens gruppägare istället för användarens primära grupp. Nya underkataloger ärver även SetGID-biten

    Fallgrop på filer: Om du sätter SetGID på en exekverbar fil (ett program) körs programmet med gruppens alla behörigheter. Om gruppen har åtkomst till känsliga filer kan sårbarheter i programmet göra det möjligt att missbruka den åtkomsten.

 

3. Sticky Bit (Octal: 1) – Raderings- och omdöpningsskydd

    Syfte: I en katalog som många får skriva i, till exempel /tmp, kan alla skapa filer. Sticky Bit hindrar normalt användare från att radera eller byta namn på andra användares filer. Katalogens ägare och root är undantag.
    Fallgrop: Sticky Bit skyddar bara mot radering/omdöpning. Om filen i mappen har skrivrättighet för övriga (rw-rw-rw-) kan en annan användare fortfarande tömma eller förstöra innehållet i filen, även om de inte kan radera själva filen.

 
Övningsuppgift (SetGID och SetUID)

Eftersom ni övat lite på Sticky bit i Labbövning 2 redan så fokuserar vi på de andra två här.

Del 1: SetGID på mappar (Arv av gruppägarskap)

Lärdom: SetGID på en katalog ser till att alla nya filer och underkataloger automatiskt ärver katalogens grupp istället för skaparens primära grupp.

 

1. Skapa en ny grupp och en projektmapp:
sudo groupadd lab_project
sudo mkdir -p /data/setgid_lab
sudo chown root:lab_project /data/setgid_lab
2. Sätt SetGID och grupprättigheter (2770), lägg till lab_bob i grupp och skapa en fil i mappen som lab_bob, och verifiera sedan arvet:

sudo chmod 2770 /data/setgid_lab

sudo usermod -aG lab_project lab_bob

sudo su - lab_bob

touch /data/setgid_lab/ny_projektfil.txt

 

Verifiera arvet:

ls -l /data/setgid_lab/ny_projektfil.txt

exit

Förväntat resultat: Filen visar lab_project som gruppägare trots att lab_bob skapade den.

Förklaring av "usermod -aG" som används ovan:

    -a (append): lägg till gruppen utan att ta bort användaren från dennes andra extragrupper.

    -G (groups): anger vilka extragrupper användaren ska tillhöra.

 
Del 2: SetUID (Root-eskalering och fallgropar)

Lärdom: SetUID gör att en körbar binär normalt körs med filägarens effektiva användar-ID. Om binären ägs av root körs den därför normalt med root som effektiv användare. Detta kan skapa säkerhetsproblem i vissa fall.

1. Skapa en kopia av textvisningsprogrammet "more" i en ny testmapp:

sudo mkdir -p /data/setuid_lab

sudo cp /usr/bin/more /data/setuid_lab/super_more

 

2. Tilldela root-ägarskap och sätt SetUID-biten:

sudo chown root:root /data/setuid_lab/super_more

sudo chmod 4755 /data/setuid_lab/super_more


3. Kontrollera rättigheter

ls -l /data/setuid_lab/super_more

Förväntat resultat: -rwsr-xr-x (Märk s i ägarens position)

 

4. Läs den skyddade filen /etc/shadow som vanliga användaren lab_alice:

sudo su - lab_alice

/data/setuid_lab/super_more /etc/shadow

exit


Slutsats: lab_alice kunde läsa en känslig systemfil eftersom super_more kördes med root-rättigheter.

 

Del 4: Återställning
Kör följande kommandon i ordning för att helt ta bort testmappar och användare från systemet.


1. Ta bort testkatalogerna med innehåll

sudo rm -rf /data/setgid_lab

sudo rm -rf /data/setuid_lab

 

2. Ta bort testanvändarna med hemkataloger och ta bort testgruppen.

sudo userdel -r lab_bob

sudo userdel -r lab_alice

sudo groupdel lab_project