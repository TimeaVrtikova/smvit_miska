# Projekt: Automatická Miska na Vodu pre Psa

**Autor:** Timea Vrtíková
**Dátum:** 16. 12. 2025
**Verzia:** 1.1

---

## 1. Téma Projektu
Vývoj a výroba funkčného prototypu inteligentnej misky pre psa, ktorá automaticky deteguje nízku hladinu vody a zabezpečí jej doplnenie. Projekt kombinuje systémové modelovanie, prácu s mikrokontrolérom Arduino, základnou elektronikou, 3D modelovaním a 3D tlačou.

## 2. Motivácia
Primárnou motiváciou pre tento projekt bola osobná požiadavka od mojej sestry. Chcela pre svojho psa misku, ktorá by zabezpečila neustály prísun čerstvej vody bez potreby manuálneho dopĺňania. Cieľom bolo vytvoriť nielen funkčné, ale aj vizuálne príjemné riešenie podľa jej predstáv.

## 3. Cieľ Projektu
Hlavným cieľom bolo vytvoriť samostatne fungujúci systém, ktorý spĺňa nasledujúce kritériá:
*   **Automatizácia:** Miska musí sama rozpoznať, kedy hladina vody klesne pod definovanú úroveň.
*   **Funkčnosť:** Na základe detekcie nízkej hladiny musí systém aktivovať mechanizmus na doplnenie vody.
*   **Bezpečnosť:** Použitý materiál pre misku musí byť bezpečný pre kontakt s vodou a zvieratami (food-safe).

## 4. Použité Nástroje a Metodiky
*   **Hardvér:** Arduino Nano, Breadboard, vodivostné sondy, rezistory, modul na nabíjanie batérie, menič napätia.
*   **Softvér a Vývoj:** Arduino IDE, Tinkercad.
*   **Modelovanie a 3D Tlač:** Softvér na 3D modelovanie, 3D tlačiareň, materiál PETG+.
*   **Analýza a Návrh Systému:** Enterprise Architect (Sparx) pre UML/SysML, metóda "Lemon Tree".
*   **Verzionovanie:** GitHub Classroom.

## 5. Postup Práce a Implementácia

### Krok 1: Prvotný návrh a pivot
Pôvodný plán bol použiť ultrazvukový senzor, no testy ukázali, že signál neprechádza spoľahlivo cez stenu misky. Preto som projekt **zmenila (pivotovala)** na použitie **vodivostného senzora**, ktorý je jednoduchší a spoľahlivejší.

### Krok 2: Finálny kód
Kód pre Arduino bol upravený tak, aby čítal hodnotu z analógového pinu a na základe nej spínal alebo vypínal výstup pre vodné čerpadlo.

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
### Krok 3: 3D Tlač a Montáž
Miska bola namodelovaná a vytlačená z materiálu PETG+. Následne bola elektronika finálne spájkovaná a integrovaná do tela misky.

## 6. Záver
Projekt úspešne splnil všetky stanovené ciele. Výsledkom je funkčný prototyp automatickej misky. Počas práce som sa naučila základy práce s Arduinom, spájkovania, 3D tlače a systémového modelovania. Kľúčovým poznatkom bola dôležitosť flexibility a schopnosti prispôsobiť technické riešenie reálnym problémom.
