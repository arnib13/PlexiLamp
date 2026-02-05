# PlexiLamp
Gögn fyrir plexi lampa með laserskornu plexi

Plexi botn: Raufin er 120mm á breidd, 4mm á breidd og amk 4mm niður. 

## XIAO ESP32C3 vs XIAO ESP32C6:

Pinni `D6` er rétt fótspor á báðum kubbum en vísar í mismunandi **GPIO** pinna í örgjörvanum:

### LED

- **XIAO ESP32C3:** `D6` → **GPIO21**
- **XIAO ESP32C6:** `D6` → **GPIO16**

### Takki

- **XIAO ESP32C3:** `D7` → **GPIO20**
- **XIAO ESP32C6:** `D7` → **GPIO17**



### Arduino IDE

Notið D6, að því gefnu að réttur kubbur sé valinn

```cpp
#include <Adafruit_NeoPixel.h>

// --------- Pin definitions ---------
#define LED_PIN    D6     // LED strip data pin
#define BUTTON_PIN D7     // Button to GND

// --------- LED strip settings ---------
int numLeds = 3;          // Number of LEDs in the strip

Adafruit_NeoPixel strip(
  numLeds,
  LED_PIN,
  NEO_GRB + NEO_KHZ800
);

// --------- Button state ---------
bool buttonPressed = false;

void setup() {
  // Button setup
  pinMode(BUTTON_PIN, INPUT_PULLUP);

  // Serial
  Serial.begin(115200);

  // LED strip setup
  strip.begin();
  strip.show(); // Make sure all LEDs are off

  // ---- Startup sequence ----
  setAll(255, 0, 0); // Red
  delay(500);

  setAll(0, 255, 0); // Green
  delay(500);

  setAll(0, 0, 255); // Blue
  delay(500);

  setAll(0, 0, 0); // All off
}

void loop() {
  // Button is pressed when it reads LOW
  if (digitalRead(BUTTON_PIN) == LOW) {
    if (!buttonPressed) {
      buttonPressed = true;
      Serial.println("Ytt a takka!");
      // Turn all LEDs red
      setAll(255, 0, 0);
    }
  } else {
    if (buttonPressed) {
      buttonPressed = false;

      // Turn all LEDs off
      setAll(0, 0, 0);
    }
  }
}

// --------- Helper function ---------
void setAll(int r, int g, int b) {
  for (int i = 0; i < numLeds; i++) {
    strip.setPixelColor(i, strip.Color(r, g, b));
  }
  strip.show();
}
```

### MicroPython
```python
# ESP32C6:
from machine import Pin
import neopixel
import time

# --------- Pin definitions ---------
LED_PIN = 21      # D6 on XIAO ESP32C3
BUTTON_PIN = 20   # D7 on XIAO ESP32C3

# --------- LED strip settings ---------
num_leds = 3

strip = neopixel.NeoPixel(
    Pin(LED_PIN, Pin.OUT),
    num_leds
)

button = Pin(BUTTON_PIN, Pin.IN, Pin.PULL_UP)

# --------- Button state ---------
button_pressed = False

# --------- Helper function ---------
def set_all(r, g, b):
    for i in range(num_leds):
        strip[i] = (r, g, b)
    strip.write()

# --------- Startup sequence ---------
set_all(255, 0, 0)   # Red
time.sleep(0.5)

set_all(0, 255, 0)   # Green
time.sleep(0.5)

set_all(0, 0, 255)   # Blue
time.sleep(0.5)

set_all(0, 0, 0)     # All off

# --------- Main loop ---------
while True:
    # Button pressed = LOW
    if button.value() == 0:
        if not button_pressed:
            button_pressed = True
            print("Ytt a takka!")
            set_all(255, 0, 0)  # All red
    else:
        if button_pressed:
            button_pressed = False
            set_all(0, 0, 0)    # All off

    time.sleep(0.01)

```

## WLED 

Upplýsingar: [https://kno.wled.ge/](https://kno.wled.ge/)

Uppsetning: [https://install.wled.me/](https://install.wled.me/)

### WLED Leiðbeiningar

#### Svona gerum við! 

- Nota Chrome eða Edge vafra
- Halda inni **B** takkanum á kubbnum
- Stinga í samband við tölvuna
- Fylgja skrefum á [https://install.wled.me/](https://install.wled.me/)
- Þegar því er lokið, nota fartölvu eða farsíma til að tengjast **wled-ap** netinu
    - Lykilorð: **wled1234**

Þar er svo hægt að fikta heilmikið og stilla allskonar.

Meðal annars að velja réttan GPIO pinna (21 fyrir C3).