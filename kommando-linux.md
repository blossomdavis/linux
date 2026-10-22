# Linux kommandon
https://mentorsparks.com/displayLessonDeck.html?lessonDeck=19204cf9-2733-410b-af09-2721711a3b64&date=2026-08-27&page=13 

## Ta reda på var du är
pwd (Print workning directory): visar sökvägen till mappen du står i just nu
cd (change directory): byter katalog
cd ~ flyttar till hemkatalogen
cd .. flyttar ett steg upp i katalogen

## lista innehåll i kataloger
ls -l långt formt med rättigheter, ägare och storlek
ls -la visa även dolda filer
ls -lh mänskligt läsliga storlekar

## Trädstruktur 
tree "projekt"
eller 
ls -R 

## skapa filer
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

## ta bort filer och kataloger 
rm <namn på fil>
rm -r <namn på katalog>

-f tvingar utan bekräftelse

## visa filens innehåll
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

## söka efter filer
find söker efter filer i ett katalog träd

find /sökväg -namn ".txt"
find /var -size +1M

## söka i filinnehåll
