---
name: spisz-koncepcje
description: Zamienia ustalenia z rozmowy i docs/DECYZJE.md w spójny dokument koncepcji docs/KONCEPCJA.md (cel, wymagania, architektura, sprzęt, firmware, format danych, plan etapów). Używaj po sesji grill-me albo gdy użytkownik prosi „spisz koncepcję”, „zrób specyfikację”, „PRD”.
---

# Spisz koncepcję

1. Przeczytaj `docs/DECYZJE.md`, aktualne `docs/KONCEPCJA.md` i ustalenia z bieżącej rozmowy.
2. Nie wymyślaj decyzji – jeśli czegoś brakuje, wpisz to w sekcji „Otwarte pytania”
   (i ewentualnie zaproponuj `grill-me` dla tej gałęzi).
3. Zaktualizuj `docs/KONCEPCJA.md` według szablonu:

```markdown
# Koncepcja: Car CAN Holter

## Cel
## Scenariusze użycia
## Wymagania
### Funkcjonalne
### Niefunkcjonalne (zasilanie, temperatura, przepustowość, czas nagrania)
## Bezpieczeństwo pojazdu
## Architektura
(diagram blokowy w mermaid: auto ↔ OBD-II ↔ transceiver ↔ MCP2515 ↔ SPI ↔ ESP32 ↔ SD/WiFi)
## Sprzęt
(lista elementów, schemat połączeń – tabela pinów)
## Firmware
(framework, moduły, przepływ danych, bufory, obsługa zaniku zasilania)
## Format danych i odczyt
## Plan etapów (MVP → kolejne)
## Otwarte pytania
```

4. Odwołuj się do decyzji po ID (`D-007`), żeby było wiadomo, skąd wynika zapis.
5. Pokaż użytkownikowi krótkie podsumowanie zmian, nie cały dokument.
