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
- **Clock**: hora y fecha sincronizadas por internet, con el cambio de hora de España automático
- **Táctil**: un toque cambia de vista (ojos → squish → reloj). Con el lienzo abierto desde el móvil, puedes dibujar con el dedo
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
| T_CLK | 6 |
| T_DIN | 7 |
| T_DO | 20 |
| SDO(MISO), T_IRQ | sin conectar |

> El táctil va en pines propios, sin compartir SCK y MOSI con la pantalla, porque a cada pin del ESP32 solo le cabe un conector Dupont. El código lo lee por software: el chip táctil es lento y no necesita más.

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

**Configurar las contraseñas (`secrets.h`):**

Las contraseñas van en un archivo aparte que **no se sube a GitHub**:

1. En la carpeta `clawd_mochi/`, copia `secrets.h.example` con el nombre `secrets.h`. En Arduino IDE también puedes hacerlo desde el menú `⋯` → *New Tab* → `secrets.h`, pegando el contenido del ejemplo
2. Rellena:
   - `WIFI_SSID` / `WIFI_PASS`: tu WiFi de casa. Tiene que ser de **2.4 GHz**, porque el ESP32-C3 no admite 5 GHz
   - `AP_PASSWORD`: la clave de la red de emergencia (mínimo 8 caracteres, distinta de la de casa)
   - `WEB_USER` / `WEB_PASS`: el usuario y la contraseña para entrar en la web de control

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
- Squish eyes más anchos y con trazo más grueso
- Nueva vista **Clock** (hora por internet)
- WiFi: se conecta a la red de casa y solo crea su propia red si falla. Potencia de emisión reducida por la antena de la Super Mini
- Web protegida con usuario y contraseña
- Táctil leído por software en pines propios

Los pines de la pantalla (CS, DC, RST, LED, SCK y MOSI) son los mismos que en el original.

---

## 📱 Cómo usarlo

1. Conecta el ESP32-C3 por USB-C a cualquier cargador
2. Tras la animación de arranque, se conecta a tu WiFi de casa y la pantalla muestra su dirección, por ejemplo `http://192.168.1.45`
3. Desde cualquier dispositivo en tu WiFi, abre esa dirección e inicia sesión con `WEB_USER` / `WEB_PASS`
4. También puedes tocar la pantalla para cambiar de vista. La IP aparece además abajo en la vista **Clock**

**Si no consigue conectarse a tu WiFi de casa**, a los 20 segundos crea la red de emergencia **`ClaWD-Mochi`** (clave `AP_PASSWORD`). Conéctate a ella y abre `http://192.168.4.1`. Cada 5 minutos vuelve a intentar conectarse a casa, y en el Serial Monitor (115200) explica por qué falla y qué redes ve con qué señal.

### 🔒 Seguridad

- Las contraseñas están solo en `secrets.h`, que está excluido del repo con `.gitignore`
- La red propia solo existe como emergencia, con clave propia. El ESP32 no enruta tráfico entre esa red y la de casa
- La web pide usuario y contraseña. Sin HTTPS viajan casi en claro por la red local: frena a curiosos, no a alguien que ya esté dentro de tu red
- No hay actualización de firmware por red: solo se puede cambiar por USB
- Con acceso físico se puede leer la memoria y la clave del WiFi. Antes de regalarlo o tirarlo, borra la memoria desde Arduino IDE (*Tools → Erase All Flash Before Sketch Upload* → *Enabled*, y sube cualquier sketch)
- Recomendado: conectarlo a una **red de invitados** del router, aislada de tus otros dispositivos

---

## 🗺️ Roadmap

**v1**
- [x] Entorno: Arduino IDE, soporte ESP32 y librerías
- [x] Cableado de la pantalla al ESP32-C3
- [x] Prueba de pantalla (`graphicstest`)
- [x] Código de Clawd Mochi adaptado a ILI9341 320×240
- [ ] Táctil (XPT2046): en pruebas en la rama `issue-6-tactil`
- [x] Reloj por WiFi (NTP)
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
