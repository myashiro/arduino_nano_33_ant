# Arduino Nano 33 ANT
Integrates Nano 33 into nRF5 SDK environment for easy ANT+ prototyping. Includes bike power meter example code.

### About
I was supposed to spend this summer at the Kennedy Space Center interning for one of NASA's biggest contractors. Unfortunately this awesome gig was cut short due to coronavirus. This forced me to get creative in terms of making money, so in between class I worked for neighbors doing landscaping and even drove Uber in the night time. I was earning decent money but was not learning any engineering.

While all this was happening, I dreamed up an idea of developing my own bike power meter. My goal was to understand the engineering concepts that go into bike power meters inside and out - from software to hardware. And yes I admit... a part of me wanted to know my FTP.

The Arduino Nano 33 BLE struck me as an ideal starting point for this project due to its small form factor and built-in LSM9DS1 IMU. I thought I could get my prototype sending power data to my Garmin Edge 500 in no time. How naive.

I was quickly faced with a terrible problem - even though the Nano 33 uses an nRF52840, it does not natively support ANT transceiving. I scoured forums searching for a solution, but all I heard were crickets. Documentation on ANT was sparse. I realized I had to figure this out myself.

Hence the birth of Arduino Nano 33 ANT. 

### Requirements
- [Arduino Nano 33 BLE](https://amzn.to/3ggFy9u)
- [J-Link EDU Mini (recommended)](https://amzn.to/38lDKJT) or another SWD Debugger
- [nRF5 SDK](https://www.nordicsemi.com/Software-and-Tools/Software/nRF5-SDK)
- [S212 SoftDevice](https://www.thisisant.com/developer/components/nrf52832/#tab_protocol_stacks) 
- [Segger Embedded Studio](https://www.segger.com/downloads/embedded-studio)
- [nRF Connect](https://www.nordicsemi.com/Software-and-tools/Development-Tools/nRF-Connect-for-desktop)

### What's in the box
- Installation guide
- Custom board definition
- ANT+ bike power example code

## Installation

**1. nRF5 SDK**  
Download and unzip. Rename to nRF5_SDK and place it as close to your main drive as possible.
```
C/nRF5_SDK
```  

**2. S212 SoftDevice**  
Download and unzip. Copy the SoftDevice headers and hex to its respective directory within the SDK. The resulting directory tree will be as follows.  
```
nRF5_SDK
├── components
│   └── softdevice
│       └── s212
│           └── headers
│               ├── nrf52
│                   └── nrf_mbr.h
│               ├── ant_error.h
│               ├── ant_interface.h
│               ├── ant_parameters.h
│               ├── ble.h
│               ├── ble_err.h
│               ├── ble_gap.h
│               ├── ble_gatt.h
│               ├── ble_gattc.h
│               ├── ble_gatts.h
│               ├── ble_hci.h
│               ├── ble_l2cap.h
│               ├── ble_ranges.h
│               ├── ble_types.h
│               ├── nrf_error.h
│               ├── nrf_error_sdm.h
│               ├── nrf_error_soc.h
│               ├── nrf_nvic.h
│               ├── nrf_sd_def.h
│               ├── nrf_sdm.h
│               ├── nrf_soc.h
│               └── nrf_svc.h
│           └── toolchain
│           └── ANT_s212_nrf52_7.0.1.hex
```

**3. ANT License Key**  
Open nrf_sdm.h in a text editor. Uncomment the line containing ANT_LICENSE_KEY. Note this key is for non-commercial purposes only.  
```
#define ANT_LICENSE_KEY "3831-521d-7df9-24d8-eff3-467b-225f-a00e" // uncomment this line
#ifndef ANT_LICENSE_KEY
   #error "You must obtain a valid license key to use ANT. You may use the evaluation key for non commercial use only by uncommenting it above this error. Commercial use license keys are available from ANT Wireless."
#endif
```

**4. Board Definition**  
Open boards.h in a text editor. Add the Arduino Nano 33 ANT board definition. 
```
#elif defined(BOARD_CUSTOM)
  #include "custom_board.h"
#elif defined(BOARD_ARDUINO_NANO_33_ANT) // add this line
  #include "arduino_nano_33_ant.h" // and this line
#else
#error "Board is not defined"
```  
Then download arduino_nano_33_ant.h and place it in nRF5_SDK/components/boards.  

**5. ANT Network Keys**  
Open ant_key_manager_config.h in a text editor. Add the public ANT+ key and ANT-FS key. These can be found [here](https://www.thisisant.com/developer/ant-plus/ant-plus-basics/network-keys).  
```
#ifndef ANT_PLUS_NETWORK_KEY
    #define ANT_PLUS_NETWORK_KEY    {0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0} // add the ANT+ key here
#endif //ANT_PLUS_NETWORK_KEY

#ifndef ANT_FS_NETWORK_KEY
    #define ANT_FS_NETWORK_KEY      {0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0, 0x0} // add the ANT-FS key here
#endif // ANT_FS_NETWORK_KEY
```  

**6. nRF Connect**  
Install with default settings (this should automatically install J-Link tools). Install the Programmer app inside of nRF Connect. This will be used to flash SoftDevice and Application stacks.

## Connecting the J-Link  
Connect the J-Link debugger to your Nano 33 using the following pinout diagram. Just match the numbers. Pin 1 is located on the bottom left-hand corner of the J-Link Mini, and is denoted by the number "1".  
<img src="https://github.com/drewvigne/arduino_nano_33_ant/blob/master/images/pinout_diagram.jpg" height="500">

The end result should look like this.  
<img src="https://github.com/drewvigne/arduino_nano_33_ant/blob/master/images/jlink_connection.jpg" height="500">  

## Bike Power TX Example

**1. Installing**  
Download the nano33 directory containing the bike power example and config files. Place this in your SDK at the following directory.  
```
C/nRF5_SDK/examples/ant/ant_plus/ant_bpwr/bpwr_tx/nano33
```

**2. Building the Application**  
Open the Nano33 emproject file in Segger Embedded Studio. Navigate to the toolbar and select "Build" and build "ant_bpwr_tx_nano33_s212".  

**3. Flashing in nRF Connect**  
Open the programmer app in nRF Connect. Load the Bike Power Application and S212 SoftDevice hex files. Erase and write.  
<img src="https://github.com/drewvigne/arduino_nano_33_ant/blob/master/images/nrf_connect.PNG" height="500">  
Boom. Done.  

**4. Using the Simulator**  
Currently the board definition file uses 3 Analog Pins as buttons. Buttons 1 and 2 increase or decrease the simulator output power. Button 3 responds to calibration requests. See the demo on my youtube channel [here](https://www.youtube.com/watch?v=6V0DYIKVKSc&t=37s).

## Resources  
- [U-Blox NINA-B3 Datasheet](https://www.u-blox.com/sites/default/files/NINA-B3_DataSheet_%28UBX-17052099%29.pdf) (the nRF52840 variant used by Nano 33)  
- [Nano 33 Schematic](https://content.arduino.cc/assets/NANO33BLE_V2.0_sch.pdf)  
- [U-Blox nRF5 Guide](https://www.u-blox.com/sites/default/files/Add-NordicSemiconductorDFU-to-SDKexample_AppNote_%28UBX-19050198%29.pdf)  
- [Another NINA-B3 Guide](https://www.u-blox.com/sites/default/files/NINA-B3_SIM_%28UBX-17056748%29.pdf)  

## Acknowledgments
The start of this project was a slow but steady learning curve. My learning was accelerated by Curtis Melainey and Gabor Ziegler, who both kindly shared their knowledge of the ANT protocol.
