Genomgång: få VirtualBox-installationen/VM att hitta din yaml-konfigurations fil

För att kunna testa er konfiguration så smidigt som möjligt ska vi nå den lokalt via en partition. 


Den här guiden visar hur du kan göra för att få installationsfilen (autoinstall.yaml) tillgänglig för linux installationsprogrammet i din VM.
Skapa en ny VM för att labba detta.

Läs in den från en partition (enklaste alternativet för oss i nuläget)
Med det här alternativet skapar du en liten (50-100mb räcker gott och väl för konfigurationsfiler) virtuell diskfil (.vhd) i Windows, formaterar den som FAT32 och lägger Autoinstall-filerna på den. Sedan ansluter du VHD-filen som en extra disk till VM:en.

 

1. Skapa och formatera VHD-filen i Windows

    Öppna Disk Management som administratör. Du kan söka efter Disk Management eller köra diskmgmt.msc.
    Se till att ha koll på dina nuvarande diskar, så du ser vilken som tillkommer efter dessa steg.
    Välj Action → Create VHD.
    Ange var filen ska sparas (rimligtvis i din VirtualBox VM folder) och ge den ett namn, exempelvis autoinstall.vhd.
    Välj formatet VHD (inte VHDX, för att hålla dig till det format som efterfrågas här).
    Ange storlek, exempelvis 64 MB, och välj Dynamically expanding.
    Klicka OK. Windows skapar och ansluter disken.
    I Disk Management hittar du den nya disken. Högerklicka på den nya disketiketten till vänster, exempelvis Disk 2, och välj Initialize Disk. Välj MBR.
    Högerklicka på det oallokerade utrymmet och välj New Simple Volume. Använd hela utrymmet.
    Tilldela en enhetsbokstav, tex "Y". Välj FAT32 som filsystem och ange volymetiketten AUTOINSTALL.
    Nu bör du se den nya disken Y:\AUTOINSTALL under "This PC" i File Explorer/Utforskaren.

2. Lägg Autoinstall-filerna på disken

Öppna den nya enhetsbokstaven (tex Y:\) i Utforskaren och lägg din .yaml konfigurationsfil direkt i rotkatalogen (Y:\autoinstall.yaml)

 

3. "Koppla loss" VHD:n från Windows

Innan du ansluter samma disk till VirtualBox:

    Stäng alla Utforskarfönster som använder VHD-enheten.
    Gå tillbaka till Disk Management.
    Högerklicka på disketiketten till vänster, exempelvis Disk 2, och välj Detach VHD. Efter detta kan du inte nå VHD disken från Windows, tills det att du manuellt "kopplar in" den igen i Disk Management.

4. Anslut VHD:n till din VM i VirtualBox

    Stäng av din VM helt.
    Markera VM:en i VirtualBox Manager och välj Settings → Storage.
    Välj Controller: SATA, där din nuvarande disk till VM ligger (Om ingen SATA-kontroller finns kan du lägga till en via lagringsinställningarna.)
    Klicka på ikonen för att lägga till en hårddisk och välj Choose Existing Disk.
    Bläddra till mappen där du från början sparade autoinstall.vhd och välj den.
    Kontrollera att VHD:n syns under SATA-kontrollern och att installations-ISO:n fortfarande sitter i den optiska enheten.
    Klicka OK.

5. Ta reda på sökvägen för din autoinstall.yaml fil

    Starta din VM med den nya disken in kopplad.
    Klicka in på "Files" och där kommer du att se en ny Disk som heter "AUTOINSTALL".
    Leta upp din fil på disken och högerklicka till "Properties" och anteckna sökvägen till din fil. 

 

6. Starta installationen (
Se dessa stegen från den tidigare länkade videonLinks to an external site.
)

    Starta VM:en från ISO-installationsfilen (som är in kopplad i VM:s optiska enhet).
    När installationsrutan/wizarden kommer upp så trycker du på Krysset uppe i högra hörnet får att komma åt "live-läget".
    Klicka in på "Files" och där kommer du nere till vänster att se en ny Disk som heter "AUTOINSTALL".
    Leta upp din autoinstall-fil på disken och högerklicka till "Properties" och anteckna sökvägen till din fil som står under "Parent folder". (Tex "/run/media/user/AUTOINSTALL/autoinstall.yaml").
    När du har denna sökväg kan du starta om VM och köra installationsdelen som vanligt.
    På steget "How would you like to install Ubuntu?" välj där "Automated install" och fyll i din sökväg som du hittade. Men lägg till "file://" innan sökvägen, se exempel nedan.
    file:///run/media/user/AUTOINSTALL/autoinstall.yaml
    Klicka sedan "Validate" och kolla så dina konfigurationer lästs in, klicka sen "Install".
