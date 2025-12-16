# Projekt: Automatická Miska na Vodu pre Psa

**Autor:** Timea Vrtíková
**Dátum:** 16. 12. 2025
**Verzia:** 2.0 (Finálna dokumentácia)

---

## 1. Téma Projektu
Vývoj a výroba funkčného prototypu inteligentnej misky pre psa, ktorá automaticky deteguje nízku hladinu vody a zabezpečí jej doplnenie. Projekt spája hardvér (mikrokontrolér Arduino a senzory), softvér (programovanie v C++), 3D modelovanie a 3D tlač do jedného funkčného celku.

## 2. Motivácia a Východiskový Stav
Primárnou motiváciou pre tento projekt bola praktická potreba mojej sestry. Chcela pre svojho psa moderné a pohodlné riešenie, ktoré by zabezpečilo, že bude mať vždy prístup k čerstvej vode, aj keď nikto nie je doma. Cieľom bolo vytvoriť produkt, ktorý by bol nielen funkčný, ale aj esteticky príjemný a zapadol do domácnosti. Ako predloha pre dizajn poslúžil jej vlastný nákres.

![Inšpirácia pre dizajn misky](obrazky/predloha_misky.jpg)
*Obr. 1: Pôvodný nákres a vizuálna predstava od sestry.*

## 3. Ciele Projektu
Na začiatku projektu som si stanovila nasledujúce kľúčové ciele:
*   **Automatizácia:** Systém musí byť schopný samostatne detegovať pokles hladiny vody pod určenú úroveň.
*   **Funkčnosť:** Po detekcii nízkej hladiny musí systém automaticky aktivovať mechanizmus na doplnenie vody (napr. malé čerpadlo).
*   **Bezpečnosť a Hygiena:** Miska musí byť vyrobená z materiálu, ktorý je bezpečný pre kontakt s potravinami a vodou (food-safe). Povrch musí byť hladký, aby sa zabránilo množeniu baktérií.
*   **Samostatnosť:** Celé zariadenie má byť napájané z batérie, aby bolo prenosné a bezpečné.

## 4. Roadmapa a Fázy Projektu
Projekt bol rozdelený do piatich logických fáz:
1.  **Fáza Analýzy a Návrhu:** Brainstorming, výber medzi projektmi, definovanie požiadaviek a prieskum vhodných komponentov a materiálov.
2.  **Fáza Prototypovania s Ultrazvukom:** Objednávka prvej sady komponentov, prvé experimenty s Arduinom a testovanie ultrazvukového senzora ako pôvodne zamýšľaného riešenia.
3.  **Fáza Pivotu a Nového Návrhu:** Identifikácia nedostatkov ultrazvukového senzora a prechod na spoľahlivejšiu metódu merania vodivosti vody.
4.  **Fáza 3D Modelovania a Výroby:** Vytvorenie presného 3D modelu misky s priestorom pre elektroniku a jeho následná tlač z materiálu PETG+.
5.  **Fáza Finálnej Montáže a Testovania:** Spájkovanie, integrácia všetkých komponentov do tela misky, finálne úpravy kódu a overenie funkčnosti celého systému.

## 5. Detailný Postup Práce a Implementácia

### 5.1. Počiatočný Prieskum a Výber Komponentov
Na začiatku som zvažovala dva nápady – samozavlažovací kvetináč a misku pre psa. Zvíťazila miska. Pre detekciu hladiny som sa rozhodla pre ultrazvukový senzor HC-SR04 ako lacnejšiu alternatívu k drahším bezkontaktným senzorom. Pre riadenie som zvolila populárny mikrokontrolér Arduino Nano.

![Prvé objednané komponenty](obrazky/komponenty.jpg)
*Obr. 2: Prvá sada komponentov pripravená na experimentovanie (Arduino, ultrazvukový senzor, breadboard).*

### 5.2. Prvý Experiment a Prekážka
Po úspešnom rozbehaní základného "Blink" testu na Arduine som sa pustila do spájkovania. Bohužiaľ, pri tomto kroku som urobila chybu a zničila prvé Arduino. Bola to dôležitá lekcia, ktorá ma naučila trpezlivosti. Objednala som nové, už predpájkované Arduino, a úspešne som zapojila a otestovala ultrazvukový senzor.

### 5.3. Zásadný Pivot – Od Ultrazvuku k Vodivosti
Počas testovania sa ukázal kľúčový problém: ultrazvukový senzor **nedokázal spoľahlivo merať hladinu cez hrubšiu stenu misky** z materiálu PETG. Signál bol príliš tlmený a merania neboli použiteľné.

Po konzultácii som sa rozhodla pre radikálnu zmenu prístupu (**pivot**). Nové riešenie bolo založené na **meraní vodivosti vody**. Dve jednoduché kovové sondy sú umiestnené v miske. Ak je voda nad nimi, elektrický obvod je spojený. Ak voda klesne, obvod sa preruší. Tento stav je veľmi ľahko a spoľahlivo čitateľný analógovým pinom Arduina.

![Schéma zapojenia vodivostného senzora](obrazky/schema.png)
*Obr. 3: Finálna schéma zapojenia s použitím vodivostného senzora, navrhnutá v Tinkercad.*

### 5.4. Finálny Kód
Na základe nového prístupu som napísala a odladila finálny kód. Kód neustále monitoruje analógový pin `A0`. Ak nameraná hodnota klesne pod prahovú úroveň `60` (čo indikuje neprítomnosť vody), na PWM pine `D3` sa nastaví maximálna hodnota, ktorá aktivuje externý mechanizmus (čerpadlo).

```cpp
// Definovanie pinov
constexpr int testPin = A0;      // Vstupny pin na citanie stavu vodivosti
constexpr int controlPWM = 3;    // Vystupny pin na ovladanie cerpadla

void setup() {
  pinMode(testPin, INPUT);
  pinMode(controlPWM, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  static int sensorValue = 0, u = 0;
  sensorValue = analogRead(testPin);

  // Ak je hodnota nízka (pod 60), voda chýba -> aktivuj cerpadlo
  if (sensorValue < 60) {
    u = 255;
  } else {
    u = 0;
  }
  analogWrite(controlPWM, u);
  delay(500);
}
```
### 5.5. 3D Model, Tlač a Montáž
Paralelne s prácou na elektronike bol vytvorený 3D model misky. Model bol navrhnutý tak, aby mal oddelený priestor pre elektroniku a batériu. Ako materiál pre tlač bol zvolený **PETG+**, ktorý je známy svojou odolnosťou a bezpečnosťou pre styk s potravinami. Po tlači som plánovala povrch vyhladiť a ošetriť epoxidom pre dokonalú hygienu.

![3D model misky pred tlačou](obrazky/3d_model.png)
*Obr. 4: Vizualizácia finálneho 3D modelu misky.*

Po úspešnej tlači nasledovala finálna montáž – spájkovanie všetkých komponentov na pevno a ich umiestnenie do tela misky.

![Hotový a funkčný prototyp misky](obrazky/finalny_produkt.jpg)
*Obr. 5: Finálny produkt – funkčná automatická miska pripravená na použitie.*

## 6. Záver a Získané Skúsenosti
Projekt úspešne splnil všetky stanovené ciele a výsledkom je plne funkčný prototyp automatickej misky. Počas práce som si osvojila širokú škálu zručností – od návrhu elektronických obvodov a programovania mikrokontrolérov, cez 3D modelovanie a tlač, až po praktické spájkovanie. Najdôležitejším poznatkom však bola dôležitosť flexibility a schopnosti zmeniť pôvodný plán, keď sa ukáže ako nefunkčný. Schopnosť urobiť "pivot" a nájsť jednoduchšie a robustnejšie riešenie bola kľúčom k úspešnému dokončeniu projektu.
