# Fildelning över nätverk (Samba / NFS)
Fildelning över nätverk handlar om att låta datorer komma åt varandras lagringsutrymmen via ett nätverk (som ett lokalt LAN) istället för att flytta runt fysiska USB-minnen. Man "monterar" en nätverksmapp från en server så att det ser ut, och fungerar, som om filerna låg direkt på din egen hårddisk.

För att datorerna ska förstå varandra måste de prata samma "språk" (protokoll). Här är de tre protokollen du ska lära dig och när de används:

**1. SMB och Samba (För blandade miljöer)**

* **Vad det är:** Server Message Block (SMB) är ursprungligen skapat av Microsoft och är standardprotokollet för fildelning i Windows. **Samba** är mjukvaran som låter en Linux-maskin prata SMB.
* **När det används:** När du har ett nätverk med *både* Windows- och Linux-datorer. Det är det överlägset vanligaste sättet att dela mappar och skrivare på en vanlig arbetsplats.

**2. NFS (För renodlade Linux/UNIX-miljöer)**

* **Vad det är:** Network File System (NFS) är ett protokoll skapat specifikt för UNIX-liknande system.
* **När det används:** När fildelningen sker *enbart* mellan Linux- eller UNIX-system (till exempel två Linux-servrar som delar data). Det är ofta snabbare, drar mindre resurser och är mer sömlöst integrerat i Linux än Samba, men är krångligt om Windows-användare behöver komma åt samma filer.

**3. SCP och SFTP (För säker filöverföring)**

* **Vad det är:** Metoder som bygger på SSH (samma protokoll du använder för att fjärrstyra servrar). All data som skickas krypteras, vilket gör det omöjligt för någon att avlyssna trafiken.
* **När det används:** När du inte behöver en permanent ansluten nätverksmapp (som med SMB eller NFS), utan bara snabbt, säkert och tillfälligt vill flytta enskilda filer mellan din dator och en server över internet.

Den viktigaste distinktionen för din workshop är valet mellan NFS och Samba. Ska Windows-maskiner ha åtkomst väljer du Samba; är det bara Linux-servrar som ska dela data bakom kulisserna väljer du NFS.


## Konfigurera Samba
I den här övningen ska vi installera Samba på en er Ubuntu server VM (individuellt), skapa en delad mapp (/srv/samba/share) och ansluta till den från en klient VM.

Mål

I denna labb ska du installera Samba-tjänsten på en Linux VM, konfigurera mappen /srv/samba/share samt ansluta och verifiera fildelningen från en klient VM.
Gällande behörigheter: I denna grundläggande övning används helt öppna rättigheter (0777) och gäståtkomst för att säkerställa att du snabbt kommer igång utan att fastna i användarhantering. Använd aldrig dessa öppna inställningar i en skarpt fungerande produktionsmiljö.

1. Öppna en terminal på din Linux VM och kör följande kommando för att hämta de senaste paketlistorna och installera Samba:

sudo apt install samba

Hur du verifierar: Kör smbd --version. Du ska få ett svar som visar den installerade Samba-versionen utan felmeddelanden.

2. Skapa mappen /srv/samba/share och tilldela den öppna läs- och skrivbehörigheter
sudo mkdir -p /srv/samba/share
sudo chmod -R 0777 /srv/samba/share

 

Hur du verifierar: Kör ls -ld /srv/samba/share. Rättigheterna ska visa drwxrwxrwx.

3. Öppna konfigurationsfilen i en texteditor (t.ex. Nano):

sudo nano /etc/samba/smb.conf

Scrolla längst ner i filen och klistra in följande konfigurationsblock:

[PublicShare]
   comment = Samba Public Share
   path = /srv/samba/share
   browsable = yes
   writable = yes
   guest ok = yes
   read only = no
Hur du verifierar: Kör kommandot testparm. Om konfigurationen är korrekt visas meddelandet "Loaded services file OK".
4. Starta om Samba-tjänsten så att de nya inställningarna läses in, samt aktivera den så att den startar automatiskt vid boot:

sudo systemctl restart smbd
sudo systemctl enable smbd

Hur du verifierar: Kör sudo systemctl status smbd. Tjänsten ska visas som active (running).

5. Anslut och testa från klient

    Från Windows:

        Tryck Win + R för att öppna dialogrutan Kör.

        Skriv \\<SERVER-IP>\PublicShare
        (ersätt <SERVER-IP> med din Ubuntu VM:s IP-adress) och tryck Enter.

    Från Linux-klient:
        Öppna terminalen och kör:
        smbclient //<SERVER-IP>/PublicShare -U guest
        (ersätt <SERVER-IP> med din Ubuntu VM:s IP-adress) och tryck Enter.

Hur du verifierar: Skapa en ny textfil i den delade mappen från klient VM:en. Gå sedan tillbaka till din Ubuntu VM och kontrollera att filen syns i /srv/samba/share med kommandot ls /srv/samba/share.