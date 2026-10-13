# Rejestr decyzji

Format wpisu:

```markdown
## D-NNN: <krótki tytuł>
- **Decyzja:** …
- **Dlaczego:** …
- **Odrzucone opcje:** …
- **Otwarte kwestie:** …
```

## D-001: Platforma bazowa
- **Decyzja:** ESP32 + kontroler CAN MCP2515 (SPI).
- **Dlaczego:** założenie projektu.
- **Odrzucone opcje:** wbudowany kontroler TWAI w ESP32 (do ponownego rozważenia – nie wymaga MCP2515, tylko transceivera).
- **Otwarte kwestie:** wariant ESP32, kwarc modułu MCP2515, transceiver.

## D-002: Cel urządzenia – diagnostyka upływu prądu po wyłączeniu auta
- **Decyzja:** Holter ma monitorować, co dzieje się w aucie po zakończeniu jazdy: który moduł się wybudza
  albo nie zasypia i powoduje upływ prądu (parasitic drain).
- **Dlaczego:** w aucie występuje upływ prądu po zgaszeniu silnika; trzeba ustalić, który układ się włącza
  lub nie wyłącza po określonym czasie.
- **Odrzucone opcje:** telemetria jazdy, reverse engineering jako cel główny (mogą być produktem ubocznym).
- **Otwarte kwestie:** czy samo CAN wystarczy, czy trzeba też mierzyć prąd/napięcie (moduł może pobierać prąd bez nadawania).

## D-003: Pojazd – Ford Focus Mk2
- **Decyzja:** urządzenie dla Forda Focusa Mk2 (2004–2011).
- **Dlaczego:** auto, w którym występuje upływ prądu.
- **Fakty o aucie (do potwierdzenia na aucie):**
  - HS-CAN 500 kbit/s – OBD-II pin 6 (CAN-H) / 14 (CAN-L): silnik (PCM), ABS, zegary, wspomaganie.
  - MS-CAN 125 kbit/s – OBD-II pin 3 (CAN-H) / 11 (CAN-L): moduły komfortu – GEM/BCM, radio, klimatyzacja,
    moduły drzwi, zegary (bramka między magistralami).
  - Moduły odpowiedzialne za zasypianie auta są głównie na **MS-CAN** – to on jest głównym celem.
  - OBD-II pin 16 – stałe 12 V, pin 4/5 – masa.
- **Otwarte kwestie:** rok/wersja wyposażenia, radio fabryczne czy nie, doposażenie (alarm, kamera, nawigacja).

## D-004: Sprzęt bazowy i kierunek przesyłu danych
- **Decyzja:** ESP32 + posiadany moduł MCP2515. Dane: zapis na nośnik lub przesył dalej – do rozstrzygnięcia.
- **Dlaczego:** sprzęt już jest.
- **Otwarte kwestie:** dokładny model modułu (kwarc, transceiver), wariant ESP32, nośnik vs WiFi.

## D-005: Pomiar prądu – bez bocznika w obwodzie rozrusznika
- **Decyzja:** holter **nie** ma żadnego elementu pomiarowego w szeregu z głównym przewodem akumulatora.
  MVP: CAN + pomiar napięcia akumulatora (OBD-II pin 16, dzielnik + ADC/ADS1115).
  Etap 2 (jeśli CAN nie wskaże winnego): pomiar spadków napięcia na bezpiecznikach (ADS1115, metoda „fuse voltage drop”)
  albo cęgowy czujnik prądu DC (Hall) nakładany na przewód.
- **Dlaczego:** prąd rozruchu 150–600 A. Bocznik na tyle prądu ma ~0,1 mΩ, więc 20–50 mA upływu to ~2–5 µV –
  poniżej rozdzielczości INA226. Bocznik czuły na mA spłonąłby przy rozruchu. Bocznik z przekaźnikiem
  obejściowym – ryzyko unieruchomienia auta przy awarii przekaźnika.
- **Odrzucone opcje:** INA226 z bocznikiem na klemie minusowej, bocznik z przekaźnikiem obejściowym.
- **Otwarte kwestie:** czy w MVP dodać ADS1115 od razu (tani, przyda się w etapie 2).
