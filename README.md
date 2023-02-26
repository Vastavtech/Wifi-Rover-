# Wifi-Rover-
Code of arduino for a rover that can be accessed from a region to around the globe , it uses concepts of cloud, networking, iot.
#include<SoftwareSerial.h>
SoftwareSerial esp8266(3,2);
#define DEBUG true 
#define SSID "srivastava"
#define PASS "77777777@@"
int state= 5;
// AT commands
String sendAT (String command,const int timeout, boolean debug){
  String response = "";
  esp8266.print(command);
  long int time = millis();
  while ((time+timeout) > millis()){
    while(esp8266.available()){
      char c= esp8266.read();
      response += c;
    }
  }
if ( debug){
  Serial.print(response);
}
return response;
}

void setup() {
  //function to initialize communication between arduino and esp8266
  Serial.begin(9600);//begin the serial communication with baudrate 9600
  esp8266.begin(9600);

  sendAT ("AT+RST\r\n",2000,DEBUG);
  sendAT("AT+CWMODE=1\r\n",1000,DEBUG);
  sendAT("AT+CWJAP=\""SSID"\",\""PASS"\"\r\n",2000,DEBUG);
  while(!esp8266.find("OK")){

  }
sendAT("AT+CIFSR\r\n",1000,DEBUG);
sendAT("AT+CIPMUX=1\r\n",1000,DEBUG);
sendAT("AT+CIPSERVER=1,80\r\n",1000,DEBUG);
delay(4000);
Serial.println("initialization done");
digitalWrite(13, HIGH); //SWITCH ON LED ON ARDUINO
 //DEFINE MOTORS PINS
 pinMode( 8,OUTPUT);
 pinMode( 9,OUTPUT);
 pinMode( 10,OUTPUT);
 pinMode( 11,OUTPUT);


}

void loop() {
  // put your main code here, to run repeatedly:
  if(esp8266.available()){
    if(esp8266.find("+IPD,")){
      String msg;
      esp8266.find("?");
      msg=esp8266.readStringUntil(' ');
      String command=msg.substring(0,3);
      String valueStr = msg.substring(4);
      int value = valueStr.toInt();
      if (DEBUG){
        Serial.println(command);
        Serial.println(value);
      }
      if(command=="cm1"){
        state = 1;
        Serial.println("forward");

      }
       if(command=="cm2"){
        state = 2;
        Serial.println("backward");

    }
     if(command=="cm3"){
        state = 3;
        Serial.println("right");
  }
   if(command=="cm4"){
        state = 4;
        Serial.println("left");

}
 if(command=="cm5"){
        state = 5;
        Serial.println("stop");
        Serial.println("now use the arrows to control the robot");
        }

 
    }


 }
 if (state==1){
   digitalWrite(9,HIGH);
   digitalWrite(11,HIGH);

 }
  if (state==2){
   digitalWrite(8,HIGH);
   digitalWrite(10,HIGH);
  }
   if (state==3){
   digitalWrite(9,HIGH);
   digitalWrite(10,HIGH);
   }
    if (state==4){
   digitalWrite(8,HIGH);
   digitalWrite(11,HIGH);
    }
     if (state==5){
   digitalWrite(9,LOW);
   digitalWrite(11,LOW);
   digitalWrite(8,LOW);
   digitalWrite(10,LOW);
     }
     if(state==6){

      
     }
}
