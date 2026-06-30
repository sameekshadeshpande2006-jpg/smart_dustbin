```cpp
#include <Servo.h>

// Pin Definitions
const int trigPin = 9;
const int echoPin = 10;
const int moisturePin = 5;
const int servoPin = 3;

Servo wasteServo;

// Threshold for moisture (Adjust based on your sensor calibration)
// Lower values typically indicate "Wet"
const int wetThreshold = 500; 

void setup() {
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  
  wasteServo.attach(servoPin);
  wasteServo.write(90); // Start at center position
  delay(1000);
}

void loop() {
  long duration;
  int distance;

  // 1. Check for an object using the Ultrasonic Sensor
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;

  // If an object is detected within 15cm
  if (distance > 0 && distance < 15) {
    delay(500); // Wait for the object to settle on the flap
    
    // 2. Read the Moisture Sensor
    int moistureValue = analogRead(moisturePin);
    Serial.print("Moisture Value: ");
    Serial.println(moistureValue);

    if (moistureValue < wetThreshold) {
      Serial.println("Wet Waste Detected!");
      wasteServo.write(45); // Rotate to Wet Bin side
    } else {
      Serial.println("Dry Waste Detected!");
      wasteServo.write(135); // Rotate to Dry Bin side
    }

    delay(3000);          // Wait for waste to fall
    wasteServo.write(90); // Return to neutral position
  }
  
  delay(100);
}
```
