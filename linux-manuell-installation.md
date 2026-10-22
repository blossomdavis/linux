Manuell installation

Hårdvaruspecifikation:

        Minimum specs för Ubuntu desktop
        Nätverk: 1 st Nätverkskort, NAT Network (eller valfri beroende på tidigare labbmiljö)
        Säkerställ UEFI-användning innan start: Välj UEFI (istället för Legacy BIOS) som Boot Mode i inställningarna för den virtuella maskinen (VM-inställningarna) innan installationen startas.
        I vissa fall kan du behöva byta "Graphics Controller" till VBoxSVGA


Genomför installationen i VirtualBox eller valfri hypervisor (Jag kommer att använda VirtualBox som referens) med fokus på viss best practices och hardening som kan ses i produktionsmiljöer.

Steg 1: Initial start & Språkval

    Skapa en VM med 40GB storage.
    Du kan behöva tilldela lite mer RAM och "Numbers of CPUs" för att få ett flytande system.
    Välj Settings på din VM > System > Motherboard & Processor
    Jag har valt "RAM: 4GB" och "Numbers of CPUS: 2" för att vara säker.
    Gå till Settings > Expert > General och gå längst ner till "Motherboard" och kryssa i "UEFI" rutan.
    Skapa ett NATNetwork, eller välj ditt befintliga NATNetwork för din nätverksadapter i din VM.
    Network > NAT Networks > Create > fyll i IP 192.168.50.0/24

    Högerklicka på Settings på din VM > Network > Välj NAT Network på adapter 1.

    Starta den virtuella maskinen från ISO-filen.
    Välj English som systemspråk.
    I menyn "Keyboard configuration": Sätt Layout till Swedish (eller det tangentbord du använder).
    Best Practice: Använd alltid engelska som systemspråk på servrar. Det underlättar felsökning, och gör felmeddelanden sökbara och förhindrar att sökvägar (som /home vs /hem) översätts.
    Kryssa inte i något i "Recommended software" delen.
    Best Practice: Vi väljer att avstå från installation av så många onödiga paket som möjligt på vår framtida server. Desto flera paket desto flera säkerhetsrisker.

 

Steg 2: Nätverkskonfiguration (Statisk IP)

    I nätverkssteget, ändra från DHCP till Manual (eller static IP) för ens33 eller eth0.
    Ange nätverksinställningar för labbmiljön:
        Address: 192.168.50.50 (eller anpassat efter labbnätet)
        Subnet: 255.255.255.0
        Gateway: 192.168.50.1
        Name servers (DNS): 1.1.1.1, 8.8.8.8
    Best Practice: Produktionsservrar ska aldrig förlita sig på dynamisk DHCP utan fasta reservationer eller statisk konfiguration. Det förhindrar att tjänster eller databaser tappar kontakten vid omstarter.

Steg 3: Diskpartitionering 

    Välj Custom storage layout (Manuell partitionering).
    Du bör här redan ha GPT-partitionstabell, då det sker automatiskt på Ubuntu 26.04. (Alternativt välj "New Partition Table" och välj där GPT.)
    Markera "sda"/den som är högst upp i listan. Längst ner till vänster så finns en drop-down meny där det står "Device for boot loader" där väljer du den som innehåller något med "VBOX..." När du gjort det så ska /boot/efi/ automatiskt ha skapats i listan.
    Markera sen det "free space/Unallocated space" sen tryck  " + " för att skapa nedan partitioner, en i taget. (du behöver inte skapa /boot/efi/ om du fick den automatiskt.)

Konfigurera partitionen enligt följande:
Denna ska skapas automatiskt som nämns i guiden ovanför.
Size: (automatisk storlek).
Format: fat32/vfat
Mount: /boot/efi
UEFI/EFI bootloader

Size: 1GB
Format: ext4
Mount: /boot
Linux kernels, old kernels for backups.

Size: 10GB
Format: ext4
Mount: /var
System logs, APT package caches, Flatpak/Snap software data, and Docker images.

Size: 2GB
Format: ext4
Mount: /tmp

Size: (Lämna tomt eller ange maximal storlek för att använda resten av disken).
Format: ext4
Mount: /
System binaries, GNOME desktop environment, and default apps and /home user files.

Best practices: Komplett partitionering med mount options, kryptering, LVM kan användas i olika utstreckning i produktionsmiljö och för härdning. Men här har jag valt att fokusera på en grundläggande del kring partitionering då vi inte hinner göra alla best practices.


Steg 4: Profil- och Inloggningsuppgifter

Fyll i uppgifterna för servern och den administrativa användaren:

    Your name: Ditt namn.
    Your server's name: Ange serverns namn/hostname (t.ex. srv01-ubuntu).
    Pick a username: Ange ett användarnamn för din administrativa användare tex ditt namn eller user (undvik admin eller root för att försvåra för hackers att komma åt konton med hög behörighet).
    Password: Ange lösenord (I en riktig miljö ska lösenordet såklart vara ett starkt lösenord enligt policy.)

 

Steg 5: IP-sättning
Välj BARA ett av alternativen, då det kan ske en krock i konfigurationen annars.
ALTERNATIV 1: För att göra detta i terminalen gör du som nedan.

sudo nano /etc/netplan/00-installer-config.yaml
image.png
Fyll sedan i din fil som nedan med din IP-sättning, tryck sedan Ctrl + X, sedan Y och sen Enter för att spara.

Kör sedan "sudo netplan generate" och sen "sudo netplan try"

ALTERNATIV 2: Du kan även göra det manuellt i det grafiska gränssnittet, välj IPv4 > Manual/Static och fyll i den planerade IP-sättningen.

 

Steg 6: Verifiering / Felsökning

Om du stöter på problem med nätverket testa då stegen som togs upp i chatten på workshopen: Guide för nätverksproblemLinks to an external site.

När installationen är klar och servern har startat om, verifiera att din manuella ip-sättning är korrekt.

Terminalen i linux:
För att se att vårt interface är uppe efter IP-sättningen i netplan.
ip -br addr

För att se att den fullständiga IP-sättningen är korrekt
ip a

Steg 7: Uppdatera ditt OS och skapa snapshot av din VM

För att uppdatera systemets paket och "paket-lista", kör först:
sudo apt update && sudo apt upgrade -y

Vi vill också ta en Snapshot/"en kopia" av din VM:s nuvarande stadie. Alltså en färdig, fräsch setup av Ubuntu, ifall något skulle gå fel senare i labbarna så kan ni bara gå tillbaka till er Snapshot och börja om utan att behöva göra hela installations delen igen. Det kan också vara smart att göra flera Snapshots efter hand, för att kunna välja vart man vill återställa ifrån.

För att ta ett snapshot:
1. Stäng av din VM
2. Markera din VM i "Hem vyn" i VirtualBox, där dina VM:s är listade.
3. Välj fliken "Snapshots" och högerklicka på "Current state" och vält "Take".