---
name: przeglad-sprzetu
description: Krytyczny przegląd schematu połączeń, listy części albo konfiguracji MCP2515 + ESP32 pod kątem bezpieczeństwa auta i typowych błędów (zasilanie 12 V, poziomy logiczne, kwarc, terminacja, listen-only). Używaj, gdy użytkownik pokazuje schemat, moduł, pinout albo pyta „czy to zadziała”, „sprawdź połączenia”.
---

# Przegląd sprzętu MCP2515 + ESP32

Przejdź po liście kontrolnej i dla każdego punktu podaj: ✅ OK / ⚠️ ryzyko / ❌ błąd / ❓ brak danych.
Na końcu – lista konkretnych poprawek w kolejności ważności.

## Lista kontrolna

**Zasilanie**
- Źródło: OBD-II pin 16 (stałe 12 V!) albo zapalniczka; czy urządzenie nie rozładuje akumulatora (pobór w uśpieniu, wykrywanie pracy silnika/napięcia ładowania ~13,8 V).
- Przetwornica 12 V → 5 V/3,3 V odporna na skoki (load dump, rozruch ~6 V), bezpiecznik, dioda/zabezpieczenie odwrotnej polaryzacji, TVS.
- Masa: OBD-II pin 4 (masa nadwozia) i 5 (masa sygnałowa).

**MCP2515 i transceiver**
- Częstotliwość kwarcu (8 MHz vs 16 MHz) zgodna z konfiguracją firmware – inaczej zła prędkość magistrali.
- Popularny moduł z TJA1050 potrzebuje 5 V; MCP2515 na 5 V wystawia SO (MISO) 5 V → ESP32 nie toleruje 5 V. Rozwiązania: zasilić MCP2515 z 3,3 V a TJA1050 z 5 V (modyfikacja modułu), konwerter poziomów, albo transceiver 3,3 V (SN65HVD230/TCAN332).
- Pin INT podłączony do GPIO z przerwaniem (polling SPI gubi ramki przy 500 kbit/s).
- Zworka terminacji 120 Ω **zdjęta** przy podłączeniu do auta (magistrala ma już terminację).

**Połączenie z autem**
- OBD-II: pin 6 = CAN-H, pin 14 = CAN-L (HS-CAN). MS-CAN zależnie od marki (np. Ford 3/11).
- Tryb **listen-only** domyślnie; nadawanie (OBD-II PID, ACK) tylko świadomie i przy właściwej prędkości – zła prędkość + tryb normalny = ramki błędów na magistrali.

**ESP32**
- Piny SPI: dla ESP32-S3 unikać GPIO 26–32 (flash) i 33–37 (PSRAM w wersjach N16R8), strapping pins 0/3/45/46.
- Karta SD: osobna magistrala SPI lub SDMMC; wspólna SPI z MCP2515 wymaga ostrożnego przydzielania.
- Zamykanie/flush plików przy spadku napięcia (detekcja zaniku + kondensator/superkondensator).

**Środowisko**
- Temperatura w kabinie latem 70 °C+, zimą −20 °C – karty SD i elementy w zakresie przemysłowym, jeśli możliwe.
- Wibracje: złącza z zatrzaskiem, bez kabelków dupont w finalnej wersji.

Nie zgaduj parametrów modułu – jeśli to istotne, poproś o zdjęcie/nazwę modułu albo sprawdź datasheet.
