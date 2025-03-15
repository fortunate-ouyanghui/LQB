# GPIO
## GPIO原理图
![1](https://github.com/user-attachments/assets/13748cbc-643c-4a67-a3e5-5ca3966f5235)
---
## GPIO功能:
4种输入模式:
- 浮空输入
- 上拉/下拉输入
- 模拟输入
  
4种输出模式:
- 开漏输出
- 推挽输出
- 复用开漏输出(数据来自片上设备)
- 复用推挽输出(同上)
---
---
# LED实验
## 目的：点亮LED
## LED原理图
![2](https://github.com/user-attachments/assets/cd2afc76-f8f2-46b2-aac0-41016a5522ea)
## STM32cubemax配置
- SYS,Debug选择serial Wire
  ![3](https://github.com/user-attachments/assets/7cd5ecf5-1709-4fca-90fe-5167e5273910)
- RCC,HSE选择第三个
  ![4](https://github.com/user-attachments/assets/4788815e-713a-4e09-90c7-cfb524371160)
- clock，选择24和80
  ![5](https://github.com/user-attachments/assets/776a4fc9-cfe5-483a-954e-43f48db8a9bc)
- GPIO_OutPut，选择PC8-PC15为GPIO输出，注意PD2也要选择GPIO输出(***LED亮的前提是置1->PD2***)
  ![QQ截图20250315125007](https://github.com/user-attachments/assets/0dedd97c-74ce-448d-9745-8eb6f036b437)
## Keil5配置
- first
  ![7](https://github.com/user-attachments/assets/684b48be-b62c-4e29-a13d-46c3297a1564)
- second
  ![8](https://github.com/user-attachments/assets/07c8898c-2ce0-4c13-bb66-cc254a3455b6)
## LED代码
- LED.h
```C
#ifndef __LED_H
#define __LED_H

#include "main.h"
#include "gpio.h"

static uint8_t led_sta=0x10;

#define on 1   //亮
#define off 0  //灭

void LED_Disp(uint8_t dsLED);//LED显示
void setup(void);//LED初始
void LED_Chg(uint8_t num,uint8_t mode);//选择某个LED亮or灭
void LED_Debug(uint8_t mode);//LED调试
void loop(void);

#endif

```
- LED.c
```C
#include "LED.h"

//PC8-PC15->8个LED

/**
*@brief LED 显示
*@param dsLED 待显示的LED dsLED=0x01->LED1亮  dsLED=0x02->LED2亮  dsLED=0x40->LED3亮
*/
void LED_Disp(uint8_t dsLED)
{
	HAL_GPIO_WritePin(GPIOC,GPIO_PIN_All,GPIO_PIN_SET);//所有LED熄灭
	HAL_GPIO_WritePin(GPIOC,dsLED<<8,GPIO_PIN_RESET);//左移8位：控制PC8-PC15引脚，值为1的点亮
	HAL_GPIO_WritePin(GPIOD,GPIO_PIN_2,GPIO_PIN_SET);//开锁存
	HAL_GPIO_WritePin(GPIOD,GPIO_PIN_2,GPIO_PIN_RESET);//关锁存
}


//LED初始化->不点亮LED
void setup(void)
{
	LED_Disp(0x00);
}


/**
*@brief 指定第几个LED亮还是灭
*@param num 第几个LED
*@param mode=1(on)->亮  mode=0(off)->灭
*/
void LED_Chg(uint8_t num,uint8_t mode)
{
	uint8_t pos=0x01<<(num-1);
	led_sta=(led_sta&~pos)|pos*mode;
	LED_Disp(led_sta);
}


/**
*@brief LED调试
*mode1 LED5常亮 LED1亮&LED2灭
*mode2 LED5常亮 LED1灭&LED2亮
*/
void LED_Debug(uint8_t mode)
{
	uint8_t led_sta=0x10;
	
	//mode1
	if(mode==1)
	{
		led_sta=(led_sta&0xfe)|0x01;//xxxx xxx0 | 0000 0001->LED1亮
		led_sta=(led_sta&0xfd)|0x00;//xxxx xx0x | 0000 0000->LED2灭
		LED_Disp(led_sta);
		HAL_Delay(100);
	}
	else if(mode==2)
	{
		led_sta=(led_sta&0xfe)|0x00;//xxxx xxx0 | 0000 0000->LED1灭
		led_sta=(led_sta&0xfd)|0x02;//xxxx xx0x | 0000 0010->LED2亮
		LED_Disp(led_sta);
		HAL_Delay(100);
	}
}


void loop(void)
{
}

```

