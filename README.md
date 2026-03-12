# ESP-32-x-BTS7960-IBT-2-
ESP32 microcontroller to control a DC motor using a BTS7960 (IBT-2) motor driver with PWM speed control.
// ======================= PIN DEFINITIONS =======================
const int R_EN = 23;
const int L_EN = 22;
const int RPWM_PIN = 19;
const int LPWM_PIN = 18;

// ======================= PWM SETTINGS ==========================
const int pwmFreq = 5000;   // 5 kHz
const int pwmRes  = 8;      // 8-bit resolution (0–255)

// ======================= SETUP ================================
void setup() {
  Serial.begin(115200);

  pinMode(R_EN, OUTPUT);
  pinMode(L_EN, OUTPUT);

  digitalWrite(R_EN, HIGH);
  digitalWrite(L_EN, HIGH);

  // NEW ESP32 core 3.x LEDC API
  ledcAttach(RPWM_PIN, pwmFreq, pwmRes);
  ledcAttach(LPWM_PIN, pwmFreq, pwmRes);

  stopMotor();
}

// ======================= LOOP ================================
void loop() {

  // Forward ramp
  for (int speed = 0; speed <= 255; speed++) {
    moveForward(speed);
    delay(10);
  }
  delay(2000);

  stopMotor();
  delay(1000);

  // Backward ramp
  for (int speed = 0; speed <= 255; speed++) {
    moveBackward(speed);
    delay(10);
  }
  delay(2000);

  stopMotor();
  delay(2000);
}

// ======================= MOTOR CONTROL =======================

void moveForward(int speed) {
  speed = constrain(speed, 0, 255);

  ledcWrite(LPWM_PIN, 0);
  ledcWrite(RPWM_PIN, speed);
}

void moveBackward(int speed) {
  speed = constrain(speed, 0, 255);

  ledcWrite(RPWM_PIN, 0);
  ledcWrite(LPWM_PIN, speed);
}

void stopMotor() {
  ledcWrite(RPWM_PIN, 0);
  ledcWrite(LPWM_PIN, 0);
}
