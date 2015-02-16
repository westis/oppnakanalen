Usage: convertfiles [OPTIONS] DIRECTORY

This script will convert video files in a directory.
Default output is in a format suitable for broadcast.
If input has a different aspect ratio than output,
pillar- or letter-boxes will be added.

Options:
   -d, --destination DIRECTORY
   Write output to provided target directory.
   Default is a subdirectory to source called 'output'

   -o, --overwrite
   Overwrite any existing output files. Default is not to.

   -l, --log
   Write timestamps and events to log in target directory.

   -m, --mp4
   Make output mp4/h264 and leave resolution as-is,
   unless -r option is used.

   -f, --flash
   Make output flash/sorenson and leave resolution as-is,
   unless -r option is used.

   -r, --resolution WIDTHxHEIGHT
   Set output resolution to provided value. Ex: 1280x720
   Default is set to standard broadcast resolutions.

   -c, --crop
   Enable cropping of existing boxes. Disabled by default.

   -s, --size WIDTHxHEIGHT
   Set crop size to provided value. Ex: 32x0
   WIDTH & HEIGHT will be cropped from both sides.
   Default is to automatically calculate the values.

   -b, --bitrate BITRATE
   Set video bitrate to specified value (in kb/s)
   Default is to use bitrate of input or 12288 for HD

   -n, --nonormal
   Disable sound normalization. Enabled by default.

   -y, --yes
   Assume answer yes to all questions except overwrite.
      
Jag tror alla val är ganska självförklarande?

**Beskrivning**
Scriptet är i grunden en loop som går igenom alla filer i käll-katalogen. Varje fil kontrolleras innan de behandlas, dels så att det är en giltig video-fil och dels för att hämta in all relevant data om filen. Ogiltiga filer hoppas över. När data är insamlad på en giltig fil körs följande logik.

**Logik**
Om -m är valt: låt upplösningen vara och sätt codec till webb-anpassad h264/aac
Om -f är valt: låt upplösningen vara och sätt codec till flash i hög kvalitet

Annars: 

Om upplösningen är 720x576 (SD): 
Om filen är mpeg2 eller h264 i 16:9 med 25 fps, låt vara, annars konvertera 
Konverterar till: 720x576 16:9 (avlånga pixlar) med 25 fps

Om upplösningen är 1280x720 (HD):
Om filen är h264 i 16:9 med 50 fps, låt vara, annars konvertera
Konverterar till: 1280x720 16:9 med 50 fps

Om upplösningen är minst 1280 bred eller 720 hög:
Konverterar till: 1280x720 16:9 med 50 fps
Efter omskalning med pålägg av sorgkanter

Om upplösningen är något annat än ovan:
Konverterar till: 720x576 16:9 (avlånga pixlar) med 25 fps
Efter omskalning med pålägg av sorgkanter

I samtliga fall där en konvertering görs så konverteras filen till h264/aac

I de fall video- eller audio-codec kopieras över oförändrad så gäller det endast strömmen/codecen. Containern konverteras alltid till flv eller mp4. 
Om användaren specificerat upplösning genom --resolution så appliceras fortfarande ovanstående logik, men efteråt skrivs upplösningen över av användarens val. Aspect ratio sätts inte eftersom det kan vara vad som helst. 

Om användaren valt att applicera cropping för att ta bort existerande sorgkanter så görs detta normalt via automatisk detektering innan övrig konvertering. Det är möjligt att med hjälp av växeln -s manuellt specificera hur en fil ska croppas och då används ej automatisk detektering. Det är rekommenderat att endast använda -s vid eventuella fall där automatiken misslyckas. 

Såvida ljudnormalisering inte är bortvalt delas konverteringen upp i bild och ljud. Normaliseringen körs enbart på ljudspåret, efter videokonverteringen, sedan slås video och ljud ihop igen till samma fil.

Om inte användaren specificerat bitrate så används ursprungsfilens bitrate om den kan hittas, vilket den normalt sett alltid kan. Om inte bitrate kan hittas av någon anledning används 6144kb/s för SD och 12288kb/s för HD. 

Om scriptet körs automatiserat är det rekommenderat att använda växeln -y för att undvika stopp som beror på eventuella frågor från programmen som scriptet använder. Dessa frågor uppkommer inte ofta men tillräckligt för att vara ett problem vid automatisering.

**Systemkrav**
Ett Unix-system med bash 3.x eller senare och standard verktyg såsom grep, cut, diff osv…

(Scriptet är enbart testat på Linux men borde fungera på de flesta andra Unix-kompatibla system. Större delen av scriptet är bourne-kompatibelt men en del funktioner behöver ändras om bourne-kompatibilitet önskas. För tillfället krävs bash.)

Avlib 

(Scriptet går enkelt att få kompatibelt med ffmpeg men detta är inte testat.)
