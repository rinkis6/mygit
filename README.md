# mygit
#pump
const int buttonPin = 2;     // 버튼 핀
const int in1Pin = 9;        // HG7881 IN1 핀
const int in2Pin = 8;        // HG7881 IN2 핀

bool buttonPressed = false;  // 버튼이 눌렸는지 여부를 저장
unsigned long previousMillis = 0; // 마지막으로 펌프가 켜진 시간을 저장

void setup() {
  pinMode(buttonPin, INPUT_PULLUP);  // 버튼 핀을 입력으로 설정하고 풀업 저항 활성화
  pinMode(in1Pin, OUTPUT);           // HG7881 IN1 핀을 출력으로 설정
  pinMode(in2Pin, OUTPUT);           // HG7881 IN2 핀을 출력으로 설정
  digitalWrite(in1Pin, LOW);         // 초기 상태 설정
  digitalWrite(in2Pin, LOW);         // 초기 상태 설정
}

void loop() {
  if (digitalRead(buttonPin) == LOW && !buttonPressed) {  // 버튼이 눌렸고 아직 처리되지 않은 경우
    buttonPressed = true;                // 버튼 눌림 상태 기록
    digitalWrite(in1Pin, HIGH);          // 펌프 켜기
    digitalWrite(in2Pin, LOW);           // 펌프 켜기
    previousMillis = millis();           // 현재 시간 저장
  }

  if (buttonPressed && millis() - previousMillis >= 2000) {  // 버튼이 눌리고 2초가 지난 경우
    digitalWrite(in1Pin, LOW);           // 펌프 끄기
    digitalWrite(in2Pin, LOW);           // 펌프 끄기
    buttonPressed = false;               // 버튼 눌림 상태 해제
  }
}
ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ
slave
#include "LedControl.h"
#define TRIG 9   // 초음파 보내는 핀
#define ECHO 8   // 초음파 받는 핀
int sensor = 7;
int value = 0;
long duration;
long distance;

LedControl lc = LedControl(12, 11, 10, 3);   // 매트릭스 3개 연결

// 광도 센서용 배열
byte Sunny[8] = {0xdf, 0x1f, 0x2f, 0x87, 0x13, 0x24, 0x41, 0x09};

// 미세먼지 배열(먼지 모양)
byte dusts[8] = {
  B00011000,
  B00100100,
  B01100110,
  B10011001,
  B10011001,
  B01100110,
  B00100100,
  B00011000   
};

// 'Rain' 그림 정의
byte Rain[8] = {
  B00011000,
  B00100100,
  B01000010,
  B11111111,
  B00011000,
  B00011000,
  B00111000,
  B00011000
};

// 'Hㅡ
#include <DHT.h>
#define DHTPIN A2 // 온, 습도 센서 핀 번호
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

int rainSensorPin = A3; // 빗방울 감지 센서 핀 번호
const int lightSensorPin = A1; // 조도 센서 핀 번호
int Vo = A0; // 미세먼지 센서 핀 번호
float dust_value = 0; 
int lightThreshold = 100; // 광량 임계값

void setup() {
  Serial.begin(9600);
  pinMode(Vo, INPUT);
  pinMode(lightSensorPin, INPUT);
  pinMode(rainSensorPin, INPUT);
  dht.begin();
}

void loop() {
  int humidity = dht.readHumidity(); // 빗방울, 습도 감지 센서
  int rainSensorValue = analogRead(rainSensorPin);
  int lightValue = analogRead(lightSensorPin);
  dust_value = analogRead(Vo); // 미세먼지 감지 센서

  // 시리얼로 데이터 전송
  if (dust_value > 5) {
    Serial.println("dusty_weather");
  }
  if (lightValue < lightThreshold) {
    Serial.println("Toomuch_sunny");
  }
  if (lightValue < lightThreshold && dust_value > 5) {
    Serial.println("Terrible");
  }
  if (rainSensorValue < 500 && humidity > 20) {
    Serial.println("Raining");
  }
  if (rainSensorValue > 700 && rainSensorValue < 900 && humidity > 20) {
    Serial.println("Just Humid");
  }

  delay(1000);
}
ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ
master
#include <DHT.h>
#define DHTPIN A2 // 온, 습도 센서 핀 번호
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

int rainSensorPin = A3; // 빗방울 감지 센서 핀 번호
const int lightSensorPin = A1; // 조도 센서 핀 번호
int Vo = A0; // 미세먼지 센서 핀 번호
float dust_value = 0; 
int lightThreshold = 100; // 광량 임계값

void setup() {
  Serial.begin(9600);
  pinMode(Vo, INPUT);
  pinMode(lightSensorPin, INPUT);
  pinMode(rainSensorPin, INPUT);
  dht.begin();
}

void loop() {
  int humidity = dht.readHumidity(); // 빗방울, 습도 감지 센서
  int rainSensorValue = analogRead(rainSensorPin);
  int lightValue = analogRead(lightSensorPin);
  dust_value = analogRead(Vo); // 미세먼지 감지 센서

  // 시리얼로 데이터 전송
  if (dust_value > 5) {
    Serial.println("dusty_weather");
  }
  if (lightValue < lightThreshold) {
    Serial.println("Toomuch_sunny");
  }
  if (lightValue < lightThreshold && dust_value > 5) {
    Serial.println("Terrible");
  }
  if (rainSensorValue < 500 && humidity > 20) {
    Serial.println("Raining");
  }
  if (rainSensorValue > 700 && rainSensorValue < 900 && humidity > 20) {
    Serial.println("Just Humid");
  }

  delay(1000);
}
