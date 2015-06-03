#Parking Assistance


This project assists vehicles to park safely by considering the distances from vehicle to the Obstacles. 
Ultrasonic sensor is used to sense the object by sending the signal through its Trigger pin and  this signal is given as an input to the Echo pin. Based on the received signal it calculates distance between the object and sensor by using inbuilt function pulseIn(). This calculated distance will act as an input to the Buzzer and based on distance Buzzer will react. Two aspects are considered to save the power in this project. Firstly, If the sensor doesn't detect any object the Buzzer will remain in low state otherwise  
start ringing accordingly. Secondly, if the object is in idle state for certain amount of time then Buzzer will go to low state.



# Requirements:


- Arduino Uno
- Ultrasonic sensor HC-SR04
- Buzzer
- External Battery
- Jumper cable

#Code: 



# define trigpin 13 //Sensor trigger set to Pin 8 as output
# define echopin 12//Sensor echo set to pin 7 as Input 
int Buzzer = 8; // Buzzer set to pin 6 as output
long distance;
int currentdistance = 0;
int previousdistance = 0;
unsigned long distancechange;

// Detection of Movement in ms
const int sensing = 1000;
const int Timeout = 20000;
int sensedelay = 1000;

void setup() {
  // put your setup code here, to run once:
  distancechange = millis();
  pinMode (Buzzer, OUTPUT);


}




void loop() {

  long totaltime, newdistance;
  //Setting of Output on triggerpin
  pinMode (trigpin, OUTPUT);
  digitalWrite (trigpin, LOW);
  delayMicroseconds (5);
  digitalWrite (trigpin, HIGH);
  delayMicroseconds(10);
  digitalWrite (trigpin, LOW);

  // Setting of input on echopin and calculate the distance of the objects
  // We are only interested in one way distance so total time is devided by 2
  //Since sound travels 29 cm/ms, by dividing the total time by 29 to get the total distance that the sound traveled

  pinMode (echopin, INPUT);
  totaltime = pulseIn (echopin, HIGH);
  newdistance = totaltime / 29 / 2;

  if (distance != newdistance) {

    // Something is moving, increase polling rate

    distancechange = millis();
    sensedelay = 100;
  }
  distance = newdistance;

  currentdistance = distance;

  // Based on different distances the frequency of the Buzzer changes
  //If the object is out of the given range then it will again go to Power saving mode (1)
  // For object in range but far from sensor the frequency of the Buzzer is low(2)
  // For near objects the frequency of the Buzzer is little high(3)
  // For the tooclose Objects the frequency of the Buzzer is high (4)

  if (currentdistance != previousdistance)
  {
    if (currentdistance >= 80) // (1)
    {
      digitalWrite(Buzzer, LOW);
    }
    else if (currentdistance >= 40) // (2)
    {
      tone(Buzzer, 400);
      delay(5);
      noTone(Buzzer);
    }
    else if (currentdistance >= 20) //(3)
    {
      tone(Buzzer, 600);
      delay(5);
      noTone(Buzzer);
    }
    else if (currentdistance <= 20) // (4)
    {
      tone(Buzzer, 900);
      delay(5);
      noTone(Buzzer);
    }
  }
  else {
    if (sensedelay != sensing && (millis() - distancechange > Timeout)) // Go into Power saving mode by setting Buzzer to Low
    {
      digitalWrite(Buzzer, LOW);
      sensedelay = sensing;
    }
  }
  previousdistance = currentdistance;
  delay(sensedelay);

}
