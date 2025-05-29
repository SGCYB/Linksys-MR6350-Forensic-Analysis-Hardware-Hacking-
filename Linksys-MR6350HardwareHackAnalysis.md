# Linksys-MR6350-Forensic-Analysis-Hardware-Hacking-


**Forensic Analysis Report: Serial Console Access on Linksys MR6350 Router**

**Objective**

The objective of this analysis is to identify and interface with the serial UART port on a Linksys MR6350 router in order to access its terminal output during boot. This process involves identifying the GPIO pinout, configuring a USB-to-TTL interface, and capturing serial communication using software tools such as PuTTY and Saleae Logic 2.

**Tools & Equipment**

*Hardware:*

Linksys MR6350 Router

DSD TECH USB-TTL Adapter (Model SH-U09C2)

AstroAI AM33D Digital Multimeter

Logic Analyzer

Chanzon Female-to-Female Dupont Jump Wires (x4)

*Software:*

PuTTY Terminal Emulator

Saleae Logic 2 (Free Version)

**Background & Preparation**

The Linksys MR6350 is a dual-band mesh Wi-Fi router. The unit used in this analysis was sourced from an old stockpile and factory reset prior to any physical or digital interaction to ensure no user data remained. Disassembly was performed with disregard for cosmetic preservation, but care was taken not to damage the PCB.

Unlike some routers, the MR6350 does not label its serial port pins. However, a pin header is pre-soldered, simplifying hardware interfacing.

![IMG_3481](https://github.com/user-attachments/assets/3ca7fbac-a15a-4aea-8ce1-91c84b0fcf55)



![IMG_3515](https://github.com/user-attachments/assets/2fb63562-7ba6-4ba3-9c14-565b8075615e)



**Pin Identification**


Using the AstroAI AM33D multimeter, six pins were probed for voltage. Results indicated:

Three pins at ~3.3V (likely VCC, TX, RX)

Three pins at 0.0V (likely GND)


**Logic Analysis**

![unnamed](https://github.com/user-attachments/assets/df8581b3-49ed-47f4-9824-04bade405ba0)

To confirm pin functions, the Saleae Logic Pro 2 App and a Logic Analyzer was used to test all candidate pins. The analysis revealed:



Two pins with significant bidirectional data activity - (RX and TX Pins) 

1 Silent Pin - (VCC Pin) 


<img width="626" alt="TrueLogicAnalyzerPictureGitHub " src="https://github.com/user-attachments/assets/7ddf7965-76b3-4e33-bf85-bd537f89c122" />



Three pins were exerting a 3.3v output. But only two of the pins were showing data activity. This means the "D2" slot that isn't showing any data activity is our VCC pin and the two other are our TX and RX. From the picture we can see the "D0" slot shows instant activity once we turned on the router, so we can assume this is our TX pin. Since we only have 1 pin left the "D1" slot is our RX pin 

![updated2](https://github.com/user-attachments/assets/cc33d1b6-7d0a-485e-808e-400261b48ec1)


*Remember! The RX pin on the router wires to the TX pin on your USB-TTL adapter and vice versa*


**Serial Communication Setup**


Using the DSD TECH USB-TTL adapter, the RX, TX, GND and VCC lines were correctly wired. PuTTY was configured as follows:

![IMG_3483](https://github.com/user-attachments/assets/aac7dff5-2af6-4946-be1f-8887194b4294)

Connection Type: Serial

Baud Rate: 115200

Serial Line: COM3 (varies by system, on a Windows OS you can go to Device Manager and look under Ports (COM & LPT) to find your device number)

<img width="332" alt="USBCOMPORT3" src="https://github.com/user-attachments/assets/8497c841-d78b-4578-96c5-1570e247e40f" />




<img width="332" alt="PuttyCOnfigScreen" src="https://github.com/user-attachments/assets/5ee933f4-0369-4e81-9d53-217a3d928dab" />


Upon powering the router with PuTTY open, boot logs began streaming, confirming successful connection.

**Terminal Access & Interaction**


During boot, a message appeared: "Hit any key to interrupt boot process". Interrupting this countdown allowed access to the device's shell environment.

A help command revealed basic system utilities and diagnostic options, similar to a lightweight shell or recovery interface. 

<img width="495" alt="helpcommandputtyrouterpic" src="https://github.com/user-attachments/assets/676f4a8a-0275-4cc0-877f-e6f55907e5b8" />

I was able to use a few commands to check out the firmware and do a memory test on the router. I was able to see that the router uses U-Boot and BusyBox, U-Boot is a universal boot loader and Busybox is found on many IOT devices and provides many UNIX utiltiies in a small executable for embedded systems. (https://www.busybox.net/ & https://www.u-boot.org/)

<img width="544" alt="versioncommandputty" src="https://github.com/user-attachments/assets/2eaefc24-18df-4eed-9619-e4761097be37" />

<img width="544" alt="memtestputtyrouter" src="https://github.com/user-attachments/assets/82f6d936-6a3f-4927-8346-60cfcd339e74" />

<img width="544" alt="busyboxsoftware" src="https://github.com/user-attachments/assets/b9965550-3864-4495-a93f-c30dc7f5d9e4" />


A subsequent boot without interaction provided a full log of system startup, including kernel loading, firmware checks, and network configuration. Since the router was reset, no identifiable user data such as SSIDs or passwords was present.
 
<img width="494" alt="linksys putty router pic " src="https://github.com/user-attachments/assets/9b0ba0fa-723a-44c3-98a8-2e35ff550d1d" />



**During Boot Process** 

During the boot process I saw a message that stated the factory SSID of the router and I was also able to see the setup AP on my iPhone. Nothing out of the ordinary but I thought it was cool to point out. 

![IMG_3517](https://github.com/user-attachments/assets/66e1248c-0b12-4189-a571-729dca56c30a)

<img width="901" alt="linksetup" src="https://github.com/user-attachments/assets/b0db42bf-43b3-46f0-9e2f-a5ff005e9b16" />


 

**Reset Button Behavior**


As an experiment, the physical reset button was pressed during boot. This action was captured in real-time on PuTTY, showing a reset countdown and a message regarding a reset event detected. It was interesting to see what the happens behind the scenes when you reset your router and wait for it to boot up. 

<img width="344" alt="resetcountdown" src="https://github.com/user-attachments/assets/56f47e92-5942-484b-a27e-1ec8cd0f0821" />


**Security Implications**


This analysis demonstrates the ease of accessing a router's internal systems with physical access and basic tools. It is definently an uncommomn point of entry but it is still something to think about. In enterprise environments, it is very common to see the use of secure physical storage—such as locked network cabinets—to protect infrastructure hardware from untrusted hands. 

**Conclusion**


This investigation successfully identified the serial interface of the Linksys MR6350 router, established terminal access via UART, and captured boot-time activity using a combination of hardware and software tools. This project serves as a foundational step into hardware hacking and embedded system analysis.

Thank you for taking the time to read over my analysis! This is one of many projects that will be posted. I hope to do more similiar projects in the future. 
