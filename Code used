#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include "esp_camera.h"

// ================= LCD =================
LiquidCrystal_I2C lcd(0x27, 16, 2);

// ================= IR Sensors =================
const int sensor1 = 14;
const int sensor2 = 27;

// ================= Buzzer =================
const int buzzer = 26;

// ================= Variables =================
unsigned long startTime = 0;
unsigned long endTime = 0;

float distance = 0.5; // meters
float timeTaken = 0;
float speed = 0;

float speedLimit = 20.0;

bool firstDetected = false;

// =====================================================
//                OV7670 CAMERA PINS
// =====================================================

#define PWDN_GPIO_NUM     -1
#define RESET_GPIO_NUM    -1
#define XCLK_GPIO_NUM      4

#define SIOD_GPIO_NUM     18
#define SIOC_GPIO_NUM     23

#define Y9_GPIO_NUM       36
#define Y8_GPIO_NUM       39
#define Y7_GPIO_NUM       34
#define Y6_GPIO_NUM       35
#define Y5_GPIO_NUM       32
#define Y4_GPIO_NUM       33
#define Y3_GPIO_NUM       25
#define Y2_GPIO_NUM       15

#define VSYNC_GPIO_NUM    19
#define HREF_GPIO_NUM      5
#define PCLK_GPIO_NUM     21

// =====================================================
//                    CAMERA SETUP
// =====================================================

void startCamera()
{
  camera_config_t config;

  config.ledc_channel = LEDC_CHANNEL_0;
  config.ledc_timer   = LEDC_TIMER_0;

  config.pin_d0 = Y2_GPIO_NUM;
  config.pin_d1 = Y3_GPIO_NUM;
  config.pin_d2 = Y4_GPIO_NUM;
  config.pin_d3 = Y5_GPIO_NUM;
  config.pin_d4 = Y6_GPIO_NUM;
  config.pin_d5 = Y7_GPIO_NUM;
  config.pin_d6 = Y8_GPIO_NUM;
  config.pin_d7 = Y9_GPIO_NUM;

  config.pin_xclk = XCLK_GPIO_NUM;
  config.pin_pclk = PCLK_GPIO_NUM;
  config.pin_vsync = VSYNC_GPIO_NUM;
  config.pin_href = HREF_GPIO_NUM;

  config.pin_sscb_sda = SIOD_GPIO_NUM;
  config.pin_sscb_scl = SIOC_GPIO_NUM;

  config.pin_pwdn  = PWDN_GPIO_NUM;
  config.pin_reset = RESET_GPIO_NUM;

  config.xclk_freq_hz = 10000000;

  config.frame_size = FRAMESIZE_QQVGA;

  config.pixel_format = PIXFORMAT_RGB565;

  config.grab_mode = CAMERA_GRAB_WHEN_EMPTY;

  config.fb_location = CAMERA_FB_IN_DRAM;

  config.jpeg_quality = 12;

  config.fb_count = 1;

  esp_err_t err = esp_camera_init(&config);

  if (err != ESP_OK)
  {
    Serial.print("Camera Init Failed: ");
    Serial.println(err);

    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Camera Failed");

    return;
  }

  Serial.println("OV7670 Camera Ready!");

  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Camera Ready");

  delay(1000);
}

// =====================================================
//                       SETUP
// =====================================================

void setup()
{
  Serial.begin(115200);

  // IR Sensors
  pinMode(sensor1, INPUT);
  pinMode(sensor2, INPUT);

  // Buzzer
  pinMode(buzzer, OUTPUT);

  digitalWrite(buzzer, LOW);

  // LCD Start
  lcd.init();
  lcd.backlight();

  lcd.setCursor(0,0);
  lcd.print("Speed Detector");

  lcd.setCursor(0,1);
  lcd.print("Initializing");

  delay(2000);

  // Start Camera
  startCamera();

  lcd.clear();

  Serial.println("System Ready");
}

// =====================================================
//                        LOOP
// =====================================================

void loop()
{
  // SENSOR 1
  if(digitalRead(sensor1) == LOW && !firstDetected)
  {
    startTime = millis();

    firstDetected = true;

    Serial.println("Vehicle at Sensor 1");

    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Vehicle Found");

    delay(200);
  }

  // SENSOR 2
  if(digitalRead(sensor2) == LOW && firstDetected)
  {
    endTime = millis();

    timeTaken = (endTime - startTime) / 1000.0;

    if(timeTaken > 0)
    {
      speed = (distance / timeTaken) * 3.6;
    }

    Serial.print("Speed: ");
    Serial.print(speed);
    Serial.println(" km/h");

    lcd.clear();

    lcd.setCursor(0,0);
    lcd.print("Speed:");

    lcd.print(speed,1);
    lcd.print("km/h");

    // ==========================================
    // OVERSPEED DETECTION
    // ==========================================

    if(speed > speedLimit)
    {
      lcd.setCursor(0,1);
      lcd.print("OVER SPEED!");

      Serial.println("Overspeed Detected!");

      digitalWrite(buzzer, HIGH);

      // ==========================================
      // CAMERA CAPTURE DEMO
      // ==========================================

      Serial.println("Capturing Image...");

     Serial.println("Capturing Image...");
      delay(1000);
        Serial.println("Image Captured Successfully!");
        Serial.print("Image Size: ");
        Serial.println(fb->len);

        // Release buffer
        esp_camera_fb_return(fb);
      }

      delay(1000);

      digitalWrite(buzzer, LOW);
    }
    else
    {
      lcd.setCursor(0,1);
      lcd.print("Normal Speed");
    }

    delay(3000);

    lcd.clear();

    firstDetected = false;
  }
}
