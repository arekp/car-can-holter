# Lista zakupów – MVP (2026-10)

Zasada: kupujemy w sklepach **z wysyłką z Polski/UE** – bez cła 3 € za każdy rodzaj towaru w paczce z Chin
(obowiązuje od 1.07.2026, do 2028) i bez cła/VAT z Wielkiej Brytanii po Brexicie.
AliExpress tylko z opcją „wysyłka z Polski / magazyn UE”.

Ceny z wyszukiwania – **sprawdź przed zakupem**. Żeby oszczędzić na wysyłce, spróbuj zebrać drobne moduły (poz. 5, 7, 8, 9, 10) w jednym sklepie (Botland albo Gotronik).

## Masz już

- ESP32-S3 DevKitC-1 N16R8
- moduł MCP2515 (sprawdzić: kwarc 8/16 MHz, transceiver TJA1050?)

## Do kupienia

| # | Komponent | Szt. | Gdzie (link) | ~Cena | Uwagi |
|---|---|---|---|---|---|
| 1 | Transceiver CAN **SN65HVD230** (Waveshare 3945) | 1 | [Botland](https://botland.com.pl/magistrala-can/6960-modul-z-interfejsem-can-sn65hvd230-waveshare-3945-5904422300715.html) | ~30–40 zł | MS-CAN → TWAI w ESP32. Zdjąć 120 Ω, jeśli jest |
| 2 | **Rozgałęźnik OBD2 (Y), wszystkie 16 pinów** | 1 | [Kaufland.pl](https://www.kaufland.pl/product/507136969/) – 74,50 zł; taniej: Allegro „rozdzielacz OBD2 16 pin” od sprzedawcy z PL (~30–50 zł) | 30–75 zł | **musi mieć piny 3 i 11** (tańsze mają tylko 6 pinów) |
| 3 | **Powerbank „Always On”** Voltaic **V25** (6700 mAh, pass-through) | 1 | [Kiwi Electronics (NL)](https://www.kiwi-electronics.com/nl/batterijen-accus-93?fm=66), dystrybutor UE: [SeeSense](https://seesense.eu/2025/07/01/what-is-an-always-on-battery/) | ~42 € netto (~52 € brutto) | nie wyłącza się przy małym poborze – [opis trybu](https://support.voltaicsystems.com/kb/getting-started/always-on-mode). Większy: V50/V75 |
| 4 | Ładowarka USB do zapalniczki (12 V → 5 V, min. 2 A) | 1 | dowolny sklep w PL | 20–30 zł | gniazdo musi gasnąć po wyjęciu kluczyka |
| 5 | Moduł microSD **3,3 V** (Pololu) | 1 | [Botland – Pololu microSD](https://botland.com.pl/en/accessories-for-memory-cards/2696-pololu-microsd-card-reader-module.html) | ~15–25 zł | czysty 3,3 V, bez konwertera 5 V |
| 6 | Karta **SanDisk High Endurance 32 GB** (SDSQQNR-032G) | 1 | sklepy w PL (x-kom, Media Expert, Allegro) | ~50–100 zł (w UE 13–27 €) | odporna na ciągły zapis i temperaturę |
| 7 | **ADS1115** – 16-bit ADC, I²C | 1 | [Gotronik](https://www.gotronik.pl/ads1115-modul-przetwornika-adc-16bit-4-kanaly-i2c-kartapdf-5642.html) / [Gotronik 2](https://www.gotronik.pl/ads1115-modul-czterokanalowegoprzetwornika-adc-16-bit-i2c-kartapdf-14840.html) | ~19 zł | pomiar napięcia akumulatora |
| 8 | RTC **DS3231** + bateria (CR1220/CR2032 wg modułu) | 1 | [Gotronik](https://gotronik.pl/modul-zegara-ds3231-rtc-wysokiej-precyzji-kartapdf-2320.html) | ~15–25 zł | czas na postoju bez WiFi |
| 9 | Elementy drobne: rezystory 1 MΩ i 220 kΩ (1%), kondensator 100 nF, dioda **BAT54S**, oprawka bezpiecznika w przewód + bezpiecznik 0,5 A | 1 kpl | [TME](https://www.tme.eu/pl/) (wysyłka z Łodzi) albo Botland/Gotronik | ~15 zł | dzielnik do pomiaru napięcia z pinu 16 |
| 10 | **Konwerter poziomów** BSS138, 4 kanały – **tylko jeśli** MCP2515 ma TJA1050 na 5 V | 2 | [Electrokit (SE)](https://www.electrokit.com/en/product/nivaomvandlare-4-kanaler-bidirektionell-2/) lub [Gotronik (2-kanałowy)](https://www.gotronik.pl/konwerter-poziomow-stanow-logicznych-3-3v-5v-ttl-kartapdf-7031.html) | ~10–15 zł/szt. | SPI ≤ 2 MHz (dla MCP2515 wystarczy). Linie: SCK, MOSI, CS, MISO, INT |
| 11 | Płytka uniwersalna, przewody, złącza JST-XH/śrubowe, skrętka, obudowa ABS | – | dowolny sklep elektroniczny | ~30–50 zł | |

## Opcjonalnie, ale polecane przed budową holtera

| Komponent | Gdzie | Po co |
|---|---|---|
| ELM327 z przełącznikiem **HS/MS-CAN** (KBE) | [morele.net](https://www.morele.net/kbe-elm327-obd2-hs-ms-can-forscan-przelacznik-ford-12169710/) | FORScan: odczyt DTC ze wszystkich modułów (w tym GEM), lista modułów auta. Po diagnozie **wyjąć z gniazda** |

## Szacunek

- Bez poz. 10 i opcji: **~400–500 zł** (z czego ~220 zł to powerbank Voltaic)
- Taniej: zwykły powerbank z trybem „low current” zamiast Voltaica (−150 zł), ale ryzyko wyłączania się na postoju – do sprawdzenia testem.
