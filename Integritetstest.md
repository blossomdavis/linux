# Integritetstest

Labbövning 1: Integritetstest 

    Integritetstest med Checksumma:
    För att kunna vara helt säker på att en fil inte förändras så måste vi använda oss av checksummor och inte bara förlita oss på vad vi med mänskliga ögat ser när vi inspekterar en fil.

        Skapa en fil: echo "Kritisk data" > /tmp/kritisk.txt

        Generera checksumma: sha256sum /tmp/kritisk.txt > /tmp/kritisk.txt.sha256

        Ta en backup med tar (som ni övat på igår), radera originalet, återställ filen och verifiera den med sha256sum -c /tmp/kritisk.txt.sha256.
        Reflektionsfråga: Varför räcker det inte med att kontrollera att backup-jobbet returnerade statuskod 0 (framgångsrikt slutfört)?
