# RTC实时时钟实验
## 简介
![7c7db6bad62fecdaf5500936bfbf1aa9](https://github.com/user-attachments/assets/be9446fb-56d7-4d79-84a9-18bbe5b3710d)
## 配置
![1](https://github.com/user-attachments/assets/4c8cd6d3-0660-45f7-b166-7067e34cffc6)
## 代码
- My.h
```C
#ifndef __MY_H
#define __MY_H


#include "lcd.h"
#include "rtc.h"
#include "stdio.h"


void setup(void);
void loop(void);


#endif

```
- My.c
```C
#include "My.h"


void setup()
{
	LCD_Init();
	LCD_Clear(Black);
  LCD_SetBackColor(Black);
	LCD_SetTextColor(White);
}
void loop()
{
	//date和time是绑定在一起的
	RTC_TimeTypeDef time;
	RTC_DateTypeDef date;
	HAL_RTC_GetTime(&hrtc,&time,RTC_FORMAT_BIN);
	HAL_RTC_GetDate(&hrtc,&date,RTC_FORMAT_BIN);
	char text[30];
	sprintf(text,"time:%d:%d:%d",time.Hours,time.Minutes,time.Seconds);
	LCD_DisplayStringLine(Line0,(uint8_t*)text);
}

```
