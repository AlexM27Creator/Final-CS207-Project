## Disco Night Lights
Made lighs that turn on when it gets dark that would work well in a night club

## Table of contents
* [General info](#general-info)
* [Program](#program-used)
* [Code](#code-for-the-project)
* [Status](#status)
* [Credit](#credit)

## General info
This project works around the light sensor. Everything that happens in this project is based on the numbers that the light sensor transmitts.If there is light in the room the lights won't turn on. If it starts to get darker in the room, you get warning lights. Then you turn on the light wanted that only change when something gets in front of the sensor allowing even less light to hit it and hence starting to change colors. Then it saves whatever light it was left on and in case lights are turned back on. As the lights start to come back, you will get warning lights again advising you of the occuring change. I took motivation for this project, mainly because I always wanted to make my own night lights and have them act the way I want them to.

## Program used
Arduino app

## Code for the project
#include <EEPROM.h> 
const int redPin = 9;
const int greenPin = 10;
const int bluePin = 11;
const int sensorPin = A4;
const int ledPin1 = 4;
const int ledPin2 = 6;
int timer = 1000;
float  hue = EEPROM.read(0), s = 1, v = 1;
float newH;
float r, b;
void hsv2rgb(float h, float s, float v, float &r, float &b);
void setup()
{
  pinMode(redPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  pinMode(bluePin, OUTPUT);
  pinMode(ledPin1, OUTPUT);
  pinMode(ledPin2, OUTPUT);
  Serial.begin(9600);
}

void loop()
{
  Serial.println(analogRead(sensorPin));
  if (analogRead(sensorPin) < 60 && analogRead(sensorPin) > 10)
  {
    hsv2rgb(hue, s, v, r, b);
    analogWrite(redPin, r);
    analogWrite(bluePin, b);
    digitalWrite(ledPin1, LOW);
    digitalWrite(ledPin2, LOW);
    delay(14);
  } else if (analogRead(sensorPin) < 10 && analogRead(sensorPin) >= 0)
  {
    hue++;
    //make hue wrap around the color wheel
    if (hue < 0 || hue > 359)
    {
      hue = 0;
    }
    hsv2rgb(hue, s, v, r, b);
    analogWrite(redPin, r);
    analogWrite(bluePin, b);
    digitalWrite(ledPin1, LOW);
    digitalWrite(ledPin2, LOW);
    EEPROM.put(0,hue); 
    //If button was just released, store hue
  } else if (analogRead(sensorPin) >= 60 && analogRead(sensorPin) < 250 )
  {
    if (timer >= 0) {
      digitalWrite(ledPin1, HIGH);
      digitalWrite(ledPin2, LOW);
      analogWrite(redPin, LOW);
      analogWrite(greenPin, LOW);
      analogWrite(bluePin, LOW);
      delay(timer);
      digitalWrite(ledPin2, HIGH);
      digitalWrite(ledPin1, LOW);
      analogWrite(redPin, LOW);
      analogWrite(greenPin, LOW);
      analogWrite(bluePin, LOW);
      delay(timer);
      timer = timer - 100;
    }
    if (timer == 0) {
      timer = 1000;
    }
  } else if (analogRead(sensorPin) > 250) {
    digitalWrite(ledPin1, LOW);
    digitalWrite(ledPin2, LOW);
    analogWrite(redPin, LOW);
    analogWrite(greenPin, LOW);
    analogWrite(bluePin, LOW);
  }
}

void hsv2rgb(float h, float s, float v, float &r, float &b)
{
  v = constrain(v, 0, 1);
  s = constrain(s, 0, 1);
  float c = v * s;
  float hprime = h / 60.0f;
  float x = c * ( 1 - abs(hprime - ((int)hprime / 2) * 2 - 1));

  if (h < 0 || h > 360)
  {
    r = b = 0;
  }
  else if ( 0 <= hprime && hprime < 1)
  {
    r = c;
    b = 0;
  }
  else if ( 1 <= hprime && hprime < 2)
  {
    r = x;
    b = 0;
  }
  else if ( 2 <= hprime && hprime < 3)
  {
    r = 0;
    b = x;
  }
  else if ( 3 <= hprime && hprime < 4)
  {
    r = 0;
    b = c;
  }
  else if ( 4 <= hprime && hprime < 5)
  {
    r = x;
    b = c;
  }
  else if ( 5 <= hprime && hprime < 6)
  {
    r = c;
    b = x;
  }

  float m = v - c;
  r += m;
  b += m;
  r *= 255;
  b *= 255;

}

## Status
Project is finished, few changes could be made.

## Credit
Took most ideas from labs done in my cs 207 class. Mainly when usign the RGB LED.
