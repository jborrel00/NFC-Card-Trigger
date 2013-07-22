//make sure GPIO from Pi is HIGH to reset pin on arduino. This allows the function to run. When the game has ended the GPIO will go LOW and then HIGH right away, resetting the arduino and letting the program run again.

#include <Wire.h>
#include <Adafruit_NFCShield_I2C.h>
#include <CapacitiveSensor.h>

#define IRQ   (2)
#define RESET (3)  // Not connected by default on the NFC Shield

Adafruit_NFCShield_I2C nfc(IRQ, RESET);

int led = 13;
//int button_left = 10; for real buttons
//int button_right = 11; for real buttons
int count = 0;
//int sensor = 12; for code reset - not currently needed

CapacitiveSensor   cs_4_6 = CapacitiveSensor(4,6);        // 10M resistor between pins 4 & 6, pin 6 is sensor pin
CapacitiveSensor   cs_8_10 = CapacitiveSensor(8,10);      // 10M resistor between pins 8 & 10, pin 10 is sensor pin

void setup(void) {
  
  nfc.begin();

  uint32_t versiondata = nfc.getFirmwareVersion();
  if (! versiondata) {
    //Serial.print("Didn't find PN53x board");
    while (1); // halt
  }

  // configure board to read RFID tags
  nfc.SAMConfig();
  
  pinMode(led,OUTPUT);
  cs_4_6.set_CS_AutocaL_Millis(0xFFFFFFFF);
  cs_8_10.set_CS_AutocaL_Millis(0xFFFFFFFF);
  Serial.begin(115200);
  //Serial.println("pick a side");
  digitalWrite(led,HIGH);
  delay(200);
  digitalWrite(led,LOW);
  delay(200);
  delay(600);
  int j=1;
  while(j==1){
  int left=cs_4_6.capacitiveSensor(30);
  int right=cs_8_10.capacitiveSensor(30);
  if(left < 100 && right < 100){
  //Serial.println(left);
  //Serial.println(right);
}
 if(left > 100){
          Serial.println("left");
          digitalWrite(led,HIGH);
          delay(100);
          digitalWrite(led,LOW);
          delay(100);
          digitalWrite(led,HIGH);
          delay(100);
          digitalWrite(led,LOW);
          delay(100);
          j=2;
        }
        if(right > 100){
          Serial.println("right");
          digitalWrite(led,HIGH);
          delay(100);
          digitalWrite(led,LOW);
          delay(100);
          j=2;
        }
        if(left > 100 && right > 100){
          Serial.println("invalid");
          digitalWrite(led,HIGH);
          delay(100);
          digitalWrite(led,LOW);
          delay(100);
          digitalWrite(led,HIGH);
          delay(100);
          digitalWrite(led,LOW);
          delay(100);
          digitalWrite(led,HIGH);
          delay(100);
          digitalWrite(led,LOW);
          delay(100);
        }
        if(left > 100 || right > 100){

//  //Serial.println("Waiting for an ISO14443A Card ...");
  j=2;
//  
} 
}
}

void(* resetFunc) (void) = 0;

void loop(void) {
  uint8_t success;                          // Flag to check if there was an error with the PN532
  uint8_t uid[] = { 0, 0, 0, 0, 0, 0, 0 };  // Buffer to store the returned UID
  uint8_t uidLength;                        // Length of the UID (4 or 7 bytes depending on ISO14443A card type)
  uint8_t currentblock;                     // Counter to keep track of which block we're on
  bool authenticated = false;               // Flag to indicate if the sector is authenticated
  uint8_t data[16];                         // Array to store block data during reads
  
  digitalWrite(led,HIGH);
  delay(200);
  digitalWrite(led,LOW);
  delay(200);
//digitalWrite(13,LOW);

  // Keyb on NDEF and Mifare Classic should be the same
  uint8_t keyuniversal[6] = { 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF };

  // Wait for an ISO14443A type cards (Mifare, etc.).  When one is found
  // 'uid' will be populated with the UID, and uidLength will indicate
  // if the uid is 4 bytes (Mifare Classic) or 7 bytes (Mifare Ultralight)

  success = nfc.readPassiveTargetID(PN532_MIFARE_ISO14443A, uid, &uidLength);


  if (success) {

    if (uidLength == 4)
    {

      
      for (currentblock = 6; currentblock < 7; currentblock++)
      {
        // Check if this is a new block so that we can reauthenticate
        if (nfc.mifareclassic_IsFirstBlock(currentblock)) authenticated = false;

        // If the sector hasn't been authenticated, do so first
        if (!authenticated)
        {
          // Starting of a new sector ... try to to authenticate
          //Serial.print("------------------------Sector ");Serial.print(currentblock/4, DEC);Serial.println("-------------------------");
          if (currentblock == 0)
          {
              // This will be 0xFF 0xFF 0xFF 0xFF 0xFF 0xFF for Mifare Classic (non-NDEF!)
              // or 0xA0 0xA1 0xA2 0xA3 0xA4 0xA5 for NDEF formatted cards using key a,
              // but keyb should be the same for both (0xFF 0xFF 0xFF 0xFF 0xFF 0xFF)
              success = nfc.mifareclassic_AuthenticateBlock (uid, uidLength, currentblock, 1, keyuniversal);
          }
          else
          {
              // This will be 0xFF 0xFF 0xFF 0xFF 0xFF 0xFF for Mifare Classic (non-NDEF!)
              // or 0xD3 0xF7 0xD3 0xF7 0xD3 0xF7 for NDEF formatted cards using key a,
              // but keyb should be the same for both (0xFF 0xFF 0xFF 0xFF 0xFF 0xFF)
              success = nfc.mifareclassic_AuthenticateBlock (uid, uidLength, currentblock, 1, keyuniversal);
          }
          if (success)
          {
            authenticated = true;
          }
          else
          {
            //Serial.println("Authentication error");
          }
        }
        // If we're still not authenticated just skip the block
        if (!authenticated)
        {
          //Serial.print("Block ");Serial.print(currentblock, DEC);Serial.println(" unable to authenticate");
        }
        else
        {
          // Authenticated ... we should be able to read the block now
          // Dump the data into the 'data' array
          
          success = nfc.mifareclassic_ReadDataBlock(currentblock, data);

          nfc.PrintHexChar(data, 16);
          count++;
       
          if (success)
          {
//            // Read successful
//            Serial.print("Block ");Serial.print(currentblock, DEC);
//            if (currentblock < 10)
//            {
//              Serial.print("  ");
//            }
//            else
//            {
//              Serial.print(" ");
//            }
//            // Dump the raw data
            //nfc.PrintHexChar(data, 16);
          }
          else
          {
            // Oops ... something happened
            //Serial.print("Block ");Serial.print(currentblock, DEC);
            //Serial.println(" unable to read this block");
          }
        }
      }
    }
  

    else
    {
      //Serial.println("Ooops ... this doesn't seem to be a Mifare Classic card!");
    }
  
    //int sensorValue = digitalRead(sensor); //
    //Serial.println(sensorValue);
    int k=1;
    // resets the card reader when it gets a HGIGH the signal would be coming from an outside source (RPi) to signal the end of the game
    if(count >= 2){
      //Serial.println("You may only have 2 players at a time");
          digitalWrite(led,HIGH);
          delay(100);
          digitalWrite(led,LOW);
          delay(100);
          digitalWrite(led,HIGH);
          delay(100);
          digitalWrite(led,LOW);
          delay(100);
          digitalWrite(led,HIGH);
          delay(100);
          digitalWrite(led,LOW);
          delay(100);
         Serial.end();
      }

    }
    delay(1000);

  // Wait a bit before trying again
  //Serial.println("\n\nSend a character to run the mem dumper for Block ");Serial.print(currentblock,DEC);Serial.print(" again!");
 // Serial.flush();
 // while (!Serial.available());
 // while (Serial.available()) {
 // Serial.read();
 // }
  Serial.flush();
  
  delay(500);
  //Serial.println("next player log in");
  
}

