# Sistema de Semáforo Inteligente - G03

## 1. INFORMAÇÕES DO PROJETO

### 1.1 Identificação
- **Nome do Projeto:** Semáforo Inteligente
- **Equipe:** Eduardo Jesus, Guilherme Schmidt, João Agmont, Leonardo Corbi, Lucas Lopez, Lucas Pomin.
  
### 1.2 Objetivo Geral
Aqui temos a documentação a respeito do semáforo feito pelo nosso grupo, para exercitar conhecimento em programção de Iot, utilizando um esp e alguns sensores junto do ubidots para fazer um semáforo inteligente, que tenha funcionamento comum do dia a dia, e para algumas situações, como durante a noite e caso tenha uma pessoa por perto querendo atravessar.

---

## 2. DESCRIÇÃO DO SISTEMA

### 2.1 Visão Geral
Aqui contamos com um esp32, que faz seu papel de microcontrolador, onde após programar, utiliza dos led em uma situação qualquer para liga-los como um semáforo comum, além disso, ao ter algo próximo do sensor ultrasônico ele altera para que os dois farois fiquem com o led vermelho, como se um pedestre estivesse querendo passar, já com o sensor ldr (ou photoresistor), quando paira algo sobre ele gerando uma sombra, simulando o cenário no horário da noite, ele mantém os dois faróis ligados no amarelo.

### 2.2 Componentes do Sistema
- **Led:** funcionam como a lâmpada do farol. (Nesse caso precisa de dois de cada cor sendo elas vermelhos, verde e amarelo)
- **Esp32:** Microcontrolador, é nele que opera o código e os comandos para sensores e led.
- **Sensor Ultrasônico:** Um sensor que detecta próximidade.
- **Sensor ldr (photoresistor):** Sensor que detecta luminosidade.
- **Protoboard:** Para conexão dos sensores com o esp e com os leds.

---

## 3. LÓGICA DE FUNCIONAMENTO

### 3.1 Estados dos Semáforos
O sistema opera com dois semáforos que trabalham de forma coordenada e mutuamente exclusiva.

#### 3.1.1 Funcionamento Simultâneo
- **Quando Semáforo 1 está VERDE** → Semáforo 2 está VERMELHO
- **Quando Semáforo 2 está VERDE** → Semáforo 1 está VERMELHO

#### 3.1.2 Sequência de Transição
A mudança entre estados segue uma sequência específica para garantir segurança:

**Transição do Semáforo 1 (Verde → Vermelho):**
1. Semáforo 1: VERDE
2. Semáforo 1: AMARELO (aguarda tempo de transição)
3. Semáforo 1: VERMELHO
4. **Somente após** o Semáforo 1 chegar ao VERMELHO → Semáforo 2: VERDE

**Transição do Semáforo 2 (Verde → Vermelho):**
1. Semáforo 2: VERDE
2. Semáforo 2: AMARELO (aguarda tempo de transição)
3. Semáforo 2: VERMELHO
4. **Somente após** o Semáforo 2 chegar ao VERMELHO → Semáforo 1: VERDE

---

## 4. Upload do Código
Utilize o código a seguir para que o sistema funcione da maneira esperada. Para isso use uma plataforma como Arduino IDE.

```c
#if __has_include(<Arduino.h>)
#include <Arduino.h>
#else
#include <cstdint>
using std::uint16_t;
using std::uint32_t;
using std::uint8_t;
#define OUTPUT 1
#define INPUT 0
#define HIGH 1
#define LOW 0
inline void pinMode(uint8_t, uint8_t) {}
inline void digitalWrite(uint8_t, bool) {}
inline uint16_t analogRead(uint8_t) { return 0; }
inline void delay(unsigned long) {}
inline void delayMicroseconds(unsigned int) {}
inline uint32_t millis() {
  static uint32_t fakeMillis = 0;
  fakeMillis += 16;
  return fakeMillis;
}
inline uint32_t pulseIn(uint8_t, uint8_t, uint32_t) { return 0; }
struct DummySerial {
  void begin(unsigned long) {}
  bool operator!() const { return false; }
  void print(const char*) {}
  void print(float) {}
  void print(unsigned long) {}
  void print(int) {}
  void println(const char*) {}
  void println(float) {}
  void println(unsigned long) {}
  void println(int) {}
} Serial;
#endif

// Pin map
const int SEM1_RED_PIN = 17;
const int SEM1_YELLOW_PIN = 16;
const int SEM1_GREEN_PIN = 4;
const int SEM2_RED_PIN = 26;
const int SEM2_YELLOW_PIN = 25;
const int SEM2_GREEN_PIN = 33;
const int LDR_PIN = 34;
const int ULTRASONIC_TRIG_PIN = 32;
const int ULTRASONIC_ECHO_PIN = 35;

// Sensor and timing settings
const int LDR_SAMPLES = 8;
const int LDR_DAY_NIGHT_THRESHOLD = 400;
const int LDR_HYSTERESIS = 200;
const float SPEED_OF_SOUND_CM_PER_US = 0.0343f;
const int MAX_DISTANCE_CM = 400;
const int VEHICLE_DETECTION_CM = 40;
const unsigned long ULTRASONIC_TIMEOUT_US = 6000;
const float EMERGENCY_TRIGGER_CM = 6.0f;
const float EMERGENCY_RELEASE_CM = 8.0f;
const unsigned long EMERGENCY_MIN_DURATION_MS = 6000;
const unsigned long GREEN_BASE_DURATION = 6000;
const unsigned long GREEN_MAX_EXTENSION = 4000;
const unsigned long YELLOW_DURATION = 2000;
const unsigned long RED_OVERLAP_DURATION = 1000;
const unsigned long NIGHT_BLINK_INTERVAL = 600;

// Phase ids
const int PHASE_SEM1_GREEN = 0;
const int PHASE_SEM1_YELLOW = 1;
const int PHASE_ALL_RED_1 = 2;
const int PHASE_SEM2_GREEN = 3;
const int PHASE_SEM2_YELLOW = 4;
const int PHASE_ALL_RED_2 = 5;

// Global state
int currentPhase = PHASE_SEM1_GREEN;
unsigned long phaseStartMillis = 0;
unsigned long nightBlinkMillis = 0;
bool blinkOn = false;
bool isNightMode = false;
bool nightDecisionMemory = false;
float lastDistanceCm = MAX_DISTANCE_CM;
bool emergencyStopActive = false;
unsigned long emergencyReleaseMillis = 0;
unsigned long lastLogMillis = 0;

void setSemaphore1(bool red, bool yellow, bool green) {
  digitalWrite(SEM1_RED_PIN, red);
  digitalWrite(SEM1_YELLOW_PIN, yellow);
  digitalWrite(SEM1_GREEN_PIN, green);
}

void setSemaphore2(bool red, bool yellow, bool green) {
  digitalWrite(SEM2_RED_PIN, red);
  digitalWrite(SEM2_YELLOW_PIN, yellow);
  digitalWrite(SEM2_GREEN_PIN, green);
}

int readLdr() {
  long total = 0;
  for (int i = 0; i < LDR_SAMPLES; i++) {
    total += analogRead(LDR_PIN);
    delay(2);
  }
  return static_cast<int>(total / LDR_SAMPLES);
}

bool detectNight(int value) {
  if (!nightDecisionMemory) {
    if (value < (LDR_DAY_NIGHT_THRESHOLD - LDR_HYSTERESIS)) {
      nightDecisionMemory = true;
    }
  } else {
    if (value > (LDR_DAY_NIGHT_THRESHOLD + LDR_HYSTERESIS)) {
      nightDecisionMemory = false;
    }
  }
  return nightDecisionMemory;
}

float readDistanceCm() {
  digitalWrite(ULTRASONIC_TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(ULTRASONIC_TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(ULTRASONIC_TRIG_PIN, LOW);

  unsigned long pulseDuration = pulseIn(ULTRASONIC_ECHO_PIN, HIGH, ULTRASONIC_TIMEOUT_US);
  if (pulseDuration == 0) {
    return MAX_DISTANCE_CM;
  }

  float distance = (pulseDuration * SPEED_OF_SOUND_CM_PER_US) / 2.0f;
  if (distance > MAX_DISTANCE_CM) {
    distance = MAX_DISTANCE_CM;
  }
  return distance;
}

bool vehicleNear() { return lastDistanceCm <= VEHICLE_DETECTION_CM; }

void moveToPhase(int nextPhase) {
  currentPhase = nextPhase;
  phaseStartMillis = millis();
}

void runDayMode(unsigned long now) {
  unsigned long elapsed = now - phaseStartMillis;

  if (currentPhase == PHASE_SEM1_GREEN) {
    bool extendGreen = vehicleNear();
    unsigned long allowedTime = GREEN_BASE_DURATION + (extendGreen ? GREEN_MAX_EXTENSION : 0);
    setSemaphore1(false, false, true);
    setSemaphore2(true, false, false);
    if (elapsed >= allowedTime) {
      moveToPhase(PHASE_SEM1_YELLOW);
    }
  } else if (currentPhase == PHASE_SEM1_YELLOW) {
    setSemaphore1(false, true, false);
    setSemaphore2(true, false, false);
    if (elapsed >= YELLOW_DURATION) {
      moveToPhase(PHASE_ALL_RED_1);
    }
  } else if (currentPhase == PHASE_ALL_RED_1) {
    setSemaphore1(true, false, false);
    setSemaphore2(true, false, false);
    if (elapsed >= RED_OVERLAP_DURATION) {
      moveToPhase(PHASE_SEM2_GREEN);
    }
  } else if (currentPhase == PHASE_SEM2_GREEN) {
    bool extendGreen = vehicleNear();
    unsigned long allowedTime = GREEN_BASE_DURATION + (extendGreen ? GREEN_MAX_EXTENSION : 0);
    setSemaphore1(true, false, false);
    setSemaphore2(false, false, true);
    if (elapsed >= allowedTime) {
      moveToPhase(PHASE_SEM2_YELLOW);
    }
  } else if (currentPhase == PHASE_SEM2_YELLOW) {
    setSemaphore1(true, false, false);
    setSemaphore2(false, true, false);
    if (elapsed >= YELLOW_DURATION) {
      moveToPhase(PHASE_ALL_RED_2);
    }
  } else {
    setSemaphore1(true, false, false);
    setSemaphore2(true, false, false);
    if (elapsed >= RED_OVERLAP_DURATION) {
      moveToPhase(PHASE_SEM1_GREEN);
    }
  }
}

void runNightMode(unsigned long now) {
  if (now - nightBlinkMillis >= NIGHT_BLINK_INTERVAL) {
    nightBlinkMillis = now;
    blinkOn = !blinkOn;
    setSemaphore1(false, blinkOn, false);
    setSemaphore2(false, blinkOn, false);
  }
}

void handleEmergency(unsigned long now) {
  bool wasActive = emergencyStopActive;

  if (lastDistanceCm < EMERGENCY_TRIGGER_CM) {
    emergencyStopActive = true;
    emergencyReleaseMillis = now + EMERGENCY_MIN_DURATION_MS;
  }

  bool canClear =
      emergencyStopActive && now >= emergencyReleaseMillis && lastDistanceCm >= EMERGENCY_RELEASE_CM;

  if (canClear) {
    emergencyStopActive = false;
    Serial.print("Emergency stop cleared (Distance=");
    Serial.print(lastDistanceCm);
    Serial.println(" cm)");
    phaseStartMillis = now;
    if (isNightMode) {
      nightBlinkMillis = now;
      blinkOn = false;
    }
  } else if (emergencyStopActive && !wasActive) {
    Serial.print("Emergency stop activated (Distance=");
    Serial.print(lastDistanceCm);
    Serial.println(" cm)");
  }
}

void logStatus(unsigned long now, int ldrValue) {
  if (now - lastLogMillis < 2000) {
    return;
  }
  lastLogMillis = now;

  Serial.print("LDR=");
  Serial.print(ldrValue);
  Serial.print(" | Night=");
  Serial.print(isNightMode ? "yes" : "no");
  Serial.print(" | Emergency=");
  Serial.print(emergencyStopActive ? "yes" : "no");
  Serial.print(" | Distance=");
  Serial.print(lastDistanceCm);
  Serial.println(" cm");
}

void setup() {
  pinMode(SEM1_RED_PIN, OUTPUT);
  pinMode(SEM1_YELLOW_PIN, OUTPUT);
  pinMode(SEM1_GREEN_PIN, OUTPUT);
  pinMode(SEM2_RED_PIN, OUTPUT);
  pinMode(SEM2_YELLOW_PIN, OUTPUT);
  pinMode(SEM2_GREEN_PIN, OUTPUT);
  pinMode(LDR_PIN, INPUT);
  pinMode(ULTRASONIC_TRIG_PIN, OUTPUT);
  pinMode(ULTRASONIC_ECHO_PIN, INPUT);

  setSemaphore1(true, false, false);
  setSemaphore2(true, false, false);

  phaseStartMillis = millis();
  nightBlinkMillis = millis();

  Serial.begin(115200);
  while (!Serial) {
    delay(10);
  }
  Serial.println("Smart semaphore ready.");
}

void loop() {
  unsigned long now = millis();

  lastDistanceCm = readDistanceCm();
  handleEmergency(now);

  int ldrValue = readLdr();
  bool nightDecision = detectNight(ldrValue);
  if (nightDecision != isNightMode) {
    isNightMode = nightDecision;
    Serial.print("Mode change -> ");
    Serial.print(isNightMode ? "night" : "day");
    Serial.print(" (LDR=");
    Serial.print(ldrValue);
    Serial.println(")");

    if (isNightMode) {
      nightBlinkMillis = now;
      blinkOn = false;
      setSemaphore1(false, blinkOn, false);
      setSemaphore2(false, blinkOn, false);
    } else {
      moveToPhase(PHASE_SEM1_GREEN);
    }
  }

  if (emergencyStopActive) {
    setSemaphore1(true, false, false);
    setSemaphore2(true, false, false);
  } else if (isNightMode) {
    runNightMode(now);
  } else {
    runDayMode(now);
  }

  logStatus(now, ldrValue);
}
```

---

## 5. ANEXOS
Aqui portamos um vídeo demonstrativo do funcionamento desse semáforo.

### 5.1 Vídeos Demonstrativos
[Links para vídeos do sistema em funcionamento](https://drive.google.com/file/d/1Oe8Aym1XwTAC3p8azcQTVJjyyRspjOiv/view?usp=sharing)]
