# Clawd Mochi Desk Pet 🦀

Claude Code's mochi mascot, now alive on your desktop.

Un compañero de escritorio físico inspirado en **Clawd**, la mascota pixel-cangrejo de Claude Code: un ESP32-C3 con una pantalla táctil a color de 2.8" que muestra expresiones animadas y se controla desde el móvil por WiFi, sin app ni internet.

Adaptación del proyecto original [yousifamanuel/clawd-mochi](https://github.com/yousifamanuel/clawd-mochi) a una pantalla más grande (ILI9341 2.8" 320×240 en lugar de ST7789 1.54" 240×240), con panel táctil y funciones extra planeadas.

> ⚠️ Proyecto independiente de fans. No está afiliado, patrocinado ni respaldado por Anthropic. "Claude" y "Clawd" son marcas de Anthropic.

---

## ✨ Qué hace ahora mismo

- **Ojos normales**: ojos cuadrados pixel-art que se mueven y parpadean
- **Squish eyes**: los ojos `> <` de cangrejo contento
- **Claude Code**: pantalla con terminal en la que escribes desde el móvil
- **Canvas**: dibuja en la pantalla desde el móvil en tiempo real
- **Controles**: velocidad de animación, color de fondo y encender/apagar la pantalla

---

## 🔧 Hardware

| Componente | Modelo |
|---|---|
| Microcontrolador | ESP32-C3 Super Mini |
| Pantalla | 2.8" TFT SPI 240×320, chip ILI9341, táctil resistivo XPT2046 |
| Conexión | Cables Dupont hembra-hembra 10 cm (los pines del ESP32 van soldados) |
| Alimentación | USB-C |

### Cableado

Los nombres de la izquierda son los que vienen impresos detrás de la pantalla. Los de la derecha, los números impresos junto a cada pin del ESP32-C3.

| Pantalla | ESP32-C3 |
|---|---|
| VCC | 3.3 |
| GND | G |
| CS | 4 |
| RESET | 2 |
| DC | 1 |
| SDI(MOSI) | 10 |
| SCK | 8 |
| LED | 3 |
| T_CS | 5 |
| SDO(MISO), T_CLK, T_DIN, T_DO, T_IRQ | sin conectar (de momento) |

> El táctil necesitará conectar también **T_CLK**, **T_DIN** y **T_DO**. Está pendiente (ver roadmap).

> El LED azul del ESP32-C3 parpadea mientras funciona: está conectado al GPIO 8, el mismo que usa la señal SCK. Es normal.

---

## 💻 Software

**Requisitos:**
- [Arduino IDE 2.x](https://www.arduino.cc/en/software)
- Placa **esp32 by Espressif Systems** (Boards Manager), con esta URL en *Preferences → Additional boards manager URLs*:
  ```
  https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
  ```
- Librerías (Library Manager): `Adafruit GFX Library`, `Adafruit ILI9341`

**Subir el código:**
1. Abre `clawd_mochi/clawd_mochi.ino` en Arduino IDE
2. *Tools → Board* → **ESP32C3 Dev Module**
3. *Tools → USB CDC On Boot* → **Enabled**
4. *Tools → Port* → el puerto del ESP32 (en Mac, `/dev/cu.usbmodem...`)
5. Pulsa **Upload**. Si falla con *"Failed to connect"*, mantén pulsado **BOOT** en la placa al empezar la subida

### Cambios respecto al proyecto original

- Librería `Adafruit_ST7789` → `Adafruit_ILI9341`, y `tft.init(240, 240)` → `tft.begin()`
- Resolución 240×240 → **320×240** (pantalla en horizontal)
- Logo desplazado 40 px para que quede centrado en el ancho extra
- Terminal ampliado a 21 columnas × 10 filas
- Lienzo de dibujo de la web a 320×240

Los pines de la pantalla (CS, DC, RST, LED, SCK y MOSI) son los mismos que en el original.

---

## 📱 Cómo usarlo

1. Conecta el ESP32-C3 por USB-C a cualquier cargador
2. Tras la animación de arranque, la pantalla muestra los datos del WiFi
3. Desde el móvil, conéctate a la red **`ClaWD-Mochi`** (contraseña **`clawd1234`**). Si avisa de que no hay internet, sigue conectada: es una red local del propio ESP32
4. Abre **`http://192.168.4.1`** en el navegador y pulsa cualquier botón

---

## 🗺️ Roadmap

**v1**
- [x] Entorno: Arduino IDE, soporte ESP32 y librerías
- [x] Cableado de la pantalla al ESP32-C3
- [x] Prueba de pantalla (`graphicstest`)
- [x] Código de Clawd Mochi adaptado a ILI9341 320×240
- [ ] Táctil (XPT2046): cables T_CLK / T_DIN / T_DO y código
- [ ] Reloj por WiFi (NTP)
- [ ] Widget del tiempo (OpenWeatherMap)
- [ ] Carcasa 3D en PLA naranja
- [ ] Montaje final

**v2 (ideas)**
- Modo Pomodoro, contador de commits de GitHub, frases aleatorias, batería del móvil
- Canción de Spotify en reproducción
- LED RGB WS2812 de estado
- Batería LiPo con módulo de carga

El seguimiento de tareas está en el [tablero de GitHub Projects](https://github.com/users/valeriazv99/projects).

---

## 📄 Licencia y créditos

Basado en [clawd-mochi](https://github.com/yousifamanuel/clawd-mochi) de Yousuf Amanuel, publicado bajo licencia MIT. El texto de esa licencia está en [`LICENSE-clawd-mochi-original`](./LICENSE-clawd-mochi-original).
