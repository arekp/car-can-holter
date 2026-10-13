---
name: grill-me
description: Przesłuchaj użytkownika pytanie po pytaniu o koncepcję, plan albo projekt (sprzęt, firmware, format danych), aż każda gałąź decyzji będzie rozstrzygnięta. Używaj, gdy użytkownik mówi „grill me”, „przemaglujmy”, „rozpracujmy koncept”, „dopytaj mnie” albo chce stres-testu pomysłu przed implementacją.
---

# Grill me – przesłuchanie koncepcji

Celem jest wspólne, pełne zrozumienie projektu **zanim** powstanie kod lub schemat.
Nie implementujesz niczego w trakcie przesłuchania.

## Jak prowadzić

1. Przeczytaj `docs/KONCEPCJA.md` i `docs/DECYZJE.md` (jeśli istnieją) – nie pytaj o rzeczy już rozstrzygnięte.
2. Zbuduj w myślach drzewo decyzji projektu. Idź po nim od korzenia: najpierw decyzje,
   od których zależą inne (cel urządzenia → sprzęt → firmware → format danych → UX).
3. Zadawaj **jedno pytanie naraz**. Do każdego pytania dołącz:
   - dlaczego to ważne (co od tej decyzji zależy),
   - **swoją rekomendację** z krótkim uzasadnieniem,
   - 2–4 realne opcje, jeśli to pytanie zamknięte.
4. Jeśli odpowiedź da się ustalić samemu (z repo, z datasheetu, z dokumentacji ESPHome/ESP-IDF,
   z internetu) – ustal ją sam zamiast pytać, i tylko poinformuj o wyniku.
5. Dopytuj, gdy odpowiedź jest niejasna, sprzeczna z wcześniejszą albo pomija ryzyko.
   Wskaż sprzeczność wprost.
6. Po każdej rozstrzygniętej decyzji dopisz ją do `docs/DECYZJE.md` w formacie:

   ```markdown
   ## D-NNN: <krótki tytuł>
   - **Decyzja:** …
   - **Dlaczego:** …
   - **Odrzucone opcje:** …
   - **Otwarte kwestie:** … (jeśli są)
   ```

7. Co ~5 decyzji podsumuj krótko, co już ustalone i co zostało w drzewie.
8. Kończysz, gdy wszystkie gałęzie są rozstrzygnięte albo świadomie odłożone
   (zapisane jako otwarte). Wtedy zaproponuj uruchomienie skilla `spisz-koncepcje`.

## Obszary, których nie wolno pominąć w tym projekcie

- **Cel i scenariusze użycia** – co dokładnie diagnozujemy, jak długo trwa nagranie, kto czyta dane.
- **Bezpieczeństwo auta** – tryb listen-only vs. wysyłanie ramek (OBD-II PID), ryzyko zakłócenia magistrali, terminacja 120 Ω.
- **Zasilanie** – 12 V z OBD-II / zapalniczki, skoki napięcia, pobór prądu przy wyłączonym silniku, usypianie, bezpieczne zamykanie plików przy zaniku zasilania.
- **Sprzęt** – wariant ESP32, moduł MCP2515 (kwarc 8/16 MHz, transceiver TJA1050 wymaga 5 V vs. SN65HVD230 3,3 V), poziomy logiczne SPI, pin INT.
- **Magistrale** – prędkości (125/250/500 kbit/s), 11/29-bit ID, ile magistral (HS-CAN, MS-CAN), dostępność na złączu OBD.
- **Przepustowość** – ile ramek/s, bufory MCP2515 (tylko 2 RX), czy zdążymy zapisać na SD.
- **Znaczniki czasu** – źródło czasu (RTC, GPS, NTP), rozdzielczość.
- **Format i nośnik danych** – SD (FAT32, rotacja plików), format (`candump`, CSV, ASC, binarny), kompresja.
- **Wyzwalacze** – nagrywanie ciągłe vs. bufor kołowy z zapisem po zdarzeniu (np. błąd DTC, przycisk).
- **Odczyt danych** – WiFi/Home Assistant/MQTT/webserwer, narzędzia na PC (SavvyCAN, cantools, DBC).
- **Obudowa i montaż** – temperatura w aucie, wibracje, złącze.

## Styl

- Po polsku, zwięźle. Jedno pytanie na wiadomość (wyjątek: krótkie pytania tak/nie mogą iść razem).
- Używaj narzędzia do pytań z opcjami (AskUserQuestion), jeśli jest dostępne.
- Bądź dociekliwy, ale nie przegaduj – rekomenduj i idź dalej.
