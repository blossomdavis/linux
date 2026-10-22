# File Directories

### **Huvudkonceptet: Rotkatalogen (`/`)**

Till skillnad från Windows (som använder enhetssortering som `C:`, `D:`, `E:`), har Linux ett enda startsystem som kallas **rotkatalogen** och representeras av ett enskilt snedstreck `/` [[00:27](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=27)].

* Tänk på `/` som stammen på ett träd – alla filer, mappar, program, hårddiskar och USB-minnen sitter fast på samma trädstruktur [[00:42](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=42)].

---

### **Genomgång av de viktigaste mapparna**

#### **Användare & Administration**

* **`/home`** – Här lagras personliga filer för vanliga användare (t.ex. `/home/john` för skrivbord, nedladdningar och projekt) [[01:04](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=64)].
* **`/root`** – Hemkatalogen för administratörskontot (*root-användaren*). Observera att `/root` bara är en mapp, medan `/` är hela filsystemets rot [[01:31](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=91)].

#### **Program & Verktyg**

* **`/bin`** (*Binaries*) – Innehåller de viktigaste kommandoradsprogrammen som Linux behöver för att fungera (t.ex. `ls`, `cp`, `mv`, `mkdir`) [[01:53](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=113)].
* **`/sbin`** (*System Binaries*) – Innehåller administrativa verktyg för systemhantering, nätverk, diskar och återställning [[02:20](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=140)].
* **`/usr`** (*Unix System Resources*) – Fungerar som det huvudsakliga mjukvarubiblioteket. Innehåller installerade program, bibliotek och verktyg (t.ex. `/usr/bin`) [[03:25](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=205)].
* **`/lib`** (*Libraries*) – Innehåller delad kod/kodbibliotek som flera program kan återanvända [[03:54](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=234)].

#### **Konfiguration & Föränderliga filer**

* **`/etc`** (*Etcetera / Settings*) – Inställningsmappen för hela operativsystemet. Här finns konfigurationsfiler för nätverk, SSH, DNS m.m. [[02:44](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=164)].
* **`/var`** (*Variable*) – För filer som ständigt ändras eller växer, såsom systemloggar, e-postköer och cache [[04:34](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=274)]. Vid felsökning letar man oftast i `/var/log` [[04:52](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=292)].
* **`/tmp`** (*Temporary*) – Används för tillfälliga filer. Innehållet raderas ofta automatiskt när systemet startas om [[04:16](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=256)].

#### **Hårdvara, Enheter & Systeminformation**

* **`/dev`** (*Devices*) – I Linux behandlas nästan allt som en fil. Hårdvara som hårddiskar (`/dev/sda`), tangentbord och möss visas som filer här [[05:42](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=342)].
* **`/media` & `/mnt**` – Platser där externa enheter monteras. `/media` används ofta automatiskt för USB-minnen/DVD:er [[05:17](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=317)], medan `/mnt` används för manuell montering av diskar och nätverksenheter [[05:25](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=325)].
* **`/proc`** (*Processes*) – Ett virtuellt filsystem skapat av Linux-kärnan. Det innehåller live-information om köra processer, CPU-användning och minne [[06:14](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=374)].
* **`/boot`** – Innehåller allt som behövs för att starta operativsystemet (som Linux-kärnan och bootloadern) [[06:45](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=405)].

---

### **Minnesregel för strukturen**

I slutet av videon ges ett enkelt sätt att gruppera mapparna [[07:00](https://www.youtube.com/watch?v=qvjRcZcW8CY&t=420)]:

* **Användarfiler:** `/home`
* **Program:** `/bin`, `/sbin`, `/usr`
* **Inställningar:** `/etc`
* **Delad kod:** `/lib`
* **Loggar & ändringsbara data:** `/var`
* **Tillfälliga filer:** `/tmp`
* **Hårdvara:** `/dev`
* **System- & processorinfo:** `/proc`
* **Startfiler:** `/boot`
* **Externa diskar:** `/media`, `/mnt`
