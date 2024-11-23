# Hazardous-gas-detection-
#include <LiquidCrystal.h>
#include <SoftwareSerial.h> 
LiquidCrystal lcd(5, 6, 8, 9, 10, 11);
SoftwareSerial gsmSerial(7, 12); 
int redled = 2;
int greenled = 3;
int buzzer = 4;
int sensor = A0;
int sensorThresh = 400;
String phoneNumber = "+1234567890"; 
void setup()
{
 pinMode(redled, OUTPUT);
 pinMode(greenled, OUTPUT);
 pinMode(buzzer, OUTPUT);
 pinMode(sensor, INPUT);
 Serial.begin(9600);
 gsmSerial.begin(9600); 
 lcd.begin(16, 2);
}
void sendSMS(String message)
{
 gsmSerial.println("AT+CMGF=1"); 
 delay(1000);
 gsmSerial.print("AT+CMGS=\"");
 gsmSerial.print(phoneNumber);
 gsmSerial.println("\"");
 delay(1000);
 gsmSerial.println(message);
 delay(1000);
 gsmSerial.write(26); 
 delay(1000);
 }
 void makeCall()
{
 gsmSerial.println("ATD" + phoneNumber + ";"); 
 delay(1000);
}
void loop()
{
 int analogValue = analogRead(sensor);
Serial.print(analogValue);
 if (analogValue > sensorThresh)
 {
 digitalWrite(redled, HIGH);
 digitalWrite(greenled, LOW);
 tone(buzzer, 1000, 10000);
 lcd.clear();
 lcd.setCursor(0, 1);
 lcd.print("ALERT");
 delay(1000);
 lcd.clear();
 lcd.setCursor(0, 1);
 lcd.print("EVACUATE");
 delay(1000);
 sendSMS("Hazardous gas detected! Evacuate immediately.");
 makeCall();
 }
 else
 {
 digitalWrite(greenled, HIGH);
 digitalWrite(redled, LOW);
 noTone(buzzer);
 lcd.clear();
 lcd.setCursor(0, 0);
 lcd.print("SAFE");
 delay(1000);
 lcd.clear();
 lcd.setCursor(0, 1);
 lcd.print("ALL CLEAR");
 delay(1000);
 }
}
