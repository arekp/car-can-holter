# Car CAN Holter

Rejestrator („holter”) magistrali CAN dla samochodów na ESP32 + MCP2515.
Urządzenie podłączane do gniazda OBD-II nagrywa ruch na magistrali CAN do późniejszej analizy.

Status: **koncepcja** – zobacz [docs/KONCEPCJA.md](docs/KONCEPCJA.md), [docs/DECYZJE.md](docs/DECYZJE.md) i rozpoznanie [docs/RESEARCH.md](docs/RESEARCH.md).

## Praca z Claude Code

W repo są skille w `.claude/skills/`:

| Skill | Do czego |
|---|---|
| `grill-me` | Przesłuchanie koncepcji pytanie po pytaniu z rekomendacjami; decyzje trafiają do `docs/DECYZJE.md` |
| `spisz-koncepcje` | Złożenie decyzji w dokument `docs/KONCEPCJA.md` |
| `przeglad-sprzetu` | Lista kontrolna schematu: zasilanie 12 V, poziomy 3,3/5 V, kwarc, terminacja, listen-only |

Start: napisz `/grill-me` albo „przemaglujmy koncept”.
