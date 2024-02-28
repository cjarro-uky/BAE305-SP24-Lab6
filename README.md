```c++
#include <SoftwareSerial.h>

//Create software serial object to communicate with HC-08
SoftwareSerial mySerial(3, 2); //HC-05 Tx & Rx is connected to Arduino #3 & #2

const byte numChars = 6;
char receivedChars[numChars];   // an array to store the received data
char tempChars[numChars];

char botDirection[1] = {0};
int botSpeed = 0;
int motorSpeed = 0;

boolean newData = false;

/********************************************************************************/
void setup()
{
   mySerial.begin(9600);         //Default Baud Rate for software serial communications
}

/********************************************************************************/
void loop()
{

  recvWithEndMarker();
//  showNewData();
  if (newData == true)
  {
    strcpy(tempChars, receivedChars);
    parseData();
    showParsedData();
    motorSpeed = botSpeed;
    newData = false;
  }
/********************************************************************************/
void recvWithEndMarker() {
    static byte ndx = 0;
    char endMarker = '\n';
    char rc;
    
    while (mySerial.available() > 0 && newData == false) {
        rc = mySerial.read();

        if (rc != endMarker) {
            receivedChars[ndx] = rc;
            ndx++;
            if (ndx >= numChars) {
                ndx = numChars - 1;
            }
        }
        else {
            receivedChars[ndx] = '\0'; // terminate the string
            ndx = 0;
            newData = true;
        }
    }
}

void parseData() {      // split the data into its parts

    char * strtokIndx; // this is used by strtok() as an index

    strtokIndx = strtok(tempChars," ");      // get the first part - the string
    strcpy(botDirection, strtokIndx); // copy it to messageFromPC
 
    strtokIndx = strtok(NULL, " "); // this continues where the previous call left off
    botSpeed = atoi(strtokIndx);     // convert this part to an integer
}
