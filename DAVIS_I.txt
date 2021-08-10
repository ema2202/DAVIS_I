int volumen = 30;
int cm = 0;
int trigger = 40;
int echo = 41;
float temperatura;
int ledGreen = 50;
int ledRed = 51;

#include "DFRobotDFPlayerMini.h"//incluye libreria de dfplayer
#include <LiquidCrystal.h> //Incluye libreria de lcd
#include <Adafruit_MLX90614.h>//Incluir libreria del sensor mlx90614

DFRobotDFPlayerMini myDFPlayer;//Nuevo objeto DFPlayerMini
LiquidCrystal lcd(27,26,25,24,23,22);//Nuevo objeto LCD
Adafruit_MLX90614 mlx = Adafruit_MLX90614();//Nuevo objeto MLX


void ajustarVolumen(int vol);
long readUltrasonicDistance(int triggerPin, int echoPin);
void inicializar();

void setup() {
  inicializar();
  lcd.setCursor(0,1);//Posición de las letras 
  ajustarVolumen(volumen);
  pinMode(ledGreen,OUTPUT);
  pinMode(ledRed,OUTPUT);
}


void loop(){
  cm = 0.01723 * readUltrasonicDistance(trigger, echo);
  Serial.print(cm);
  Serial.println("cm");
  delay(100); // Wait for 100 millisecond(s)

  if(cm <= 4.5 && cm >= 3.3){
     temperatura = mlx.readObjectTempC();
     Serial.println("Temperatura: ");
     Serial.println(temperatura);
     lcd.clear();
     lcd.setCursor(6,1);
     lcd.print(temperatura);
     lcd.setCursor(13,1);
     lcd.print("°C");
     delay(1000);
     lcd.clear();
     
     
    if(temperatura <= 37.5){
       lcd.clear();
       lcd.setCursor(1,1);
       lcd.print("Temperatura");//Imprime el lcd
       lcd.setCursor(1,2);
       lcd.print("Normal");
       myDFPlayer.volume(volumen);
       myDFPlayer.play(0001);
       digitalWrite(ledGreen,HIGH);
       delay(2000);lcd.clear();
       digitalWrite(ledGreen,LOW);
      }else{
       lcd.clear();
       lcd.setCursor(1,1);
       lcd.print("Temperatura");//Imprime el lcd
       lcd.setCursor(1,2);
       lcd.print("Anormal");
       myDFPlayer.volume(volumen);
        myDFPlayer.play(0002);
        digitalWrite(ledRed,HIGH);
        delay(2000);lcd.clear();
        digitalWrite(ledRed,LOW);
        }
    }
}




/**********  TODAS LAS FUNCIONES  **********/

void inicializar(){
 Serial.begin(9600);//Inicializar puerto serial
 Serial1.begin(9600); //Inicializa el otro puerto serial
 myDFPlayer.begin(Serial1); //Inicializa el módulo mp3
 lcd.begin(16,2); //Inicializa el lcd
 mlx.begin(); //Inicializa sensor mlx90614
  
  }


void ajustarVolumen( int vol){
  myDFPlayer.volume(vol);//volumen del 0 al 30
  }

long readUltrasonicDistance(int triggerPin, int echoPin)
{
  pinMode(triggerPin, OUTPUT);  // Clear the trigger
  digitalWrite(triggerPin, LOW);
  delayMicroseconds(2);
  // Sets the trigger pin to HIGH state for 10 microseconds
  digitalWrite(triggerPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(triggerPin, LOW);
  pinMode(echoPin, INPUT);
  // Reads the echo pin, and returns the sound wave travel time in microseconds
  return pulseIn(echoPin, HIGH);
}
