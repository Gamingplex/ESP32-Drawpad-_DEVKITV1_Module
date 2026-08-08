# ESP32 OLED Draw Pad

Draw on a web page from your phone's browser and watch it mirror live on a 128×64 OLED screen — no app required, no internet needed.

The ESP32 hosts the entire web page itself and streams your strokes to the display in real time over WebSocket.

## How it works

```
Phone Browser  --WebSocket-->  ESP32 DevKit V1  --I2C-->  SSD1306 OLED (128x64)
```

1. Open the ESP32's IP address in your phone's browser.
2. Draw on the on-screen canvas with your finger.
3. Touch points and lines are sent to the ESP32 over WebSocket as lightweight text commands.
4. The ESP32 renders them onto the OLED using the Adafruit GFX library.

## Features

- **Live mirroring** — draws appear on the OLED almost instantly as you move your finger.
- **Adjustable pen size** — slider control from 1–8px.
- **Clear button** — wipes the OLED panel remotely.
- **No app needed** — the whole UI is a webpage served directly from the ESP32.
- **WiFi fallback** — if your WiFi network isn't reachable within 10 seconds, the ESP32 automatically starts its own hotspot so you can connect directly.

## Hardware

| Component | Notes |
|---|---|
| ESP32 DevKit V1 | Any standard ESP32 dev board works |
| SSD1306 OLED (128×64, I2C) | Default I2C address `0x3C` |

### Wiring

| OLED Pin | ESP32 Pin |
|---|---|
| VCC | 3V3 |
| GND | GND |
| SDA | GPIO 21 |
| SCL | GPIO 22 |

## Required Arduino Libraries

Install these via the Arduino Library Manager:

- [Adafruit GFX Library](https://github.com/adafruit/Adafruit-GFX-Library)
- [Adafruit SSD1306](https://github.com/adafruit/Adafruit_SSD1306)
- [ESPAsyncWebServer](https://github.com/me-no-dev/ESPAsyncWebServer)
- [AsyncTCP](https://github.com/me-no-dev/AsyncTCP)

## Setup

1. Clone this repo and open `esp32_oled_drawpad.ino` in the Arduino IDE.
2. Install the libraries listed above.
3. Edit your WiFi credentials near the top of the file:

   ```cpp
   const char* WIFI_SSID     = "YOUR_WIFI_NAME";
   const char* WIFI_PASSWORD = "YOUR_WIFI_PASSWORD";
   ```

4. Select your ESP32 board and port, then upload.
5. Open the Serial Monitor at `115200` baud to see the assigned IP address (or hotspot details if WiFi failed).
6. Open that IP address in your phone's browser and start drawing.

### If WiFi isn't available

The ESP32 automatically creates its own hotspot after a 10-second connection timeout:

- **SSID:** `OLED-DrawPad`
- **Password:** `draw12345`
- **URL:** `http://192.168.4.1`

## Communication Protocol

The web page sends simple comma-separated text commands over WebSocket, with multiple commands per message separated by `;`:

| Command | Format | Description |
|---|---|---|
| Clear | `CLR` | Clears the OLED display |
| Point | `P,x,y,size` | Draws a dot at `(x, y)` |
| Line | `L,x0,y0,x1,y1,size` | Draws a line from `(x0, y0)` to `(x1, y1)` |

Example batched message:
```
P,10,10,2;L,10,10,20,20,2;L,20,20,30,30,2
```

## License

Feel free to use and modify this project for personal or educational purposes.
