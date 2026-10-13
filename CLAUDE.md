# Instrukcje dla Claude

- Zawsze odpowiadaj po polsku.

## Projekt

„Holter” dla samochodu: rejestrator ruchu na magistrali CAN oparty na ESP32 i kontrolerze MCP2515 (SPI).
Projekt jest na etapie koncepcji – kod i schematy powstają dopiero po rozstrzygnięciu decyzji.

- Koncepcja: `docs/KONCEPCJA.md`
- Rejestr decyzji: `docs/DECYZJE.md` (format `D-NNN`, nie usuwać starych wpisów – nadpisaną decyzję oznacz jako zastąpioną)

## Skille

- `grill-me` – przesłuchanie koncepcji pytanie po pytaniu, decyzje lądują w `docs/DECYZJE.md`.
- `spisz-koncepcje` – składa decyzje w `docs/KONCEPCJA.md`.
- `przeglad-sprzetu` – lista kontrolna schematu/modułów (zasilanie 12 V, poziomy 3,3/5 V, kwarc, terminacja).

## Zasady bezpieczeństwa

- Domyślnie MCP2515 w trybie **listen-only**. Nadawanie na magistralę auta tylko po wyraźnej zgodzie użytkownika.
- Nie proponuj zmian, które mogą zakłócić pracę sterowników pojazdu, bez opisania ryzyka.
