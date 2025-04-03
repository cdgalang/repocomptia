# Essential Hardware Components
---

### Motherboard

* main circuit in a computer, where it serves as the central hub for connecting devices. It contains slots and ports for CPU, memory, storage devices and expansion cards. 

### Central Processing Unit (CPU)

* Brain of the computer
* Executes instructions, performs calculations, and manages data flow
* CPU performance include clock speed, number of cores and cache sieze

### Random Access Memory (RAM)

* provides temporary storage for data and instructions that the CPU need to access quickly
* RAM is volatile, it will lose its content when power is removed

### Storage devices

* Holds data permanently, even if the computer is turned off
* Hard Disk Drives (HDDs)
    * uses magnetic platters to store data
* Solid State Drives (SSDs)
    * uses flash memory to store data
* M.2 SSDs
    * connects directly to motherboard



### Power Supply Unit (PSU)

* converts AC power from wall outlet to DC power that components can use

### Graphics Processing Unit (GPU)

* handles he rendering of images and videos

### Cooling Systems

* prevents overheating
* heat sinks, fans, liquid cooling
* Proper cooling is important for system stability and longetivity

### Input Devices

* Allow users to interact with computers
* Examples includes keyboards, mice, webcams
* Typically uses USBs or Bluetooth

### Output Devices

* present information to the user
* Examples includes monitors, speakers, headphones, printers

### Network Interface Cards (NICs)

* enable communication with other devices on a network
* **Switches**: central points within LAN linking multiple devices together
* **Routers**: connecting different networks and directing traffic between them and functions at layer 3
* **Wireless Access Points (WAPs)**: bridge the gap between wired and wireless networking.
* **Modems**: gateway between digital networks and analog transmission systems like telephone and cable lines


### Expansion Cards

* add functionality to a computer system
* Includes sound cards, capture cards, and specialized cards

### Optical Drives

* reads and writes to CDs, DVDs, and Blueray Discs

### BIOS/UEFI chip

* contains firmware for booting process
* runtime services for OS and programs

* BIOS or Basic Input/Output Systems
    * Key Functions of BIOS include:
        * checks hardware components
        * setup utility
        * managing power states
        * handling basic input/output operations

* UEFI
    * Successor to BIOS

* Provides **setup utility** which allows user to configure various system

* Firmware
    * Type of software that provides low-level control for devices

* Secure Boot
    * UEFI feature which helps prevents unauthorized OS and malware from loading during booting process.

### CMOS battery
 
* maintains system settings and the real time clock when computer is powered off

# RAIDs

* Redundant Array of Independent Disks
* Combines multiple storage devices into a single logical unit

    * RAID 0 (Striping)
        * not fault tolerant
        * data is not duplicated
        * striped across two separate disks
        * improves performance

    * RAID 1 (Mirroring)
        * fault tolerant
        * copies on 2 disks
        * provides redundancy
        * failure on one drive does not result in data loss

    * RAID 5
        * 3 or more disks
        * common setup
        * store a large amount of data
        * not duplicated but striped to multiple drives
        * good balance of performance, capacity and redundancy

    * RAID 10
        * combines RAID 1 and 0
        * requires 4 or more disks
        * improved perforamnce and redundancy 
        * benefits the fault tolerance of RAID 1 and the speed of RAID 0

    * RAID Controllers

    ---