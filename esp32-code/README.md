#include <TinyGPS++.h>
#include "BluetoothSerial.h"

// ----- GPS Setup -----
TinyGPSPlus gps;
HardwareSerial GPSSerial(2); // Use UART2: RX=16, TX=17
const uint32_t GPS_BAUD = 9600;

// ----- Bluetooth Setup -----
BluetoothSerial SerialBT; // Built-in ESP32 Bluetooth

// ----- SOS Button -----
const int BUTTON_PIN = 4; // Active LOW (connect to GND when pressed)
unsigned long lastDebounce = 0;
const unsigned long DEBOUNCE_MS = 50;
bool lastButtonState = HIGH;

void setup() {
  Serial.begin(115200);
  
  // Initialize GPS serial
  GPSSerial.begin(GPS_BAUD, SERIAL_8N1, 16, 17);
  Serial.println("GPS Serial started.");
  
  // Initialize Bluetooth
  SerialBT.begin("WearableSOS"); // Device name for phone pairing
  Serial.println("Bluetooth 'WearableSOS' started.");
  
  // Initialize SOS button
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  lastButtonState = digitalRead(BUTTON_PIN);

  Serial.println("Setup complete. Waiting for GPS fix...");
}

void loop() {
  // Read GPS data continuously
  while (GPSSerial.available()) {
    gps.encode(GPSSerial.read());
  }

  // Check SOS button (with debounce)
  int reading = digitalRead(BUTTON_PIN);
  if (reading != lastButtonState) lastDebounce = millis();

  if ((millis() - lastDebounce) > DEBOUNCE_MS) {
    static bool pressedPreviously = false;
    if (reading == LOW && !pressedPreviously) { // Button pressed
      pressedPreviously = true;
      sendSOS();
    } else if (reading == HIGH) { // Button released
      pressedPreviously = false;
    }
  }
  lastButtonState = reading;

  // Periodic location update every 10 seconds
  static unsigned long lastUpdate = 0;
  if (millis() - lastUpdate > 10000) {
    lastUpdate = millis();
    sendLocationUpdate();
  }
}

// ----- Send GPS location periodically -----
void sendLocationUpdate() {
  if (gps.location.isValid()) {
    String msg = "UPDATE," + String(gps.location.lat(), 6) + "," + String(gps.location.lng(), 6);
    SerialBT.println(msg); // Send via Bluetooth
    Serial.println("Sent: " + msg);
  } else {
    SerialBT.println("NO_FIX"); // GPS not ready yet
    Serial.println("No GPS fix yet.");
  }
}

// ----- Send SOS immediately -----
void sendSOS() {
  if (gps.location.isValid()) {
    String msg = "SOS," + String(gps.location.lat(), 6) + "," + String(gps.location.lng(), 6);
    SerialBT.println(msg); // Send via Bluetooth
    Serial.println("SOS sent: " + msg);
  } else {
    SerialBT.println("SOS,NO_FIX");
    Serial.println("SOS sent but no GPS fix.");
  }
}
