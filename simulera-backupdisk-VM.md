Simulera backupdisk i vår VM

För att simulera en separat backupdisk i vår Ubuntu VM så skapar vi en extra virtuell disk som "Ubuntu använder" som övnings- och backupdisk. Den här disken är en fil på värddatorn, inte en fysisk disk. I VirtualBox skapas den via Virtual Media Manager, och sedan ansluts den till VM:ens lagringsstyrenhet.



1. Skapa den virtuella disken i VirtualBox & koppla in den

    Stäng av Ubuntu-VM:en helt – pausa eller spara inte bara dess tillstånd.

    Öppna VirtualBox. Öppna Tools/Verktyg → Virtual Media Manager/Mediahanterare. I vissa versioner kan du också gå via VM:ens Settings/Inställningar → Storage/Lagring och välja att skapa en ny hårddisk.
    Välj Hard Disks/Hårddiskar och klicka Create/Skapa.

    Välj diskformat. VDI är ett bra val om disken bara ska användas i VirtualBox.

    Välj Dynamically allocated/Dynamiskt allokerad för en övningsdisk. Då växer diskfilen på värddatorn efter hand, upp till den angivna maxstorleken. Vi skapar en mindre storlek för denna övning, välj 5 GB.

    Ge disken ett tydligt namn, till exempel ubuntu-backup.vdi, och klicka Finish/Slutför

    För att koppla in disken i VirtualBox markerar du Ubuntu VM:en och öppnar Settings/Inställningar → Storage/Lagring.

    Markera VM:ens SATA Controller. (Om ingen SATA-styrenhet finns, lägg till en med " + "-knappen/ikonen för att lägga till lagringsstyrenhet/controller.)

    Klicka på ikonen för att lägga till hårddisk bredvid styrenheten.

    Välj Choose Existing Disk/Välj befintlig disk och välj ubuntu-backup.vdi som du nyss skapade. Alternativt kan du skapa disken härifrån direkt.

    Bekräfta med OK och starta Ubuntu-VM:en.

 

 
2. Skapa partition och formatera den virtuella disken i Ubuntu

För att kunna använda vår nya disk så måste vi preparera den genom att formatera den till ett passande filsystem och partitionera den. Varning: Formatering raderar innehållet på den valda partitionen. Kontrollera noga att du arbetar med den nya, tomma disken och inte någon annan disk.

 

1. Öppna Terminal och kör först:

lsblk -o NAME,SIZE,FSTYPE,LABEL,MOUNTPOINTS

Identifiera den nya disken genom storleken.
I exemplet nedan antar vi att den heter /dev/sdb och att den nya partitionen blir /dev/sdb1 (dina namn kan vara annorlunda.)


2. Skapa partitionen:

sudo fdisk /dev/sdb

I fdisk skriver du n för en ny partition och följer standardvalen för att använda hela disken. Skriv sedan w för att spara ändringarna.

 

3. Formatera därefter partitionen:

sudo mkfs.ext4 -L BACKUP /dev/sdb1

 

4. Skapa en monteringspunkt och montera den:

sudo mkdir -p /mnt/backup
sudo mount /dev/sdb1 /mnt/backup

 

5. Kontrollera att den är monterad:

df -h /mnt/backup

Kommandona för att partitionera, formatera och montera förutsätter att du byter ut enhetsnamnen (/dev/sdb) mot det som faktiskt visas hos dig i steg 1.
