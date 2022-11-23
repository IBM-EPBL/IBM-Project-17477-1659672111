
#include <LiquidCrystal.h>
LiquidCrystal lcd(2,3,4,5,6,7);
#include <SoftwareSerial.h>
 
SoftwareSerial mySerial(9, 10);
 
int gasValue = A0; // smoke / gas sensor connected with analog pin A1 of the arduino / mega.
int data = 0;
int buzzer = 13;
int G_led = 8; // choose the pin for the Green LED
int R_led = 9; // choose the pin for the Red Led


 
void setup()
{
  pinMode(buzzer,OUTPUT);
  pinMode(R_led,OUTPUT); // declare Red LED as output
  pinMode(G_led,OUTPUT); // declare Green LED as output
randomSeed(analogRead(0));
mySerial.begin(9600); // Setting the baud rate of GSM Module
Serial.begin(9600); // Setting the baud rate of Serial Monitor (Arduino)
lcd.begin(16,2);
pinMode(gasValue, INPUT);
lcd.print (" Gas Leakage ");
lcd.setCursor(0,1);
lcd.print (" Detector Alarm ");
delay(3000);
lcd.clear();
}
 
void loop()
{
 
data = analogRead(gasValue);
Serial.print("Gas Level: ");
Serial.println(data);
lcd.print ("Gas Scan is ON");
lcd.setCursor(0,1);
lcd.print("Gas Level: ");
lcd.print(data);
delay(1000);
 
if ( data > 90) //
{
  digitalWrite(buzzer, HIGH);
  digitalWrite(R_led, HIGH); // Turn LED on.
  digitalWrite(G_led, LOW); // Turn LED off.
SendMessage();
Serial.print("Gas detect alarm");
lcd.clear();
lcd.setCursor(0,0);
lcd.print("Gas Level Exceed");
lcd.setCursor(0,1);
lcd.print("SMS Sent");

delay(1000);
 
}
else
{
  digitalWrite(buzzer, LOW);
  digitalWrite(R_led, LOW); // Turn LED off.
  digitalWrite(G_led, HIGH); // Turn LED on.
Serial.print("Gas Level Low");
lcd.clear();
lcd.setCursor(0,0);
lcd.print("Gas Level Normal");

delay(1000);
}
 
lcd.clear();
}
 
void SendMessage()
{
Serial.println("I am in send");
mySerial.println("AT+CMGF=1"); //Sets the GSM Module in Text Mode
delay(1000); // Delay of 1000 milli seconds or 1 second
mySerial.println("AT+CMGS=\"+91xxxxxxxxxx\"\r"); // Replace x with mobile number
delay(1000);
mySerial.println("Excess Gas Detected.");// The SMS text you want to send
mySerial.println(data);
delay(100);
mySerial.println((char)26);// ASCII code of CTRL+Z
delay(1000);
}
