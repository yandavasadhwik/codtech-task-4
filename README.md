# codtech-task-4

To build an IoT Air Quality Monitor that tracks air quality metrics like PM2.5, CO2 levels, and displays the data on a cloud dashboard, we’ll need to integrate several components, including air quality sensors, a microcontroller with Wi-Fi capability, and a cloud platform to visualize and store the data.

Below is a step-by-step guide to help you build this system.

1. Hardware Components Needed:
Microcontroller: ESP32 or ESP8266 (These microcontrollers have built-in Wi-Fi and are perfect for IoT projects)
Air Quality Sensors:
PM2.5 Sensor (e.g., PMS5003, or MH-Z19 for CO2): These sensors measure particulate matter (PM2.5), which is crucial for assessing air quality.
CO2 Sensor (e.g., MH-Z19 or CCS811): To measure the CO2 levels in the air.
OLED Display (optional): To display real-time air quality data locally on the device.
Jumper Wires & Breadboard: For connecting the components.
Power Supply: Suitable power supply (e.g., 5V for the ESP32 and sensors).
2. Cloud Platform for Data Visualization:
ThingSpeak (Free IoT Cloud Platform): ThingSpeak is an open-source IoT platform that allows you to send, visualize, and analyze data from IoT devices.
Alternatively, Firebase or Adafruit IO can be used for storing and visualizing data.
3. System Design Overview:
Air Quality Monitoring: The PM2.5 sensor and CO2 sensor measure the concentration of particulate matter and carbon dioxide in the air.
Data Transmission: The ESP32 microcontroller reads data from these sensors and sends it over Wi-Fi to a cloud platform like ThingSpeak.
Real-Time Dashboard: The ThingSpeak platform will display the data on a real-time dashboard with graphs showing live air quality metrics and historical trends.
Optional Local Display: You can use an OLED display to show real-time air quality on the device itself.
4. Wiring the System:
Connect PM2.5 Sensor (e.g., PMS5003) to ESP32:

VCC (PM2.5) to 5V (or 3.3V depending on your sensor's requirement)
GND (PM2.5) to GND on ESP32
TX (PM2.5) to RX on ESP32
RX (PM2.5) to TX on ESP32
Fan pin (if required) to 5V.
Connect CO2 Sensor (e.g., MH-Z19) to ESP32:

VCC (CO2) to 5V or 3.3V (depending on your sensor’s requirement)
GND (CO2) to GND on ESP32
TX (CO2) to RX on ESP32
RX (CO2) to TX on ESP32
5. Arduino Code for the Air Quality Monitor:
Here’s an example of how to write the code to get the readings from the sensors, send the data to ThingSpeak, and optionally display it on an OLED screen.

Install Required Libraries:
ThingSpeak Library (for sending data to ThingSpeak)
SoftwareSerial (for communication with PM2.5 and CO2 sensors)
Adafruit SSD1306 (for OLED display)
Install these libraries in Arduino IDE via the Library Manager.

Arduino Code:
cpp
Copy
#include <Wire.h>
#include <WiFi.h>
#include <ThingSpeak.h>
#include <Adafruit_SSD1306.h>
#include <SoftwareSerial.h>

// WiFi credentials
const char *ssid = "Your_WiFi_SSID";
const char *password = "Your_WiFi_Password";

// ThingSpeak API credentials
unsigned long channelID = 123456;  // Replace with your ThingSpeak Channel ID
const char *writeAPIKey = "Your_ThingSpeak_Write_API_Key";  // Replace with your API Key

// Setup for sensors and display
SoftwareSerial mySerial(16, 17); // Define RX and TX pins for PM2.5 sensor
Adafruit_SSD1306 display(128, 64, &Wire, -1);

// Variables for air quality
float pm25 = 0;
float co2 = 0;

void setup() {
  // Start Serial Monitor for debugging
  Serial.begin(115200);
  
  // Connect to WiFi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");

  // Initialize ThingSpeak
  ThingSpeak.begin(WiFi);

  // Initialize sensors (assuming you are using the MH-Z19 for CO2 and PMS5003 for PM2.5)
  mySerial.begin(9600);  // PM2.5 sensor baud rate

  // Initialize OLED Display
  if (!display.begin(SSD1306_I2C_ADDRESS, 4)) {
    Serial.println(F("SSD1306 allocation failed"));
    for (;;);
  }
  display.display();
  delay(2000);
}

void loop() {
  // Read PM2.5 and CO2 sensor data
  pm25 = readPM25();
  co2 = readCO2();

  // Display on OLED
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);
  display.print("PM2.5: ");
  display.println(pm25);
  display.print("CO2: ");
  display.println(co2);
  display.display();

  // Send data to ThingSpeak
  ThingSpeak.setField(1, pm25); // Field 1 for PM2.5
  ThingSpeak.setField(2, co2);  // Field 2 for CO2
  ThingSpeak.writeFields(channelID, writeAPIKey);
  
  delay(30000);  // Update every 30 seconds
}

float readPM25() {
  // Dummy code for PM2.5 reading. Replace with actual communication with PMS5003
  return random(5, 50);  // Simulate PM2.5 readings (in µg/m³)
}

float readCO2() {
  // Dummy code for CO2 reading. Replace with actual communication with MH-Z19
  return random(300, 1000);  // Simulate CO2 readings (in ppm)
}
6. Cloud Setup on ThingSpeak:
Create a ThingSpeak Account:

Go to ThingSpeak and sign up or log in.
Create a new channel by clicking "Create Channel". Give it a name (e.g., "Air Quality Monitor").
In your channel settings, create two fields:
Field 1: PM2.5
Field 2: CO2
After saving, you'll be given an API Key. This is required for writing data to the channel from the ESP32.
Visualizing the Data:

Go to the "Channels" section in ThingSpeak and select your channel.
Click on the "Private View" or "Public View" to see real-time graphs of PM2.5 and CO2.
You can customize the graphs and also view the historical data for trends.
7. Testing and Calibration:
Once everything is wired and the code is uploaded to the ESP32, power up the device.
Check the serial monitor to ensure the ESP32 connects to Wi-Fi and sends data correctly.
Verify the data on ThingSpeak: You should start seeing live air quality data appear on your ThingSpeak dashboard.
8. Optional Features and Enhancements:
Email Notifications or SMS Alerts: Use IFTTT or ThingSpeak's built-in triggers to send email or SMS alerts when certain thresholds (e.g., PM2.5 > 100 or CO2 > 800) are exceeded.
More Sensors: You can add additional sensors like temperature and humidity to monitor the full environmental conditions.
Data Logging: Store data on Firebase or Google Sheets for more detailed analysis and long-term storage.
9. Conclusion:
You’ve now built an IoT Air Quality Monitor that reads real-time data on PM2.5 and CO2 levels, displays this data on a cloud dashboard (ThingSpeak), and optionally shows it on an OLED display. This system can be expanded with additional features such as alarms, more sensors, and data storage for historical analysis. This project can help you monitor air quality and take action based on the real-time data.



