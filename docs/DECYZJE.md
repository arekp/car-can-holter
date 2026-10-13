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
