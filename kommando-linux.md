# Linux kommandon

## Ta reda på var du är
pwd (Print workning directory): visar sökvägen till mappen du står i just nu
cd (change directory): byter katalog
cd ~ flyttar till hemkatalogen
cd .. flyttar ett steg upp i katalogen

## Lista innehåll i kataloger
ls -l långt formt med rättigheter, ägare och storlek
ls -la visa även dolda filer
ls -lh mänskligt läsliga storlekar

## Trädstruktur 
tree "projekt"
eller 
ls -R 

## Skapa filer
touch <namnet på filen>

## Skapa kataloger
mkdir <namn på katalog>

## Kopiera filer och kataloger
cp <namn på källa-fil> <namn på mål-fil>
cp -r <namn på källa-katalog> <namn på mål-katalog>

## Flytta och döp om filer 
mv <namn på källa-fil> <namn på mål-fil>

Om de ligger i samma katalog blir det ett namnbyte 
mv <namn på gammal-fil> <namn på ny-fil>

## Ta bort filer och kataloger 
rm <namn på fil>
rm -r <namn på katalog>

-f tvingar utan bekräftelse

## Visa filens innehåll
cat <namn på fil> 
less - bläddrar sida för sida (avsluta med q)
head - visar de första raderna
tail - visar de sista raderna
head -n 5 /etc/passwd
tail -n 3 /var/log/syslog

echo "hej världen" > fil.txt

## Redigera filer med nano
nano är en enkel terminalbaserad texteditor 
Ctrl + O sparar
Ctrl + X Avslutar

nano <namn på fil>

## Söka efter filer
find söker efter filer i ett katalog träd

find /sökväg -namn ".txt"
find /var -size +1M

## Söka i filinnehåll
grep letar efter mönster i text. -r söker rekursivt, -i ignorerar gemener/versaler, -n visar radnummer. 

grep root /etc/passwd
grep -ri error /var/log

## Ändra rättigheter (symboliskt)
chmod ändrar rättigheter. Symboliskt: chmod u+x fil (ge ägaren exekvera), chmod go-w fil (ta bort skrivrätt för grupp och övriga).

chmod u+x notes.txt
chmod o-r notes.txt

## Ändra rättigheter (oktal)
Oktal notation: r=4, w=2, x=1. chmod 755 fil ger rwxr-xr-x. chmod 644 fil ger rw-r--r--

chmod 644 notes.txt
chmod 755 script.sh

## Ändra ägare
chown ändrar ägare och grupp. chown anna:anna fil. Kräver ofta sudo (root-rättigheter).

touch ägd.txt
sudo chown root:root ägd.txt

## Sudo
sudo kör ett kommando som superanvändare (root). Används när du behöver administratörsrättigheter, t.ex. för att installera program eller ändra systemfiler.

sudo apt update
sudo ls -l /root

## Diskutrymme
df visar ledigt utrymme per filsystem. du visar diskanvändning för kataloger. -h ger läsliga storlekar.

df -h
du -sh ~

## Processer
ps visar snapshop av processer. ps aux visar alla processer. top (eller htop) visar levande uppdatering.

ps aux (kör alla processer i detaljformat)
ps aux | grep root (söker efter processer som tillhör användaren root)

## Avsluta processer
kill skickar signaler till processer. kill PID avslutar, kill -9 PID tvingar avslut. Hitta PID med ps eller pgrep.

Startar en långvarig process i bakgrunden: sleep 600 &.
Hittar dess PID och avsluta den med kill:
sleep 600 &
pgrep sleep
kill $(pgrep sleep)

## Nätverk
ping testar anslutning till en värd. ip a visar nätverksgränssnitt (ersätter det gamla ifconfig).

ping -c 4 localhost
ip a

## Lyssna på portar
ss (eller netstat) visar nätverksanslutningar och lyssnande portar. -tulpn visar TCP/UDP, lyssnande, med programnamn.

ss -tulpn

## Läsning av loggar
Systemloggar ligger oftast i /var/log. Vanliga filer: syslog, auth.log, dmesg. Kombinera med grep och tail -f för felsökning.

Visar de senaste 10 inloggningsförsöken i /var/log/auth.log (eller journalctl på systemd-system).
Följ syslog i realtid i en ny terminal i 5 sekunder och avbryt med Ctrl+C:
sudo tail -n 10 /var/log/auth.log
journalctl -n 10 _SYSTEMD_UNIT=ssh.service
tail -f /var/log/syslog

## Hitta fel
dmesg visar kernelns meddelandebuffert – användbart för hårdvarufel, USB, diskar.

dmesg | tail
dmesg | grep -i error

## Pakethantering
På Debian/Ubuntu används apt. apt update uppdaterar listor, apt install installerar, apt remove tar bort.

apt search curl
apt show bash

## Användarinformation
whoami – vem är jag?
id – mitt UID, GID och gruppmedlemskap.
who – vilka användare är inloggade?

## Miljövariabler
Miljövariabler styr hur program beter sig. env listar dem. $VARIABEL hämtar värdet. PATH avgör var systemet letar efter program.

env
echo $HOME
echo $PATH

## Alias och historik
alias skapar kortkommandon. history visar tidigare kommandon. Ctrl+R söker bakåt i historiken interaktivt.

alias ll='ls -la'
history 20

# Scenario
Scenario 1: Städa upp en nedladdningsmapp

Teori & Koncept
Kombinera flera kommandon för att lösa ett verkligt problem: hitta, sortera och ta bort gamla filer.

Övning / Uppgift

    Lista alla .log-filer större än 10 MB i /var/log.
    Ta bort filer i din Downloads som är äldre än 7 dagar (simulera med find -mtime).

Förväntat svar / Kommandon

find /var/log -name "*.log" -size +10M
find ~/Downloads -type f -mtime +7

Scenario 2: Säkra en konfigurationsfil

Teori & Koncept
Konfigurationsfiler ska ofta bara vara läsbara/skrivbara av ägaren.

Övning / Uppgift

    Skapa config.conf med lite innehåll.
    Sätt rättigheter så att bara ägaren kan läsa/skriva (600).
    Kontrollera resultatet.

Förväntat svar / Kommandon

echo "port=8080" > config.conf
chmod 600 config.conf
ls -l config.conf

Scenario 3: Hitta vad som fyller disken

Teori & Koncept
Använd du för att hitta tunga kataloger, sortera med sort.

Övning / Uppgift

    Hitta de 10 största katalogerna under /var.
    Visa dem sorterat efter storlek.

Förväntat svar / Kommandon

du -h /var | sort -rh | head -n 10
