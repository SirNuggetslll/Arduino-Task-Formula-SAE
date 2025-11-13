# Arduino-Task-Formula-SAE
For the embedded controls subsystem

#include <Wire.h>                 // For I2C communication to LCD
#include <LiquidCrystal_I2C.h>    // For 16x2 I2C LCD
#include <Adafruit_BME280.h>      // Adafruit BME280 library
#include <SPI.h>                  // Required for SPI mode BME280

// -------------------------------
// SPI PIN DEFINITIONS (Uno)
// -------------------------------
// SCK  = 13
// MISO = 12
// MOSI = 11
// CS   = Any digital pin (we choose 10)

#define BME_CS 10   // Chip select for BME280 on SPI

// Create BME280 object in SPI mode
Adafruit_BME280 bme(BME_CS, SPI);

// Create LCD object: 0x27 is common, yours may be 0x3F
LiquidCrystal_I2C lcd(0x27, 16, 2);



void setup() {
  // put your setup code here, to run once:

 // Initialize serial for debugging
  Serial.begin(9600);

  // Initialize LCD
  lcd.init();       // Start LCD
  lcd.backlight();  // Turn on LCD backlight

  // -------------------------------
  // Initialize BME280 in SPI mode
  // -------------------------------
  bool status = bme.begin();
  
  if (!status) {
    lcd.clear();
    lcd.print("BME280 ERROR");
    Serial.println("Could not find BME280! Check wiring.");
    while (1); // Halt so user notices the issue
  }

  // Display boot message
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("BME280 (SPI)");
  lcd.setCursor(0, 1);
  lcd.print("Initializing...");
  delay(1500);

}

void loop() {
  // put your main code here, to run repeatedly:

// Read temperature and pressure from sensor
  float temperature = bme.readTemperature();  // Celsius
  float pressure = bme.readPressure() / 100.0; // Convert Pa → hPa

  // Clear and update LCD
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Temp: ");
  lcd.print(temperature, 1); // One decimal place
  lcd.print(" C");

  lcd.setCursor(0, 1);
  lcd.print("Press: ");
  lcd.print(pressure, 1);
  lcd.print(" hPa");

  delay(1000); // Update once per second

}
