# Rozpoznanie: możliwości i projekty open source

Stan na 2026-10. Punkty oznaczone „(do weryfikacji)” trzeba potwierdzić na aucie albo w datasheecie.

## 1. Ford Focus Mk2 – co jest na magistralach

| Magistrala | Prędkość | OBD-II | Co tam jest |
|---|---|---|---|
| HS-CAN | 500 kbit/s | 6 (H) / 14 (L) | PCM (silnik), ABS/ESP, wspomaganie, zegary |
| MS-CAN | 125 kbit/s | 3 (H) / 11 (L) | moduły komfortu: GEM (BCM), radio/audio, klimatyzacja, nawigacja, moduły drzwi, zegary (bramka) |

- Zasypianie auta koordynują moduły komfortu – **MS-CAN jest głównym celem**, HS-CAN pomocniczo
  (np. czy PCM/ABS na pewno milkną).
- GEM zasypia po ok. 15–20 min od zamknięcia auta – prawidłowy pomiar upływu dopiero po tym czasie.
  Typowi winowajcy w Fordach z tej epoki: radio, alarm (dodatkowy lub fabryczny), przekaźnik zasilania PCM.
- Gotowej, pełnej mapy ID dla MS-CAN Mk2 **nie ma** – są fragmenty:
  - [RealDash forum – Focus Mk2/Mk2.5 HS-CAN i MS-CAN mapping](https://forum.realdash.net/t/ford-focus-mk2-hs-can-and-ms-can-code-mapping/10063) – pliki XML z dekodowaniem (Mk2.5, rynek BR),
  - [gpatsiaouras/ford-focus-canbus](https://github.com/gpatsiaouras/ford-focus-canbus) – Arduino + MCP2515 w Focusie Mk2 2005 (prędkość, światła),
  - [InkyMonster/Ford-Focus-LW-instrument-Cluster-CANbus-](https://github.com/InkyMonster/Ford-Focus-LW-instrument-Cluster-CANbus-) – ID zegarów na MS-CAN (Mk3, ale ta sama rodzina Forda),
  - [iDoka/awesome-automotive-can-id](https://github.com/iDoka/awesome-automotive-can-id) – zbiór baz DBC różnych aut.
- Wniosek: **nie musimy znać znaczenia ramek**, żeby znaleźć upływ. Wystarczy wiedzieć, *które ID nadają i kiedy*
  – nowe ID pojawiające się po uśpieniu auta = konkretny moduł. ID modułu ustalimy porównaniem
  (odpięcie bezpiecznika/wtyczki → znika ID) albo FORScan-em (darmowy, nie open source).

## 2. Sprzęt – możliwości

### Dwie magistrale na jednym ESP32
- ESP32 ma wbudowany kontroler CAN (**TWAI**) z trybem **listen-only** (nie wysyła ACK ani ramek).
  Potrzebuje tylko transceivera 3,3 V (SN65HVD230 / TCAN1042V / TJA1042T/3).
- **Propozycja:** TWAI → MS-CAN 125 kbit/s, MCP2515 → HS-CAN 500 kbit/s (albo odwrotnie). Oba w listen-only.
  Taki układ ma [CanLog2](https://github.com/DaTwo/CanLog2) (MCP2515 + TWAI, zapis SD, format GVRET/SavvyCAN).

### Pobór prądu samego holtera – kluczowy problem
Urządzenie szukające upływu 20–50 mA nie może samo brać kilkudziesięciu mA.

| Element | Typowy pobór | Uwagi |
|---|---|---|
| ESP32 aktywny + WiFi | 80–240 mA | tylko krótko |
| ESP32 light sleep | ~0,8 mA | budzenie z GPIO/timera, RAM zachowany |
| ESP32 deep sleep (sam układ) | ~10 µA | płytka DevKit: LDO + USB-UART + LED → kilka mA (do zmierzenia) |
| MCP2515 sleep | ~5 µA | budzi się przy aktywności na magistrali (przerwanie WAKIF → INT) (do weryfikacji w datasheecie) |
| TJA1050 (popularne moduły) | ~10 mA, brak trybu uśpienia | **zły wybór do pracy na postoju** |
| SN65HVD230 standby (Rs = 3,3 V) | ~0,4 mA | odbiornik działa w standby – może budzić |
| TJA1042/TCAN1042 standby | ~10–15 µA | wyjście RXD sygnalizuje wybudzenie z magistrali |

- Wniosek: docelowo transceiver z trybem standby + przetwornica 12 V → 3,3 V z niskim prądem spoczynkowym
  (np. moduł na TPS62933/LMR36006/MP2451 lub LDO automotive), bez płytki DevKit z USB-UART w wersji finalnej.
- Holter **musi zmierzyć i logować własny pobór** (żeby nie mylić go z upływem auta).

### Strategia pracy
1. **Jazda** (napięcie > 13,2 V): pełny zapis obu magistral (lub tylko statystyki).
2. **Po zgaszeniu**: pełny zapis przez np. 30–60 min (zasypianie modułów – najciekawszy fragment).
3. **Postój**: MCP2515/transceiver w standby, ESP32 w light/deep sleep, budzenie przez:
   aktywność na CAN, timer (pomiar napięcia co N min), ewentualnie czujnik ruchu/drzwi.
   Po wybudzeniu – zapis kto obudził magistralę i jak długo była aktywna.

## 3. Dodatkowe czujniki („co się da, czym się da”)

| Co | Czym | Po co |
|---|---|---|
| Napięcie akumulatora | dzielnik + ADS1115 (16 bit) z OBD pin 16 | tempo rozładowania, wykrycie pracy silnika (>13,2 V), skoki przy wybudzeniu modułów |
| Spadki napięcia na bezpiecznikach | ADS1115 (±256 mV) na wybranych bezpiecznikach | **który obwód** pobiera prąd, także gdy moduł milczy na CAN |
| Prąd całkowity | cęgowy czujnik DC (Hall) – opcja | bez przecinania instalacji |
| Temperatura | DS18B20 / czujnik w ADS/BME280 | korelacja z porą i zachowaniem akumulatora |
| Czas | DS3231 RTC (+ NTP przy WiFi) | znaczniki czasu w zapisach z postoju bez sieci |
| Ruch/wstrząs | akcelerometr (np. LIS3DH, budzi ESP32) | otwarcie drzwi, wsiadanie – wyjaśnia wybudzenia |
| Światło w kabinie | fotorezystor/BH1750 | lampka sufitowa/bagażnika nie gaśnie |

## 4. Firmware i oprogramowanie – gotowe klocki

| Projekt | Co daje |
|---|---|
| [CanLog2](https://github.com/DaTwo/CanLog2) | ESP32: MCP2515 + TWAI jednocześnie, zapis na SD (SD_MMC), format GVRET → SavvyCAN. Wczesny etap, dobra baza do nauki |
| [ESP32RET](https://github.com/collin80/ESP32RET) (fork: [aszu80/ESP32RET](https://github.com/aszu80/ESP32RET)) | referencyjny firmware GVRET dla ESP32 (biblioteka esp32_can: TWAI + MCP2515 + MCP2517FD), podgląd na żywo w SavvyCAN przez WiFi/USB |
| [wifican](https://github.com/uhi22/wifican) | ESP32-S3, GVRET przez WiFi do SavvyCAN |
| [esp-idf-can2mqtt](https://github.com/nopnop2002/esp-idf-can2mqtt) | most CAN → MQTT (ID → topic) |
| [RejsaCAN-ESP32](https://github.com/MagnusThome/RejsaCAN-ESP32) | otwarta płytka ESP32 do auta (zasilanie 5–15 V, CAN) – wzór sprzętu |
| [ACAN2515](https://www.arduino.cc/reference/en/libraries/acan2515) | dojrzała biblioteka MCP2515 (Arduino, działa na ESP32) |
| ESPHome `canbus` (mcp2515 / esp32_can) | szybki start + Home Assistant; słabszy do zapisu każdej ramki i pracy na niskim poborze |

**Na PC:** [SavvyCAN](https://www.savvycan.com/) (analiza, porównania „przed/po”, wykrywanie zmieniających się bitów),
`python-can` + `cantools` (skrypty, format `candump`), Wireshark (SocketCAN), Grafana/InfluxDB (wykresy w czasie),
FORScan (darmowy: lista modułów, DTC, konfiguracja GEM – do identyfikacji, nie do logowania).

## 5. Dane – zapis i przesył

- **SD (SPI lub SDMMC)** – zawsze, jako bufor pierwotny; format `candump -l` (tekst: `(czas) can0 ID#DANE`)
  – czytają go python-can, SavvyCAN (import) i cantools. Rotacja plików co sesję/godzinę.
- **WiFi** – auto zwykle stoi w zasięgu domowej sieci, czyli **dokładnie wtedy, gdy występuje upływ**:
  po wybudzeniu ESP32 wysyła podsumowanie/plik do MQTT/HA/serwera (InfluxDB), na żywo GVRET do SavvyCAN.
  Uwaga: WiFi to największy pobór – wysyłać paczkami, rzadko.
- Opcja dalsza: LTE (SIM7080/A7670) – na razie niepotrzebne.

## 6. Ryzyka

- Tryb normalny (nie listen-only) przy złej prędkości → ramki błędów na magistrali auta. **Zawsze listen-only.**
- Sam fakt podpięcia może budzić moduły (np. ELM327 w gnieździe OBD) – holter nie może nadawać nic.
- Moduły TJA1050 na 5 V: wyjście MISO MCP2515 5 V → niebezpieczne dla ESP32 (patrz skill `przeglad-sprzetu`).
- Pin 16 OBD to stałe 12 V – holter na stałe w aucie musi mieć bezpiecznik i niski pobór na postoju.
