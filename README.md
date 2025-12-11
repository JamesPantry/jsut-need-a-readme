# Heating Controller – README

## Project Overview
This project implements a smart heating controller on the **PIC18F45K22** using the EasyPIC7A development board.  
It provides:

- Temperature measurement using an LM35 sensor  
- Time-based heating control using an external RTC  
- LCD menu interface with button navigation  
- Alarm system with selectable tones and buzzer playback  
- Persistent configuration stored in EEPROM  
- Fully non-blocking, modular software architecture  

---

## 1. Building the Project

1. Open **MPLAB X IDE**  
2. Go to **File → Open Project…**  
3. Select the folder containing the project `.X` directory  
4. Click **Build Project** (hammer icon)

---

## 2. Running the Project on the Board

1. Connect the EasyPIC7A to your PC using USB  
2. Open **CodeGrip** and verify that the board is detected  
3. Return to **MPLAB X IDE**, select the board as the programmer/debug tool  
4. Click **Run** (green arrow) to flash and start the program  

---

## 3. Hardware Connections

| Component | PIC Pin(s) | Description |
|----------|------------|-------------|
| **LM35 Temperature Sensor** | AN6 / RE1 | Analogue input → ADC |
| **RTC (I²C)** | RC3 (SCL), RC4 (SDA) | Timekeeping interface |
| **Buttons** | RC6 (UP), RC7 (DOWN), RC0 (SELECT), RC1 (BACK) | Debounced digital inputs |
| **7-Segment Display** | PORTD | Multiplexed display |
| **LCD 16×2** | PORTB | Menus + system state |
| **Heating LED** | RE0 | Indicates heating ON/OFF |
| **Buzzer** | RC2 | PWM tone / melody output |

---

## 4. System Startup Behaviour

- Configuration values are loaded from EEPROM  
  - If invalid or first run → defaults are written  
- UI initialises and the **home screen** appears  
- Seven-segment display begins showing **temperature ×100**  
- LM35 sampling, RTC reading, heating logic, and buzzer engine all start automatically  

---

## 5. Button Controls

| Button | PIC Pin | Function |
|--------|---------|----------|
| **SELECT** | RC0 | Confirm / next menu item |
| **BACK** | RC1 | Cancel / go back / silence alarm |
| **UP** | RC6 | Increase value / scroll up |
| **DOWN** | RC7 | Decrease value / scroll down |

---

## 6. Using the System

### **Home Screen (LCD)**
- **Line 1:** Temperature (e.g., `Temp: 22.34C`)  
- **Line 2:** Heating status (ON/OFF) and current time  
- **Shortcut:** Press **DOWN** on the home screen to quickly toggle the alarm ON/OFF  

### **Menu Options**
- **Temp Limits**  
  Set heating low and high thresholds  
- **Heating Window**  
  Configure allowed heating start/end time  
- **Alarm Settings**  
  Set alarm hour/minute, enable, tone  
- **Exit**  
  Return to home screen  

### **Configurable Settings**
- Temperature low/high limits  
- Heating window start/end  
- Alarm hour and minute  
- Alarm enable/disable  
- Alarm tone  

All settings are saved automatically to EEPROM.

---

## 7. Heating Behaviour

Heating turns **ON** when:
- The current time is within the configured heating window, and  
- The measured temperature is **≤ low limit**

Heating turns **OFF** when:
- The measured temperature is **≥ high limit**, or  
- The system is **outside** the heating window

Heating state is displayed on:
- The **LCD**  
- The **RE0 LED**

---

## 8. Alarm and Buzzer

- When RTC time matches the **alarm hour** and **minute**, the system plays the selected tone or melody  
- Buzzer playback is fully **non-blocking**, allowing the UI and heating control to continue working  
- Press **BACK** at any time to silence the alarm  
- Alarm settings (enabled state + tone) are stored in EEPROM  

---

## 9. File Structure

```text
Project/
│
├── README.md
├── main.c
├── lm35.c / lm35.h
├── rtc16.c / rtc16.h
├── buttons.c / buttons.h
├── lcd.c / lcd.h
├── sevenseg.c / sevenseg.h
├── heating.c / heating.h
├── alarm.c / alarm.h
├── buzzer.c / buzzer.h
├── eeprom_cfg.c / eeprom_cfg.h
└── ui.c / ui.h
