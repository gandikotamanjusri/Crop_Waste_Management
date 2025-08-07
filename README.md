# Crop_Waste_Management
// Pin definitions
const int soilMoisturePin = A0;  // Soil Moisture Sensor
const int phSensorPin = A1;      // pH Sensor
const int relayPin = 7;          // Relay to control motor

// Thresholds (adjust as needed)
const int soilDryThreshold = 400;      // Higher value = dry soil
const float minPH = 6.0;
const float maxPH = 7.5;

void setup() {
  Serial.begin(9600);
  pinMode(relayPin, OUTPUT);
  digitalWrite(relayPin, LOW); // Motor OFF initially
  Serial.println("Automatic Mulching System Started");
}

void loop() {
  // Read soil moisture
  int soilValue = analogRead(soilMoisturePin);
  Serial.print("Soil Moisture Value: ");
  Serial.println(soilValue);

  // Read and calculate pH
  int phRaw = analogRead(phSensorPin);
  float voltage = phRaw * (5.0 / 1023.0);  // Convert to voltage
  float pHValue = 7 + ((2.5 - voltage) / 0.18);  // Approximate formula

  Serial.print("pH Sensor Raw Value: ");
  Serial.print(phRaw);
  Serial.print("  Voltage: ");
  Serial.print(voltage, 2);
  Serial.print(" V  -->  pH Value: ");
  Serial.println(pHValue, 2);

  // Decision: Check if mulching needed
  if (soilValue > soilDryThreshold && pHValue >= minPH && pHValue <= maxPH) {
    Serial.println("Status: Soil is DRY and pH is OPTIMAL.");
    Serial.println("Action: Relay ON → Motor ON (Applying mulch)");
    digitalWrite(relayPin, HIGH); // Motor ON
    delay(5000);                  // Run motor for 5 seconds
    digitalWrite(relayPin, LOW);  // Motor OFF
    Serial.println("Action: Relay OFF → Motor OFF (Mulching complete)");
  } else {
    Serial.println("Status: Conditions NOT suitable for mulching.");
    Serial.println("Action: Relay OFF → Motor OFF");
    digitalWrite(relayPin, LOW); // Ensure motor is OFF
  }

  Serial.println("--------------------------------------------------");
  delay(3000); // Wait before next cycle
}
