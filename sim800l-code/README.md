#include <SoftwareSerial.h>
#include <TinyGPS++.h>

#define SOS_BUTTON 2        // Pin for SOS button
#define CANCEL_BUTTON 3     // Pin for Cancel button

// GSM Module pins
#define GSM_TX 7   // Arduino RX <- GSM TX
#define GSM_RX 8   // Arduino TX -> GSM RX

// GPS Module pins
#define GPS_TX 4   // Arduino RX <- GPS TX
#define GPS_RX 5   // Arduino TX -> GPS RX

SoftwareSerial gsm(GSM_TX, GSM_RX);
SoftwareSerial gpsSerial(GPS_TX, GPS_RX);
TinyGPSPlus gps;

const char phone_number[] = "8787760875";   // Change to your phone number
bool sosTriggered = false;
unsigned long sosTime = 0;
const unsigned long cancelWindow = 10000;  // 10 seconds

void setup() {
  pinMode(SOS_BUTTON, INPUT_PULLUP);
  pinMode(CANCEL_BUTTON, INPUT_PULLUP);

  Serial.begin(9600);
  gsm.begin(9600);
  gpsSerial.begin(9600);

  delay(2000);
  Serial.println("Women Safety Device Initialized");
  sendSMS("Device Started and Ready");
}

void loop() {
  // Read GPS data continuously
  while (gpsSerial.available() > 0) {
    gps.encode(gpsSerial.read());
  }

  // Check SOS button press
  if (digitalRead(SOS_BUTTON) == LOW && !sosTriggered) {
    sosTriggered = true;
    sosTime = millis();
    Serial.println("SOS Button Pressed! Waiting for Cancel...");
    delay(500); // debounce
  }

  // Check Cancel button within 10 sec
  if (digitalRead(CANCEL_BUTTON) == LOW && sosTriggered) {
    if (millis() - sosTime <= cancelWindow) {
      sosTriggered = false;
      Serial.println("SOS Cancelled!");
      delay(500); // debounce
    }
  }

  // If 10 seconds passed and not cancelled → Send SOS
  if (sosTriggered && (millis() - sosTime > cancelWindow)) {
    sendLocationSMS();
    sosTriggered = false;
  }
}

void sendSMS(String message) {
  gsm.println("AT+CMGF=1");  
  delay(1000);
  gsm.print("AT+CMGS=\"");
  gsm.print(phone_number);
  gsm.println("\"");
  delay(1000);
  gsm.print(message);
  delay(500);
  gsm.write(26); // CTRL+Z to send SMS
  delay(5000);
}

void sendLocationSMS() {
  String message = "🚨 Emergency! I need help.\n";

  if (gps.location.isValid()) {
    message += "My Location: https://maps.google.com/?q=";
    message += String(gps.location.lat(), 6);
    message += ",";
    message += String(gps.location.lng(), 6);
  } else {
    message += "Location not found!";
  }

  sendSMS(message);
  Serial.println("SOS Sent with Location");
}
