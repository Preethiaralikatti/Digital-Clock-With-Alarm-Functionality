## INTRODUCTION


A digital clock with alarm functionality is a ubiquitous device found in homes, offices, and personal devices like smartphones,It combines the basic function of timekeeping with the added utility of setting alarms to alert users at specified times. 
This dual functionality is essential in modern life for managing schedules, waking up on time, and organizing daily activities.
The most distinguishing feature of a digital clock is its display, which shows the time in numerical digits rather than using hands on a dial. This makes reading the time quick and straightforward.
Digital clocks allow users to set alarms for specific times. These alarms can often be customized with various sound options and volumes, and many modern digital clocks support multiple alarms for different times of the day.


## OVERVIEW
To Create a digital clock using a RISC-V board that displays the current time and allows the user to set alarms. 
The clock should read the time from a real-time clock (RTC) module and display it on an LCD. 
It should also have an interrupt-driven system to trigger the alarm at the set time.

## COMPONENTS REQUIRED
VSD Squadron Mini Board

O LED Display (to show the time)


Buzzer (for the alarm sound)


Push Buttons (to set the time and alarm)


Wires and Breadboard (for connections)

## BILL OF MATERIALS(BOM)
O LED Display: Rs.270

Buttons: Rs.5 to 20

Buzzer: Rs.25

Wires and Breadboard: Rs.200

## CIRCUIT CONNECTION DIAGRAM

![Screenshot 2024-06-07 212823](https://github.com/Vigneshr2106/Digital-Clock-With-Alarm-Functionality/assets/165415082/e36d8ed4-38fa-40a9-8660-173346e137e5)





## PIN CONNECTIONS

![Screenshot 2024-06-07 214935](https://github.com/Vigneshr2106/Digital-Clock-With-Alarm-Functionality/assets/165415082/223ec815-8eef-4d82-9a2d-4840fc03cd8b)




## CODE

#include "debug.h"

#include "ssd1306.h"

#include "ch32v00x.h"

void GPIO_Config(void)

{
   
   GPIO_InitTypeDef GPIO_InitStructure = {0};
    
   RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE);
    
   RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);

   // Light pin configuration
   
   GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2;
   
   GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    
   GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    
   GPIO_Init(GPIOD, &GPIO_InitStructure);
   
}

// Define a structure to hold the time

typedef struct 

{

   int hours;
   
   int minutes;
   
   int seconds;
   
} 

Time;

Time currentTime = {0, 0, 0};

Time alarmTime = {0, 0, 10}; 

// Example alarm time at 06:30:00

void Start()

{
    OLED_GotoXY(1, 1);
    
   OLED_Puts("DIGITAL", &Font_11x18, 1);
    
   OLED_GotoXY(1, 30);
    
   OLED_Puts("CLOCK", &Font_11x18, 1);
    
   OLED_UpdateScreen();
    
   Delay_Ms(1000);
   
}


void Buzzer(void)

{
   uint32_t elapsedTime = 0;
    
   while (elapsedTime < 5000)
   
   {
        GPIO_WriteBit(GPIOD, GPIO_Pin_2, Bit_SET);
        
   Delay_Ms(500);
        
   GPIO_WriteBit(GPIOD, GPIO_Pin_2, Bit_RESET);
        
   Delay_Ms(500);
        
   elapsedTime += 1000;
        
   }
}


void UpdateClockDisplay(Time time)

{
    char buffer[9];  // Buffer to hold the time string in HH:MM:SS format
    
   snprintf(buffer, sizeof(buffer), "%02d:%02d:%02d", time.hours, time.minutes, time.seconds);
    
   OLED_GotoXY(1, 1);
    
   OLED_Puts(buffer, &Font_11x18, 1);
    
   OLED_UpdateScreen();
    
   Delay_Ms(1000);
   
}

void CheckAlarm(Time time, Time alarm)

{

   if (time.hours == alarm.hours && time.minutes == alarm.minutes && time.seconds == alarm.seconds) 
   
   {
   
   OLED_GotoXY(1, 30);
   
   OLED_Puts("ALARM!", &Font_11x18, 1);
   
   OLED_UpdateScreen();
   
   Buzzer();
    
   }
    
}

void IncrementTime(Time *time)

{
    time->seconds++;
    
   if (time->seconds >= 60) 
   
   {
    
   time->seconds = 0;
        
   time->minutes++;
    
   }
    
  if (time->minutes >= 60) 
  
  {
  
   time->minutes = 0;
   
   time->hours++;
   
   }
    
  if (time->hours >= 24) 
  
  {
    
  time->hours = 0;
  
   }
    
}

int main(void)

{
    SystemCoreClockUpdate();
    
   Delay_Init();
   
   OLED_Init();
   
   GPIO_Config();
   
   Start();

   while (1)
   
   {
    
   UpdateClockDisplay(currentTime);
        
   CheckAlarm(currentTime, alarmTime);
   
   IncrementTime(&currentTime);
   
   Delay_Ms(500);  // Wait for 1 second
    
   }
}


## Demo Video


https://github.com/Vigneshr2106/Digital-Clock-With-Alarm-Functionality/assets/165021886/01d60591-b670-43f5-b198-7104ecc43a32

