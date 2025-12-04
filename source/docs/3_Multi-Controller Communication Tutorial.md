# 3. Multi-Controller Communication Tutorial

## 3.1 Master-Slave Communication Principle

This section introduces users to the master-slave communication relationship of the uHand UNO robotic hand (hereinafter referred to as uHand UNO) when communicating with different devices, such as Arduino or PC. It explains how the uHand UNO functions as a slave device to communicate with other devices, and how those devices act as masters to control the uHand UNO.

In this chapter, the uHand UNO is used exclusively as a slave device, transmitting and receiving data with other devices via the UART serial interface.

### 3.1.1 Master-Slave Relationship

In a master-slave control system, the uHand UNO functions as the slave device, while other microcontrollers or similar devices act as the master.

### 3.1.2 Functions of uHand UNO as a Slave Device

1)  Receiving and Parsing Signals from the Master

The uHand UNO waits for serial port signals. When data is received through the serial port, it parses the data according to the communication protocol, and can then call the corresponding functions based on the information contained in the data.

2)  Invoking uHand UNO Functions Based on Received Data

Once a signal is parsed, the corresponding uHand UNO functions are executed, such as controlling servo movement, reading servo angles, controlling the buzzer, or controlling the RGB light.

3)  Data Packaging and Feedback

When a read command is received, the corresponding read function is called. The retrieved data is then packaged into a data packet according to the communication protocol and sent back to the master device.

### 3.1.3 Other Devices as the Master

1)  Command Packaging and Transmission

The master device must package control commands and data into data packets according to the communication protocol, then send them to the slave device.

2)  Control Coordination

The master device should manage the overall coordination of the system, ensuring that communication and operations between the uHand UNO and other devices are conflict-free and remain in optimal working condition.

3)  Data Reception

When reading servo status, the master device sends a read command and then receives the status data sent by the uHand UNO. It must ensure the integrity and accuracy of the data, parse the data packet, and extract the useful information.

### 3.1.4 Hardware Connection

Connect the RXD, TXD, and GND pins of the USB adapter to the IO12, IO10, and GND ports of the Arduino UNO expansion board, respectively, using Dupont wires. Then insert the USB adapter into the computer to complete the setup.

<img class="common_img" src="../_static/media/section_13_Master-Slave Communication Principle\media\image2.png" style="width:500px"  />

### 3.1.5 Data Transmission Format

The default data transmission format for the uHand UNO UART serial port is:

| Baud rate | 9600 |
| :-------: | :--: |
| Data bit  |  8   |
| Checksum  | None |
| Stop bit  |  1   |

### 3.1.6 Communication Protocol

| Frame Header | Function Code | Data Length | Data Info | **Checksum** |
| :----------: | :-----------: | :---------: | :-------: | :----------: |
|  0xAA 0x77   |     func      |     len     |   data    |    check     |

1.  **Frame Header:** Indicates the start of data transmission; the frame header is fixed at 2 bytes in length.

2.  **Function Code:** Specifies the purpose of the data frame.

3.  **Data Length:** Indicates the length of the subsequent data.

4.  **Data Information:** Contains the transmitted data.

5.  **Checksum:** Calculated by taking the bitwise inverse of (Function Code + Data Length + Data) and then extracting the lower byte.

### 3.1.7 Important Notes

1)  The power supplies for the master device and the uHand UNO can be different; however, they must share a common ground connection to ensure stable communication levels.

2)  When wiring the devices, make sure to cross-connect the UART serial port TX and RX pins; otherwise, communication will fail.

## 3.2 Color Recognition

### 3.2.1 Arduino UNO

* **Project Process**

<img class="common_img" src="../_static/media/section_14_01 Color Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_14_01 Color Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1) Locate the **ColorDetect_RGB/ColorDetect_RGB.ino** program file in the same directory as this document.

<img class="common_img" src="../_static/media/section_14_01 Color Recognition/media/image3.png" style="width:500px"  />

2)  Connect the Arduino controller to the computer using a USB Type-B cable.

<img class="common_img" src="../_static/media/section_14_01 Color Recognition/media/image4.png" style="width:500px"  />

3)  In the Arduino IDE, click the **Select Board** option. The software will automatically detect the connected Arduino serial port, then click to connect.

<img class="common_img" src="../_static/media/section_14_01 Color Recognition/media/image5.png" style="width:500px"  />

4)  Then click the upload button to upload the program to the Arduino. Wait for the upload to complete.

<img class="common_img" src="../_static/media/section_14_01 Color Recognition/media/image6.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_14_01 Color Recognition/media/image7.png" style="width:500px"  />

* **Project Outcome**

The WonderCam vision module will automatically switch to **Color Learning** mode. First, prepare the red, green, and blue colors, and then proceed with the learning process for each one. Once the learning is complete, the RGB light will illuminate the corresponding color when the module detects it again.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts serial communication, sets the vision module to color detection mode, initializes the LED matrix module, and updates and displays the current pixel status.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  // Serial.println(a);
  wc.changeFunc(APPLICATION_COLORDETECT);
  pixels.begin();
  pixels.clear(); // Set all pixel colors to 'off'
  pixels.show();
}
```

**2. Main Function: loop()**

The main function is responsible for detecting color and providing the corresponding feedback. It first checks whether any color has been detected. If so, it then checks if a specific color with a particular ID is detected.

Next, based on the detected color ID, different actions are executed. Three cases are defined, including Case 1, Case 2, and Case 3, each corresponding to a color: red, green, and blue.

After each case, the show_color function is called to display the respective color, and the return statement is used to exit the current function execution.

```cpp
void loop() {
  wc.updateResult();
  if (wc.anyColorDetected()) {
    for (int i = 1; i < 4; ++i) {
      if (wc.colorIdDetected(i)) {
        switch (i) {
          case 1:
            show_color(255, 0, 0);
            return;
          case 2:
            show_color(0, 255, 0);
            return;
          case 3:
            show_color(0, 0, 255);
            return;
          default:
            return;
        }
      }
    }
```

### 3.2.2 microbit

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Click icon <img src="../_static/media/section_15_02 Color Recognition\media\image3.png" style="width:50px" /> on the left bottom of the interface, then click **Connect Device**, and follow the on-screen instructions to connect the micro:bit controller.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image4.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image5.png" style="width:500px"  />

2)  Once connected, click the **Download** button in the bottom-left corner to flash the program to your micro:bit.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image6.png" style="width:500px"  />

3)  After the download is complete, the indicator light will stop flashing rapidly and remain steadily lit. At the same time, a download completion message will appear on the programming interface.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image7.png" style="width:500px"  />

* **Project Outcome**

The WonderCam vision module will automatically switch to **Color Learning** mode. First, prepare the red, green, and blue colors, and then proceed with the learning process for each one. Once the learning is complete, the RGB light will illuminate the corresponding color when the module detects it again, and the LED matrix will display the first letter of the corresponding color's English name.

* **Program Brief Analysis**

1)  The program first sets the initial state of the device upon startup. Here, the transmission address of WonderCam is configured, and its function is switched to color recognition.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image8.png" style="width:500px"  />

2)  Through a continuous loop, the program keeps fetching the recognition results from the vision module. Color blocks are recognized only after learning the color beforehand. For the learning steps, refer to **[2. Function Instruction \\ 2.1 Color Recognition](2_Function Instruction.md#Color-Recognition)**.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image9.png" style="width:500px"  />

3)  Next, if and else if statements are used to set different feedback for different colors. If the vision module detects ID1 for red and the area of red is greater than 400, the RGB light on the controller is set to red, and the LED screen on the controller displays the string R.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image10.png" style="width:500px"  />

4)  Feedback for other colors is similarly set using else if statements.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image12.png" style="width:500px"  />

5)  If no color block already learned is detected, the LED screen will not display any feedback.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image13.png" style="width:500px"  />

* **Feature Extensions**

**1. Add New Recognizable Color**

1.  Locate the section of code that recognizes color ID4, and click **+** at the end to add a new condition.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image14.png" style="width:500px"  />

2.  Right-click the Detected color ID4 code block and copy it. Then, right-click the If the width × height of color ID4 \> 400 code block and copy it.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image15.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_15_02 Color Recognition\media\image16.png" style="width:500px"  />

3.  Place the copied code into the new condition, and change the recognized color ID to 5.

<img class="common_img" src="../_static/media/section_15_02 Color Recognition/media/image17.png" style="width:500px"  />

### 3.2.3 STM32F103

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

First, install the 32-bit microcontroller onto the open-source controller. Then, connect the WonderCam to the 4-pin connector, with the other end plugged into the I2C port on the controller as indicated by the arrows in the image below.

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image2.png" style="width:500px"  />

Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image3.png" style="width:500px"  />

* **Program Download**

1)  Connect the USB downloader to the controller using Dupont wires, and plug the USB end of the downloader into the computer.

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image4.png" style="width:500px"  />

2)  On the computer, open the FlyMcu tool. The application file for FlyMcu is located in the directory **[2. Software & Firmware/03 FlyMcu]().**

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image5.png" style="width:500px"  />

3)  Select the port for the controller connection and set the baud rate to 115200. **Do not choose COM1, as it is the system communication port.**

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image6.png" style="width:500px"  />

4)  Click the button <img src="../_static/media/section_16_03 Color Recognition\media\image7.png" style="width:50px" /> to choose the program file to be flashed. Once selected, check the "**Auto Reload Before Program**" option.

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image8.png" style="width:500px"  />

The program file is located in the directory **02 Program\Obj**.

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image9.png" style="width:500px"  />

5. Take down the jumper cap to the controller, press the **RST** reset button on the STM32 core board, turn on the power switch on the controller, and click **Start ISP**.

   <img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image10.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image12.png" style="width:500px"  />

6)  After the program is successfully flashed, reconnect the jumper cap to the controller, press the **RST** button on the controller, and restart the controller.

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image13.jpeg" style="width:500px"  />

* **Project Outcome**

1)  Download the **OpenArmSTM32.hex** file from the Obj folder in the "**[STM32 Example Source Code\5. Color Recognition]()**" directory to the STM32 microcontroller.

2)  Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image14.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) Initialize the system clock, I2C interface, and serial port.

```c
int main(void)
{
    uint8_t x;
    SystemInit(); 		// Initialize system clock to 72MHz (SYSCLK_FREQ_72MHz)
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);	// Set NVIC interrupt priority group 2: 2 bits for preemption priority, 2 bits for sub priority
    InitDelay(72); 		// Initialize delay function
    IIC_init();		    // I2C (Inter-Integrated Circuit) initialization
    DelayMs(100);       // Delay 100 milliseconds
    Usart1_Init();      // USART1 initialization
    DelayMs(1000);      // Delay 1000 milliseconds
}
```

\(2\) After initialization, the serial port will print the "**start**" string. The WonderCam will then switch to face recognition mode, and the fill light will be turned on.

```c
	printf(" ");
	printf("start\r\n");
	changeFunc(APPLICATION_COLORDETECT);
```

\(3\) In the loop, update and retrieve the recognition results. When a color is detected, the system will loop 4 times to verify the recognized color. If a color is detected, the color ID will be printed to the serial port. If no color is detected after 4 attempts, the serial port will print "**Color not recognized**."

```c
	while(1)
	{
  updateResult();
  if (anyColorDetected()) {
    for (int i = 1; i < 4; ++i) {
      if (colorIdDetected(i)) {
		printf("颜色ID:%d\r\n",i);
      }
    }
  } else {
    printf("未识别到颜色\r\n");
  }
  DelayMs(100);
	}
}
```

* **Feature Extensions**

```py
  if (anyColorDetected()) {
    for (int i = 1; i < 4; ++i) {
      if (colorIdDetected(i)) {
		printf("颜色ID:%d\r\n",i);
      }
    }
  } else {
    printf("未识别到颜色\r\n");
  }
```

When determining the color, first check if a previously learned color has been detected, and then check the color ID, which can speed up the detection process.

<img class="common_img" src="../_static/media/section_16_03 Color Recognition\media\image19.png" style="width:500px"  />

After reading the color block data, a structure will be returned with the following fields: x and y represent the coordinates of the color block's bounding box on WonderCam, with the top-left corner of the screen as the origin. w and h represent the width and height of the bounding box.

### 3.2.4 STM32F407

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam with the 4-pin connector, and plug the other end into the I2C interface on the STM32 controller.

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Connect the Type-C cable to the Type-C port on the STM32 controller. Make sure to use the UART1 port as shown in the figure below and plug the other end into the USB port of your computer.

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image3.png" style="width:500px"  />

2)  Open the **Device Manager** on your computer and check the COM port number under the **Ports** section.

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image4.png" style="width:500px"  />

3)  Launch the ATK-XISP software in the directory **[2. Software & Firmware/04 ATK-XISP]()**, select the corresponding COM port, and set the baud rate to 115200.

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image5.png" style="width:500px"  />

4)  Configure the software as shown in the image below.

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image6.png" style="width:500px"  />

5)  click the **File** button and select the **"[02 Program\MDK-ARM\color_identification\color_identification.hex]()**" file for programming.

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image7.png" style="width:500px"  />

6)  Click the **Start Programming** button on the interface to flash the generated **.hex** file to the STM32 controller. Wait for the programming to complete successfully.

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image8.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image9.png" style="width:500px"  />

* **Project Outcome**

Connect the controller to the computer using a USB data cable. Then, open any serial assistant, select the appropriate COM port, set the baud rate to 115200, and open the serial port to receive data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image10.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main program, first initialize the WonderCam object. Call the `ChangeFunc()` function to switch WonderCam to color recognition mode, and then turn off the vision module's fill light.

```c
	camset.Init(&camset);
	camset.ChangeFunc(&camset, APPLICATION_COLORDETECT);
	camset.SetLed(&camset,LED_OFF);
```

\(2\) In the while loop, call the `UpdateResult()` function to update the recognition results of WonderCam and check if any color has been detected.

```c
  while (1)
  {
    /* USER CODE END WHILE */
	
    /* USER CODE BEGIN 3 */
		
		camset.UpdateResult(&camset);
		if (colordetectfunc.anyColorDetected()) {
```

\(3\) Once a color is detected, use a for loop to check the recognized color ID. This program supports recognizing IDs 1 to 3. Then, print the detected color ID to the serial port in the format: "**Color ID:** " + detected color ID.

```c
			for (int i = 1; i < 4; ++i) {
				if (colordetectfunc.colorIdDetected(i)) {
					printf("颜色ID(color ID):%d\r\n",i);
				}
```

\(4\) If no color is detected, print "**No color detected**" to the serial port.

```c
		else
			printf("未识别到颜色(No color recognized.)\r\n");
		
		HAL_Delay(200);
  }
```

* **Feature Extensions**

```c
		if (colordetectfunc.anyColorDetected()) {
			for (int i = 1; i < 4; ++i) {
				if (colordetectfunc.colorIdDetected(i)) {
					printf("颜色ID(color ID):%d\r\n",i);
				}
			}
		}
		else
			printf("未识别到颜色(No color recognized.)\r\n");
		
		HAL_Delay(200);
  }
```

When determining the color, first check if a previously learned color has been detected, and then check the color ID, which can speed up the detection process.

<img class="common_img" src="../_static/media/section_17_04 Color Recognition\media\image16.png" style="width:500px"  />

After reading the color block data, a structure will be returned with the following fields: x and y represent the coordinates of the color block's bounding box on WonderCam, with the top-left corner of the screen as the origin. w and h represent the width and height of the bounding box.

### 3.2.5 Raspberry Pi

* **Project Process**

<img class="common_img" src="../_static/media/section_18_05 Color Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam to any I2C port on the Raspberry Pi expansion board.

<img class="common_img" src="../_static/media/section_18_05 Color Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1.  After turning on the Raspberry Pi, connect the computer to the corresponding Wi-Fi.

2.  Open the remote desktop software<img src="../_static/media/section_18_05 Color Recognition\media\image3.png" style="width:50px" />.

3.  In the search bar, enter "192.168.149.1" and press **Enter** to access the Raspberry Pi desktop.

<img class="common_img" src="../_static/media/section_18_05 Color Recognition\media\image4.png" style="width:500px"  />

4.  In the Username field, enter **pi** and in the Password field, enter **raspberry**. Check the **Remember password** box, then click the **OK** button. You will then see the Raspberry Pi desktop opened remotely.

<img class="common_img" src="../_static/media/section_18_05 Color Recognition\media\image5.png" style="width:500px"  />

The following image shows successful desktop access:

<img class="common_img" src="../_static/media/section_18_05 Color Recognition\media\image6.png" style="width:500px"  />

5.  Click the red box in the image below to transfer files.

<img class="common_img" src="../_static/media/section_18_05 Color Recognition\media\image7.png" style="width:500px"  />

6.  Click the button indicated by the red arrow in the image below, then select the file.

<img class="common_img" src="../_static/media/section_18_05 Color Recognition\media\image8.png" style="width:500px"  />

7.  Choose the **color_discrimination.py** file and wait for the transfer to complete.

<img class="common_img" src="../_static/media/section_18_05 Color Recognition\media\image9.png" style="width:500px"  />

* **Project Outcome**

\(1\) Enter the command to navigate to the desktop.

```py
cd Desktop/
```

\(2\) Enter the command to run the program.

```py
python3 color_discrimination.py
```

\(3\) After the program starts, the WonderCam vision module will automatically switch to **Color Learning** mode. First, prepare the red, green, and blue colors, and then proceed with the learning process for each one. Wait for the learning to complete. Once the module detects the color again, it will print the color's ID and coordinates.

* **Program Brief Analysis**

**1. Import Libraries**

Import the **time** library to control system delays, the **struct** library to parse data transmitted by the vision module, and the **smbus2** library for I2C communication with the WonderCam vision module.

```py
import time
import struct
from smbus2 import SMBus, i2c_msg
```

**2. Program Brief Analysis**

1. In the main program, a communication object for the vision module is created. The cam.set_func() function is used to switch the WonderCam to color recognition mode.

   ```py
   if __name__ == "__main__":
       cam = WonderCam()
   
       # """颜色识别"""
       cam.set_func(WONDERCAM_FUNC_COLOR_DETECT)
   ```

2. Once initialization is complete, the program enters a loop. The cam.update_result() function is called to update the data received from WonderCam, and the cam.set_led() function is used to turn on the vision module's fill light.

   ```py
       while True:
           try:
               cam.update_result()
               cam.set_led(True)
   ```

3. Next, the cam.is_color_blob_detected() function is used to check if any learned color is detected. If a color is detected, the program iterates through all colors, identifies the detected color ID, and prints the color data.

   ```py
               if cam.is_color_blob_detected():
                   for i in range(1,8):
                       if cam.get_color_blob(i):
                           print("识别到颜色ID： %d" %i)
                           print(cam.get_color_blob(i))
               else:
                   print("未识别到颜色")
               time.sleep(0.5)
           except KeyboardInterrupt:
               break
           except Exception as e:
               print(e)
               continue
   ```

### 3.2.6 ESP32 (Arduino)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_20_06.1 Color Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_20_06.1 Color Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Locate the **ColorDetect_RGB/ColorDetect_RGB.ino** program file in the same directory as this document.

<img class="common_img" src="../_static/media/section_20_06.1 Color Recognition/media/image3.png" style="width:500px"  />

2)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_20_06.1 Color Recognition/media/image4.png" style="width:500px"  />

3)  Click the **Select Board** option, search for **ESP32 Dev Module**, and click **OK**.

<img class="common_img" src="../_static/media/section_20_06.1 Color Recognition/media/image5.png" style="width:500px"  />

4)  Click <img src="../_static/media/section_20_06.1 Color Recognition/media/image6.png" style="width:50px" /> to download the program to the ESP32, and wait for the download to complete.

<img class="common_img" src="../_static/media/section_20_06.1 Color Recognition/media/image7.png" style="width:500px"  />

* **Project Outcome**

When WonderCam detects a learned color, it will print the recognized color's ID to the serial port.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication and sets the vision module to color recognition mode.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_COLORDETECT);
}
```

**2. Main Function: loop()**

The main function is responsible for detecting color and providing the corresponding feedback. It first checks whether any color has been detected. If so, it then checks if a specific color with a particular ID is detected.

Next, based on the detected color ID, the result is printed to the serial port.

```cpp
void loop() {
  wc.updateResult();
  if (wc.anyColorDetected()) {
    for(int i = 1; i < 6; i++){
      if(wc.colorIdDetected(i)){
        Serial.print("color detect ID:");
        Serial.println(i);
      } 
    }
  }
  delay(300);
}
```

### 3.2.7 ESP32 (Python)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_21_06 Color Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_21_06 Color Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Open the Hiwonder Python editor software<img src="../_static/media/section_21_06 Color Recognition\media\image3.png" style="width:100px" />。

2)  Drag the **main.py** and **WonderCam.py** files from the **02 Program** folder in the same directory into the Wonder Python Editor.

<img class="common_img" src="../_static/media/section_21_06 Color Recognition\media\image4.png" style="width:500px"  />

3)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_21_06 Color Recognition\media\image5.png" style="width:500px"  />

4)  Click the Connect button <img src="../_static/media/section_21_06 Color Recognition\media\image6.png" style="width:50px" /> in the top menu. When successfully connected, the icon will turn green<img src="../_static/media/section_21_06 Color Recognition\media\image7.png" style="width:50px" />.

5)  After connecting, click the Download button <img src="../_static/media/section_21_06 Color Recognition\media\image8.png" style="width:50px" /> in the menu to upload the program to the ESP32 controller. Wait for the message box at the bottom of the editor to confirm the download is complete.

<img class="common_img" src="../_static/media/section_21_06 Color Recognition\media\image9.png" style="width:500px"  />

6)  Finally, click the Restart button <img src="../_static/media/section_21_06 Color Recognition\media\image10.png" style="width:50px" /> to reboot the ESP32 controller. The program will start running automatically.

* **Project Outcome**

When WonderCam detects a learned color, it will print the recognized color's ID to the serial port.

* **Program Brief Analysis**

1. First, initialize the I2C hardware interface using pins IO16 and IO17, initialize the WonderCam, and switch its function mode to color recognition.

   ```py
   i2c = I2C(0, scl=Pin(16), sda=Pin(17), freq=400000)
   colordetect = WonderCam(i2c)
   
   colordetect.set_func(WONDERCAM_FUNC_COLOR_DETECT)
   ```

2. In the loop, first update and retrieve the recognition results. Use the `is_color_blob_detected()` function to check for color IDs from ID1 to ID5, and then print the detected color ID to the serial port.

   ```py
   while True:
     colordetect.update_result()
     for color_id in range(1,6):
       if colordetect.is_color_blob_detected(color_id):
         print("color detect ID:",color_id)
       time.sleep_ms(200)
   ```

### 3.2.8 C51

* **Project Process**

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

\(1\) Install the 51 microcontroller onto the open-source controller.

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image2.png" style="width:500px"  />

\(2\) Connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the open-source controller. The ports indicated by the arrows in the image below are all I2C ports.

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image3.png" style="width:500px"  />

\(3\) Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image4.jpeg" style="width:500px"  />

The table below shows the wiring instructions for the USB-to-serial adapter and the open-source controller.

| **USB-to-Serial Adapter** | **Open-Source Controller** |
| :-----------------------: | :------------------------: |
|          5V(VCC)          |             5V             |
|          RX(RXD)          |             TX             |
|          TX(TXD)          |             RX             |
|            GND            |            GND             |

* **Program Download**

1)  Connect the USB-to-serial adapter to the computer.

2)  After confirming that the serial port driver is correctly installed, open **This PC** on the computer desktop. Then, click **Properties** -\> **Device Manager** and check the corresponding serial port number for the controller. **COM port numbers are not unique, and the device port ends with "CH340".**

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image5.png" style="width:500px"  />

3)  Remove the jumper cap from the 51 microcontroller and turn on the power switch to enter programming mode.

> [!NOTE]
>
> **Note:** If the controller is powered on first and then the jumper cap is removed, the controller needs to be turned off and then back on to enter programming mode.

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image6.png" style="width:500px"  />

Open the AIapp-ISP-v6.95E download tool in **[2. Software & Firmware /05 AIapp-ISP-v6.95E]()**:

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image7.png" style="width:300px" />

The AIapp-ISP-v6.95E configuration is shown as follows:

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image8.png" style="width:700px"  />

Click “Open file” and find the hex file in “**02 Program\Objects.**”

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image9.png" style="width:500px"  />

After opening the program file, click “**Download/Program**” to download the program to the 51 microcontroller.

> [!NOTE]
>
> **Note:** When downloading the program, you need to remove the jumper cap from the 51 microcontroller first. Once the AIapp-ISP-v6.95E tool in the lower right corner displays the message indicating it is detecting the target microcontroller, you can insert the jumper cap back in place. At this point, the program will start programming.

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image10.png" style="width:700px"  />

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image11.png" style="width:700px"  />

* **Project Outcome**

The WonderCam vision module will automatically switch to **Color Learning** mode. First, prepare the red, green, and blue colors, and then proceed with the learning process for each one. Wait for the learning process to complete. Once the module detects the color again, the serial assistant will print the current recognized color ID, position, and size.

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image12.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main function, the initialization of I2C and serial communication is performed first.

```py
void main(void)
{ 
    int i;
	P0M1=P0M0=P1M1=P1M0=P2M1=P2M0=P3M1=P3M0=P4M1=P4M0=P5M1=P5M0=P6M1=P6M0=P7M1=P7M0= 0;	
    I2C_Init(); 
    InitUart1();
    EA = 1;              // Enable global interrupt    
```

\(2\) After initialization, the serial port prints **start**, and the WonderCam's function is set to color recognition.

```py
	Delay_ms(1000);
	printf("start\r\n");
	changeFunc(APPLICATION_COLORDETECT);
	setLed(true);
```

\(3\) In the loop, the recognition results of the vision module are updated and retrieved. The `anyColorDetected()` function is used to check if a color has been detected. Once a color is detected, a structure p is created to store the color data, and a for loop is used to check the current detected color ID.

```py
	for(;;)
	{
  updateResult();
  if (anyColorDetected()) { //Whether a color is detected(是否识别到了颜色)
		struct WonderCamColorDetectResult xdata p;
    for (i = 1; i < 8; ++i) {
```

\(4\) Once color ID i is detected, the serial port prints the current detected color ID. Then, the `colorId()` function stores the color data of ID i in structure p, and the position coordinates, size, and width of the color are printed to the serial port. Finally, if no color is detected, the serial port prints **No color detected**.

```py
      if (colorIdDetected(i)) {      
        printf("识别到颜色ID:%d\r\n",i);    
			   }
			if(colorId(i,&p)){
				printf("位置坐标:(%d,%d)\r\n",p.x,p.y);
				printf("大小宽高: %u*%u\r\n",p.w,p.h);
        }
      }
    }
   else {
    printf("未识别到颜色\r\n");
  }
  Delay_ms(200);
  }
}
```

* **Feature Extensions**

```py
      if (colorIdDetected(i)) {      
        printf("识别到颜色ID:%d\r\n",i);    
			   }
			if(colorId(i,&p)){
				printf("位置坐标:(%d,%d)\r\n",p.x,p.y);
				printf("大小宽高: %u*%u\r\n",p.w,p.h);
        }
      }
    }
   else {
    printf("未识别到颜色\r\n");
  }
```

First, check if a color has been detected, then verify the color ID. If a color is detected, the color ID, position coordinates, and size (width and height) are printed.

<img class="common_img" src="../_static/media/section_22_07 Color Recognition/media/image17.png" style="width:500px"  />

After reading the color block data, a structure will be returned with the following fields: x and y represent the coordinates of the color block's bounding box on WonderCam, with the top-left corner of the screen as the origin. w and h represent the width and height of the bounding box.

## 3.3 Facial Recognition

### 3.3.1 Arduino UNO

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_23_01 Facial Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_23_01 Facial Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

\(1\) Locate the '**FaceDetect_ID/FaceDetect_ID.ino**' program file in the same directory as this document.

<img class="common_img" src="../_static/media/section_23_01 Facial Recognition/media/image3.png" style="width:500px"  />

\(2\) Connect the Arduino controller to the computer using a USB Type-B cable for UNO.

<img class="common_img" src="../_static/media/section_23_01 Facial Recognition/media/image4.png" style="width:500px"  />

\(3\) In the Arduino IDE, click the **Select Board** option. The software will automatically detect the connected Arduino serial port, then click to connect.

<img class="common_img" src="../_static/media/section_23_01 Facial Recognition/media/image5.png" style="width:500px"  />

Then click the upload button <img src="../_static/media/section_23_01 Facial Recognition/media/image6.png" style="width:50px" /> to upload the program to the Arduino. Wait for the upload to complete.

<img class="common_img" src="../_static/media/section_23_01 Facial Recognition/media/image7.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_23_01 Facial Recognition/media/image8.png" style="width:500px"  />

* **Project Outcome**

When the module detects a learned face, the matrix display will show the face ID. At the same time, open the serial monitor in the Arduino IDE, where the following message will be printed:

<img class="common_img" src="../_static/media/section_23_01 Facial Recognition/media/image9.png" style="width:500px"  />

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts serial communication, sets the vision module to face detection mode, initializes the dot matrix module, and updates and displays the current pixel status.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_FACEDETECT);
  wc.setLed(true);
  matrixLed.setBrightness(3);
  matrixLed.clearScreen();

  uint8_t drawBuffer[16] = {
    0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00
    , 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00
  };
  for (int i = 0; i < 16; i++) {
    drawBuffer[i] = 0xff;
    matrixLed.drawBitmap(0, 0, 16, drawBuffer);
    delay(20);
  }
  delay(500);
  matrixLed.clearScreen();
}
```

**2.Main Function: loop()**

The main function is used to detect faces and provide corresponding feedback. First, check if a face is detected. If a face is detected, verify if a specific face ID has been recognized.

Next, based on the detected face ID, display the current face ID on the matrix module and print "**Learned face detected**" to the serial port.

```cpp
void loop() {
  wc.updateResult();

  if (wc.anyFaceDetected()) { //是否识别到了人脸
    for (int i = 1; i < 6; i++)
    {
      if (wc.faceOfIdDetected(i)) {
        Serial.println("识别到特定人脸");
        char str[20] = "ID:";
        str[4] = '\0';
        str[3] = 48 + i;
        int offset = 0;
        while (offset > (-(6 * 4))) {
          matrixLed.drawStr(offset, 8, str);
          offset -= 1;
          delay(200);
        }
      }
    }
  }
  delay(200);
}
```

### 3.3.2 micro：bit

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Click the icon <img src="../_static/media/section_24_02 Facial Recognition/media/image3.png" style="width:50px" /> on the bottom left of the interface, then click **Connect Device**, and follow the on-screen instructions to connect the micro:bit controller.

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image4.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image5.png" style="width:500px"  />

2)  Once connected, click the **Download** button in the bottom-left corner to flash the program to your micro:bit.

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image6.png" style="width:500px"  />

3)  After the download is complete, the indicator light will stop flashing rapidly and remain steadily lit. At the same time, a download completion message will appear on the programming interface.

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image7.png" style="width:500px"  />

* **Project Outcome**

When running the program for the first time, the face needs to be trained. Once the training is complete, upon detecting a face again, the expansion board will randomly flash lights, play music, and the LED matrix will display the corresponding face ID.

* **Program Brief Analysis**

1)  The program first sets the initial state of the device upon startup. Here, the transmission address of WonderCam is configured, and its function is switched to face recognition.

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image8.png" style="width:500px"  />

2)  A continuous loop is used to constantly retrieve the recognition results from the vision module. If a face is detected, the value of the no face count variable is set to 0, and a specific audio is played. This step is designed to prevent misrecognition by the vision module, meaning that if no face is detected, the following statements will not execute.

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image9.png" style="width:500px"  />

3)  When face ID 1 is recognized, the RGB lights will turn on as feedback. The RGB color components are set randomly, so the color that lights up will be different each time. Then, the LED screen on the controller will display the number "1," and the no face count variable will be set to 5, signaling the end of the current recognition feedback and allowing the next recognition to begin.

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image10.png" style="width:500px"  />

4)  The feedback for other face IDs is set in a similar manner. The specific program is shown in the diagram below:

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image12.png" style="width:500px"  />

5)  When the recognition step exits, the **no face count** variable will be 5. At this point, the value will start to decrease until it reaches less than 0, allowing the recognition process to continue. If no face is detected, both the RGB lights and the LED will be turned off.

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image13.png" style="width:500px"  />

* Feature Extensions

**1. Add New Recognizable Face**

\(1\) Locate the section of the code where face ID 3 is recognized, and click **+** at the end to add a new condition.

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image14.png" style="width:500px"  />

\(2\) In the new condition, change the recognized face ID to 4 and modify the displayed number to 4.

<img class="common_img" src="../_static/media/section_24_02 Facial Recognition/media/image15.png" style="width:500px"  />

### 3.3.3 STM32F103

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

First, install the 32-bit microcontroller onto the open-source controller. Then, connect the WonderCam to the 4-pin connector, with the other end plugged into the I2C port on the controller as indicated by the arrows in the image below.

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image2.png" style="width:500px"  />

Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image3.png" style="width:500px"  />

* **Program Download**

1)  Connect the USB downloader to the controller using Dupont wires, and plug the USB end of the downloader into the computer.

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image4.png" style="width:500px"  />

2)  On the computer, open the FlyMcu tool. The application file for FlyMcu is located in the directory **2. Software & Firmware/03 FlyMcu**.

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image5.png" style="width:500px"  />

3)  Select the port for the controller connection and set the baud rate to 115200. **Do not choose COM1, as it is the system communication port.**

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image6.png" style="width:500px"  />

4)  Click the button <img src="../_static/media/section_25_03 Facial Recognition\media\image7.png" style="width:50px" /> to choose the program file to be flashed. Once selected, check the "**Auto Reload Before Program**" option.

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image8.png" style="width:500px"  />

The program file is located in the directory **02 Program\Obj**.

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image9.png" style="width:500px"  />

5. Take down the jumper cap to the controller, press the **RST** reset button on the STM32 core board, turn on the power switch on the controller, and click “**Start ISP(P)”.**

   <img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image10.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image12.png" style="width:500px"  />

6)  After the program is successfully flashed, reconnect the jumper cap to the controller, press the **RST** button on the controller, and restart the controller.

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image13.jpeg" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image14.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) First, initialize the system clock, I2C interface, and serial port.

```c
int main(void)
{
uint8_t x;
	SystemInit(); 		//Initialize system clock to 72MHz (系统时钟初始化为72M)	  SYSCLK_FREQ_72MHz
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);	//Set NVIC interrupt priority group 2: 2 bits for preemption priority, 2 bits for response priority (设置NVIC中断分组2:2位抢占优先级，2位响应优先级)
	InitDelay(72); 		//Initialize delay function (初始化延时函数)
	IIC_init();		//IIC initialization (IIC初始化)
	DelayMs(100);
	Usart1_Init(); //USART1 initialization (串口初始化)
}
```

\(2\) After initialization, the serial port will print the start string. The WonderCam will then switch to face recognition mode.

```py
	printf(" ");
	printf("start\r\n");
	changeFunc(APPLICATION_FACEDETECT);
```

\(3\) Update and retrieve the recognition results, check if a face is detected, and then use a for loop to verify the detected face ID.

```py
	while(1)
	{
		updateResult();

		if (anyFaceDetected()) { //是否识别到了人脸
			for (int i = 1; i < 6; i++)
			{
				if (faceOfIdDetected(i)) {
```

\(4\) Once a learned face is recognized, print the face ID and the X, Y coordinates of the top-left corner to the serial port.

```c
		if (anyFaceDetected()) { // Whether any face detected（是否识别到了人脸）
			for (int i = 1; i < 6; i++)
			{
				if (faceOfIdDetected(i)) {
			struct WonderCamFaceDetectResult p;
					printf("Recognized a specific face（识别到特定人脸）");
					char str[20] = "ID:";
					str[4] = '\0';
					str[3] = 48 + i;
			printf("%s\r\n",&str);
			getFaceOfId(i, &p);
			printf("X:");
			printf("%d",p.x);
			printf(" Y:");
			printf("%d\r\n",p.y);
			DelayMs(200);
				}
			}
		}
  }
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_25_03 Facial Recognition\media\image19.png" style="width:500px"  />

After reading the face data, a structure will be returned with the following fields: x and y represent the coordinates of the face's bounding box on WonderCam, with the top-left corner of the screen as the origin. w and h represent the width and height of the bounding box.

### 3.3.4 STM32F407

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam with the 4-pin connector, and plug the other end into the I2C interface on the STM32 controller.

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Connect the Type-C cable to the Type-C port on the STM32 controller. Make sure to use the UART1 port as shown in the figure below and plug the other end into the USB port of your computer.

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image3.png" style="width:500px"  />

2)  Open the **Device Manager** on your computer and check the COM port number under the **Ports** section.

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image4.png" style="width:500px"  />

3)  Launch the ATK-XISP software in the directory **2. Software & Firmware/04 ATK-XISP**, select the corresponding COM port, and set the baud rate to 115200.

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image5.png" style="width:500px"  />

4)  Configure the software as shown in the image below.

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image6.png" style="width:500px"  />

5)  click the "File" button and select the **"02 Program\MDK-ARM\face_identification\face_identification.hex**" file for programming.

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image7.png" style="width:500px"  />

6)  Click the “Start Programming” button on the interface to flash the generated .hex file to the STM32 controller. Wait for the programming to complete successfully.

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image8.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image9.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image10.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main program, first initialize the WonderCam object. Call the `ChangeFunc()` function to switch WonderCam to face recognition mode, and then turn off the vision module's fill light.

```c
	camset.Init(&camset);
	camset.ChangeFunc(&camset, APPLICATION_FACEDETECT);
	camset.SetLed(&camset,LED_OFF);
```

\(2\) In the while loop, call the `UpdateResult()` function to update the recognition results of WonderCam and check if any color has been detected.

```c
  while (1)
  {
    /* USER CODE END WHILE */
	
    /* USER CODE BEGIN 3 */
		
		camset.UpdateResult(&camset);
		if (facefunc.anyFaceDetected()) { //是否识别到了人脸
```

\(3\) Once a color is detected, use a for loop to check the recognized color ID. This program supports recognizing IDs 1 to 5. Create a structure P to store the face data. Then, print the recognized face ID to the serial port, with the message "Recognized specific face ID: " + recognized face ID.

```c
			for (int i = 1; i < 6; i++)
			{
				if (facefunc.faceOfIdDetected(i)) {
			struct FaceDataObjectStructDef p;
					printf("识别到特定人脸");
					char str[20] = "ID:";
					str[4] = '\0';
					str[3] = 48 + i;
```

\(4\) Next, use getFaceOfId() to retrieve the face data, storing it in structure P, and print the X and Y coordinate values of the face to the serial port.

```c
			facefunc.getFaceOfId(i, &p);
			printf("X:");
			printf("%d",p.x);
			printf(" Y:");
			printf("%d\r\n",p.y);
			HAL_Delay(200);
				}
			}
		}
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_26_04 Facial Recognition/media/image15.png" style="width:500px"  />

After reading the face data, a structure will be returned with the following fields: x and y represent the coordinates of the face's bounding box on WonderCam, with the top-left corner of the screen as the origin. w and h represent the width and height of the bounding box.

### 3.3.5 Raspberry Pi

* **Project Process**

<img class="common_img" src="../_static/media/section_27_05 Facial Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam to any I2C port on the Raspberry Pi expansion board.

<img class="common_img" src="../_static/media/section_27_05 Facial Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1.  After turning on the Raspberry Pi, connect the computer to the corresponding Wi-Fi.

2.  Open the remote desktop software<img src="../_static/media/section_27_05 Facial Recognition\media\image3.png" style="width:50px" />.

3.  In the search bar, enter "**192.168.149.1**" and press **Enter** to access the Raspberry Pi desktop.

<img class="common_img" src="../_static/media/section_27_05 Facial Recognition\media\image4.png" style="width:500px"  />

4.  In the Username field, enter **pi** and in the Password field, enter **raspberry**. Check the **Remember password** box, then click the **OK** button. You will then see the Raspberry Pi desktop opened remotely.

<img class="common_img" src="../_static/media/section_27_05 Facial Recognition\media\image5.png" style="width:500px"  />

The following image shows successful desktop access:

<img class="common_img" src="../_static/media/section_27_05 Facial Recognition\media\image6.png" style="width:500px"  />

Click the red box in the image below to transfer files.

<img class="common_img" src="../_static/media/section_27_05 Facial Recognition\media\image7.png" style="width:500px"  />

Click the button indicated by the red arrow in the image below, then select the file.

<img class="common_img" src="../_static/media/section_27_05 Facial Recognition\media\image8.png" style="width:500px"  />

Choose the **facial_recognition.py** file and wait for the transfer to complete.

<img class="common_img" src="../_static/media/section_27_05 Facial Recognition\media\image9.png" style="width:500px"  />

* **Project Outcome**

\(1\) Enter the command to navigate to the desktop.

```py
cd Desktop/
```

\(2\) Enter the command to run the program.

```py
python3 facial_recognition.py
```

\(3\) After the program starts, the WonderCam vision module will automatically switch to **Face Learning** mode. When the WonderCam detects a learned face, it will print the face ID and coordinates.

* Program Brief Analysis

**1. Import Libraries**

Import the **time** library to control system delays, the **struct** library to parse data transmitted by the vision module, and the **smbus2** library for I2C communication with the WonderCam vision module.

```py
import time
import struct
from smbus2 import SMBus, i2c_msg
```

**2. Program Brief Analysis**

1. In the main program, a communication object for the vision module is created. The `cam.set_func()` function is used to switch the WonderCam to face recognition mode.

   ```py
   if __name__ == "__main__":
   
       cam = WonderCam()
       # """识别人脸并返回坐标"""
       cam.set_func(WONDERCAM_FUNC_FACE_DETECT)
   ```

2. Once initialization is complete, the program enters a loop. The `cam.update_result()` function is called to update the data received from WonderCam, and the `cam.set_led()` function is used to turn on the vision module's fill light.

   ```py
       while True:
           try:
               cam.update_result()
               cam.set_led(True)
   ```

3. After initialization, `call cam.is_face_detected()` to check if a face has been detected. Then, iterate through the face IDs to check if a learned face has been recognized. If a learned face is detected, print the face ID along with the face data.

   ```py
               if cam.is_face_detected():
                   for i in range(1,6):
                       if cam.get_face(i):
                           print("识别到人脸ID: %d" %i)
                           print(cam.get_face(i))
               else:
                   print("未识别到人脸")
               time.sleep(0.5)
           except KeyboardInterrupt:
               break
           except Exception as e :
               print(e)
               continue
   ```

### 3.3.6 ESP32(Arduino)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_28_06.1 Facial Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_28_06.1 Facial Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1)  \(1\) Locate the '**FaceDetect_ID/FaceDetect_ID.ino**' program file in the same directory as this document.

<img class="common_img" src="../_static/media/section_28_06.1 Facial Recognition/media/image3.png" style="width:500px"  />

2)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_28_06.1 Facial Recognition/media/image4.png" style="width:500px"  />

3)  Click the **Select Board** option, search for **ESP32 Dev Module**, and click **OK**.

<img class="common_img" src="../_static/media/section_28_06.1 Facial Recognition/media/image5.png" style="width:500px"  />

4)  Click <img src="../_static/media/section_28_06.1 Facial Recognition/media/image6.png" style="width:50px" /> to download the program to the ESP32, and wait for the download to complete.

<img class="common_img" src="../_static/media/section_28_06.1 Facial Recognition/media/image7.png" style="width:500px"  />

* **Project Outcome**

When the WonderCam detects a learned face, it will print "Learned face detected" and the face ID to the serial port.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication and sets the vision module to color recognition mode.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_FACEDETECT);
  wc.setLed(true);

}
```

**2. Main Function: loop()**

The main function is used to detect faces and provide corresponding feedback. First, check if a face is detected. If a face is detected, verify if a specific face ID has been recognized.

Next, based on the detected face ID, display the current face ID on the matrix module and print "Learned face detected" to the serial port.

```cpp
void loop() {
  wc.updateResult();
  if (wc.anyFaceDetected()) { //是否识别到了人脸
    for (int i = 1; i < 6; i++)
    {
      if (wc.faceOfIdDetected(i)) {
        Serial.print("识别到特定人脸");
        Serial.println(i);
      }
    }
  }
  delay(300);
}
```

### 3.3.7 ESP32(Python)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_29_06 Facial Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_29_06 Facial Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Open the Hiwonder Python editor software<img src="../_static/media/section_29_06 Facial Recognition/media/image3.png" style="width:100px" />。

2)  Drag the "**main.py**" and "**WonderCam.py**" files from the **02 Program** folder in the same directory into the Wonder Python Editor.

<img class="common_img" src="../_static/media/section_29_06 Facial Recognition/media/image4.png" style="width:500px"  />

3)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_29_06 Facial Recognition/media/image5.png" style="width:500px"  />

4)  Click the Connect button <img src="../_static/media/section_29_06 Facial Recognition/media/image6.png" style="width:50px" /> in the top menu. When successfully connected, the icon will turn green<img src="../_static/media/section_29_06 Facial Recognition/media/image7.png" style="width:50px" />.

5)  After connecting, click the Download button <img src="../_static/media/section_29_06 Facial Recognition/media/image8.png" style="width:50px" /> in the menu to upload the program to the ESP32 controller. Wait for the message box at the bottom of the editor to confirm the download is complete.

<img class="common_img" src="../_static/media/section_29_06 Facial Recognition/media/image9.png" style="width:500px"  />

6)  Finally, click the Restart button <img src="../_static/media/section_29_06 Facial Recognition/media/image10.png" style="width:50px" /> to reboot the ESP32 controller. The program will start running automatically.

* **Project Outcome**

When the WonderCam detects a learned face, it will print the recognized face ID to the serial port.

* **Program Brief Analysis**

1. First, initialize the I2C hardware interface using pins IO16 and IO17, initialize the WonderCam, and switch its function mode to face recognition.

   ```py
   i2c = I2C(0, scl=Pin(16), sda=Pin(17), freq=400000)
   colordetect = WonderCam(i2c)
   
   colordetect.set_func(WONDERCAM_FUNC_FACE_DETECT)
   ```

2. In the loop, first update and retrieve the recognition results. Use the `is_face_detected()` function to check for color IDs from ID1 to ID5, and then print the detected face ID to the serial port.

   ```py
   while True:
     colordetect.update_result()
     for face_id in range(1,6):
       if colordetect.is_face_detected(face_id):
         face_data = colordetect.get_face(face_id)
         print("x:",face_data[0]," y",face_data[1])
         print("face detect ID:",face_id)
     time.sleep_ms(300)
   ```

### 3.3.8 C51

* **Project Process**

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

\(1\) Install the 51 microcontroller onto the open-source controller.

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image2.png" style="width:500px"  />

\(2\) Connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the open-source controller. The ports indicated by the arrows in the image below are all I2C ports.

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image3.png" style="width:500px"  />

\(3\) Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image4.jpeg" style="width:500px"  />

The table below shows the wiring instructions for the USB-to-serial adapter and the open-source controller.

| **USB-to-Serial Adapter** | **Open-Source Controller** |
| :-----------------------: | :------------------------: |
|          5V(VCC)          |             5V             |
|          RX(RXD)          |             TX             |
|          TX(TXD)          |             RX             |
|            GND            |            GND             |

* **Program Download**

1)  Connect the USB-to-serial adapter to the computer.

2)  After confirming that the serial port driver is correctly installed, open **This PC** on the computer desktop. Then, click **Properties** -\> **Device Manager** and check the corresponding serial port number for the controller. **COM port numbers are not unique, and the device port ends with "CH340".**

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image5.png" style="width:500px"  />

3)  Remove the jumper cap from the 51 microcontroller and turn on the power switch to enter programming mode.

> [!NOTE]
>
> **Note:** If the controller is powered on first and then the jumper cap is removed, the controller needs to be turned off and then back on to enter programming mode.

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image6.png" style="width:500px"  />

Open the AIapp-ISP-v6.95E download tool in **[2. Software & Firmware /05 AIapp-ISP-v6.95E]()**:

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image7.png" style="width:300px" />

The AIapp-ISP-v6.95E configuration is shown as follows:

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image8.png" style="width:700px"  />

Click “Open file” and find the hex file in “**02 Program\Objects**”

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image9.png" style="width:500px"  />

After opening the program file, click “**Download/Program**” to download the program to the 51 microcontroller.

> [!NOTE]
>
> **Note:** When downloading the program, you need to remove the jumper cap from the 51 microcontroller first. Once the AIapp-ISP-v6.95E tool in the lower right corner displays the message indicating it is detecting the target microcontroller, you can insert the jumper cap back in place. At this point, the program will start programming.

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image10.png" style="width:700px"  />

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image11.png" style="width:700px"  />

* **Project Outcome**

When the WonderCam detects a learned face, the serial monitor will display the face's position and ID.

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image12.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main function, the initialization of I2C and serial communication is performed first.

```c
void main(void)
{ 
	int i;
	P0M1=P0M0=P1M1=P1M0=P2M1=P2M0=P3M1=P3M0=P4M1=P4M0=P5M1=P5M0=P6M1=P6M0=P7M1=P7M0= 0;	
  I2C_Init();
	InitUart1();
	EA = 1;			// Enable global interrupt（开总中断）
```

\(2\) After initialization, the serial port prints start. And the WonderCam's function is set to face recognition.

```c
	Delay_ms(1000);
	printf("start\r\n");
	changeFunc(APPLICATION_FACEDETECT);
	setLed(true);
```

\(3\) Continuously update and retrieve the recognition results from the vision module. Use the `anyFaceDetected()` function to check if a face has been detected. When a face is detected, create a structure p to store the face data. Then, use a for loop to check the current recognized face ID.

```c
	for(;;)
	{
  updateResult();
  if (anyFaceDetected()) { // Whether any face is detected（是否识别到了人脸）
		struct WonderCamFaceDetectResult xdata p;
    for (i = 1; i < 6; i++) {
```

\(4\) Once face ID i is recognized, the serial port will print "**Recognized specific face**" along with the face ID. Then, use the `getFaceOfId()` function to store the face data of ID i in structure p and print the face's coordinates and size (width and height) to the serial port. Finally, if no face is detected, the serial port will print the message "**No face detected**." 

```c
      if (faceOfIdDetected(i)) {
				
				printf("Recognized a specific face \r\n （识别到特定人脸\r\n）");
        printf("ID:%d\r\n", i); 
			 if(getFaceOfId(i,&p)){
				 printf("Face coordinates: (%d,%d)\r\n （人脸坐标:(%d,%d)\r\n）",p.x,p.y);
				 printf("Face size: %u*%u\r\n （人脸宽高: %u*%u\r\n）",p.w,p.h);
				}
       }
      }
    }
   else {
    printf("No face detected\r\n （未识别到人脸\r\n）");
  }
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_30_07 Facial Recognition\media\image17.png" style="width:500px"  />

After reading the face data, a structure will be returned with the following fields: x and y represent the coordinates of the face's bounding box on WonderCam, with the top-left corner of the screen as the origin. w and h represent the width and height of the bounding box.

## 3.4 Tag Recognition

### 3.4.1 Arduino UNO

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_31_01 Tag Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_31_01 Tag Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

Locate the "**07_AprilTag_DISP/07_AprilTag_DISP.ino**" program file in the same directory as this document.

<img class="common_img" src="../_static/media/section_31_01 Tag Recognition/media/image3.png" style="width:500px"  />

2)  Connect the Arduino controller to the computer using a USB Type-B cable for UNO.

<img class="common_img" src="../_static/media/section_31_01 Tag Recognition/media/image4.png" style="width:500px"  />

3)  In the Arduino IDE, click the **Select Board** option. The software will automatically detect the connected Arduino serial port, then click to connect.

<img class="common_img" src="../_static/media/section_31_01 Tag Recognition/media/image5.png" style="width:500px"  />

4)  Then click the upload button <img src="../_static/media/section_31_01 Tag Recognition/media/image6.png" style="width:50px" /> to upload the program to the Arduino. Wait for the upload to complete.

<img class="common_img" src="../_static/media/section_31_01 Tag Recognition/media/image7.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_31_01 Tag Recognition/media/image8.png" style="width:500px"  />

* **Project Outcome**

When the WonderCam detects a tag, it will outline it, and the matrix display will scroll to show the recognized tag ID.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication, sets the vision module's function to tag recognition, and initializes the OLED module by updating and clearing the display screen.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_APRILTAG);
  wc.setLed(true);
  matrixLed.setBrightness(3);
  matrixLed.clearScreen();

  uint8_t drawBuffer[16] = {
    0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00
    , 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00
  };
  for (int i = 0; i < 16; i++) {
    drawBuffer[i] = 0xff;
    matrixLed.drawBitmap(0, 0, 16, drawBuffer);
    delay(20);
  }
  delay(500);
  matrixLed.clearScreen();
}
```

**2. Main Function: loop()**

The main function first updates and retrieves the recognition results from the vision module, then creates a str array to display the string on the OLED module.

```cpp
void loop() {
  wc.updateResult();
char str[20];
```

If a tag is detected, the `tagIdDetected()` function is used to check the detected tag ID. The str array is then updated to display the tag's ID, and the drawStr() function prints the string on the OLED display.

```cpp
void loop() {
  wc.updateResult();
char str[20];
  if (wc.anyTagDetected()) { //是否识别到了标签
    for (int i = 0; i < 200; i++) {
      if (wc.tagIdDetected(i)) {
        sprintf(str, "ID:%d", i); 
        int offset = 16;
        int len = strlen(str);
        while (offset > (-(6 * (len)))) {
          matrixLed.drawStr(offset, 8, str);
          offset -= 1;
          delay(150);
        }
      }
    }
```

Finally, if no tag is detected, the serial port will print No tag detected.

```cpp
  } else {
    Serial.println("未扫描到标签");
  }
  delay(200);
}
```

### 3.4.2 micro：bit

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Click icon <img src="../_static/media/section_32_02 Tag Recognition\media\image3.png" style="width:50px" /> on the left bottom of the interface, then click **Connect Device**, and follow the on-screen instructions to connect the micro:bit controller.

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image4.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image5.png" style="width:500px"  />

2)  Once connected, click the **Download** button in the bottom-left corner to flash the program to your micro:bit.

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image6.png" style="width:500px"  />

3)  After the download is complete, the indicator light will stop flashing rapidly and remain steadily lit. At the same time, a download completion message will appear on the programming interface.

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image7.png" style="width:500px"  />

* **Project Outcome**

Align the vision module with the provided AprilTag ID 10 tag. When the tag is detected, a point will be displayed on the LED matrix. The position of the point will change vertically based on the rotation angle of the vision module relative to the tag along the X-axis.

* **Program Brief Analysis**

1)  The program first sets the initial state of the device upon startup. Here, the transmission address of WonderCam is configured, and its function is switched to tag recognition.

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image8.png" style="width:500px"  />

2)  Continuously retrieve recognition results from the vision module, and check if the ID 10 tag is detected. If this tag is recognized, the buzzer on the controller will play a specified sound. A variable named "last coordinates" is set, but its value is not set to the X-axis rotation angle, as this value is obtained by the vision module.

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image9.png" style="width:500px"  />

3)  Next, round the retrieved X-axis rotation angle to the nearest integer. This can be done by applying the following formula for rounding.

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image10.png" style="width:500px"  />

4)  Then, the X-axis rotation angle information will be displayed on the controller's LED screen. The LED position is determined by the X and Y coordinates. The X value corresponds to the horizontal axis, while the Y value corresponds to the "**X-axis rotation**" variable with (0,0) representing the top-left corner. For example, X=2 represents the second row of LEDs, and the column is determined by the "X-axis rotation" value, as shown in the diagram below.

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image11.png" style="width:500px"  />

5)  Compare the X-axis rotation angle values obtained during two consecutive readings. If the value differs from the previous result, the corresponding LED on the matrix screen will remain off.

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition\media\image12.png" style="width:500px"  />

* **Feature Extensions**

**1. Modify Tag Recognition**

Change the number inside the ID at the position shown in the diagram below:

<img class="common_img" src="../_static/media/section_32_02 Tag Recognition/media/image13.png" style="width:500px"  />

### 3.4.3 STM32F103

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

First, install the 32-bit microcontroller onto the open-source controller. Then, connect the WonderCam to the 4-pin connector, with the other end plugged into the I2C port on the controller as indicated by the arrows in the image below.

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image2.png" style="width:500px"  />

Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image3.png" style="width:500px"  />

* **Program Download**

1)  Connect the USB downloader to the controller using Dupont wires, and plug the USB end of the downloader into the computer.

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image4.png" style="width:500px"  />

2)  On the computer, open the FlyMcu tool. The application file for FlyMcu is located in the directory **[2. Software & Firmware/03 FlyMcu]()**.

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image5.png" style="width:500px"  />

3)  Select the port for the controller connection and set the baud rate to 115200. Do not choose COM1, as it is the system communication port.

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image6.png" style="width:500px"  />

4)  Click the button <img src="../_static/media/section_33_03 Tag Recognition/media/image7.png" style="width:50px" /> to choose the program file to be flashed. Once selected, check the **Auto Reload Before Program**.

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image8.png" style="width:500px"  />

The program file is located in the directory **02 Program\Obj**.

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image9.png" style="width:500px"  />

5. Take down the jumper cap to the controller, press the **RST** reset button on the STM32 core board, turn on the power switch on the controller, and click **Start ISP(P)**.

   <img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image10.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image12.png" style="width:500px"  />

6)  After the program is successfully flashed, reconnect the jumper cap to the controller, press the **RST** button on the controller, and restart the controller.

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image13.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image14.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) Initialize the system clock, I2C interface, and serial port.

```c
	uint8_t x;
	SystemInit(); 		//系统时钟初始化为72M	  SYSCLK_FREQ_72MHz
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);	//设置NVIC中断分组2:2位抢占优先级，2位响应优先级
	InitDelay(72); 		//初始化延时函数
	IIC_init();		//IIC初始化
	DelayMs(100);
	Usart1_Init(); //串口初始化
	DelayMs(1000);
```

\(2\) After initialization, the serial port will print the start string. The WonderCam will then switch to tag recognition mode, and the fill light will be turned on.

```c
	printf(" ");
	printf("start\r\n");
	changeFunc(APPLICATION_APRILTAG);
	setLed(true);
```

\(3\) Inside the while loop, call the `updateResult()` function to refresh and obtain the recognition results from the WonderCam vision module. Use a for loop to check the detected tag IDs and print them.

```c
	while(1)
	{
  updateResult();
  if (anyTagDetected()) { //是否识别到了AprilTag
    for (int i = 0; i < 200; i++) {
      if (tagIdDetected(i)) {
        printf("ID:%d\r\n", i); 
        }
      }
    }
```

\(4\) If no tag is detected, print No AprilTag detected to the serial port.

```c
   else {
    printf("未扫描到AprilTag\r\n");
  }
  DelayMs(200);
  }
}
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_33_03 Tag Recognition/media/image19.png" style="width:500px"  />

After reading the tag data, a structure like the following is returned:
x and y: the XY coordinates of the bounding box around the tag on the WonderCam vision module’s screen, with the origin at the top-left corner.
w and h: the width and height of the bounding box. 
x_t, y_t, z_t: the tag’s translation offsets relative to the camera along the X, Y, and Z axes. 
x_r, y_r, z_r: the tag’s rotation relative to the camera around the X, Y, and Z axes.

### 3.4.4 STM32F407

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam with the 4-pin connector, and plug the other end into the I2C interface on the STM32 controller.

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Connect the Type-C cable to the Type-C port on the STM32 controller. Make sure to use the UART1 port as shown in the figure below and plug the other end into the USB port of your computer.

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image3.png" style="width:500px"  />

2)  Open the **Device Manager** on your computer and check the COM port number under the **Ports** section.

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image4.png" style="width:500px"  />

3)  Launch the ATK-XISP software in the directory **[2. Software & Firmware/04 ATK-XISP]()**, select the corresponding COM port, and set the baud rate to 115200.

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image5.png" style="width:500px"  />

4)  Configure the software as shown in the image below.

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image6.png" style="width:500px"  />

5)  click the "File" button and select the **"02 Program\MDK-ARM\apriltag_identification\apriltag_identification.hex**" file for programming.

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image7.png" style="width:500px"  />

6)  Click the “**Start Programming**” button on the interface to flash the generated .hex file to the STM32 controller. Wait for the programming to complete successfully.

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image8.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image9.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image10.png" style="width:500px"  />

* **Program Brief Analysis**

(1\) In the main program, first initialize the WonderCam object. Call the `ChangeFunc()` function to switch WonderCam to tag recognition mode, and then turn off the vision module's fill light.

```c
	camset.Init(&camset);
	camset.ChangeFunc(&camset, APPLICATION_APRILTAG);
	camset.SetLed(&camset,LED_OFF);
```

\(2\) In the while loop, call the `UpdateResult()` function to update the recognition results of WonderCam and check if any tag has been detected.

```c
  while (1)
  {
    /* USER CODE END WHILE */
	
    /* USER CODE BEGIN 3 */
		
		camset.UpdateResult(&camset);
		if (apriltagfunc.anyTagDetected()) { //是否识别到了AprilTag(Did you identify the AprilTag?)
```

\(3\) When a tag is detected, use a for loop to check the detected tag IDs. Then print each detected tag ID to the serial port in the format: "ID: " + the tag ID.

```py
    for (int i = 0; i < 200; i++) {
      if (apriltagfunc.tagIdDetected(i)) {
        printf("ID:%d\r\n", i); 
        }
      }
```

\(4\) Otherwise, if no tag is detected, print "**No AprilTag detected**" to the serial port.

```c
   else {
    printf("未扫描到AprilTag(No AprilTag was detected.)\r\n");
  }
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_34_04 Tag Recognition/media/image15.png" style="width:500px"  />

After reading the tag data, a structure like the following is returned: 
x and y: the XY coordinates of the bounding box around the tag on the WonderCam vision module’s screen, with the origin at the top-left corner. 
w and h: the width and height of the bounding box. 
x_t, y_t, z_t: the tag’s translation offsets relative to the camera along the X, Y, and Z axes. 
x_r, y_r, z_r: the tag’s rotation relative to the camera around the X, Y, and Z axes.

### 3.4.5 Raspberry Pi

* **Project Process**

<img class="common_img" src="../_static/media/section_35_05 Tag Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam to any I2C port on the Raspberry Pi expansion board.

<img class="common_img" src="../_static/media/section_35_05 Tag Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1.  After turning on the Raspberry Pi, connect the computer to the corresponding Wi-Fi.

2.  Open the remote desktop software<img src="../_static/media/section_35_05 Tag Recognition\media\image3.png" style="width:50px" />.

3.  In the search bar, enter "**192.168.149.1**" and press Enter to access the Raspberry Pi desktop.

<img class="common_img" src="../_static/media/section_35_05 Tag Recognition\media\image4.png" style="width:500px"  />

4.  In the Username field, enter **pi** and in the Password field, enter **raspberry**. Check the **Remember password** box, then click the **OK** button. You will then see the Raspberry Pi desktop opened remotely.

<img class="common_img" src="../_static/media/section_35_05 Tag Recognition\media\image5.png" style="width:500px"  />

The following image shows successful desktop access:

<img class="common_img" src="../_static/media/section_35_05 Tag Recognition\media\image6.png" style="width:500px"  />

5.  Click the red box in the image below to transfer files.

<img class="common_img" src="../_static/media/section_35_05 Tag Recognition\media\image7.png" style="width:500px"  />

6.  Click the button indicated by the red arrow in the image below, then select the file.

<img class="common_img" src="../_static/media/section_35_05 Tag Recognition\media\image8.png" style="width:500px"  />

7.  Choose the "**tag_identification.py**" file and wait for the transfer to complete.

<img class="common_img" src="../_static/media/section_35_05 Tag Recognition\media\image9.png" style="width:500px"  />

* **Project Outcome**

\(1\) Enter the command to navigate to the desktop.

```py
cd Desktop/
```

\(2\) Enter the command to run the program.

```py
python3 tag_identification.py
```

\(3\) After the program starts, the WonderCam vision module will automatically switch to “**Tag Recognition**” mode. When the module detects a learned tag, it will print the tag’s ID along with its coordinates.

* **Program Brief Analysis**

**1. Import Libraries**

Import the **time** library to control system delays, the **struct** library to parse data transmitted by the vision module, and the **smbus2** library for I2C communication with the WonderCam vision module.

```py
import time
import struct
from smbus2 import SMBus, i2c_msg
```

**2. Program Brief Analysis**

1. In the main program, a communication object for the vision module is created. The `cam.set_func()` function is used to switch the WonderCam to tag recognition mode.

   ```py
   if __name__ == "__main__":
       cam = WonderCam()
   
       # """Tag recognition（标签识别）"""
       cam.set_func(WONDERCAM_FUNC_APRILTAG)
   ```

2. Once initialization is complete, the program enters a loop. The `cam.update_result()` function is called to update the data received from WonderCam, and the `cam.set_led()` function is used to turn on the vision module's fill light.

   ```py
       while True:
           try:
               cam.update_result()
               cam.set_led(True)
   ```

3. Call the `cam.is_tag_detected()` function to check whether any tag has been detected. Then use `cam.num_of_tag_detected()` to get the number of detected tags and print the result. Next, retrieve the tag data using the `cam.get_tag()` function, which takes two arguments: the ID of the tag to retrieve and the index of the tag in the current frame. Finally, print the tag ID along with its associated data.

   ```py
               if cam.is_tag_detected():
                   num = cam.num_of_tag_detected()
                   print("Detected %d tags (识别到标签)" %num)
                   for i in range(0,201):
                       num1 = cam.num_of_tag_detected()
                       for j in range(1,num1+1):
                           if cam.get_tag(i,j):
                               print("Tag:(标签) %d "%i)
                               print(cam.get_tag(i,j))
               else:
                   print("No tag detected (未识别到标签)")
               time.sleep(0.5)
           except KeyboardInterrupt:
               break
           except Exception as e:
               print(e)
               continue
   ```

### 3.4.6 ESP32(Arduino)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_37_06.1 Tag Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_37_06.1 Tag Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Locate the program file **AprilTag_DISP/AprilTag_DISP.ino** in the same directory as this document.

<img class="common_img" src="../_static/media/section_37_06.1 Tag Recognition/media/image3.png" style="width:500px"  />

2)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_37_06.1 Tag Recognition/media/image4.png" style="width:500px"  />

3)  Click the **Select Board** option, search for **ESP32 Dev Module**, and click **OK**.

<img class="common_img" src="../_static/media/section_37_06.1 Tag Recognition/media/image5.png" style="width:500px"  />

4)  Click <img src="../_static/media/section_37_06.1 Tag Recognition/media/image6.png" style="width:50px" /> to download the program to the ESP32, and wait for the download to complete.

<img class="common_img" src="../_static/media/section_37_06.1 Tag Recognition/media/image7.png" style="width:500px"  />

* **Project Outcome**

When the vision module detects a tag, it will draw a bounding box around it and print the detected tag’s ID to the serial port.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication and sets the vision module to tag recognition mode.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_APRILTAG);

}
```

**2. Main Function: loop()**

The main function first updates and retrieves the recognition results from the vision module. If a tag is detected, it checks the detected tag ID using the `tagIdDetected()` function, and then prints the tag ID to the serial port.

```py
void loop() {
  wc.updateResult();
  if (wc.anyTagDetected()) { //If any label been identified? (是否识别到了标签)
    for (int i = 0; i < 200; i++) {
      if (wc.tagIdDetected(i)) {
        Serial.print("tag detect ID:");
        Serial.println(i);
      }
    }
  }
  delay(300);
}
```

### 3.4.7 ESP32(Python)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_38_06 Tag Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_38_06 Tag Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Open the Hiwonder Python editor software<img src="../_static/media/section_38_06 Tag Recognition\media\image3.png" style="width:100px" />。

2)  Drag the "**main.py**" and "**WonderCam.py**" files from the **02 Program** folder in the same directory into the Wonder Python Editor.

<img class="common_img" src="../_static/media/section_38_06 Tag Recognition\media\image4.png" style="width:500px"  />

3)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_38_06 Tag Recognition\media\image5.png" style="width:500px"  />

4)  Click the Connect button <img src="../_static/media/section_38_06 Tag Recognition\media\image6.png" style="width:50px" /> in the top menu. When successfully connected, the icon will turn green<img src="../_static/media/section_38_06 Tag Recognition\media\image7.png" style="width:50px" />.

5)  After connecting, click the Download button <img src="../_static/media/section_38_06 Tag Recognition\media\image8.png" style="width:50px" /> in the menu to upload the program to the ESP32 controller. Wait for the message box at the bottom of the editor to confirm the download is complete.

<img class="common_img" src="../_static/media/section_38_06 Tag Recognition\media\image9.png" style="width:500px"  />

6)  Finally, click the Restart button <img src="../_static/media/section_38_06 Tag Recognition\media\image10.png" style="width:50px" /> to reboot the ESP32 controller. The program will start running automatically.

* **Project Outcome**

When the vision module detects a tag, it will draw a bounding box around it and print the detected tag’s ID to the serial port.

* **Program Brief Analysis**

1. First, initialize the I2C hardware interface using pins IO16 and IO17, initialize the WonderCam, and switch its function mode to tag recognition.

   ```py
   i2c = I2C(0, scl=Pin(16), sda=Pin(17), freq=400000)
   colordetect = WonderCam(i2c)
   
   colordetect.set_func(WONDERCAM_FUNC_APRILTAG)

In the loop, first update and retrieve the recognition results. Use the `is_tag_detected()` function to check for color IDs from ID1 to ID200, and then print the detected tag ID to the serial port.

```py
while True:
  colordetect.update_result()
  for tag_id in range(1,200):
    if colordetect.is_tag_detected(tag_id):
      print("tag detect ID:",tag_id)
  time.sleep_ms(300)
```

### 3.4.8 C51

* **Project Process**

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

\(1\) Install the 51 microcontroller onto the open-source controller.

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image2.png" style="width:500px"  />

\(2\) Connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the open-source controller. The ports indicated by the arrows in the image below are all I2C ports.

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image3.png" style="width:500px"  />

\(3\) Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image4.jpeg" style="width:500px"  />

The table below shows the wiring instructions for the USB-to-serial adapter and the open-source controller.

| **USB-to-Serial Adapter** | **Open-Source Controller** |
| :-----------------------: | :------------------------: |
|          5V(VCC)          |             5V             |
|          RX(RXD)          |             TX             |
|          TX(TXD)          |             RX             |
|            GND            |            GND             |

* **Program Download**

1)  Connect the USB-to-serial adapter to the computer.

2)  After confirming that the serial port driver is correctly installed, open **This PC** on the computer desktop. Then, click **Properties** -\> **Device Manager** and check the corresponding serial port number for the controller. **COM port numbers are not unique, and the device port ends with "CH340".**

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image5.png" style="width:500px"  />

3)  Remove the jumper cap from the 51 microcontroller and turn on the power switch to enter programming mode.

> [!NOTE]
>
> **Note:** If the controller is powered on first and then the jumper cap is removed, the controller needs to be turned off and then back on to enter programming mode.

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image6.png" style="width:500px"  />

Open the AIapp-ISP-v6.95E download tool in **[2. Software & Firmware /05 AIapp-ISP-v6.95E]()**:

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image7.png" style="width:300px" />

The AIapp-ISP-v6.95E configuration is shown as follows:

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image8.png" style="width:700px"  />

Click “Open file” and find the hex file in “**02 Program\Objects**”

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image9.png" style="width:500px"  />

After opening the program file, click “**Download/Program**” to download the program to the 51 microcontroller.

> [!NOTE]
>
> **Note:** When downloading the program, you need to remove the jumper cap from the 51 microcontroller first. Once the AIapp-ISP-v6.95E tool in the lower right corner displays the message indicating it is detecting the target microcontroller, you can insert the jumper cap back in place. At this point, the program will start programming.

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image10.png" style="width:700px"  />

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image11.png" style="width:700px"  />

* **Project Outcome**

When the vision module detects a tag, it draws a bounding box around it, and the serial monitor prints the detected tag’s ID, coordinates, and bounding box width and height.

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image12.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main function, the initialization of I2C and serial communication is performed first.

```c
void main(void)
{ 
	int i,index;
	P0M1=P0M0=P1M1=P1M0=P2M1=P2M0=P3M1=P3M0=P4M1=P4M0=P5M1=P5M0=P6M1=P6M0=P7M1=P7M0= 0;	
  I2C_Init();
	InitUart1();
	EA = 1;			// Enable global interrupt（开总中断）
```

\(2\) After initialization, the serial port prints start. And the WonderCam's function is set to tag recognition.

```c
	Delay_ms(1000);
	printf("start\r\n");
	changeFunc(APPLICATION_APRILTAG);
	setLed(true);
```

\(3\) Inside the loop, create a structure p to store tag data, and update and retrieve the recognition results from the vision module. Use the `anyTagDetected()` function to check whether any tag has been detected.

```c
	for(;;)
	{
	struct WonderCamAprilTagResult xdata p;
  updateResult();
  if (anyTagDetected()) { // Whether AprilTag is detected（是否识别到了AprilTag）
```

\(4\) Check the detected tag IDs and print the current tag’s ID to the serial port. Use the `tagId()` function to retrieve the recognition data for the tag with ID i, and print its coordinates and bounding box width and height to the serial port. Finally, if no tag is detected, print "**No AprilTag detected**" to the serial port.

```c
    for (i = 0; i < 20; i++) {
      if (tagIdDetected(i)) {
        printf("ID:%d\r\n", i); 		
        }
			//index = numOfTagIdDetected(i);
			if(tagId(i,1,&p))
			{
				printf("Tag coordinates: (%d,%d)\r\n （标签坐标:(%d,%d)\r\n）",p.x,p.y);
			  printf("Tag width and height: %d*%d\r\n （标签宽高:%d*%d\r\n）",p.w,p.h);
      }
		 }
    }
   else {
    printf("No AprilTag detected\r\n （未扫描到AprilTag\r\n）");
  }
  Delay_ms(200);
  }
}
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_39_07 Tag Recognition/media/image17.png" style="width:500px"  />

After reading the tag data, a structure like the following is returned: 
x and y: the XY coordinates of the bounding box around the tag on the WonderCam vision module’s screen, with the origin at the top-left corner. 
w and h: the width and height of the bounding box. 
x_t, y_t, z_t: the tag’s translation offsets relative to the camera along the X, Y, and Z axes. 
x_r, y_r, z_r: the tag’s rotation relative to the camera around the X, Y, and Z axes.

## 3.5 Vision Line Following

### 3.5.1 Arduino UNO

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_40_01 Vision Line Following\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_40_01 Vision Line Following\media\image2.png" style="width:500px"  />

* **Program Download**

Locate the "**06_Linefollowing/06_Linefollowing.ino**" program file in the same directory as this document.

<img class="common_img" src="../_static/media/section_40_01 Vision Line Following\media\image3.png" style="width:500px"  />

2)  Connect the Arduino controller to the computer using a USB Type-B cable for UNO.

<img class="common_img" src="../_static/media/section_40_01 Vision Line Following\media\image4.png" style="width:500px"  />

3)  In the Arduino IDE, click the **Select Board** option. The software will automatically detect the connected Arduino serial port, then click to connect.

<img class="common_img" src="../_static/media/section_40_01 Vision Line Following\media\image5.png" style="width:500px"  />

4)  Then click the upload button <img src="../_static/media/section_40_01 Vision Line Following\media\image6.png" style="width:50px" /> to upload the program to the Arduino. Wait for the upload to complete.

<img class="common_img" src="../_static/media/section_40_01 Vision Line Following\media\image7.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_40_01 Vision Line Following\media\image8.png" style="width:500px"  />

* **Project Outcome**

Align the vision module with a learned line. When the line is detected, the OLED screen will display its trajectory.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication, sets the vision module to line-following mode, and initializes the OLED module.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_LINEFOLLOW);
  wc.setLed(true);
  u8g2.begin();
  u8g2.enableUTF8Print();    // enable UTF8 support for the Arduino print() function
}
```

**2. Main Function: loop()**

In the main function, first update and retrieve the recognition results from the vision module. Check whether Line 1 is detected. If detected, store the line data in the P structure using the `lineId()` function.

```cpp
uint8_t no_counter = 0;
void loop() {
  wc.updateResult();

  char buf[100];
  if (wc.lineIdDetected(1)) { //Check if a line is detected (是否识别到了线条)
    WonderCamLineResult p;
    if (wc.lineId(1, &p)) {
```

Next, display the retrieved line information on the OLED screen. The map function is used to convert the line’s start and end X, Y coordinates detected by the WonderCam vision module to the OLED screen: X coordinates are mapped from 0–319 to 0–127, and Y coordinates are mapped from 0–239 to 0–31.

```cpp
      u8g2.setFont(u8g2_font_unifont_t_chinese2);
      u8g2.setFontDirection(0);
      u8g2.setDrawColor(1);
      u8g2.firstPage();
      do {
        u8g2.drawLine(map(p.start_x, 0, 319, 0, 127), map(p.start_y, 0, 239, 0, 31), map(p.end_x, 0, 319, 0, 127), map(p.end_y, 0, 239, 0, 31));
      } while ( u8g2.nextPage() );
```

Finally, if Line 1 is not detected, clear the OLED screen.

```cpp
    }  else {
      no_counter++;
      if (no_counter > 5) {
        u8g2.clearDisplay();
      }
    }
  }
  delay(50);
```

### 3.5.2 micro：bit

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Click icon <img src="../_static/media/section_41_02 Vision Line Following/media/image3.png" style="width:50px" /> on the left bottom of the interface, then click **Connect Device**, and follow the on-screen instructions to connect the micro:bit controller.

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image4.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image5.png" style="width:500px"  />

2)  Once connected, click the **Download** button in the bottom-left corner to flash the program to your micro:bit.

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image6.png" style="width:500px"  />

3)  After the download is complete, the indicator light will stop flashing rapidly and remain steadily lit. At the same time, a download completion message will appear on the programming interface.

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image7.png" style="width:500px"  />

* **Project Outcome**

First, the line is learned as Line ID 1. When the line is detected, a point will be displayed on the LED matrix. The position of this point changes according to the angle of the line.

* **Program Brief Analysis**

1)  The program first sets the initial state of the device upon startup. Here, the transmission address of WonderCam is configured, and its function is switched to vision line following.

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image8.png" style="width:500px"  />

2)  Continuously retrieve recognition results from the vision module, and check whether Line ID 1 has been detected. The line must be learned beforehand, and for the learning procedure, refer to Vision Line Following in the folder **2. Function Instruction**. If the line is detected, the program proceeds with the following steps.

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image9.png" style="width:500px"  />

3)  Obtain the line’s angle from the vision module and assign this value to the variable angle.

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image10.png" style="width:500px"  />

4)  Round the angle to the nearest integer, and only assign it to angle if it falls within the specified range according to the following formula.

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image11.png" style="width:500px"  />

5)  Next, use the controller’s LED matrix to display the angle’s position. The LED is positioned using X and Y coordinates. X for horizontal, Y for vertical, with (0,0) at the top-left corner. The X coordinate is determined by the rounded angle value, while the Y coordinate is fixed at 0, the first row as illustrated below.

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image12.png" style="width:500px"  />

6)  Compare the current angle with the previous reading. If the two values differ, the corresponding LED on the matrix will not light up.

<img class="common_img" src="../_static/media/section_41_02 Vision Line Following/media/image13.png" style="width:500px"  />

### 3.5.3 STM32F103

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

First, install the 32-bit microcontroller onto the open-source controller. Then, connect the WonderCam to the 4-pin connector, with the other end plugged into the I2C port on the controller as indicated by the arrows in the image below.

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image2.png" style="width:500px"  />

Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image3.png" style="width:500px"  />

* **Program Download**

1)  Connect the USB downloader to the controller using Dupont wires, and plug the USB end of the downloader into the computer.

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image4.png" style="width:500px"  />

2)  On the computer, open the FlyMcu tool. The application file for FlyMcu is located in the directory **[2. Software & Firmware/03 FlyMcu]()**.

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image5.png" style="width:500px"  />

3)  Select the port for the controller connection and set the baud rate to 115200. Do not choose COM1, as it is the system communication port.

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image6.png" style="width:500px"  />

4)  Click the button <img src="../_static/media/section_42_03 Vision Line Following\media\image7.png" style="width:50px" /> to choose the program file to be flashed. Once selected, check the **Auto Reload Before Program**.

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image8.png" style="width:500px"  />

The program file is located in the directory **02 Program\Obj**.

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image9.png" style="width:500px"  />

5. Take down the jumper cap to the controller, press the **RST** reset button on the STM32 core board, turn on the power switch on the controller, and click **Start ISP(P)**.

   <img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image10.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image12.png" style="width:500px"  />

6)  After the program is successfully flashed, reconnect the jumper cap to the controller, press the **RST** button on the controller, and restart the controller.

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image13.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image14.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) Initialize the system clock, I2C interface, and serial port.

```c
	uint8_t x;
	SystemInit(); 		// Initialize system clock to 72M  SYSCLK_FREQ_72MHz (系统时钟初始化为72M  SYSCLK_FREQ_72MHz)
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);	// Set NVIC interrupt group 2: 2 bits for preemption priority, 2 bits for response priority (设置NVIC中断分组2:2位抢占优先级，2位响应优先级)
	InitDelay(72); 		// Initialize delay function (初始化延时函数)
	IIC_init();		// Initialize IIC (IIC初始化)
	DelayMs(100);
	Usart1_Init(); // Initialize UART (串口初始化)
```

\(2\) After initialization, the serial port will print the "start" string. The WonderCam will then switch to vision line following mode, and the fill light will be turned on.

```c
	printf(" ");
	printf("start\r\n");
	changeFunc(APPLICATION_LINEFOLLOW);
	setLed(true);
```

\(3\) Inside the while loop, call the updateResult() function to update and retrieve the line-following recognition results. If Line 1 is detected, create a structure to store the line data.

```c
	while(1)
	{
		updateResult();
		char buf[100];
		if (lineIdDetected(1)) { // Whether a line has been detected (是否识别到了线条)
			struct WonderCamLineResult p;
```

\(4\) Then store Line 1’s information in the P structure, and print the line’s ID, start coordinates, end coordinates, and other relevant data.

```c
			if (lineId(1, &p)) {
			printf("Line ID:1\r\n （线条ID:1\r\n）");
			printf("Start X coordinate:%d\r\n （起点X坐标:%d\r\n）",p.start_x);
			printf("Start Y coordinate:%d\r\n （起点Y坐标:%d\r\n）",p.start_y);
			printf("End X coordinate:%d\r\n  （终点X坐标:%d\r\n）",p.end_x);
			printf("End X coordinate:%d\r\n  （终点X坐标:%d\r\n）",p.end_y);
			printf("Line angle:%d\r\n （线条角度:%d\r\n）",p.angle);
			printf("Line offset:%d\r\n （线条偏移:%d\r\n）",p.offset);
			} 
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_42_03 Vision Line Following\media\image19.png" style="width:500px"  />

After reading the route data, a structure is returned where `start_x` and `start_y` are the XY coordinates of the route’s start point on the WonderCam vision module’s screen with the origin at the top-left corner, `end_x` and `end_y` are the end point coordinates, angle represents the route’s angle, and offset represents the offset value.

### 3.5.4 STM32F407

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam with the 4-pin connector, and plug the other end into the I2C interface on the STM32 controller.

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Connect the Type-C cable to the Type-C port on the STM32 controller. Make sure to use the UART1 port as shown in the figure below and plug the other end into the USB port of your computer.

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image3.png" style="width:500px"  />

2)  Open the **Device Manager** on your computer and check the COM port number under the **Ports** section.

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image4.png" style="width:500px"  />

3)  Launch the ATK-XISP software in the directory **[2. Software & Firmware/04 ATK-XISP]()**, select the corresponding COM port, and set the baud rate to 115200.

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image5.png" style="width:500px"  />

4)  Configure the software as shown in the image below.

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image6.png" style="width:500px"  />

5)  click the "File" button and select the **"02 Program\MDK-ARM\linefollow_identification\linefollow_identification.hex**" file for programming.

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image7.png" style="width:500px"  />

6)  Click the “**Start Programming**” button on the interface to flash the generated .hex file to the STM32 controller. Wait for the programming to complete successfully.

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image8.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image9.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image10.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main program, first initialize the WonderCam object. Call the `ChangeFunc()` function to switch WonderCam to vision line following recognition mode, and then turn off the vision module's fill light.

```c
	camset.Init(&camset);
	camset.ChangeFunc(&camset, APPLICATION_LINEFOLLOW);
	camset.SetLed(&camset,LED_OFF);
```

\(2\) In the while loop, call the `UpdateResult()` function to update the recognition results of WonderCam and check if Line 1 has been detected.

```c
  while (1)
  {
    /* USER CODE END WHILE */
	
    /* USER CODE BEGIN 3 */
		
		camset.UpdateResult(&camset);
		if (linefollowfunc.lineIdDetected(1)) {
```

\(3\) When Line 1 is detected, create a structure P to store the line data, retrieve Line 1’s data using the `lineId()` function, and store it in the P structure. Then print the retrieved line data to the serial port.

```c
			struct LineDataObjectStructDef p;
			if(linefollowfunc.lineId(1, &p))
			printf("线条ID(Line ID)：1\r\n");
			printf("起点X坐标(Starting point X coordinate):%d\r\n",p.start_x);
			printf("起点Y坐标(Starting point Y coordinate):%d\r\n",p.start_y);
			printf("终点X坐标(End point X coordinate):%d\r\n",p.end_x);
			printf("终点X坐标(End point Y coordinate):%d\r\n",p.end_y);
			printf("线条角度(Line angle):%d\r\n",p.angle);
			printf("线条偏移(Line offset):%d\r\n",p.offset);
			HAL_Delay(200);
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_43_04 Vision Line Following/media/image14.png" style="width:500px"  />

After reading the route data, a structure is returned where `start_x` and `start_y` are the XY coordinates of the route’s start point on the WonderCam vision module’s screen with the origin at the top-left corner, `end_x` and `end_y` are the end point coordinates, angle represents the route’s angle, and offset represents the offset value.

### 3.5.5 Raspberry Pi

* **Project Process**

<img class="common_img" src="../_static/media/section_44_05 Vision Line Following\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam to any I2C port on the Raspberry Pi expansion board.

<img class="common_img" src="../_static/media/section_44_05 Vision Line Following\media\image2.png" style="width:500px"  />

* **Program Download**

1.  After turning on the Raspberry Pi, connect the computer to the corresponding Wi-Fi.

2.  Open the remote desktop software<img src="../_static/media/section_44_05 Vision Line Following\media\image3.png" style="width:50px" />.

3.  In the search bar, enter "**192.168.149.1**" and press Enter to access the Raspberry Pi desktop.

<img class="common_img" src="../_static/media/section_44_05 Vision Line Following\media\image4.png" style="width:500px"  />

4.  In the Username field, enter pi and in the Password field, enter raspberry. Check the "**Remember password**" box, then click the "**OK**" button. You will then see the Raspberry Pi desktop opened remotely.

<img class="common_img" src="../_static/media/section_44_05 Vision Line Following\media\image5.png" style="width:500px"  />

The following image shows successful desktop access:

<img class="common_img" src="../_static/media/section_44_05 Vision Line Following\media\image6.png" style="width:500px"  />

5.  Click the red box in the image below to transfer files.

<img class="common_img" src="../_static/media/section_44_05 Vision Line Following\media\image7.png" style="width:500px"  />

6.  Click the button indicated by the red arrow in the image below, then select the file.

<img class="common_img" src="../_static/media/section_44_05 Vision Line Following\media\image8.png" style="width:500px"  />

7.  Choose the "**visual_patrol.py**" file and wait for the transfer to complete.

<img class="common_img" src="../_static/media/section_44_05 Vision Line Following\media\image9.png" style="width:500px"  />

* **Project Outcome**

\(1\) Enter the command to navigate to the desktop.

```py
cd Desktop/
```

\(2\) Enter the command **python3 visual_patrol.py** to run the program.

```py
python3 visual_patrol.py
```

\(3\) After the program starts, the WonderCam vision module will automatically switch to “**Vision Line Following**” mode. When the module detects a learned line, it prints the line’s ID, the X and Y coordinates of the arrow’s tip and tail, as well as the offset and angle.

* **Program Brief Analysis**

 **1. Import Libraries**

Import the **time** library to control system delays, the **struct** library to parse data transmitted by the vision module, and the **smbus2** library for I2C communication with the WonderCam vision module.

```py
import time
import struct
from smbus2 import SMBus, i2c_msg
```

**2. Program Brief Analysis**

1. In the main program, a communication object for the vision module is created. The `cam.set_func()` function is used to switch the WonderCam to vision line following mode.

   ```py
   if __name__ == "__main__":
       cam = WonderCam()
       # """Recognize specified line (识别指定线条)"""
       cam.set_func(WONDERCAM_FUNC_LINE_FOLLOWING)
   ```

2. Once initialization is complete, the program enters a loop. The `cam.update_result()` function is called to update the data received from WonderCam, and the `cam.set_led()` function is used to turn on the vision module's fill light.

   ```py
       while True:
           try:
               cam.update_result()
               cam.set_led(True)
   ```

3)  Call the `cam.is_line_detected()` function to check whether any line has been detected. If a line is detected, iterate through the line IDs and use `cam.get_line()` to retrieve each line’s data. Finally, print both the line IDs and their corresponding data.

<img class="common_img" src="../_static/media/section_44_05 Vision Line Following/media/image15.png" style="width:500px"  />

### 3.5.6 ESP32(Arduino)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_46_06.1 Vision Line Following\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_46_06.1 Vision Line Following\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Locate the program file **Linefollowing/Linefollowing.ino** in the same directory as this document.

<img class="common_img" src="../_static/media/section_46_06.1 Vision Line Following\media\image3.png" style="width:500px"  />

2)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_46_06.1 Vision Line Following\media\image4.png" style="width:500px"  />

3)  Click the **Select Board** option, search for **ESP32 Dev Module**, and click **OK**.

<img class="common_img" src="../_static/media/section_46_06.1 Vision Line Following\media\image5.png" style="width:500px"  />

4)  Click <img src="../_static/media/section_46_06.1 Vision Line Following\media\image6.png" style="width:50px" /> to download the program to the ESP32, and wait for the download to complete.

<img class="common_img" src="../_static/media/section_46_06.1 Vision Line Following\media\image7.png" style="width:500px"  />

* **Project Outcome**

When the vision module detects a learned line, the serial port prints the line’s ID, start coordinates, end coordinates, tilt angle, and offset.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication and sets the vision module to color recognition mode.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_LINEFOLLOW);
}
```

**2. Main Function: loop()**

The main function is responsible for detecting line and providing the corresponding feedback. It first checks whether any line has been detected. If so, it then checks if a specific line with a particular ID is detected. Then, based on the detected line ID, print the line’s ID, start coordinates, end coordinates, tilt angle, and offset to the serial port.

```cpp
void loop() {
  wc.updateResult();
  if (wc.anyLineDetected()) { //Any line get detected?(是否识别到了线条)
    WonderCamLineResult p;
    for(int i = 1;i < 6;i++){
      if(wc.lineIdDetected(i)){
        if (wc.lineId(i, &p)) {
          Serial.print("line detect ID:"); Serial.println(i);
          Serial.print("start_x:"); Serial.println(p.start_x);
          Serial.print("start_y:"); Serial.println(p.start_y);
          Serial.print("end_x:"); Serial.println(p.end_x);
          Serial.print("end_y:"); Serial.println(p.end_y);
          Serial.print("angle:"); Serial.println(p.angle);
          Serial.print("offset:"); Serial.println(p.offset);
        }
      }
    }
  }
  delay(800);
}
```

### 3.5.7 ESP32(Python)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_47_06 Vision Line Following\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_47_06 Vision Line Following\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Open the Hiwonder Python editor software<img src="../_static/media/section_47_06 Vision Line Following\media\image3.png" style="width:100px" />。

2)  Drag the **main.py** and **WonderCam.py** files from the **02 Program** folder in the same directory into the Wonder Python Editor.

<img class="common_img" src="../_static/media/section_47_06 Vision Line Following\media\image4.png" style="width:500px"  />

3)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_47_06 Vision Line Following\media\image5.png" style="width:500px"  />

4)  Click the Connect button <img src="../_static/media/section_47_06 Vision Line Following\media\image6.png" style="width:50px" /> in the top menu. When successfully connected, the icon will turn green<img src="../_static/media/section_47_06 Vision Line Following\media\image7.png" style="width:50px" />.

5)  After connecting, click the Download button <img src="../_static/media/section_47_06 Vision Line Following\media\image8.png" style="width:50px" /> in the menu to upload the program to the ESP32 controller. Wait for the message box at the bottom of the editor to confirm the download is complete.

<img class="common_img" src="../_static/media/section_47_06 Vision Line Following\media\image9.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_47_06 Vision Line Following\media\image10.png" style="width:500px"  />

6)  Finally, click the Restart button <img src="../_static/media/section_47_06 Vision Line Following\media\image11.png" style="width:50px" /> to reboot the ESP32 controller. The program will start running automatically.

* **Project Outcome**

When the vision module detects a learned line, the serial port prints the line’s ID, start coordinates, end coordinates, tilt angle, and offset.

* **Program Brief Analysis**

1. First, initialize the I2C hardware interface using pins IO16 and IO17, initialize the WonderCam, and switch its function mode to vision line following.

   ```py
   i2c = I2C(0, scl=Pin(16), sda=Pin(17), freq=400000)
   colordetect = WonderCam(i2c)
   
   colordetect.set_func(WONDERCAM_FUNC_LINE_FOLLOWING)
   ```

2. In the loop, the recognition results are continuously updated and retrieved. The function is_color_line_detected(1) is used to check whether line 1 is detected. Once detected, the starting point, ending point, tilt angle, and deviation data of line 1 are printed through the serial port.

   ```py
   while True:
     colordetect.update_result()
     if colordetect.is_line_detected(1):
       line_data = colordetect.get_line(1)
       print("start_x:",line_data[0]," start_y:",line_data[1])
       print("end_x:",line_data[2]," end_y:",line_data[3])
       print("line_angle:",line_data[4]," line_offset:",line_data[5])
       print("line detect ID:1")
     time.sleep_ms(500)
   ```

### 3.5.8 C51

* **Project Process**

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

\(1\) Install the 51 microcontroller onto the open-source controller.

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image2.png" style="width:500px"  />

\(2\) Connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the open-source controller. The ports indicated by the arrows in the image below are all I2C ports.

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image3.png" style="width:500px"  />

\(3\) Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image4.jpeg" style="width:500px"  />

The table below shows the wiring instructions for the USB-to-serial adapter and the open-source controller.

| **USB-to-Serial Adapter** | **Open-Source Controller** |
| :-----------------------: | :------------------------: |
|          5V(VCC)          |             5V             |
|          RX(RXD)          |             TX             |
|          TX(TXD)          |             RX             |
|            GND            |            GND             |

* **Program Download**

1)  Connect the USB-to-serial adapter to the computer.

2)  After confirming that the serial port driver is correctly installed, open **This PC** on the computer desktop. Then, click **Properties** -\> **Device Manager** and check the corresponding serial port number for the controller. **COM port numbers are not unique, and the device port ends with "CH340".**

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image5.png" style="width:500px"  />

3)  Remove the jumper cap from the 51 microcontroller and turn on the power switch to enter programming mode.

> [!NOTE]
>
> **Note:** If the controller is powered on first and then the jumper cap is removed, the controller needs to be turned off and then back on to enter programming mode.

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image6.png" style="width:500px"  />

Open the AIapp-ISP-v6.95E download tool in **[2. Software & Firmware /05 AIapp-ISP-v6.95E]()**:

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image7.png" style="width:300px" />

The AIapp-ISP-v6.95E configuration is shown as follows:

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image8.png" style="width:700px"  />

Click “Open file” and find the hex file in “**02 Program\Objects**”

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image9.png" style="width:500px"  />

After opening the program file, click “Download/Program” to download the program to the 51 microcontroller.

> [!NOTE]
>
> **Note:** When downloading the program, you need to remove the jumper cap from the 51 microcontroller first. Once the AIapp-ISP-v6.95E tool in the lower right corner displays the message indicating it is detecting the target microcontroller, you can insert the jumper cap back in place. At this point, the program will start programming.

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image10.png" style="width:700px"  />

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image11.png" style="width:700px"  />

* **Project Outcome**

Point the vision module toward the learned line. Once the line is detected, the serial monitor will display the corresponding line data.

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image12.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main function, the initialization of I2C and serial communication is performed first.

```c
void main(void)
{ 
	int i;
	P0M1=P0M0=P1M1=P1M0=P2M1=P2M0=P3M1=P3M0=P4M1=P4M0=P5M1=P5M0=P6M1=P6M0=P7M1=P7M0= 0;	
  I2C_Init();
	InitUart1();
	EA = 1;			// Enable global interrupt（开总中断）
```

\(2\) After initialization, the serial port prints "**start**". And the WonderCam's function is set to vision line following.

```c
	Delay_ms(1000);
	printf("start\r\n");
	changeFunc(APPLICATION_LINEFOLLOW);
	setLed(true);
```

\(3\) In the loop, the recognition results of the vision module are updated and retrieved. The lineIdDetected(1) function is used to check if line 1 has been detected. Once line 1 is detected, a structure p is created to store the color data.

```c
	for(;;)
	{
		updateResult();
		if (lineIdDetected(1)) { // Whether a line is detected（是否识别到了线条）
			struct WonderCamLineResult xdata p;
```

The `lineId()` function stores the data of line 1 into the structure p. Then, a “**Line detected**” message is printed through the serial port, followed by the coordinates of the start and end points, the angle, and the offset angle. Finally, if no line is detected, the message “**No line detected**” is displayed instead.

```c
				if (lineId(1, &p)) {
					 //printf("%s\r\n",result_summ);
			  	 printf("Line detected\r\n（识别到了线条\r\n）");
					 printf("Start coordinate: (%d,%d)\r\n（起点坐标:(%d,%d)\r\n）", p.start_x,p.start_y);
					 printf("End coordinate：(%d,%d)\r\n （终点坐标:(%d,%d)\r\n）", p.end_x,p.end_y);
					 printf("Angle:%d\r\n（角度:%d\r\n）", p.angle);
					 printf("Offset:%d\r\n（偏移:%d\r\n）", p.offset);		
		   }
		 }
				
		else {
			printf("No line detected\r\n （未扫描到线条\r\n）");
		}
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_48_07 Vision Line Following\media\image17.png" style="width:500px"  />

After reading the route data, a structure is returned where `start_x` and `start_y` are the XY coordinates of the route’s start point on the WonderCam vision module’s screen with the origin at the top-left corner, `end_x` and `end_y` are the end point coordinates, angle represents the route’s angle, and offset represents the offset value.

## 3.6 Number Recognition

### 3.6.1 Arduino UNO

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_49_01 Number Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_49_01 Number Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

Locate the "**11_Number_oled/11_Number_oled.ino**" program file in the same directory as this document.

<img class="common_img" src="../_static/media/section_49_01 Number Recognition\media\image3.png" style="width:500px"  />

2)  Connect the Arduino controller to the computer using a USB Type-B cable for UNO.

<img class="common_img" src="../_static/media/section_49_01 Number Recognition\media\image4.png" style="width:500px"  />

3)  In the Arduino IDE, click the **Select Board** option. The software will automatically detect the connected Arduino serial port, then click to connect.

<img class="common_img" src="../_static/media/section_49_01 Number Recognition\media\image5.png" style="width:500px"  />

4)  Then click the upload button <img src="../_static/media/section_49_01 Number Recognition\media\image6.png" style="width:50px" /> to upload the program to the Arduino. Wait for the upload to complete.

<img class="common_img" src="../_static/media/section_49_01 Number Recognition\media\image7.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_49_01 Number Recognition\media\image8.png" style="width:500px"  />

* **Project Outcome**

When the vision module detects a number, the OLED screen displays the recognized digit. At the same time, if the Arduino serial monitor is open, the recognized number and its confidence level are printed.

<img class="common_img" src="../_static/media/section_49_01 Number Recognition\media\image9.png" style="width:500px"  />

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication, sets the vision module to number recognition mode, and initializes the OLED module.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_NUMBER_REC);
  wc.setLed(true);
  u8g2.begin();
  u8g2.enableUTF8Print();    // enable UTF8 support for the Arduino print() function
}
```

**2. Main Function: loop()**

First, the recognition results are updated and retrieved. The detected digit ID and its confidence are assigned to the variables id and max_prob, and both values are printed to the serial port.

```cpp
void loop() {
  wc.updateResult();

  char buf[100];
  int id = wc.numberWithMaxProb();
  float max_prob = wc.numberMaxProb();
  Serial.print(id);
  Serial.print(",");
  Serial.println(max_prob);
```

If the confidence of the detected digit exceeds 0.5, the current digit ID is displayed on the OLED screen. For example, if the digit 1 is detected with a confidence of 0.9, the OLED shows **Number: 1**.

```cpp
  if (max_prob > 0.5 && id > 0) {
    sprintf(buf, "Number: %d", id);

    u8g2.firstPage();
    do {
      u8g2.setCursor(0, 15);
      u8g2.setFont(u8g2_font_ncenB10_tr);
      u8g2.print(buf);
    } while ( u8g2.nextPage() );
```

If the confidence is below 0.5, the detection is considered unreliable, and the OLED displays **Number: None**.

```cpp
  }  else {
    no_counter++;
    if (no_counter > 2) {
      u8g2.firstPage();
      do {
        u8g2.setCursor(0, 15);
        u8g2.setFont(u8g2_font_ncenB10_tr);
        u8g2.print("Number: None");
      } while ( u8g2.nextPage() );
    }
  }
  delay(100);
}
```

### 3.6.2 micro：bit

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_50_02 Number Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_50_02 Number Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Click icon <img src="../_static/media/section_50_02 Number Recognition\media\image3.png" style="width:50px" /> on the left bottom of the interface, then click **Connect Device**, and follow the on-screen instructions to connect the micro:bit controller.

<img class="common_img" src="../_static/media/section_50_02 Number Recognition\media\image4.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_50_02 Number Recognition\media\image5.png" style="width:500px"  />

2)  Once connected, click the **Download** button in the bottom-left corner to flash the program to your micro:bit.

<img class="common_img" src="../_static/media/section_50_02 Number Recognition\media\image6.png" style="width:500px"  />

3)  After the download is complete, the indicator light will stop flashing rapidly and remain steadily lit. At the same time, a download completion message will appear on the programming interface.

<img class="common_img" src="../_static/media/section_50_02 Number Recognition\media\image7.png" style="width:500px"  />

* **Project Outcome**

Place the number cards one by one under the WonderCam vision module. When a number is recognized, the LED matrix will display the corresponding digit.

* **Program Brief Analysis**

1)  The program first sets an initial state when the device powers on. Here, it configures the WonderCam vision module’s communication address, switches its function to number recognition, and turns on the module’s fill light.

<img class="common_img" src="../_static/media/section_50_02 Number Recognition\media\image8.png" style="width:500px"  />

2)  An infinite loop is then used to continuously recognize number cards with the vision module. Numbers are identified based on confidence levels. The number cards have been pre-trained, and the trained model has been burned into the vision module along with the firmware pre-installed at the factory. When a recognized number has a confidence greater than 0.5 and is greater than 0, the LED matrix on the controller lights up the corresponding number. Otherwise, it displays “X.”

<img class="common_img" src="../_static/media/section_50_02 Number Recognition/media/image9.png" style="width:500px"  />

### 3.6.3 STM32F103

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

First, install the 32-bit microcontroller onto the open-source controller. Then, connect the WonderCam to the 4-pin connector, with the other end plugged into the I2C port on the controller as indicated by the arrows in the image below.

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image2.png" style="width:500px"  />

Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image3.png" style="width:500px"  />

* **Program Download**

1)  Connect the USB downloader to the controller using Dupont wires, and plug the USB end of the downloader into the computer.

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image4.png" style="width:500px"  />

2)  On the computer, open the FlyMcu tool. The application file for FlyMcu is located in the directory **[2. Software & Firmware/03 FlyMcu]()**.

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image5.png" style="width:500px"  />

3)  Select the port for the controller connection and set the baud rate to 115200. Do not choose COM1, as it is the system communication port.

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image6.png" style="width:500px"  />

4)  Click the button <img src="../_static/media/section_51_03 Number Recognition/media/image7.png" style="width:50px" /> to choose the program file to be flashed. Once selected, check the **Auto Reload Before Program**.

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image8.png" style="width:500px"  />

The program file is located in the directory **02 Program\Obj**.

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image9.png" style="width:500px"  />

5. Take down the jumper cap to the controller, press the **RST** reset button on the STM32 core board, turn on the power switch on the controller, and click **Start ISP(P)**.

   <img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image10.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image12.png" style="width:500px"  />

6)  After the program is successfully flashed, reconnect the jumper cap to the controller, press the **RST** button on the controller, and restart the controller.

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image13.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image14.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) Initialize the system clock, I2C interface, and serial port. The two previously created variables are as follows: number_id stores the ID of the recognized number, and prob stores the confidence level.

```c
	int number_id = 0;
	float prob = 0;
	SystemInit(); 		// System clock initialized to 72M  (系统时钟初始化为72M） SYSCLK_FREQ_72MHz
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);	// Set NVIC interrupt group 2: 2 bits preemption priority, 2 bits response priority (设置NVIC中断分组2:2位抢占优先级，2位响应优先级)
	InitDelay(72); 		// Initialize delay function (初始化延时函数)
	IIC_init();		// I2C initialization (IIC初始化)
	DelayMs(100);
	Usart1_Init(); // UART initialization (串口初始化)
	DelayMs(1000);
```

\(2\) After initialization, the serial port will print the "start" string. The WonderCam will then switch to number recognition mode.

```c
	printf(" ");
	printf("start\r\n");
	changeFunc(APPLICATION_NUMBERDETECT);
```

\(3\) Inside the while loop, the program updates and retrieves the number recognition results, storing the recognized number ID and its confidence level in two variables. When the confidence exceeds 0.4, the recognized number ID is printed to the serial port.

```c
	while(1)
	{
		updateResult();
		number_id = numberIdOfMaxProb();
		prob = numberMaxProb();
		if (prob > 0.4) 
		{
			printf("Recognize numbers ID: %d", number_id);
		}
		DelayMs(200);
	}
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_51_03 Number Recognition/media/image18.png" style="width:500px"  />

The above functions relate to number recognition: The first function retrieves the number ID with the highest recognition similarity. The second function retrieves the highest similarity value during recognition. The last function retrieves the highest similarity value for a specified ID.

### 3.6.4 STM32F407

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam with the 4-pin connector, and plug the other end into the I2C interface on the STM32 controller.

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Connect the Type-C cable to the Type-C port on the STM32 controller. Make sure to use the UART1 port as shown in the figure below and plug the other end into the USB port of your computer.

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image3.png" style="width:500px"  />

2)  Open the **Device Manager** on your computer and check the COM port number under the **Ports** section.

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image4.png" style="width:500px"  />

3)  Launch the ATK-XISP software in the directory **[2. Software & Firmware/04 ATK-XISP]()**, select the corresponding COM port, and set the baud rate to 115200.

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image5.png" style="width:500px"  />

4)  Configure the software as shown in the image below.

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image6.png" style="width:500px"  />

5)  click the "File" button and select the **"02 Program\MDK-ARM\digital_recognition\digital_recognition.hex**" file for programming.

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image7.png" style="width:500px"  />

6)  Click the “**Start Programming**” button on the interface to flash the generated .hex file to the STM32 controller. Wait for the programming to complete successfully.

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image8.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image9.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image10.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main program, first initialize the WonderCam object. Call the `ChangeFunc()` function to switch WonderCam to number recognition mode, and then turn off the vision module's fill light.

```c
	camset.Init(&camset);
	camset.ChangeFunc(&camset, APPLICATION_NUMBERDETECT);

	camset.SetLed(&camset,LED_OFF);
```

\(2\) Inside the while loop, call the `UpdateResult()` function to update the recognition results of the WonderCam vision module, and use the `numberIdOfMaxProb()` and `numberMaxProb()` functions to get the currently recognized number ID and its maximum confidence.

```c
  while (1)
  {
    /* USER CODE END WHILE */
	
    /* USER CODE BEGIN 3 */
		
		camset.UpdateResult(&camset);
		number_id = numberfunc.numberIdOfMaxProb();
		prob = numberfunc.numberMaxProb();
```

When the confidence exceeds 0.4, print the recognized number ID to the serial port with the format: "**Recognize numbers ID:** " + recognized number ID.

```c
		if (prob > 0.4) 
			printf("Recognize numbers ID: %d", number_id);
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_52_04 Number Recognition/media/image14.png" style="width:500px"  />

The above functions relate to number recognition: The first function retrieves the number ID with the highest recognition similarity. The second function retrieves the highest similarity value during recognition. The last function retrieves the highest similarity value for a specified ID.

### 3.6.5 Raspberry Pi

* **Project Process**

<img class="common_img" src="../_static/media/section_53_05 Number Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam to any I2C port on the Raspberry Pi expansion board.

<img class="common_img" src="../_static/media/section_53_05 Number Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1.  After turning on the Raspberry Pi, connect the computer to the corresponding Wi-Fi.

2.  Open the remote desktop software<img src="../_static/media/section_53_05 Number Recognition/media/image3.png" style="width:50px" />.

3.  In the search bar, enter "**192.168.149.1**" and press **Enter** to access the Raspberry Pi desktop.

<img class="common_img" src="../_static/media/section_53_05 Number Recognition/media/image4.png" style="width:500px"  />

4.  In the Username field, enter pi and in the Password field, enter raspberry. Check the **Remember password** box, then click the **OK** button. You will then see the Raspberry Pi desktop opened remotely.

<img class="common_img" src="../_static/media/section_53_05 Number Recognition/media/image5.png" style="width:500px"  />

The following image shows successful desktop access:

<img class="common_img" src="../_static/media/section_53_05 Number Recognition/media/image6.png" style="width:500px"  />

5.  Click the red box in the image below to transfer files.

<img class="common_img" src="../_static/media/section_53_05 Number Recognition/media/image7.png" style="width:500px"  />

6.  Click the button indicated by the red arrow in the image below, then select the file.

<img class="common_img" src="../_static/media/section_53_05 Number Recognition/media/image8.png" style="width:500px"  />

7.  Choose the "**recognition.py**" file and wait for the transfer to complete.

<img class="common_img" src="../_static/media/section_53_05 Number Recognition/media/image9.png" style="width:500px"  />

* **Project Outcome**

\(1\) Enter the command to navigate to the desktop.

```py
cd Desktop/
```

\(2\) Enter the command to run the program.

```py
python3 recognition.py
```

\(3\) After the program starts, the WonderCam vision module will automatically switch to “**Number Recognition**” mode. When the module detects a number card, it prints the recognized digit along with its accuracy.

* **Program Brief Analysis**

**1. Import Libraries**

Import the **time** library to control system delays, the **struct** library to parse data transmitted by the vision module, and the **smbus2** library for I2C communication with the WonderCam vision module.

```py
import time
import struct
from smbus2 import SMBus, i2c_msg
```

**2. Program Brief Analysis**

1. In the main program, a communication object for the vision module is created. The `cam.set_func()` function is used to switch the WonderCam to number recognition mode.

   ```py
   if __name__ == "__main__":
       cam = WonderCam()
       # """Number recognition （数字识别）"""
       cam.set_func(WONDERCAM_FUNC_NUMBER_REC)
   ```

2. Once initialization is complete, the program enters a loop. The `cam.update_result()` function is called to update the data received from WonderCam, and the `cam.set_led()` function is used to turn on the vision module's fill light.

   ```py
       while True:
           try:
               cam.update_result()
               cam.set_led(True)
               number = cam.number_most_likely()
   ```

3. The function `cam.number_most_likely()` is called to obtain the recognized digit ID. If the value of number is greater than 0, it indicates that a digit has been detected, and the detected number is printed. Then, `cam.number_max_conf()` is used to get the confidence level, which is also printed.

   ```py
               number = cam.number_most_likely()
               if number :
                   print("Detected number: %d （识别到数字： %d）" %number)
                   print("Accuracy: %f （准确度： %f）" %cam.number_max_conf())
               else:
                   print("No number detected （没有识别到数字）")
               time.sleep(0.5)
           except KeyboardInterrupt:
               break
           except Exception as e:
               print(e)
               continue
   ```

### 3.6.6 ESP32(Arduino)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_55_06.1 Number Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_55_06.1 Number Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Locate the program file “**Number_oled/Number_oled.ino**” in the same directory as this document.

<img class="common_img" src="../_static/media/section_55_06.1 Number Recognition\media\image3.png" style="width:500px"  />

2)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_55_06.1 Number Recognition\media\image4.png" style="width:500px"  />

3)  Click the **Select Board** option, search for **ESP32 Dev Module**, and click **OK**.

<img class="common_img" src="../_static/media/section_55_06.1 Number Recognition\media\image5.png" style="width:500px"  />

4)  Click <img src="../_static/media/section_55_06.1 Number Recognition\media\image6.png" style="width:50px" /> to download the program to the ESP32, and wait for the download to complete.

<img class="common_img" src="../_static/media/section_55_06.1 Number Recognition\media\image7.png" style="width:500px"  />

* **Project Outcome**

When the vision module detects a number, the recognition result is printed through the serial port.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication and sets the vision module to number recognition mode.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_NUMBER_REC);
}
```

**2. Main Function: loop()**

First, the recognition results are updated and retrieved. The detected digit ID and its confidence are assigned to the variables id and max_prob, and both values are printed to the serial port.

```cpp
void loop() {
  wc.updateResult();

  int id = wc.numberWithMaxProb();
  float max_prob = wc.numberMaxProb();
  if (max_prob > 0.4 && id > 0) {
    Serial.print("Numnber :");
    Serial.println(id);
  }
  delay(300);
}
```

### 3.6.7 ESP32(Python)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_56_06 Number Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_56_06 Number Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Open the Hiwonder Python editor software<img src="../_static/media/section_56_06 Number Recognition\media\image3.png" style="width:100px" />。

2)  Drag the "**main.py**" and "**WonderCam.py**" files from the **02 Program** folder in the same directory into the Wonder Python Editor.

<img class="common_img" src="../_static/media/section_56_06 Number Recognition\media\image4.png" style="width:500px"  />

3)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_56_06 Number Recognition\media\image5.png" style="width:500px"  />

4)  Click the Connect button <img src="../_static/media/section_56_06 Number Recognition\media\image6.png" style="width:50px" /> in the top menu. When successfully connected, the icon will turn green<img src="../_static/media/section_56_06 Number Recognition\media\image7.png" style="width:50px" />.

5)  After connecting, click the Download button <img src="../_static/media/section_56_06 Number Recognition\media\image8.png" style="width:50px" /> in the menu to upload the program to the ESP32 controller. Wait for the message box at the bottom of the editor to confirm the download is complete.

<img class="common_img" src="../_static/media/section_56_06 Number Recognition\media\image9.png" style="width:500px"  />

6)  Finally, click the Restart button <img src="../_static/media/section_56_06 Number Recognition\media\image10.png" style="width:50px" /> to reboot the ESP32 controller. The program will start running automatically.

* **Project Outcome**

When the vision module detects a number, the recognition result is printed through the serial port.

* **Program Brief Analysis**

1. First, initialize the I2C hardware interface using pins IO16 and IO17, initialize the WonderCam, and switch its function mode to number recognition.

   ```py
   i2c = I2C(0, scl=Pin(16), sda=Pin(17), freq=400000)
   colordetect = WonderCam(i2c)
   
   colordetect.set_func(WONDERCAM_FUNC_NUMBER_DETECT)
   ```

2. In the loop, the recognition results are continuously updated and retrieved. The detected digit ID and confidence are assigned to the variables `number_id` and `number_prob`. When the confidence is greater than 0.4 and the ID is not zero, the recognized digit ID is printed through the serial port.

   ```py
   while True:
     colordetect.update_result()
     number_id = colordetect.most_likely_id()
     number_prob = colordetect.max_conf()
     if number_prob > 0.4 and number_id > 0:
       print("number detect ID:",number_id)
     time.sleep_ms(200)
   ```

### 3.6.8 C51

* **Project Process**

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

\(1\) Install the 51 microcontroller onto the open-source controller.

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image2.png" style="width:500px"  />

\(2\) Connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the open-source controller. The ports indicated by the arrows in the image below are all I2C ports.

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image3.png" style="width:500px"  />

\(3\) Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image4.jpeg" style="width:500px"  />

The table below shows the wiring instructions for the USB-to-serial adapter and the open-source controller.

| **USB-to-Serial Adapter** | **Open-Source Controller** |
| :-----------------------: | :------------------------: |
|          5V(VCC)          |             5V             |
|          RX(RXD)          |             TX             |
|          TX(TXD)          |             RX             |
|            GND            |            GND             |

* **Program Download**

1)  Connect the USB-to-serial adapter to the computer.

2)  After confirming that the serial port driver is correctly installed, open "This PC" on the computer desktop. Then, click Properties -\> Device Manager and check the corresponding serial port number for the controller. **COM port numbers are not unique, and the device port ends with "CH340".**

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image5.png" style="width:500px"  />

3)  Remove the jumper cap from the 51 microcontroller and turn on the power switch to enter programming mode.

> [!NOTE]
>
> **Note:** If the controller is powered on first and then the jumper cap is removed, the controller needs to be turned off and then back on to enter programming mode.

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image6.png" style="width:500px"  />

Open the AIapp-ISP-v6.95E download tool in **2. Software & Firmware /05 AIapp-ISP-v6.95E**:

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image7.png" style="width:300px" />

The AIapp-ISP-v6.95E configuration is shown as follow:

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image8.png" style="width:700px"  />

Click “Open file” and find the hex file in “**02 Program\Objects**”

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image9.png" style="width:500px"  />

After opening the program file, click “**Download/Program**” to download the program to the 51 microcontroller.

> [!NOTE]
>
> **Note:** When downloading the program, you need to remove the jumper cap from the 51 microcontroller first. Once the AIapp-ISP-v6.95E tool in the lower right corner displays the message indicating it is detecting the target microcontroller, you can insert the jumper cap back in place. At this point, the program will start programming.

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image10.png" style="width:700px"  />

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image11.png" style="width:700px"  />

* **Project Outcome**

1)  When the vision module detects a number, the serial monitor displays the recognized digit.

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image12.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main function, the initialization of I2C and serial communication is performed first.

```c
void main(void)
{ 
	int number_id;
  float prob;
	P0M1=P0M0=P1M1=P1M0=P2M1=P2M0=P3M1=P3M0=P4M1=P4M0=P5M1=P5M0=P6M1=P6M0=P7M1=P7M0= 0;	
  I2C_Init();
	InitUart1();
	EA = 1;			// Enable global interrupt（开总中断）
```

\(2\) After initialization, the serial port prints "start". And the WonderCam's function is set to number recognition.

```c
	Delay_ms(1000);
	printf("start\r\n");
	changeFunc(APPLICATION_NUMBERDETECT);
```

\(3\) Continuously update and retrieve the recognition results from the vision module using the `numberIdOfMaxProb()` and `numberMaxProb()` functions to obtain the digit id and its confidence value prob.

```c
	for(;;)
	{
  updateResult();
  number_id = numberIdOfMaxProb();
	prob = numberMaxProb();
```

When the confidence prob is greater than 0.4, the recognized digit ID is printed through the serial port.

```c
  if (prob > 0.4) 
	{
    printf("Recognize numbers ID：%d", number_id);
	}
  Delay_ms(200);
  }
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_57_07 Number Recognition/media/image17.png" style="width:500px"  />

The above functions relate to number recognition: The first function retrieves the number ID with the highest recognition similarity. The second function retrieves the highest similarity value during recognition. The last function retrieves the highest similarity value for a specified ID.

## 3.7 Road Sign Recognition

### 3.7.1 Arduino UNO

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_58_01 Road Sign Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_58_01 Road Sign Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

Locate the "**12_Landmark_oled/12_Landmark_oled.ino**" program file in the same directory as this document.

<img class="common_img" src="../_static/media/section_58_01 Road Sign Recognition/media/image3.png" style="width:500px"  />

2)  Connect the Arduino controller to the computer using a USB Type-B cable for UNO.

<img class="common_img" src="../_static/media/section_58_01 Road Sign Recognition/media/image4.png" style="width:500px"  />

3)  In the Arduino IDE, click the **Select Board** option. The software will automatically detect the connected Arduino serial port, then click to connect.

<img class="common_img" src="../_static/media/section_58_01 Road Sign Recognition/media/image5.png" style="width:500px"  />

4)  Then click the upload button <img src="../_static/media/section_58_01 Road Sign Recognition/media/image6.png" style="width:50px" /> to upload the program to the Arduino. Wait for the upload to complete.

<img class="common_img" src="../_static/media/section_58_01 Road Sign Recognition/media/image7.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_58_01 Road Sign Recognition/media/image8.png" style="width:500px"  />

* **Project Outcome**

When the vision module detects a traffic sign, the OLED screen displays the corresponding English label. The mapping between the sign cards and the displayed names is shown below:

|                        **Sign Card**                         | **Displayed Name** |                        **Sign Card**                         | **Displayed Name** |
| :----------------------------------------------------------: | :----------------: | :----------------------------------------------------------: | :----------------: |
| <img src="../_static/media/section_58_01 Road Sign Recognition/media/image9.png" style="width:200px" /> |         Go         | <img src="../_static/media/section_58_01 Road Sign Recognition/media/image10.png" style="width:200px" /> |     Turn left      |
| <img src="../_static/media/section_58_01 Road Sign Recognition/media/image11.png" style="width:200px" /> |     Turn right     | <img src="../_static/media/section_58_01 Road Sign Recognition/media/image12.png" style="width:200px" /> |        Back        |
| <img src="../_static/media/section_58_01 Road Sign Recognition/media/image13.png" style="width:200px" /> |        Stop        |                                                              |                    |

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication, sets the vision module to road sign recognition mode, and initializes the OLED module.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_LANDMARK_REC);
  wc.setLed(true);
  u8g2.begin();
  u8g2.enableUTF8Print();    // enable UTF8 support for the Arduino print() function
}
```

**2. Main Function: loop()**

First, the recognition results are updated and retrieved. The detected traffic sign ID and confidence are assigned to the variables id and max_prob.

```cpp
uint8_t no_counter = 0;
void loop() {
  wc.updateResult();

  char buf[100];
  int id = wc.landmarkIdWithMaxProb();
  float max_prob = wc.landmarkMaxProb();
```

When the confidence is greater than 0.5, a switch statement is used to determine which sign has been detected. If the sign ID equals 1, it indicates that the “Go” sign is recognized, and the string "Go" is written to the OLED display buffer buf.

```cpp
  if (max_prob > 0.5 && id > 0) {
    switch (id) {
      case 1: {
          sprintf(buf, "Go");
          break;
        }
```

Similarly, when other signs are detected, their corresponding English labels are stored in the display buffer buf.

```cpp
      case 2: {
          sprintf(buf, "Turn left");
          break;
        }
      case 3: {
          sprintf(buf, "Turn right");
          break;
        }
      case 4: {
          sprintf(buf, "Back");
          break;
        }
      case 5: {
          sprintf(buf, "Stop");
          break;
        }
      default: {
          sprintf(buf, "");
          break;
        }
    }
```

Finally, the string in the buffer is sent to the OLED screen for display.

```cpp
    u8g2.firstPage();
    do {
      u8g2.setCursor(0, 15);
      u8g2.setFont(u8g2_font_ncenB10_tr);
      u8g2.print(buf);
    } while ( u8g2.nextPage());
```

If no traffic sign is detected, the OLED screen is cleared.

```cpp
  }  else {
    no_counter++;
    if (no_counter > 2) {
      u8g2.clearDisplay();
    }
  }
  delay(100);
}
```

### 3.7.2 micro：bit

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Click icon <img src="../_static/media/section_59_02 Road Sign Recognition/media/image3.png" style="width:50px" /> on the left bottom of the interface, then click **Connect Device**, and follow the on-screen instructions to connect the micro:bit controller.

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image4.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image5.png" style="width:500px"  />

2)  Once connected, click the **Download** button in the bottom-left corner to flash the program to your micro:bit.

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image6.png" style="width:500px"  />

3)  After the download is complete, the indicator light will stop flashing rapidly and remain steadily lit. At the same time, a download completion message will appear on the programming interface.

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image7.png" style="width:500px"  />

* **Project Outcome**

Place the sign cards one at a time under the WonderCam vision module. When a sign is recognized, the LED matrix display shows the corresponding sign graphic.

* **Program Brief Analysis**

1)  The program first sets an initial state when the device powers on. Here, it configures the WonderCam vision module’s communication address, switches its function to road sign recognition, and turns on the module’s fill light.

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image8.png" style="width:500px"  />

2)  Set up an infinite loop so that the vision module continuously recognizes the road sign cards. The signs are identified based on confidence values. The road sign cards have been pre-trained, and the trained model is preloaded into the vision module along with the firmware which is already programmed at the factory. When the detected sign’s confidence exceeds 0.5 and the recognition result is not empty, the LED matrix on the controller lights up the corresponding direction as feedback. Otherwise, it displays an “X”.

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image9.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image10.png" style="width:500px"  />

3)  Each road sign triggers a specific LED matrix pattern. The corresponding patterns are shown in the figure below:

<img class="common_img" src="../_static/media/section_59_02 Road Sign Recognition/media/image11.png" style="width:500px"  />

### 3.7.3 STM32F103

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

First, install the 32-bit microcontroller onto the open-source controller. Then, connect the WonderCam to the 4-pin connector, with the other end plugged into the I2C port on the controller as indicated by the arrows in the image below.

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image2.png" style="width:500px"  />

Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image3.png" style="width:500px"  />

* **Program Download**

1)  Connect the USB downloader to the controller using Dupont wires, and plug the USB end of the downloader into the computer.

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image4.png" style="width:500px"  />

2)  On the computer, open the FlyMcu tool. The application file for FlyMcu is located in the directory **[2. Software & Firmware/03 FlyMcu]()**.

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image5.png" style="width:500px"  />

3)  Select the port for the controller connection and set the baud rate to 115200. Do not choose COM1, as it is the system communication port.

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image6.png" style="width:500px"  />

4)  Click the button <img src="../_static/media/section_60_03 Road Sign Recognition/media/image7.png" style="width:50px" /> to choose the program file to be flashed. Once selected, check the **Auto Reload Before Program**.

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image8.png" style="width:500px"  />

The program file is located in the directory **02 Program\Obj**.

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image9.png" style="width:500px"  />

5. Take down the jumper cap to the controller, press the **RST** reset button on the STM32 core board, turn on the power switch on the controller, and click **Start ISP(P)**.

   <img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image10.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image12.png" style="width:500px"  />

6)  After the program is successfully flashed, reconnect the jumper cap to the controller, press the **RST** button on the controller, and restart the controller.

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image13.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image14.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) Initialize the system clock, I2C interface, and serial port. The two previously created variables are as follows: landmark_id stores the ID of the recognized road sign, and prob stores the confidence level.

```c
	int landmark_id = 0;
	float prob = 0;
	SystemInit(); 		//// System clock initialized to 72M （系统时钟初始化为72M）	  SYSCLK_FREQ_72MHz
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);	// Set NVIC interrupt group 2: 2 bits preemption priority, 2 bits response priority (设置NVIC中断分组2:2位抢占优先级，2位响应优先级)
	InitDelay(72); 		// Initialize delay function (初始化延时函数)
	IIC_init();		// I2C initialization (IIC初始化)
	DelayMs(100);
	Usart1_Init(); // UART initialization (串口初始化)
	DelayMs(1000);
```

\(2\) After initialization, the serial port will print the "start" string. The WonderCam will then switch to road sign recognition mode.

```c
	printf(" ");
	printf("start\r\n");
	changeFunc(APPLICATION_LANDMARKDETECT);
```

\(3\) The recognition results are then updated and retrieved. The detected sign ID and confidence are stored in the two variables. When the confidence exceeds 0.4, the recognized traffic sign ID is printed through the serial port.

```c
	while(1)
	{
		updateResult();
		landmark_id = landmarkIdOfMaxProb();
		prob = landmarkMaxProb();
		if (prob > 0.4)
		{
			printf("landmark ID: %d", landmark_id);
		}
		DelayMs(200);
	}
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_60_03 Road Sign Recognition/media/image18.png" style="width:500px"  />

The above functions relate to road sign recognition: The first function retrieves the road sign ID with the highest recognition similarity. The second function retrieves the highest similarity value during recognition. The last function retrieves the highest similarity value for a specified ID.

### 3.7.4 STM32F407

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam with the 4-pin connector, and plug the other end into the I2C interface on the STM32 controller.

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Connect the Type-C cable to the Type-C port on the STM32 controller. Make sure to use the UART1 port as shown in the figure below and plug the other end into the USB port of your computer.

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image3.png" style="width:500px"  />

2)  Open the **Device Manager** on your computer and check the COM port number under the **Ports** section.

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image4.png" style="width:500px"  />

3)  Launch the ATK-XISP software in the directory **[2. Software & Firmware/04 ATK-XISP]()**, select the corresponding COM port, and set the baud rate to 115200.

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image5.png" style="width:500px"  />

4)  Configure the software as shown in the image below.

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image6.png" style="width:500px"  />

5)  click the "File" button and select the **"02 Program\MDK-ARM\landmark_identification\landmark_identification.hex**" file for programming.

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image7.png" style="width:500px"  />

6)  Click the “Start Programming” button on the interface to flash the generated .hex file to the STM32 controller. Wait for the programming to complete successfully.

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image8.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image9.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image10.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main program, first initialize the WonderCam object. Call the `ChangeFunc()` function to switch WonderCam to road sign recognition mode, and then turn off the vision module's fill light.

```c
	camset.Init(&camset);
	camset.ChangeFunc(&camset, APPLICATION_LANDMARKDETECT);
	camset.SetLed(&camset,LED_OFF);
```

\(2\) Inside the while loop, call the `UpdateResult()` function to update the recognition results of the WonderCam vision module, and use the `landmarkIdOfMaxProb()` and `landmarkMaxProb()` functions to get the currently recognized road sign ID and its maximum confidence.

```c
  while (1)
  {
    /* USER CODE END WHILE */
	
    /* USER CODE BEGIN 3 */
		
		
		camset.UpdateResult(&camset);
		int landmark_id = landmarkfunc.landmarkIdOfMaxProb();
		float prob = landmarkfunc.landmarkMaxProb();
```

When the confidence exceeds 0.4, print the recognized road sign ID to the serial port with the format: "landmark ID: " + recognized road sign ID.

```c
		if(prob > 0.4)
			printf("landmark ID: %d", landmark_id);
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_61_04 Road Sign Recognition\media\image14.png" style="width:500px"  />

The above functions relate to road sign recognition: The first function retrieves the road sign ID with the highest recognition similarity. The second function retrieves the highest similarity value during recognition. The last function retrieves the highest similarity value for a specified ID.

### 3.7.5 Raspberry Pi

* **Project Process**

<img class="common_img" src="../_static/media/section_62_05 Road Sign Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam to any I2C port on the Raspberry Pi expansion board.

<img class="common_img" src="../_static/media/section_62_05 Road Sign Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1.  After turning on the Raspberry Pi, connect the computer to the corresponding Wi-Fi.

2.  Open the remote desktop software<img src="../_static/media/section_62_05 Road Sign Recognition\media\image3.png" style="width:50px" />.

3.  In the search bar, enter "**192.168.149.1**" and press **Enter** to access the Raspberry Pi desktop.

<img class="common_img" src="../_static/media/section_62_05 Road Sign Recognition\media\image4.png" style="width:500px"  />

4.  In the Username field, enter pi and in the Password field, enter raspberry. Check the "**Remember password**" box, then click the "**OK**" button. You will then see the Raspberry Pi desktop opened remotely.

<img class="common_img" src="../_static/media/section_62_05 Road Sign Recognition\media\image5.png" style="width:500px"  />

The following image shows successful desktop access:

<img class="common_img" src="../_static/media/section_62_05 Road Sign Recognition\media\image6.png" style="width:500px"  />

5.  Click the red box in the image below to transfer files.

<img class="common_img" src="../_static/media/section_62_05 Road Sign Recognition\media\image7.png" style="width:500px"  />

6.  Click the button indicated by the red arrow in the image below, then select the file.

<img class="common_img" src="../_static/media/section_62_05 Road Sign Recognition\media\image8.png" style="width:500px"  />

7.  Choose the "**sign_recognition.py**" file and wait for the transfer to complete.

<img class="common_img" src="../_static/media/section_62_05 Road Sign Recognition\media\image9.png" style="width:500px"  />

* **Project Outcome**

\(1\) Enter the command to navigate to the desktop.

```py
cd Desktop/
```

\(2\) Enter the command to run the program.

```py
python3 sign_recognitio.py
```

\(3\) After the program starts, the WonderCam vision module will automatically switch to “**Road Sign Recognition**” mode. When the module detects a traffic sign card, it prints the recognized sign along with its confidence level.

* **Program Brief Analysis**

**1. Import Libraries**

Import the **time** library to control system delays, the **struct** library to parse data transmitted by the vision module, and the **smbus2** library for I2C communication with the WonderCam vision module.

```py
import time
import struct
from smbus2 import SMBus, i2c_msg
```

**2. Program Brief Analysis**

1. In the main program, a communication object for the vision module is created. The `cam.set_func()` function is used to switch the WonderCam to road sign recognition mode.

   ```py
   if __name__ == "__main__":
       cam = WonderCam()
   
       # """Landmark recognition（路标识别）"""
       cam.set_func(WONDERCAM_FUNC_LANDMARK_REC)
   ```

2. Once initialization is complete, the program enters a loop. The `cam.update_result()` function is called to update the data received from WonderCam, and the `cam.set_led()` function is used to turn on the vision module's fill light.

   ```py
       while True:
           try:
               cam.update_result()
               cam.set_led(True)
   ```

3. Call `cam.landmark_most_likely()` to obtain the detected traffic sign ID and print it. Then, use `cam.landmark_max_conf()` to retrieve the confidence level and print it.

   ```py
               lm = cam.landmark_most_likely()
               name = {1:"No landmark detected （没有识别到路标）",
   					2:"Move forward （前进）", 
   					3:"Turn left（左转）", 
   					4:"Turn right（右转）", 
   					5:"Make a U-turn （调头）", 
   					6:"Stop（停止）"}[lm]
               print(name)
               if lm > 1:
                   print("Accuracy：（准确度）%f"%cam.landmark_max_conf())
               time.sleep(0.5)
           except KeyboardInterrupt:
               break
           except Exception as e:
               print(e)
               continue
   ```

### 3.7.6 ESP32(Arduino)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_64_06.1 Road Sign Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_64_06.1 Road Sign Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Locate the program file “**Number_oled/Number_oled.ino**” in the same directory as this document.

<img class="common_img" src="../_static/media/section_64_06.1 Road Sign Recognition\media\image3.png" style="width:500px"  />

2)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_64_06.1 Road Sign Recognition\media\image4.png" style="width:500px"  />

3)  Click the **Select Board** option, search for **ESP32 Dev Module**, and click **OK**.

<img class="common_img" src="../_static/media/section_64_06.1 Road Sign Recognition\media\image5.png" style="width:500px"  />

4)  Click <img src="../_static/media/section_64_06.1 Road Sign Recognition\media\image6.png" style="width:50px" /> to download the program to the ESP32, and wait for the download to complete.

<img class="common_img" src="../_static/media/section_64_06.1 Road Sign Recognition\media\image7.png" style="width:500px"  />

* **Project Outcome**

When the vision module detects a traffic sign, the sign’s ID and confidence level are printed through the serial port.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication and sets the vision module to road sign recognition mode.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_LANDMARK_REC);
}
```

**2. Main Function: loop()**

First, the recognition results are updated and retrieved. The detected traffic sign ID and confidence are assigned to the variables id and max_prob. Next, if the confidence of the currently detected traffic sign exceeds 0.4, the sign card’s ID and confidence are printed through the serial port.

```cpp
void loop() {
  wc.updateResult();

  int id = wc.landmarkMaxProb();
  float max_prob = wc.landmarkProbOfId();
  if (max_prob > 0.4 && id > 0) {
    Serial.print("landmarks id:");
    Serial.println(id);
  }
  delay(300);
}
```

### 3.7.7 ESP32(Python)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_65_06 Road Sign Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_65_06 Road Sign Recognition\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Open the Hiwonder Python editor software<img src="../_static/media/section_65_06 Road Sign Recognition\media\image3.png" style="width:100px" />。

2)  Drag the "**main.py**" and "**WonderCam.py**" files from the **02 Program** folder in the same directory into the Wonder Python Editor.

<img class="common_img" src="../_static/media/section_65_06 Road Sign Recognition\media\image4.png" style="width:500px"  />

3)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_65_06 Road Sign Recognition\media\image5.png" style="width:500px"  />

4)  Click the Connect button <img src="../_static/media/section_65_06 Road Sign Recognition\media\image6.png" style="width:50px" /> in the top menu. When successfully connected, the icon will turn green<img src="../_static/media/section_65_06 Road Sign Recognition\media\image7.png" style="width:50px" />.

5)  After connecting, click the Download button <img src="../_static/media/section_65_06 Road Sign Recognition\media\image8.png" style="width:50px" /> in the menu to upload the program to the ESP32 controller. Wait for the message box at the bottom of the editor to confirm the download is complete.

<img class="common_img" src="../_static/media/section_65_06 Road Sign Recognition\media\image9.png" style="width:500px"  />

6)  Finally, click the Restart button <img src="../_static/media/section_65_06 Road Sign Recognition\media\image10.png" style="width:50px" /> to reboot the ESP32 controller. The program will start running automatically.

* **Project Outcome**

When the vision module detects a traffic sign, the sign’s ID and confidence level are printed through the serial port.

* **Program Brief Analysis**

1. First, initialize the I2C hardware interface using pins IO16 and IO17, initialize the WonderCam, and switch its function mode to road sign recognition.

   ```py
   i2c = I2C(0, scl=Pin(16), sda=Pin(17), freq=400000)
   colordetect = WonderCam(i2c)
   
   colordetect.set_func(WONDERCAM_FUNC_LANDMARKDETECT)
   ```

2. In the loop, the recognition results are first updated and retrieved. The detected traffic sign ID and confidence are assigned to the variables feature_id and feature_prob. When the confidence is greater than 0.4 and the ID is not zero, the recognized traffic sign ID is printed through the serial port.

   ```py
   while True:
     colordetect.update_result()
     feature_id = colordetect.most_likely_id()
     feature_prob = colordetect.max_conf()
     if feature_prob > 0.4 and feature_id > 0:
       print("feature ID:",feature_id)
     time.sleep_ms(200)
   ```

### 3.7.8 C51

* **Project Process**

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

\(1\) Install the 51 microcontroller onto the open-source controller.

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image2.png" style="width:500px"  />

\(2\) Connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the open-source controller. The ports indicated by the arrows in the image below are all I2C ports.

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image3.png" style="width:500px"  />

\(3\) Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image4.jpeg" style="width:500px"  />

The table below shows the wiring instructions for the USB-to-serial adapter and the open-source controller.

| **USB-to-Serial Adapter** | **Open-Source Controller** |
| :-----------------------: | :------------------------: |
|          5V(VCC)          |             5V             |
|          RX(RXD)          |             TX             |
|          TX(TXD)          |             RX             |
|            GND            |            GND             |

* **Program Download**

1)  Connect the USB-to-serial adapter to the computer.

2)  After confirming that the serial port driver is correctly installed, open **This PC** on the computer desktop. Then, click **Properties** -\> **Device Manager** and check the corresponding serial port number for the controller. **COM port numbers are not unique, and the device port ends with "CH340".**

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image5.png" style="width:500px"  />

3)  Remove the jumper cap from the 51 microcontroller and turn on the power switch to enter programming mode.

> [!NOTE]
>
> **Note:** If the controller is powered on first and then the jumper cap is removed, the controller needs to be turned off and then back on to enter programming mode.

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image6.png" style="width:500px"  />

Open the AIapp-ISP-v6.95E download tool in **[2. Software & Firmware /05 AIapp-ISP-v6.95E]()**:

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image7.png" style="width:300px" />

The AIapp-ISP-v6.95E configuration is shown as follows:

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image8.png" style="width:700px"  />

Click “Open file” and find the hex file in “**02 Program\Objects**”

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image9.png" style="width:500px"  />

After opening the program file, click “**Download/Program**” to download the program to the 51 microcontroller.

> [!NOTE]
>
> **Note:** When downloading the program, you need to remove the jumper cap from the 51 microcontroller first. Once the AIapp-ISP-v6.95E tool in the lower right corner displays the message indicating it is detecting the target microcontroller, you can insert the jumper cap back in place. At this point, the program will start programming.

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image10.png" style="width:700px"  />

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image11.png" style="width:700px"  />

* **Project Outcome**

When the vision module detects a traffic sign, the corresponding sign ID is displayed on the serial monitor.

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image12.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main function, the initialization of I2C and serial communication is performed first.

```c
void main(void)
{ 
	int landmark_id;
  float prob;
	P0M1=P0M0=P1M1=P1M0=P2M1=P2M0=P3M1=P3M0=P4M1=P4M0=P5M1=P5M0=P6M1=P6M0=P7M1=P7M0= 0;	
  I2C_Init();
	InitUart1();
	EA = 1;			// Enable global interrupt（开总中断）
```

\(2\) After initialization, the serial port prints "start". And the WonderCam's function is set to road sign recognition.

```c
	Delay_ms(1000);
	printf("start\r\n");
	changeFunc(APPLICATION_LANDMARKDETECT);
```

(3) Continuously update and retrieve the recognition results from the vision module. Use the functions `landmarkIdOfMaxProb()` and `landmarkMaxProb()` to obtain the traffic sign card’s id and confidence prob.

```c
	for(;;)
	{
  updateResult();
  landmark_id = landmarkIdOfMaxProb();
```

When the confidence prob is greater than 0.4, the recognized roadside sign ID is printed through the serial port.

```c
	prob = landmarkMaxProb();
  if (prob > 0.4) 
	{
    printf("feature ID：%d", landmark_id);
	}
  Delay_ms(200);
  }
}
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_66_07 Road Sign Recognition\media\image17.png" style="width:500px"  />

The above functions relate to road sign recognition: The first function retrieves the road sign ID with the highest recognition similarity. The second function retrieves the highest similarity value during recognition. The last function retrieves the highest similarity value for a specified ID.

## 3.8 Image Classification

### 3.8.1 Arduino UNO

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_67_01 Image Classification\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_67_01 Image Classification\media\image2.png" style="width:500px"  />

* **Program Download**

Locate the "**03_Classific_DISP/03_Classific_DISP.ino**" program file in the same directory as this document.

<img class="common_img" src="../_static/media/section_67_01 Image Classification\media\image3.png" style="width:500px"  />

2)  Connect the Arduino controller to the computer using a USB Type-B cable for UNO.

<img class="common_img" src="../_static/media/section_67_01 Image Classification\media\image4.png" style="width:500px"  />

3)  In the Arduino IDE, click the **Select Board** option. The software will automatically detect the connected Arduino serial port, then click to connect.

<img class="common_img" src="../_static/media/section_67_01 Image Classification\media\image5.png" style="width:500px"  />

4)  Then click the upload button <img src="../_static/media/section_67_01 Image Classification\media\image6.png" style="width:50px" /> to upload the program to the Arduino. Wait for the upload to complete.

<img class="common_img" src="../_static/media/section_67_01 Image Classification\media\image7.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_67_01 Image Classification\media\image8.png" style="width:500px"  />

* **Project Outcome**

The image classification feature works together with the 12 waste-sorting cards we provide. In the program, these cards are grouped into four categories. When the WonderCam vision module is pointed at any of the cards, it will recognize the category and display the corresponding English name for that waste type on the OLED screen.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication, sets the vision module to image classification mode, and initializes the OLED module.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_CLASSIFICATION);
  wc.setLed(true);

  u8g2.begin();
  u8g2.enableUTF8Print();    // enable UTF8 support for the Arduino print() function
}
```

**2. Main Function: loop()**

First, the recognition results are updated and retrieved. The detected image ID and confidence are assigned to the variables id and max_prob.

```cpp
void loop() {
  wc.updateResult();
  int class_id = wc.classIdOfMaxProb();
  float prob = wc.classMaxProb();
```

When the confidence is greater than 0.4, if the current image ID is less than 2, the screen displays “**None**”, indicating that no valid card is detected.

```cpp
  if (prob > 0.4) {
    if (class_id < 2) {
    u8g2.setFont(u8g2_font_unifont_t_chinese2);
    u8g2.setFontDirection(0);
    u8g2.firstPage();
    do {
      u8g2.setCursor(0, 15);
      u8g2.print("None");
    } while ( u8g2.nextPage() );
```

If the image ID is greater than or equal to 2 and less than 5, the screen displays “**Harmful waste**”, indicating hazardous waste.

```cpp
    } else if (class_id < 5) {
      u8g2.setFont(u8g2_font_unifont_t_chinese2);
      u8g2.setFontDirection(0);
      u8g2.firstPage();
      do {
        u8g2.setCursor(0, 15);
        u8g2.print("Harmful Waste");
      } while ( u8g2.nextPage() );
```

If the image ID is greater than or equal to 5 and less than 8, the screen displays “**Recyclable waste**”, indicating recyclable waste.

```cpp
    } else if (class_id < 8) {
      u8g2.setFont(u8g2_font_unifont_t_chinese2);
      u8g2.setFontDirection(0);
      u8g2.firstPage();
      do {
        u8g2.setCursor(0, 15);
        u8g2.print("Recyclable waste");
      } while ( u8g2.nextPage() );
```

If the image ID is greater than or equal to 8 and less than 11, the screen displays “**Wet garbage**”, indicating wet waste.

```cpp
    } else if (class_id < 11) {
      u8g2.setFont(u8g2_font_unifont_t_chinese2);
      u8g2.setFontDirection(0);
      u8g2.firstPage();
      do {
        u8g2.setCursor(0, 15);
        u8g2.print("Wet garbage");
      } while ( u8g2.nextPage() );
```

If the image ID is greater than or equal to 11, the screen displays “**Dry garbage**”, indicating dry waste.

```cpp
    } else {
      u8g2.setFont(u8g2_font_unifont_t_chinese2);
      u8g2.setFontDirection(0);
      u8g2.firstPage();
      do {
        u8g2.setCursor(0, 15);
        u8g2.print("Dry garbage");
      } while ( u8g2.nextPage() );
    }
  }
```

### 3.8.2 micro：bit

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Click icon <img src="../_static/media/section_68_02 Image Classification\media\image3.png" style="width:50px" /> on the left bottom of the interface, then click **Connect Device**, and follow the on-screen instructions to connect the micro:bit controller.

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image4.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image5.png" style="width:500px"  />

2)  Once connected, click the **Download** button in the bottom-left corner to flash the program to your micro:bit.

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image6.png" style="width:500px"  />

3)  After the download is complete, the indicator light will stop flashing rapidly and remain steadily lit. At the same time, a download completion message will appear on the programming interface.

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image7.png" style="width:500px"  />

* **Project Outcome**

The image classification feature works with the 12 waste-sorting cards provided. In the program, the cards are divided into four categories, each represented by the first letter of its English name: H for harmful waste, R for recyclable waste, D for dry waste, and W for wet waste. When the WonderCam vision module is pointed at any card, it identifies the category, lights up the indicator for that waste type, and displays the corresponding letter on the LED matrix.

* **Program Brief Analysis**

1)  The program first sets an initial state when the device powers on. Here, it configures the WonderCam vision module’s communication address, switches its function to image recognition, and turns on the module’s fill light. And the “no_count” variable is decremented. When its value reaches 0, both the RGB light and the LED are turned off.

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image8.png" style="width:500px"  />

2)  Set up an infinite loop so the vision module continuously recognizes the waste-sorting cards. The card category is determined by its ID, and the ID is identified based on the confidence value. The waste-sorting cards have been pre-trained to generate a model, and the closer the confidence is to 1, the more accurate the detected ID will be.

3)  If the detected ID is less than 2, meaning no valid waste card is recognized, the “no_count” variable decreases by 1. When “no_count” reaches 0, the “valid_count” variable is also reset to 0, clearing all stored values to start the next recognition cycle, and both the RGB light and the LED matrix on the controller are turned off.

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image9.png" style="width:500px"  />

4)  During recognition, each newly detected ID is compared with the previously detected result. If the two IDs match, the “**valid_count**” variable increases by 1. When the count reaches 5, meaning the same result has been detected five times consecutively, the buzzer plays a designated sound as feedback. This mechanism ensures recognition accuracy by only outputting the result after five consistent detections.

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image10.png" style="width:500px"  />

5)  If the recognized card ID is less than 5, meaning the ID is between 2 and 4, the card is classified as harmful waste. In this case, the RGB light turns red and the LED matrix displays “H” as feedback.

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image11.png" style="width:500px"  />

6)  If the ID is less than 8, meaning the ID is between 5 and 7, the card is classified as recyclable waste. The RGB light turns blue and the LED matrix displays “R” as feedback.

<img class="common_img" src="../_static/media/section_68_02 Image Classification\media\image12.png" style="width:500px"  />

7)  Feedback behaviors for wet waste and dry waste are shown in the figure below.

<img class="common_img" src="../_static/media/section_68_02 Image Classification/media/image13.png" style="width:500px"  />

### 3.8.3 STM32F103

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

First, install the 32-bit microcontroller onto the open-source controller. Then, connect the WonderCam to the 4-pin connector, with the other end plugged into the I2C port on the controller as indicated by the arrows in the image below.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image2.png" style="width:500px"  />

Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image3.png" style="width:500px"  />

* **Program Download**

1)  Connect the USB downloader to the controller using Dupont wires, and plug the USB end of the downloader into the computer.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image4.png" style="width:500px"  />

2)  On the computer, open the FlyMcu tool. The application file for FlyMcu is located in the directory **[2. Software & Firmware/03 FlyMcu]()**.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image5.png" style="width:500px"  />

3)  Select the port for the controller connection and set the baud rate to 115200. Do not choose COM1, as it is the system communication port.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image6.png" style="width:500px"  />

4)  Click the button <img src="../_static/media/section_69_03 Image Classification\media\image7.png" style="width:50px" /> to choose the program file to be flashed. Once selected, check the ”Auto Reload Before Program”.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image8.png" style="width:500px"  />

The program file is located in the directory **02 Program\Obj**.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image9.png" style="width:500px"  />

5. Take down the jumper cap to the controller, press the **RST** reset button on the STM32 core board, turn on the power switch on the controller, and click **Start ISP(P)**.

   <img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image10.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image12.png" style="width:500px"  />

6)  After the program is successfully flashed, reconnect the jumper cap to the controller, press the **RST** button on the controller, and restart the controller.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image13.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image14.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) Initialize the system clock, I2C interface, and serial port.

```c
	uint8_t x;
	SystemInit(); 		// System clock initialized to 72M (系统时钟初始化为72M）  SYSCLK_FREQ_72MHz
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);	// Set NVIC interrupt group 2: 2 bits preemption priority, 2 bits response priority (设置NVIC中断分组2:2位抢占优先级，2位响应优先级)
	InitDelay(72); 		// Initialize delay function (初始化延时函数)
	IIC_init();		// I2C initialization (IIC初始化)
	DelayMs(100);
	Usart1_Init(); // UART initialization (串口初始化)
	DelayMs(1000);
```

\(2\) After initialization, the serial port will print the "start" string. The WonderCam will then switch to image classification mode, and the fill light will be turned on.

```c
	printf(" ");
	printf("start\r\n");
	changeFunc(APPLICATION_CLASSIFICATION);
	setLed(true);
```

\(3\) Inside the while loop, the recognition results are continuously updated and retrieved. Two variables are created: class_id to store the detected image ID, and prob to store the confidence score. When the confidence is greater than 0.4, further classification is performed.

```c
	while(1)
	{
  updateResult();
  int class_id = classIdOfMaxProb();
  float prob = classMaxProb();
  if (prob > 0.4) {
```

If the image ID is less than 2, it indicates a blank image, meaning no valid card is detected. When the ID is greater than or equal to 2 and less than 5, the card is classified as harmful waste. When the ID is greater than or equal to 5 and less than 8, it is classified as recyclable waste. When the ID is greater than or equal to 8 and less than 11, it is classified as wet waste. When the ID is greater than or equal to 11, it is classified as dry waste.

```c
    if (class_id < 2) {
      printf("None\r\n"); 
    } else if (class_id < 5) {
        printf("Harmful Waste\r\n");
    } else if (class_id < 8) {
        printf("Recyclable waste\r\n");
    } else if (class_id < 11) {
        printf("Wet garbage\r\n");
    } else {
        printf("Dry garbage\r\n");
    }
```

* **Feature Extensions**

```c
  if (prob > 0.4) {
    if (class_id < 2) {
      printf("None\r\n"); 
    } else if (class_id < 5) {
        printf("Harmful Waste\r\n");
    } else if (class_id < 8) {
        printf("Recyclable waste\r\n");
    } else if (class_id < 11) {
        printf("Wet garbage\r\n");
    } else {
        printf("Dry garbage\r\n");
    }
  }
```

The detected ID is used to classify the card into harmful waste, recyclable waste, wet waste, or dry waste.

<img class="common_img" src="../_static/media/section_69_03 Image Classification\media\image20.png" style="width:500px"  />

The code above is responsible for obtaining the card’s ID and similarity scores. Specifically, it includes: the ID of the card with the highest similarity, the maximum confidence of the detected target card, and the similarity for a specified ID.

### 3.8.4 STM32F407

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam with the 4-pin connector, and plug the other end into the I2C interface on the STM32 controller.

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Connect the Type-C cable to the Type-C port on the STM32 controller. Make sure to use the UART1 port as shown in the figure below and plug the other end into the USB port of your computer.

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image3.png" style="width:500px"  />

2)  Open the **Device Manager** on your computer and check the COM port number under the **Ports** section.

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image4.png" style="width:500px"  />

3)  Launch the ATK-XISP software in the directory **[2. Software & Firmware/04 ATK-XISP]()**, select the corresponding COM port, and set the baud rate to 115200.

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image5.png" style="width:500px"  />

4)  Configure the software as shown in the image below.

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image6.png" style="width:500px"  />

5)  click the "File" button and select the **"02 Program\MDK-ARM\class_identification\class_identification.hex**" file for programming.

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image7.png" style="width:500px"  />

6)  Click the “Start Programming” button on the interface to flash the generated .hex file to the STM32 controller. Wait for the programming to complete successfully.

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image8.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image9.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image10.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main program, first initialize the WonderCam object. Call the `ChangeFunc()` function to switch WonderCam to image recognition mode, and then turn off the vision module's fill light.

```c
	camset.Init(&camset);
	camset.ChangeFunc(&camset, APPLICATION_CLASSIFICATION);
	camset.SetLed(&camset,LED_OFF);
```

\(2\) Inside the while loop, call the `UpdateResult()` function to update the recognition results of the WonderCam vision module, and use the `classIdOfMaxProb()` and `classkMaxProb()` functions to get the currently recognized image ID and its maximum confidence.

```c
  while (1)
  {
    /* USER CODE END WHILE */
	
    /* USER CODE BEGIN 3 */
		
		
		camset.UpdateResult(&camset);
		int class_id = classificationfunc.classIdOfMaxProb();
		float prob = classificationfunc.classMaxProb();
```

\(3\) When the confidence is greater than 0.4, the image is classified based on its ID: IDs 0–1 indicate no image, IDs 2–4 indicate harmful waste, IDs 5–7 indicate recyclable waste, IDs 8–10 indicate wet waste, and IDs 11–13 indicate dry waste.

```c
		if (prob > 0.4) {
			if (class_id < 2) {
				printf("None\r\n"); 
			} else if (class_id < 5) {
					printf("Harmful Waste\r\n");
			} else if (class_id < 8) {
					printf("Recyclable waste\r\n");
			} else if (class_id < 11) {
					printf("Wet garbage\r\n");
			} else {
					printf("Dry garbage\r\n");
			}
		}
```

* **Feature Extensions**

```c
		if (prob > 0.4) {
			if (class_id < 2) {
				printf("None\r\n"); 
			} else if (class_id < 5) {
					printf("Harmful Waste\r\n");
			} else if (class_id < 8) {
					printf("Recyclable waste\r\n");
			} else if (class_id < 11) {
					printf("Wet garbage\r\n");
			} else {
					printf("Dry garbage\r\n");
			}
		}
```

The detected ID is used to classify the card into harmful waste, recyclable waste, wet waste, or dry waste.

<img class="common_img" src="../_static/media/section_70_04 Image Classification\media\image15.png" style="width:500px"  />

The code above is responsible for obtaining the card’s ID and similarity scores. Specifically, it includes: the ID of the card with the highest similarity, the maximum confidence of the detected target card, and the similarity for a specified ID.

### 3.8.5 Raspberry Pi

* **Project Process**

<img class="common_img" src="../_static/media/section_71_05 Image Classification\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam to any I2C port on the Raspberry Pi expansion board.

<img class="common_img" src="../_static/media/section_71_05 Image Classification\media\image2.png" style="width:500px"  />

* **Program Download**

1.  After turning on the Raspberry Pi, connect the computer to the corresponding Wi-Fi.

2.  Open the remote desktop software<img src="../_static/media/section_71_05 Image Classification\media\image3.png" style="width:50px" />.

3.  In the search bar, enter "**192.168.149.1**" and press **Enter** to access the Raspberry Pi desktop.

<img class="common_img" src="../_static/media/section_71_05 Image Classification\media\image4.png" style="width:500px"  />

4.  In the Username field, enter **pi** and in the Password field, enter **raspberry**. Check the "**Remember password**" box, then click the "**OK**" button. You will then see the Raspberry Pi desktop opened remotely.

<img class="common_img" src="../_static/media/section_71_05 Image Classification\media\image5.png" style="width:500px"  />

The following image shows successful desktop access:

<img class="common_img" src="../_static/media/section_71_05 Image Classification\media\image6.png" style="width:500px"  />

5.  Click the red box in the image below to transfer files.

<img class="common_img" src="../_static/media/section_71_05 Image Classification\media\image7.png" style="width:500px"  />

6.  Click the button indicated by the red arrow in the image below, then select the file.

<img class="common_img" src="../_static/media/section_71_05 Image Classification\media\image8.png" style="width:500px"  />

7.  Choose the "**image_classification.py**" file and wait for the transfer to complete.

<img class="common_img" src="../_static/media/section_71_05 Image Classification\media\image9.png" style="width:500px"  />

* **Project Outcome**

\(1\) Enter the command to navigate to the desktop.

```py
cd Desktop/
```

\(2\) Enter the command to run the program.

```py
python3 image_classification.py
```

\(3\) After the program starts, the WonderCam vision module will automatically switch to “**Image Classification**” mode. When the module detects an image card, it prints the recognized image ID along with its confidence.

* **Program Brief Analysis**

**1. Import Libraries**

Import the **time** library to control system delays, the **struct** library to parse data transmitted by the vision module, and the **smbus2** library for I2C communication with the WonderCam vision module.

```py
import time
import struct
from smbus2 import SMBus, i2c_msg
```

**2. Program Brief Analysis**

1. In the main program, a communication object for the vision module is created. The `cam.set_func()` function is used to switch the WonderCam to image classification mode.

   ```py
   if __name__ == "__main__":
       cam = WonderCam()
       # """Image Classification (图像分类)"""
       cam.set_func(WONDERCAM_FUNC_CLASSIFICATION)
   ```

2. Once initialization is complete, the program enters a loop. The `cam.update_result()` function is called to update the data received from WonderCam, and the `cam.set_led()` function is used to turn on the vision module's fill light.

   ```py
       while True:
           try:
               cam.update_result()
               cam.set_led(True)
   ```

3. Call `cam.most_likely_id()` to obtain the detected ID and print it. Then, `cam.max_conf()` is used to get the confidence level, which is also printed.

   ```py
               id = cam.most_likely_id()
               print("Detected ID：(识别到ID) %d" %id )
               print("Accuracy：（准确度）%f" %cam.max_conf() )
               time.sleep(0.5)
           except KeyboardInterrupt:
               break
           except Exception as e:
               print(e)
               continue
   ```

### 3.8.6 ESP32(Arduino)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_73_06.1 Image Classification\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_73_06.1 Image Classification\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Locate the program file **Classific_DISP/Classific_DISP.ino** in the same directory as this document.

<img class="common_img" src="../_static/media/section_73_06.1 Image Classification\media\image3.png" style="width:500px"  />

2)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_73_06.1 Image Classification\media\image4.png" style="width:500px"  />

3)  Click the **Select Board** option, search for **ESP32 Dev Module**, and click **OK**.

<img class="common_img" src="../_static/media/section_73_06.1 Image Classification\media\image5.png" style="width:500px"  />

4)  Click <img src="../_static/media/section_73_06.1 Image Classification\media\image6.png" style="width:50px" /> to download the program to the ESP32, and wait for the download to complete.

<img class="common_img" src="../_static/media/section_73_06.1 Image Classification\media\image7.png" style="width:500px"  />

* **Project Outcome**

When the vision module detects a waste card, the waste category is printed to the serial port.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication and sets the vision module to image classification mode.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_CLASSIFICATION);
}
```

**2. Main Function: loop()**

First, the recognition results are updated and retrieved. The detected image ID and confidence are assigned to the variables id and max_prob.

```cpp
void loop() {
  wc.updateResult();

  int id = wc.classIdOfMaxProb();
  float max_prob = wc.classMaxProb();
```

When the confidence is greater than 0.4, if the current image ID is less than 2, the serial port displays “**None**”, indicating that no valid card is detected.

```cpp
  if (max_prob > 0.4) {
    if(class_id < 2)
      Serial.println("None");
```

If the image ID is greater than or equal to 2 and less than 5, the serial port displays “**Harmful waste**”, indicating hazardous waste.

```cpp
    else if(class_id < 5)
      Serial.println("Harmful Waste");
```

If the image ID is greater than or equal to 5 and less than 8, the serial port displays “**Recyclable waste**”, indicating recyclable waste.

```cpp
    else if(class_id < 8)
      Serial.println("Recyclable waste");
```

If the image ID is greater than or equal to 8 and less than 11, the serial port displays “**Wet garbage**”, indicating wet waste.

```cpp
    else if(class_id < 11)
      Serial.println("wet garbage");
```

If the image ID is greater than or equal to 11, the serial port displays “**Dry garbage**”, indicating dry waste.

```cpp
    else
      Serial.println("Dry garbage");
  }
  delay(300);
}
```

### 3.8.7 ESP32(Python)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_74_06 Image Classification\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_74_06 Image Classification\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Open the Hiwonder Python editor software<img src="../_static/media/section_74_06 Image Classification\media\image3.png" style="width:100px" />。

2)  Drag the "**main.py**" and "**WonderCam.py**" files from the **02 Program** folder in the same directory into the Wonder Python Editor.

<img class="common_img" src="../_static/media/section_74_06 Image Classification\media\image4.png" style="width:500px"  />

3)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_74_06 Image Classification\media\image5.png" style="width:500px"  />

4)  Click the Connect button <img src="../_static/media/section_74_06 Image Classification\media\image6.png" style="width:50px" /> in the top menu. When successfully connected, the icon will turn green<img src="../_static/media/section_74_06 Image Classification\media\image7.png" style="width:50px" />.

5)  After connecting, click the Download button <img src="../_static/media/section_74_06 Image Classification\media\image8.png" style="width:50px" /> in the menu to upload the program to the ESP32 controller. Wait for the message box at the bottom of the editor to confirm the download is complete.

<img class="common_img" src="../_static/media/section_74_06 Image Classification\media\image9.png" style="width:500px"  />

6)  Finally, click the Restart button <img src="../_static/media/section_74_06 Image Classification\media\image10.png" style="width:50px" /> to reboot the ESP32 controller. The program will start running automatically.

* **Project Outcome**

When the vision module detects a waste card, the waste category is printed to the serial port.

* **Program Brief Analysis**

1. First, initialize the I2C hardware interface using pins IO16 and IO17, initialize the WonderCam, and switch its function mode to image classification.

   ```py
   i2c = I2C(0, scl=Pin(16), sda=Pin(17), freq=400000)
   colordetect = WonderCam(i2c)
   
   colordetect.set_func(WONDERCAM_FUNC_CLASSIFICATION)
   ```

2. In the loop, the recognition results are first updated and retrieved. The detected waste card’s ID and confidence are assigned to the variables class_id and class_prob.

   ```py
   while True:
     colordetect.update_result()
     class_id = colordetect.most_likely_id()
     class_prob = colordetect.max_conf()
   ```

3. When the confidence is greater than 0.4, the ID is used for classification: IDs less than 2 indicate no image detected, IDs 2, 3, and 4 indicate harmful waste, IDs 5, 6, and 7 indicate recyclable waste, IDs 8, 9, and 10 indicate dry waste, and IDs 11, 12, and 13 indicate wet waste.

   ```py
     if class_prob > 0.4:
       if class_id < 2:
         print("None")
       elif class_id < 5:
         print("Harmful Waste")
       elif class_id < 8:
         print("Recyclable waste")
       elif class_id < 11:
         print("Wet garbage")
       else:
         print("Dry garbage")
     time.sleep_ms(500)
   ```

### 3.8.8 C51

* **Project Process**

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

\(1\) Install the 51 microcontroller onto the open-source controller.

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image2.png" style="width:500px"  />

\(2\) Connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the open-source controller. The ports indicated by the arrows in the image below are all I2C ports.

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image3.png" style="width:500px"  />

\(3\) Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image4.jpeg" style="width:500px"  />

The table below shows the wiring instructions for the USB-to-serial adapter and the open-source controller.

| **USB-to-Serial Adapter** | **Open-Source Controller** |
| :-----------------------: | :------------------------: |
|          5V(VCC)          |             5V             |
|          RX(RXD)          |             TX             |
|          TX(TXD)          |             RX             |
|            GND            |            GND             |

* **Program Download**

1)  Connect the USB-to-serial adapter to the computer.

2)  After confirming that the serial port driver is correctly installed, open **This PC** on the computer desktop. Then, click **Properties** -\> **Device Manager** and check the corresponding serial port number for the controller. **COM port numbers are not unique, and the device port ends with "CH340".**

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image5.png" style="width:700px"  />

3)  Remove the jumper cap from the 51 microcontroller and turn on the power switch to enter programming mode.

> [!NOTE]
>
> **Note:** If the controller is powered on first and then the jumper cap is removed, the controller needs to be turned off and then back on to enter programming mode.

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image6.png" style="width:500px"  />

Open the AIapp-ISP-v6.95E download tool in **[2. Software & Firmware /05 AIapp-ISP-v6.95E]()**:

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image7.png" style="width:300px" />

The AIapp-ISP-v6.95E configuration is shown as follows:

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image8.png" style="width:700px"  />

Click “Open file” and find the hex file in “**02 Program\Objects**”

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image9.png" style="width:500px"  />

After opening the program file, click “**Download/Program**” to download the program to the 51 microcontroller.

> [!NOTE]
>
> **Note:** When downloading the program, you need to remove the jumper cap from the 51 microcontroller first. Once the AIapp-ISP-v6.95E tool in the lower right corner displays the message indicating it is detecting the target microcontroller, you can insert the jumper cap back in place. At this point, the program will start programming.

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image10.png" style="width:700px"  />

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image11.png" style="width:700px"  />

* **Project Outcome**

The image classification feature works together with the 12 waste-sorting cards we provide. In the program, these cards are grouped into four categories. When the WonderCam vision module is pointed at any of the cards, it will recognize the category and display the corresponding text for that waste type on the serial port assistance.

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image12.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main function, the initialization of I2C and serial communication is performed first.

```c
void main(void)
{ 
	int i;
	int class_id;
  float prob;
	P0M1=P0M0=P1M1=P1M0=P2M1=P2M0=P3M1=P3M0=P4M1=P4M0=P5M1=P5M0=P6M1=P6M0=P7M1=P7M0= 0;	
  I2C_Init();
	InitUart1();
	EA = 1;			// Enable global interrupt（开总中断）
```

\(2\) After initialization, the serial port prints "start". And the WonderCam's function is set to color recognition.

```c
	Delay_ms(1000);
	printf("start\r\n");
	changeFunc(APPLICATION_CLASSIFICATION);
	setLed(true);
```

\(3\) Continuously update and retrieve the recognition results from the vision module. Use the functions `classIdOfMaxProb()` and `classMaxProb()` to obtain the image card’s ID and confidence prob.

```c
	for(;;)
	{
  updateResult();
  class_id = classIdOfMaxProb();
```

\(4\) If the confidence prob is greater than 0.4, the image is classified into four categories based on the value of class_id. If class_id is 0 or 1, it indicates that no image was detected, and the message “No image detected” is printed to the serial port.

```c
  if (prob > 0.4) {
    if (class_id < 2) {
			printf("No image detected\r\n （未识别到任何图片\r\n）");
		}
		else if (class_id < 5) {
			printf("Detected hazardous waste\r\n （识别到有害垃圾\r\n）");
		}
		else if (class_id < 8) {
			printf("Detected recyclable waste\r\n （识别到可回收垃圾\r\n）");
		}
		else if (class_id < 11) {
			printf("Detected wet waste\r\n （识别到湿垃圾\r\n）");
		}
		else {
			printf("Detected dry waste\r\n （识别到干垃圾\r\n）");
		}
	}	
```

* **Feature Extensions**

```c
  if (prob > 0.4) {
    if (class_id < 2) {
			printf("No image detected\r\n （未识别到任何图片\r\n）");
		}
		else if (class_id < 5) {
			printf("Detected hazardous waste\r\n （识别到有害垃圾\r\n）");
		}
		else if (class_id < 8) {
			printf("Detected recyclable waste\r\n （识别到可回收垃圾\r\n）");
		}
		else if (class_id < 11) {
			printf("Detected wet waste\r\n （识别到湿垃圾\r\n）");
		}
		else {
			printf("Detected dry waste\r\n （识别到干垃圾\r\n）");
		}
	}	
```

The detected ID is used to classify the card into harmful waste, recyclable waste, wet waste, or dry waste.

<img class="common_img" src="../_static/media/section_75_07 Image Classification\media\image17.png" style="width:500px"  />

The code above is responsible for obtaining the card’s ID and similarity scores. Specifically, it includes: the ID of the card with the highest similarity, the maximum confidence of the detected target card, and the similarity for a specified ID.

## 3.9 Feature Learning

### 3.9.1 Arduino UNO

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_76_01 Feature Learning\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_76_01 Feature Learning\media\image2.png" style="width:500px"  />

* **Program Download**

Locate the program file **Featurelearing_DISP/Featurelearing_DISP.ino** in the same directory as this document.

<img class="common_img" src="../_static/media/section_76_01 Feature Learning\media\image3.png" style="width:500px"  />

2)  Connect the Arduino controller to the computer using a USB Type-B cable for UNO.

<img class="common_img" src="../_static/media/section_76_01 Feature Learning\media\image4.png" style="width:500px"  />

3)  In the Arduino IDE, click the **Select Board** option. The software will automatically detect the connected Arduino serial port, then click to connect.

<img class="common_img" src="../_static/media/section_76_01 Feature Learning\media\image5.png" style="width:500px"  />

4)  Then click the upload button <img src="../_static/media/section_76_01 Feature Learning\media\image6.png" style="width:50px" /> to upload the program to the Arduino. Wait for the upload to complete.

<img class="common_img" src="../_static/media/section_76_01 Feature Learning\media\image7.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_76_01 Feature Learning\media\image8.png" style="width:500px"  />

* **Project Outcome**

When the vision module detects a trained feature image, the OLED screen displays the ID of that image.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication, sets the vision module to feature learning mode, and initializes the OLED module.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  wc.changeFunc(APPLICATION_FEATURELEARNING);
  u8g2.begin();
//  u8g2.enableUTF8Print();    // enable UTF8 support for the Arduino print() function

}
```

**2. Main Function: loop()**

First, update and retrieve the recognition results. The detected feature image ID and confidence are assigned to the variables class_id and prob.

```cpp
void loop() {
  wc.updateResult();
  int class_id = wc.featureIdOfMaxProb();
  float prob = wc.featureMaxProb();
  char str[20];
```

If the confidence of the current feature image is greater than 0.4 and its ID is not 0, the feature image ID is stored in the OLED display buffer str.

```cpp
  if (prob > 0.4 && class_id != 0) {
    sprintf(str, "ID:%d", class_id);
    int offset = 14;
    int len = strlen(str);
```

Then, the string in the buffer is displayed on the OLED screen. For example, if feature image 1 is recognized, the OLED screen will display "ID: 1".

```cpp
  u8g2.setFont(u8g2_font_ncenB14_tr);
    u8g2.setFontDirection(0);
    u8g2.firstPage();
    do {
      u8g2.setCursor(0, 15);
      u8g2.print(str);
    } while ( u8g2.nextPage() );
```

### 3.9.2 micro：bit

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Click icon <img src="../_static/media/section_77_02 Feature Learning\media\image3.png" style="width:50px" /> on the left bottom of the interface, then click **Connect Device**, and follow the on-screen instructions to connect the micro:bit controller.

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image4.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image5.png" style="width:500px"  />

2)  Once connected, click the **Download** button in the bottom-left corner to flash the program to your micro:bit.

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image6.png" style="width:500px"  />

3)  After the download is complete, the indicator light will stop flashing rapidly and remain steadily lit. At the same time, a download completion message will appear on the programming interface.

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image7.png" style="width:500px"  />

* **Project Outcome**

Train feature ID 1 as an image with no objects placed. When an object enters, the image changes, and the following three events will occur simultaneously: the buzzer sounds, the expansion board flashes a red light, and the LED matrix displays an angry face.

* **Program Brief Analysis**

The program first sets the initial state of the device upon startup. Here, the transmission address of WonderCam is configured, and its function is switched to feature learning.

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image8.png" style="width:500px"  />

Obtain the recognition results from the vision module. Before recognition, the color of the color blocks must be learned. For specific learning steps, refer to the **Feature Learning** file in the folder **Function Instruction**. If the obtained confidence ID is 0, it means the item has not been pre-learned, and the variable Change Detected is set to 0.

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image9.png" style="width:500px"  />

When the confidence exceeds 0.8, indicating that a previously learned item is recognized, the Change Detected variable is set to 1.

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image10.png" style="width:500px"  />

When the value of Change Detected is 1, the buzzer on the main controller plays a designated sound, and the RGB light turns red.

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_77_02 Feature Learning\media\image12.png" style="width:500px"  />

The LED matrix on the controller displays a specified pattern, as shown in the figure below:

<img class="common_img" src="../_static/media/section_77_02 Feature Learning/media/image13.png" style="width:500px"  />

### 3.9.3 STM32F103

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

First, install the 32-bit microcontroller onto the open-source controller. Then, connect the WonderCam to the 4-pin connector, with the other end plugged into the I2C port on the controller as indicated by the arrows in the image below.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image2.png" style="width:500px"  />

Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image3.png" style="width:500px"  />

* **Program Download**

1)  Connect the USB downloader to the controller using Dupont wires, and plug the USB end of the downloader into the computer.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image4.png" style="width:500px"  />

2)  On the computer, open the FlyMcu tool. The application file for FlyMcu is located in the directory **[2. Software & Firmware/03 FlyMcu]()**.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image5.png" style="width:500px"  />

3)  Select the port for the controller connection and set the baud rate to 115200. Do not choose COM1, as it is the system communication port.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image6.png" style="width:500px"  />

4)  Click the button <img src="../_static/media/section_78_03 Feature Learning/media/image7.png" style="width:50px" /> to choose the program file to be flashed. Once selected, check the **Auto Reload Before Program**.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image8.png" style="width:500px"  />

The program file is located in the directory **02 Program\Obj**.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image9.png" style="width:500px"  />

5. Take down the jumper cap to the controller, press the **RST** reset button on the STM32 core board, turn on the power switch on the controller, and click **Start ISP(P)**.

   <img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image10.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image11.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image12.png" style="width:500px"  />

6)  After the program is successfully flashed, reconnect the jumper cap to the controller, press the **RST** button on the controller, and restart the controller.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image13.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image14.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) Initialize the system clock, I2C interface, and serial port. The two previously created variables are as follows: landmark_id stores the ID of the recognized road sign, and prob stores the confidence level.

```c
	uint8_t x;
	SystemInit(); 		// System clock initialized to 72M （系统时钟初始化为72M）  SYSCLK_FREQ_72MHz
	/*Set NVIC interrupt group 2: 2 bits preemption priority, 2 bits response priority 
		(设置NVIC中断分组2:2位抢占优先级，2位响应优先级)*/
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);	
	InitDelay(72); 		// Initialize delay function (初始化延时函数)
	IIC_init();		// I2C initialization (IIC初始化)
	DelayMs(100);
	Usart1_Init(); // UART initialization (串口初始化)
	DelayMs(1000);
```

\(2\) After initialization, the serial port will print the "**start**" string. The WonderCam will then switch to feature learning mode.

```c
	printf(" ");
	printf("start\r\n");
	changeFunc(APPLICATION_FEATURELEARNING);
```

\(3\) Inside the while loop, first update and retrieve the recognition results, then create two variables: class_id to store the feature ID and prob to store the confidence. Next, if the confidence is greater than 0.4 and the feature ID is not 0, print the current feature ID.

```c
	while(1)
	{
  updateResult();
  int class_id = featureIdOfMaxProb();
  float prob = featureMaxProb();
  char str[20];
  if (prob > 0.4 && class_id != 0) {
    sprintf(str, "ID:%d", class_id);
    printf("%s\r\n",str);
    } 
  DelayMs(100);
  }
}
```

* **Feature Extensions**

```c
  if (prob > 0.4 && class_id != 0) {
    sprintf(str, "ID:%d", class_id);
    printf("%s\r\n",str);
    } 
```

The prob represents the confidence, ranging from 0 to 1. The higher the confidence, the more accurate the recognition. By adjusting the confidence threshold, the recognition results can be made more precise.

<img class="common_img" src="../_static/media/section_78_03 Feature Learning/media/image20.png" style="width:500px"  />

The functions related to feature learning are listed above: The first function retrieves the ID with the highest similarity from the feature learning, the second function obtains the maximum similarity value during recognition, and the last one retrieves the maximum similarity value for a specified ID.

### 3.9.4 STM32F407

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam with the 4-pin connector, and plug the other end into the I2C interface on the STM32 controller.

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image2.png" style="width:500px"  />

* **Program Download**

1)  Connect the Type-C cable to the Type-C port on the STM32 controller. Make sure to use the UART1 port as shown in the figure below and plug the other end into the USB port of your computer.

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image3.png" style="width:500px"  />

2)  Open the **Device Manager** on your computer and check the COM port number under the **Ports** section.

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image4.png" style="width:500px"  />

3)  Launch the ATK-XISP software in the directory **2. Software & Firmware/04 ATK-XISP**, select the corresponding COM port, and set the baud rate to 115200.

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image5.png" style="width:500px"  />

4)  Configure the software as shown in the image below.

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image6.png" style="width:500px"  />

5)  click the "File" button and select the **"02 Program\MDK-ARM\feature_learing\feature_learing.hex**" file for programming.

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image7.png" style="width:500px"  />

6)  Click the “Start Programming” button on the interface to flash the generated .hex file to the STM32 controller. Wait for the programming to complete successfully.

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image8.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image9.png" style="width:500px"  />

* **Project Outcome**

Connect the USB-to-serial adapter to the computer and open any serial assistant. Select the appropriate COM port, set the baud rate to 9600, and open the serial port to begin receiving data from the STM32 microcontroller.

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image10.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main program, first initialize the WonderCam object. Call the `ChangeFunc()` function to switch WonderCam to feature learning mode, and then turn off the vision module's fill light.

```c
	camset.Init(&camset);
	camset.ChangeFunc(&camset, APPLICATION_FEATURELEARNING);
	camset.SetLed(&camset,LED_OFF);
```

\(2\) Inside the while loop, call the `UpdateResult()` function to update the recognition results of the WonderCam vision module, and use the `featureIdOfMaxProb()` and `featureMaxProb()` functions to get the currently recognized feature image ID and its maximum confidence.

```c
  while (1)
  {
    /* USER CODE END WHILE */
	
    /* USER CODE BEGIN 3 */
		
		
		camset.UpdateResult(&camset);
		int class_id = featurenlearningfunc.featureIdOfMaxProb();
		float prob = featurenlearningfunc.featureMaxProb();
```

\(3\) When the confidence is greater than 0.4, print the recognized feature image ID to the serial port, with the output formatted as “ID:” followed by the detected feature image ID.

```c
		if (prob > 0.4 && class_id != 0)
			printf("ID:%d\r\n",class_id);
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image14.png" style="width:500px"  />

The prob represents the confidence, ranging from 0 to 1. The higher the confidence, the more accurate the recognition. By adjusting the confidence threshold, the recognition results can be made more precise.

<img class="common_img" src="../_static/media/section_79_04 Feature Learning/media/image15.png" style="width:500px"  />

The functions related to feature learning are listed above: The first function retrieves the ID with the highest similarity from the feature learning, the second function obtains the maximum similarity value during recognition, and the last one retrieves the maximum similarity value for a specified ID.

### 3.9.5 Raspberry Pi

* **Project Process**

<img class="common_img" src="../_static/media/section_80_05 Feature Learning\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Connect the WonderCam to any I2C port on the Raspberry Pi expansion board.

<img class="common_img" src="../_static/media/section_80_05 Feature Learning\media\image2.png" style="width:500px"  />

* **Program Download**

1.  After turning on the Raspberry Pi, connect the computer to the corresponding Wi-Fi.

2.  Open the remote desktop software<img src="../_static/media/section_80_05 Feature Learning\media\image3.png" style="width:50px" />.

3.  In the search bar, enter "**192.168.149.1**" and press **Enter** to access the Raspberry Pi desktop.

<img class="common_img" src="../_static/media/section_80_05 Feature Learning\media\image4.png" style="width:500px"  />

4.  In the Username field, enter **pi** and in the Password field, enter **raspberry**. Check the "**Remember password**" box, then click the "**OK**" button. You will then see the Raspberry Pi desktop opened remotely.

<img class="common_img" src="../_static/media/section_80_05 Feature Learning\media\image5.png" style="width:500px"  />

The following image shows successful desktop access:

<img class="common_img" src="../_static/media/section_80_05 Feature Learning\media\image6.png" style="width:500px"  />

5.  Click the red box in the image below to transfer files.

<img class="common_img" src="../_static/media/section_80_05 Feature Learning\media\image7.png" style="width:500px"  />

6.  Click the button indicated by the red arrow in the image below, then select the file.

<img class="common_img" src="../_static/media/section_80_05 Feature Learning\media\image8.png" style="width:500px"  />

7.  Choose the "**feature_learning.py**" file and wait for the transfer to complete.

<img class="common_img" src="../_static/media/section_80_05 Feature Learning\media\image9.png" style="width:500px"  />

* **Project Outcome**

\(1\) Enter the command to navigate to the desktop.

```py
cd Desktop/
```

\(2\) Enter the command to run the program.

```py
python3 feature_learning.py
```

\(3\) After the program starts, the WonderCam vision module will automatically switch to “**Feature Learning**” mode. When the module detects a previously learned item, it prints the recognized item ID along with its confidence.

* **Program Brief Analysis**

**1. Import Libraries**

Import the **time** library to control system delays, the **struct** library to parse data transmitted by the vision module, and the **smbus2** library for I2C communication with the WonderCam vision module.

```py
import time
import struct
from smbus2 import SMBus, i2c_msg
```

**2. Program Brief Analysis**

1. In the main program, a communication object for the vision module is created. The `cam.set_func()` function is used to switch the WonderCam to feature learning mode.

   ```py
   if __name__ == "__main__":
       cam = WonderCam()
   
       # """Feature Learning (特征学习)"""
       cam.set_func(WONDERCAM_FUNC_FEATURE_LEARNING)
   ```

2. Once initialization is complete, the program enters a loop. The `cam.update_result()` function is called to update the data received from WonderCam, and the `cam.set_led()` function is used to turn on the vision module's fill light.

   ```py
       while True:
           try:
               cam.update_result()
               cam.set_led(True)
   ```

3. Call `cam.most_likely_id()` to obtain the detected object ID and print the recognized ID. Then, `cam.max_conf()` is used to get the confidence level, which is also printed.

   ```py
               id = cam.most_likely_id()
               print("Detected ID：(识别到ID) %d" %id )
               print("Accuracy：（准确度）%f" %cam.max_conf() )
               time.sleep(0.5)
           except KeyboardInterrupt:
               break
           except Exception as e:
               print(e)
               continue
   ```

### 3.9.6 ESP32(Arduino)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_82_06.1 Feature Learning\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_82_06.1 Feature Learning\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Locate the program file **Featurelearing_DISP/Featurelearing_DISP.ino** in the same directory as this document.

<img class="common_img" src="../_static/media/section_82_06.1 Feature Learning\media\image3.png" style="width:500px"  />

2)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_82_06.1 Feature Learning\media\image4.png" style="width:500px"  />

3)  Click the **Select Board** option, search for **ESP32 Dev Module**, and click **OK**.

<img class="common_img" src="../_static/media/section_82_06.1 Feature Learning\media\image5.png" style="width:500px"  />

4)  Click <img src="../_static/media/section_82_06.1 Feature Learning\media\image6.png" style="width:50px" /> to download the program to the ESP32, and wait for the download to complete.

<img class="common_img" src="../_static/media/section_82_06.1 Feature Learning\media\image7.png" style="width:500px"  />

* **Project Outcome**

When the vision module detects a previously learned feature scene, the serial monitor prints the corresponding scene ID.

* **Program Brief Analysis**

**1. Initialization Function: setup()**

The initialization function primarily starts the serial communication and sets the vision module to feature learning mode.

```cpp
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  wc.begin();
  Serial.println("START");
  wc.changeFunc(APPLICATION_FEATURELEARNING);
}
```

**2. Main Function: loop()**

First, update and retrieve the recognition results. The detected feature image ID and confidence are assigned to the variables class_id and prob. If the confidence score of the current feature scene is greater than 0.4 and its ID is not 0, the ID of the detected feature scene will be printed through the serial monitor.

```cpp
void loop() {
  wc.updateResult();

  int id = wc.featureIdOfMaxProb();
  float max_prob = wc.featureMaxProb();
  if (max_prob > 0.4 && id > 0) {
    Serial.print("feature id :");
    Serial.println(id);
  }
  delay(300);
}
```

### 3.9.7 ESP32(Python)

* **Project Process**

**Project Flowchart**

<img class="common_img" src="../_static/media/section_83_06 Feature Learning\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

Install the ESP32 microcontroller onto the expansion board, then connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the expansion board as shown in the image below.

<img class="common_img" src="../_static/media/section_83_06 Feature Learning\media\image2.png" style="width:500px"  />

* **Program Download**

1)  Open the Hiwonder Python editor software<img src="../_static/media/section_83_06 Feature Learning\media\image3.png" style="width:100px" />。

2)  Drag the "**main.py**" and "**WonderCam.py**" files from the **02 Program** folder in the same directory into the Wonder Python Editor.

<img class="common_img" src="../_static/media/section_83_06 Feature Learning\media\image4.png" style="width:500px"  />

3)  Connect the ESP32 controller to your computer using a USB data cable.

<img class="common_img" src="../_static/media/section_83_06 Feature Learning\media\image5.png" style="width:500px"  />

4)  Click the Connect button <img src="../_static/media/section_83_06 Feature Learning\media\image6.png" style="width:50px" /> in the top menu. When successfully connected, the icon will turn green<img src="../_static/media/section_83_06 Feature Learning\media\image7.png" style="width:50px" />.

5)  After connecting, click the **Download** button <img src="../_static/media/section_83_06 Feature Learning\media\image8.png" style="width:50px" /> in the menu to upload the program to the ESP32 controller. Wait for the message box at the bottom of the editor to confirm the download is complete.

<img class="common_img" src="../_static/media/section_83_06 Feature Learning\media\image9.png" style="width:500px"  />

6)  Finally, click the Restart button <img src="../_static/media/section_83_06 Feature Learning\media\image10.png" style="width:50px" /> to reboot the ESP32 controller. The program will start running automatically.

* **Project Outcome**

When the vision module detects a previously learned feature scene, the serial monitor prints the corresponding scene ID.

* **Program Brief Analysis**

1. First, initialize the I2C hardware interface using pins IO16 and IO17, initialize the WonderCam, and switch its function mode to color recognition.

   ```py
   i2c = I2C(0, scl=Pin(16), sda=Pin(17), freq=400000)
   colordetect = WonderCam(i2c)
   
   colordetect.set_func(WONDERCAM_FUNC_FEATURE_LEARNING)
   ```

2. In the loop, the recognition result is first updated and retrieved, and the detected feature ID and confidence are assigned to the variables feature_id and feature_prob. When the confidence is greater than 0.4 and the ID is not 0, the ID of the detected feature scene will be printed through the serial monitor.

   ```py
   while True:
     colordetect.update_result()
     feature_id = colordetect.most_likely_id()
     feature_prob = colordetect.max_conf()
     if feature_prob > 0.4 and feature_id > 0:
       print("feature learning ID:",feature_id)
     time.sleep_ms(200)
   ```

### 3.9.8 C51

* **Project Process**

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image1.png" style="width:500px"  />

* **Wiring Instruction**

\(1\) Install the 51 microcontroller onto the open-source controller.

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image2.png" style="width:500px"  />

\(2\) Connect the WonderCam vision module to the 4-pin connector, with the other end plugged into the I2C port on the open-source controller. The ports indicated by the arrows in the image below are all I2C ports.

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image3.png" style="width:500px"  />

\(3\) Next, connect a USB-to-serial adapter to the open-source controller. Please prepare the adapter yourself. Note that the silk-screen markings on different USB-to-serial adapters may vary. Ensure the power supply is correctly connected, and then connect the adapter’s transmit (TXD) pin to the controller’s RX pin, and the adapter’s receive (RXD) pin to the controller’s TX pin.

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image4.jpeg" style="width:500px"  />

The table below shows the wiring instructions for the USB-to-serial adapter and the open-source controller.

| **USB-to-Serial Adapter** | **Open-Source Controller** |
| :-----------------------: | :------------------------: |
|          5V(VCC)          |             5V             |
|          RX(RXD)          |             TX             |
|          TX(TXD)          |             RX             |
|            GND            |            GND             |

* **Program Download**

1)  Connect the USB-to-serial adapter to the computer.

2)  After confirming that the serial port driver is correctly installed, open **This PC** on the computer desktop. Then, click **Properties** -\> **Device Manager** and check the corresponding serial port number for the controller. **COM port numbers are not unique, and the device port ends with "CH340".**

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image5.png" style="width:500px"  />

3)  Remove the jumper cap from the 51 microcontroller and turn on the power switch to enter programming mode.

> [!NOTE]
>
> **Note:** If the controller is powered on first and then the jumper cap is removed, the controller needs to be turned off and then back on to enter programming mode.

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image6.png" style="width:500px"  />

Open the AIapp-ISP-v6.95E download tool in **2. Software & Firmware /05 AIapp-ISP-v6.95E**:

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image7.png" style="width:300px" />

The AIapp-ISP-v6.95E configuration is shown as follow:

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image8.png" style="width:700px"  />

Click “Open file” and find the hex file in “**02 Program\Objects**”

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image9.png" style="width:500px"  />

After opening the program file, click “Download/Program” to download the program to the 51 microcontroller.

> [!NOTE]
>
> **Note:** When downloading the program, you need to remove the jumper cap from the 51 microcontroller first. Once the AIapp-ISP-v6.95E tool in the lower right corner displays the message indicating it is detecting the target microcontroller, you can insert the jumper cap back in place. At this point, the program will start programming.

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image10.png" style="width:700px"  />

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image11.png" style="width:700px"  />

* **Project Outcome**

When the vision module recognizes a previously learned feature scene, the serial monitor will display the ID of that scene.

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image12.png" style="width:500px"  />

* **Program Brief Analysis**

\(1\) In the main function, the initialization of I2C and serial communication is performed first.

```c
void main(void)
{ 
	int feature_id;
  float prob;
	P0M1=P0M0=P1M1=P1M0=P2M1=P2M0=P3M1=P3M0=P4M1=P4M0=P5M1=P5M0=P6M1=P6M0=P7M1=P7M0= 0;	
  I2C_Init();
	InitUart1();
	EA = 1;			// Enable global interrupt（开总中断）
```

\(2\) After initialization, the serial port prints "start". And the WonderCam's function is set to feature learning.

```c
	Delay_ms(1000);
	printf("start\r\n");
	changeFunc(APPLICATION_FEATURELEARNING);
```

\(3\) Continuously update and retrieve the recognition results from the vision module, and use the `featureIdOfMaxProb()` and `featureMaxProb()` functions to obtain the ID and confidence value prob of the currently recognized feature scene.

```c
	for(;;)
	{
  updateResult();
  feature_id = featureIdOfMaxProb();
	prob = featureMaxProb();
```

\(4\) When the confidence value is greater than 0.4, print the current feature scene ID to the serial monitor.

```c
  if (prob > 0.4) 
	{
    printf("feature ID：%d", feature_id);
	}
  Delay_ms(200);
  }
}
```

* **Feature Extensions**

<img class="common_img" src="../_static/media/section_84_07 Feature Learning\media\image17.png" style="width:500px"  />

The functions related to feature learning are listed above: The first function retrieves the ID with the highest similarity from the feature learning, the second function obtains the maximum similarity value during recognition, and the last one retrieves the maximum similarity value for a specified ID.

