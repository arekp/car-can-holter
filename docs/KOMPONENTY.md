# Lista komponentów (BOM)

Wersja robocza. Ceny orientacyjne (PLN, AliExpress/krajowe sklepy). Pozycje „(do weryfikacji)” sprawdzić w datasheecie przed zakupem.

## Etap 1 – MVP (prototyp: obie magistrale + napięcie + SD)

### Rdzeń

| # | Komponent | Ilość | Po co | Uwagi | ~Cena |
|---|---|---|---|---|---|
| 1 | ESP32-S3 DevKitC-1 (lub inny ESP32) | 1 | mikrokontroler, TWAI, WiFi | **jest**. Do prototypu OK; w wersji docelowej płytka bez USB-UART/LED (niższy pobór w uśpieniu) | – |
| 2 | Moduł MCP2515 | 1 | HS-CAN 500 kbit/s przez SPI | **jest**. Sprawdzić kwarc (8/16 MHz) i transceiver. Jeśli TJA1050 (5 V) → patrz poz. 4 | – |
| 3 | Moduł transceivera **SN65HVD230** (3,3 V), np. Waveshare | 1 | MS-CAN do TWAI w ESP32 (MCP2515 ma własny transceiver) | tani, 3,3 V. Pin Rs: na tanich modułach bywa na sztywno do GND – do standby trzeba go wyprowadzić (do weryfikacji) | 10–15 |
| 4 | Konwerter poziomów 4-kanałowy (BSS138) | 1 | SPI ESP32 (3,3 V) ↔ MCP2515 na 5 V | tylko jeśli moduł MCP2515 ma TJA1050 i jest zasilany z 5 V | 5 |

Alternatywa dla 3+4 w wersji docelowej: transceivery z prawdziwym standby i wybudzaniem – **TJA1042T/3** lub **TCAN1042V** (~10–15 µA w standby).

### Połączenie z autem i zasilanie (wg D-006: własny akumulator ładowany w czasie jazdy)

| # | Komponent | Ilość | Po co | Uwagi | ~Cena |
|---|---|---|---|---|---|
| 5 | **Rozgałęźnik OBD (Y)** albo wtyk OBD-II męski z przewodami | 1 | dostęp do pinów 3/11 (MS-CAN), 6/14 (HS-CAN), 5 (masa), 16 (pomiar napięcia) | rozgałęźnik zostawia wolne gniazdo dla FORScan/ELM327 | 15–30 |
| 6 | **Powerbank z trybem „always on” / „low current” i pass-through** (10 000 mAh) | 1 | zasilanie holtera na postoju (prototyp) | sprawdzić: nie wyłącza się przy ~20–50 mA i nie przerywa wyjścia przy podłączeniu/odłączeniu ładowania | 80–150 |
| 7 | Ładowarka USB do gniazda zapalniczki (12 V → 5 V) | 1 | ładowanie powerbanka tylko w czasie jazdy | gniazdo musi gasnąć po wyjęciu kluczyka (sprawdzić w aucie) | 15–30 |
| 8 | Bezpiecznik 0,5 A + oprawka (linia pomiarowa z pinu 16) | 1 | ochrona instalacji auta | zaraz przy wtyku OBD | 5 |
| 9 | Skrętka (np. para z kabla sieciowego) | ~1 m | przewody CAN-H/CAN-L | krótko (< 30–50 cm), każda magistrala na swojej parze | – |

Wersja docelowa zamiast 6–7: ogniwo **LiFePO4** + BMS z czujnikiem temperatury + moduł UPS z power-path
+ ładowanie włączane przy napięciu auta > 13,2 V.

### Zapis danych i czas

| # | Komponent | Ilość | Po co | Uwagi | ~Cena |
|---|---|---|---|---|---|
| 11 | Moduł microSD **3,3 V** (bez stabilizatora/konwertera 5 V) | 1 | zapis logów `candump` | moduły „5 V” z 74LVC125 potrafią blokować magistralę SPI – lepiej osobna SPI lub SDMMC | 5–10 |
| 12 | Karta microSD 8–32 GB, najlepiej przemysłowa (High Endurance) | 1 | nośnik | temperatura w aucie −20…+70 °C | 30–50 |
| 13 | RTC **DS3231** (moduł) + CR2032 | 1 | czas na postoju bez WiFi | na popularnym module usunąć diodę/rezystor ładowania (CR2032 nie jest akumulatorkiem) | 10 |

### Pomiary

| # | Komponent | Ilość | Po co | Uwagi | ~Cena |
|---|---|---|---|---|---|
| 14 | **ADS1115** (16-bit ADC, I²C) | 1 | napięcie akumulatora (dokładnie), później spadki na bezpiecznikach | 4 wejścia, wzmocnienie do ±256 mV | 10–15 |
| 15 | Rezystory 1%: 1 MΩ i 220 kΩ (dzielnik) + 100 nF + dioda podwójna BAT54S | 1 kpl | 12–15 V → ~2,7 V na wejście ADS1115 | pobór z auta ~12 µA; BAT54S ogranicza skoki do zakresu 0–3,3 V | 2 |

### Montaż prototypu

| # | Komponent | Po co |
|---|---|---|
| 16 | Płytka uniwersalna/stykowa, przewody, złącza JST-XH lub śrubowe | prototyp; w aucie bez kabelków „dupont” (wibracje) |
| 17 | Obudowa ABS z dławnicą | montaż pod deską |
| 18 | Multimetr z zakresem mA | pomiar poboru samego holtera i weryfikacja upływu |

## Etap 2 – lokalizacja obwodu i kontekst (opcjonalnie)

| # | Komponent | Po co | ~Cena |
|---|---|---|---|
| 19 | Drugi ADS1115 + przewody pomiarowe do bezpieczników (cienkie końcówki/„fuse tap”) | spadki napięcia na 4–8 bezpiecznikach → który obwód ciągnie prąd | 20–30 |
| 20 | Cęgowy czujnik prądu DC (Hall) z wyjściem analogowym | prąd całkowity bez przecinania przewodu (czułość w mA ograniczona) | 50–200 |
| 21 | Akcelerometr **LIS3DH** (przerwanie budzi ESP32) | otwarcie drzwi, wsiadanie, wstrząsy | 8 |
| 22 | Czujnik światła **BH1750** | lampka kabiny/bagażnika nie gaśnie | 8 |
| 23 | Czujnik temperatury **DS18B20** | kontekst (zima/lato, akumulator) | 5 |

## Oprogramowanie (darmowe)

- Firmware: Arduino/PlatformIO lub ESP-IDF; biblioteki: sterownik TWAI (wbudowany), ACAN2515 / esp32_can (MCP2515), SdFat, RTClib, Adafruit_ADS1X15.
- PC: SavvyCAN, python-can + cantools, Grafana/InfluxDB (opcjonalnie), FORScan (identyfikacja modułów, DTC).

## Szacunkowy koszt

- MVP (bez rzeczy, które już są): **~150–250 zł**
- Etap 2: **+50–250 zł** (zależnie od cęgów)
