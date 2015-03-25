# Setup for DUE with RADDS and Graphical LCD:
RADDS has a specific LCD connectors different from RepRap standard LCD - and today only Text LCD exists so far for RADDS.
<img src=https://github.com/luc-github/DUE-RADDS-GLCD/blob/master/Images/lcd_anleitung_4.jpg?raw=true>  

But I wanted a graphical LCD for my DUE/RADDS,  and also have already  RepRap Text LCD s, so why to buy an extra LCD.   
<img src=https://github.com/luc-github/DUE-RADDS-GLCD/blob/master/Images/GLCD.png?raw=true>  
I digged in and got a good setup to make it work :

#Hardware

Here the normal usage:
<img src=https://github.com/luc-github/DUE-RADDS-GLCD/blob/master/Images/RADDS.png?raw=true>
<CENTER><img src=https://github.com/luc-github/DUE-RADDS-GLCD/blob/master/Images/RADDS-REPRAPGRAPHICLCD.png?raw=true width=75%></CENTER>
When connections are ok with Ramps-FD, I got some issues with RADDS, the schematics suggest to use pins 42/43 for RS and EN but for me it is not working.
I have tried on both RADDS I have and initialization is ramdomly failed or give a garbage screen. I got same behaviour with text LCD.
I suspect some "noise" due to proximity of VCC so I RS/EN then to pins 46/ 47 because Graphical LCD does not need and bingo - no more issue, same for text LCD using 42/43 for LCD 6/LCD 7 and 46/47 for RS/EN also solved the problem.

There is no cable today so far for the connection so I built mine.
Some pins : Gnd SDCard/3.3v SDCard/SCL/CS0 /LCD5/LCD6/LCD7 pins are not used so they could be  used to add a keypad.    
Here the corresponding pins:   
<img src=https://github.com/luc-github/DUE-RADDS-GLCD/blob/master/Images/RADDS%20-%20REPRAPFULLGRAPHICLCDV2.png?raw=true>

(sorry for the picture, I do not know how to make it clear)    

#Firmware
I use repetier as seems the most stable for DUE
1 - Edit the uconfig.h
*Beeper Pin (41)
```
    #define BEEPER_PIN 41
```
 *And of  course the display type
 ```
     #define UI_DISPLAY_TYPE DISPLAY_U8G
 ```
 
 *Define the pins for LCD in direct connections, we only need 3 pins (LCD 4 : 44, RS : 46, EN : 47)
 ```
     #else // Direct display connections
     #define UI_DISPLAY_RS_PIN		46	
     #define UI_DISPLAY_RW_PIN		-1
     #define UI_DISPLAY_ENABLE_PIN  47
     #define UI_DISPLAY_D0_PIN		-1
     #define UI_DISPLAY_D1_PIN		-1
     #define UI_DISPLAY_D2_PIN		-1
     #define UI_DISPLAY_D3_PIN		-1
     #define UI_DISPLAY_D4_PIN		44
     #define UI_DISPLAY_D5_PIN		-1
     #define UI_DISPLAY_D6_PIN		-1
     #define UI_DISPLAY_D7_PIN		-1
```
*Display has key 
```
     #define UI_HAS_KEYS 1
```

*But no back key (unless you add the extra key pad)
```
     #define UI_HAS_BACK_KEY 0
```
*The encoder need to be defined and ( 48, 50, 52) comment the extra key we do not have 
```
  UI_KEYS_INIT_CLICKENCODER_LOW(50,52); // click encoder on pins 50 and 52. Phase is connected with gnd for signals.
  UI_KEYS_INIT_BUTTON_LOW(48); // push button, connects gnd to pin
  //UI_KEYS_INIT_BUTTON_LOW(5);
  //UI_KEYS_INIT_BUTTON_LOW(6);
  //UI_KEYS_INIT_BUTTON_LOW(11);
  //UI_KEYS_INIT_BUTTON_LOW(42);
  ```
  
*The encoder actions. and comment the others keys
 UI_KEYS_CLICKENCODER_LOW_REV(50,52); // click encoder on pins 50 and 52. Phase is connected with gnd for signals.
 UI_KEYS_BUTTON_LOW(48,UI_ACTION_OK); // push button, connects gnd to pin
 //UI_KEYS_BUTTON_LOW(5,UI_ACTION_NEXT); // push button, connects gnd to pin
 //UI_KEYS_BUTTON_LOW(6,UI_ACTION_PREVIOUS); // push button, connects gnd to pin
 //UI_KEYS_BUTTON_LOW(11,UI_ACTION_BACK); // push button, connects gnd to pin
 //UI_KEYS_BUTTON_LOW(42,UI_ACTION_SD_PRINT ); // push button, connects gnd to pin

2- Edit configuration.h
*LCD has SD Card support
```
     #define SDSUPPORT 1
```    
*The pin for SD Card detection is 14
```
     #define SDCARDDETECT 14
```
*We use UICONFIG_CONTROLLER not the RADDS one
```
    #define FEATURE_CONTROLLER UICONFIG_CONTROLLER
```

3-Compile/Upload    
Seems during upload sometimes buzzer is doing noise - but all is ok when upload is done   

<img src=https://github.com/luc-github/DUE-RADDS-GLCD/blob/master/Images/final.png?raw=true>
