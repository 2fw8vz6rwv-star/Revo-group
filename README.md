# Revo group däckhotell

Mobilanpassat svenskt däck- och kundregister. Appen är byggd som statiska filer och behöver ingen installation av Node eller databas-server.

## Starta appen

1. Öppna `outputs/index.html` i en modern webbläsare. För att använda kameran, kör webbservern enligt stegen nedan eller publicera sidan via HTTPS.
2. Lägg till en kund via **Kunder** → **Lägg till kund**.
3. Registrera kundens däck via **Däckregister** → **Registrera däck**.

För lokal webbserver: öppna terminalen i mappen `outputs`, kör `python3 -m http.server 8000` och öppna `http://localhost:8000`. Detta kräver Python 3. Ge webbläsaren kamerabehörighet när du provar kamerafunktionen.

## Publicera webbappen på GitHub Pages

Projektet innehåller ett GitHub Actions-flöde i `../.github/workflows/deploy-pages.yml`. Lägg projektmappen i ett GitHub-repo med standardgrenen `main` eller `master`, välj **Settings → Pages → Source: GitHub Actions** och skicka en push. Flödet publicerar automatiskt webbappen och visar sidans URL under **Actions**. Webbappen är PWA-förberedd och fungerar offline efter att den öppnats minst en gång. På iPhone kan den läggas till via Safari → **Dela → Lägg till på hemskärmen**. GitHub Pages publicerar webbversionen; det bygger eller installerar inte iOS-appen. LiDAR-djupskanning fungerar endast i iOS-appen på en kompatibel iPhone.

## Skanna mönsterdjup och QR-koder

I iOS-appen öppnas LiDAR-skanningen direkt från registreringen av varje däck. Mätningen analyserar nivåskillnader i en central del av LiDAR-djupkartan och returnerar ett estimat i millimeter till rätt däck och hjulposition. Foton av däcksidan eller slitbanan och AI-bildanalys ingår inte i registreringsflödet. Märke, modell och dimension fylls i som vanliga däckuppgifter.

LiDAR-avläsningen är experimentell: ARKit lämnar avstånd till ytor, inte ett kalibrerat mönsterdjup. Vinkel, däckets krökning, gummi och sensorupplösning kan påverka mm-värdet. Kontrollera varje resultat mot en fysisk mönsterdjupsmätare innan det används som beslutsunderlag. Webbversionen kan inte använda iPhones LiDAR; mm-skanningen finns endast i iOS-appen på en kompatibel iPhone. Webben visar därför ett tydligt besked i stället för att påstå att den har gjort en LiDAR-mätning.

QR-skanning finns även i webbversionen. Publicera och öppna appen via HTTPS, tillåt kameran och välj **Däckregister → Skanna QR**. Webbläsaren behöver stöd för `BarcodeDetector`; om kameraåtkomst nekas eller API:t saknas visas ett felmeddelande. Etiketter som läses upp öppnar kunduppgifterna från registret som finns i den aktuella webbläsaren.

## Registreringsflöde

1. Registrera kunden.
2. Registrera ett fyrhjulsset i ett sammanhängande flöde. Appen går igenom höger fram, vänster fram, höger bak och vänster bak och håller kunden vald genom alla fyra däcken.
3. Ange zon, hylla och plats. Etiketten visas när däcket sparas; skriv ut den eller fortsätt till nästa position.
4. Skanna etikettens QR-kod i iOS-appen för att öppna den kopplade kunden direkt i kundregistret.

Däckmärkets uppgifter registreras utan AI-bildanalys. LiDAR-skanning är endast tillgänglig i iOS-appen på en kompatibel iPhone.

## Funktioner

- Kundregister med namn, telefon, registreringsnummer, e-post och anteckning.
- Däckregister kopplat till kund med automatiskt unikt internt däck-ID.
- Sökning i däckregistret via kundnamn, telefon, registreringsnummer, däck-ID, märke och modell.
- Fyrhjulsregistrering i ett flöde med positionerna höger fram, vänster fram, höger bak och vänster bak. Däckuppgifter och lagerplats registreras i webben; LiDAR-estimat i mm finns endast i iOS-appen.
- Kundkort visar kundnummer och alla kopplade däck. En kund kan ha valfritt antal däck: lägg till ett däck eller fler fyrhjulsset, redigera skanning och lagerplats, visa/skriv ut varje däcks QR-etikett och ta bort däck enskilt eller alla på kunden.
- Aktivitetslogg för kund- och däckregistreringar och ändringar.
- Sökbar lagerlista, statusfilter, responsiv vy och etikettförhandsvisning i storlek 100 × 150 mm.
- QR-kod (QR Code Model 2, byte mode, version 3-L) kodar det interna däck-ID:t.
- JSON-export av kund-, däck- och historikdata som säkerhetskopia.

## Lagring och begränsningar

I webbversionen sparas data i webbläsarens `localStorage` på den aktuella enheten och i den aktuella webbläsarprofilen. Publicering på GitHub Pages gör inte kundregistret delat mellan personalens telefoner. iOS-projektet använder appens lokala fil för registret och synkar inte data mellan enheter. Exportera regelbundet en säkerhetskopia. Import av säkerhetskopia, inloggning, behörigheter och delad fleranvändardrift ingår inte.

LiDAR-estimatet i mm och faktisk mätprecision behöver verifieras mot en fysisk mönsterdjupsmätare på flera däck. OCR och Bluetooth-utskrift är inte implementerade eller testade. Etikettförhandsvisning och utskrift via webbläsarens utskriftsdialog är implementerade. iOS-appen använder Apples AirPrint-dialog för nätverksskrivare som stöder AirPrint. Kontrollera skrivarens pappersformat och skalning för att få exakt 100 × 150 mm.

## Verifiering

Ingen automatiserad testsvit eller byggpipeline ingår. Genomfört: JavaScript-syntaxkontroll och isolerade kontroller av QR-kodsgenereringen (matrisstorlek, sökmönster, payload-skillnader och maxlängd). Webbläsartest blockerades av den här miljöns webbläsarpolicy för lokala filer och lokala HTTP-servrar. Verifiera därför kund/däckflöde, mobilkamera, QR-avläsning och utskrift i en modern webbläsare innan produktionsbruk.

## iOS-app

Xcode-projektet finns i [`ios-app`](ios-app/README.md). Det kräver Xcode för att byggas, signeras och installeras på iPhone.
