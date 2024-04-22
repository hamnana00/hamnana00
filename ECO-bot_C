#include <Arduino.h>
#include <Wire.h>
#include <Adafruit_PWMServoDriver.h>

Adafruit_PWMServoDriver pwm = Adafruit_PWMServoDriver();

//para las ruedas
  #define SERVOMIN 320
  #define SERVOSTOP 380
  #define SERVOMAX 440
  #define servo_left 0
  #define servo_right 1
//los leds
  #define green_led 9
  #define red_led 12
//ssensor ultrasonido
  #define pingPin 5
//sensor infrarrojo
  #define IR_left 2
  #define IR_right 3
//zumbador
  #define DO 523
  #define RE 587
  #define MI 659
  #define FA 698
  #define SOL 784
  #define LA 880
  #define SI 988
  #define DOs 1047
  #define Buzzpin 13
//pulsador
  #define button_pin 11
  volatile bool alarm_on=true;//esta es para la interrupcion
//para el primer espiral
int v=0;
int t=0;
//para el espiral de giro
int v2=0;
int t2=0;
//reducir la velocidad
int v3=0;
int t3=0;

//distancia
long distancia;
//leds
int encen_v=0;
int encen_r=0;
//estado para coger la primera distancia menor de 16
boolean estado=false;

//formula con el que el sensor ultrasonido utiliza para medir la distancia
long calcularDistancia(){
  long duration, cm;
  pinMode(pingPin, OUTPUT);
  digitalWrite(pingPin, LOW);
  delayMicroseconds(10);
  digitalWrite(pingPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(pingPin, LOW);
  pinMode(pingPin, INPUT);
  duration = pulseIn(pingPin, HIGH);
  cm=duration/29/2;
  return cm;
}
//para la interrupcion
void switch_alarm_on(){
  alarm_on=!alarm_on;
}
void setup(){
  Serial.begin(9600);//observar velocidad en el monitor
  //luz
  pinMode(green_led, OUTPUT);
  pinMode(red_led,OUTPUT);
  //rueda
  pwm.begin();
  pwm.setPWMFreq(60);
  //sensir infrarrojo
  pinMode(IR_left, INPUT);
  pinMode(IR_right, INPUT);
  //pulsador
  pinMode(button_pin, INPUT_PULLUP);//ponemos el PULLUP para habilitar la resistencia interna del piny asegurar que el boton funciona correctamente
  //para que se empiece con el led encendido y el gire el espirar
  digitalWrite(green_led, HIGH); 
  encen_v=1;
  estado=false;
  attachInterrupt(digitalPinToInterrupt(button_pin), switch_alarm_on, RISING);//la interrupcion

}

void espiral_principal(){
  pwm.setPWM(servo_left, 0, 380-v);//rueda izquierda va adquiriendo velocidad poco a poco
  pwm.setPWM(servo_right, 0, SERVOMAX);//ruedad derecha no cambia velocidad
  delay(500+t);
  v=v+5;
  t=t+250;
}

void giro180(){
  pwm.setPWM(servo_left,0,SERVOSTOP);//se para una rueda y el otra avanza para obtener medio circulo
  pwm.setPWM(servo_right,0,SERVOMAX);
  delay(2500);
}

void espiral_contrario(){
  pwm.setPWM(servo_left, 0, SERVOMIN);//la rueda izquierda no cambi la velocidad
  pwm.setPWM(servo_right, 0, 380+v2);//la derecha va obteniendo cada vez mas velocidad
  delay(500+t2);
  v2=v2+5;
  t2=t2+250;
}

void parar(){
  pwm.setPWM(servo_left, 0, SERVOSTOP);
  pwm.setPWM(servo_right, 0, SERVOSTOP);
  delay(4000);
}

void cambio_led(){
  if(encen_v==1){ 
    digitalWrite(green_led, LOW);
      encen_v=0;
    digitalWrite(red_led, HIGH);
      encen_r=1;
   }else if (encen_r==1){
    digitalWrite(red_led, LOW);
      encen_r=0;
    digitalWrite(green_led, HIGH);
      encen_v=1;
    }
}

void mantener_led(){
  if(encen_v==1){ 
    digitalWrite(green_led, HIGH);
      encen_v=1;
    digitalWrite(red_led, LOW);
      encen_r=0;
   }else if (encen_r==1){
    digitalWrite(red_led, HIGH);
      encen_r=1;
    digitalWrite(green_led, LOW);
      encen_v=0;
    }
}

void parpadeo(){
   if(encen_v==1){
      digitalWrite(green_led, LOW);
      delay(500);
      digitalWrite(green_led, HIGH);
      delay(500);
      digitalWrite(green_led, LOW);
      delay(500);
      digitalWrite(green_led, HIGH);
      delay(500);
      digitalWrite(green_led, HIGH);
    }else if(encen_r==1){
      digitalWrite(red_led, LOW);
      delay(500);
      digitalWrite(red_led, HIGH);
      delay(500);
      digitalWrite(red_led, LOW);
      delay(500);
      digitalWrite(red_led, HIGH);
      delay(500);
      digitalWrite(red_led, HIGH);
    }
}

void cambio_giro(){
  if(estado==true){
    estado=false;
  }else if(estado==false){
    estado=true;
  }
}

void espiral_dentro(){
  pwm.setPWM(servo_left, 0, SERVOSTOP-20);
  pwm.setPWM(servo_right, 0, SERVOMAX-16);
  delay(200);
  pwm.setPWM(servo_left, 0, SERVOSTOP-15);
  pwm.setPWM(servo_right, 0, SERVOMAX-11);
  delay(200);
  pwm.setPWM(servo_left, 0, SERVOSTOP-12);
  pwm.setPWM(servo_right, 0, SERVOMAX-8);
  delay(200);
}

void sonido(){
  tone(Buzzpin,DO);
  delay(500);
  tone(Buzzpin,MI);
  delay(500);
  tone(Buzzpin,SOL);
  delay(500);
  tone(Buzzpin,SI);
  delay(500);
    noTone(Buzzpin);
}

void reducir_velocidad(){
  pwm.setPWM(servo_left, 0, 380-(v3*0.8));//*0.8 para reducir su incremento de velocidad
    pwm.setPWM(servo_right, 0, SERVOMAX);
    delay(1000+t3);
}

void loop(){
  distancia=calcularDistancia();
  if(distancia<=16){
    parar();
    cambio_led();
    giro180();
    cambio_giro();
  }
  if(estado==true){
    espiral_contrario();
  }else{
    espiral_principal();
  }

  int button_value = digitalRead(button_pin);
  if(button_value == HIGH){
    parar();
    espiral_dentro();
    pwm.setPWM(servo_left, 0, SERVOSTOP);
    pwm.setPWM(servo_right, 0, SERVOSTOP);
    sonido();
  }
  
  int valor_IR_left=digitalRead(IR_left);
  int valor_IR_right=digitalRead(IR_right);
  if(valor_IR_left ==0 || valor_IR_right==0){
    parar();
    sonido();
    parpadeo();
    mantener_led();
  }
  
  int light = analogRead(A0);
  if (light<=100){
    digitalWrite(red_led, HIGH);
    digitalWrite(green_led, HIGH);
    reducir_velocidad();
  }else{
    pwm.setPWM(servo_left, 0, 380-v);//que siga el transcurso con la velocidad normal
    pwm.setPWM(servo_right, 0, SERVOMAX);
  }
   if(encen_r==1){
    digitalWrite(red_led, HIGH);
    encen_r=1;
    digitalWrite(green_led, LOW);
    encen_v=0;
  }else if(encen_v==1){
    digitalWrite(red_led, LOW);
    encen_r=0;
    digitalWrite(green_led, HIGH);
    encen_v=1;
  }

}
