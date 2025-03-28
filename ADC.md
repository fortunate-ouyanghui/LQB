# ADC模拟电压测量实验(单通道非扫描)
## 原理
![ADC](https://github.com/user-attachments/assets/580f3f3f-3e08-4ddb-be02-d29b44f1547f)
## 配置
- first

![1](https://github.com/user-attachments/assets/9fe74d77-2699-437f-bca8-b5c0b00f6957)
- second
![2](https://github.com/user-attachments/assets/a6837312-7e81-4018-8273-c87f44d082a1)
- third
![3](https://github.com/user-attachments/assets/6e84d91b-4d41-48d8-a331-72eccac7e66c)
- fourth
![4](https://github.com/user-attachments/assets/04811e77-6761-4d39-8f0a-77bef365c67c)
- fifth
![5](https://github.com/user-attachments/assets/9a6f598d-919e-47dc-a176-b8b7366d659d)
- 原理图
![3](https://github.com/user-attachments/assets/716cb4c1-b0fe-4099-a0d5-f5cd98edb939)
## 代码
- My.h
```C
#ifndef __MY_H
#define __MY_H


#include "gpio.h"
#include "adc.h"
#include "lcd.h"
#include "stdio.h"

void setup(void);
void loop(void);
float adc_read(ADC_HandleTypeDef *hadc);


#endif

```
- My.c
```C
#include "My.h"


void setup()
{
	LCD_Init();
	LCD_Clear(White);
  LCD_SetBackColor(White);
	LCD_SetTextColor(Black);//设置文本颜色
	HAL_ADCEx_Calibration_Start(&hadc2,ADC_SINGLE_ENDED);
}

void loop()
{
	float adc_data;
  adc_data =adc_read(&hadc2);
	char text[30];
	sprintf(text,"PB15:%.4f",adc_data);
	LCD_DisplayStringLine(Line9,(uint8_t*)text);
}

float adc_read(ADC_HandleTypeDef *hadc)
{
	uint16_t adc_val;
	float adc_f;
	HAL_ADC_Start(hadc);
	adc_val=HAL_ADC_GetValue(hadc);
	adc_f=adc_val*3.3/4096.0f;
	return adc_f;
}
```
## 关键API
- ADC校准函数
```C
HAL_ADCEx_Calibration_Start(&hadc2,ADC_SINGLE_ENDED);//第二个参数有两种选择，一个是单端模式校准，另一个是差分模式校准
```
- ADC开始转换函数
```C
HAL_ADC_Start(hadc);	
```
- ADC停止转换
```C
HAL_ADC_Stop(&hadc1);
```
- 等待转换完成函数
```C
HAL_ADC_PollForConversion(&hadc, HAL_MAX_DELAY)在这个HAL_MAX_DELAY时间范围内，判断是否转换完成（EOC是否置1）,若发现转换完成则返回HAL_OK
```
- 读取ADC的值
```C
HAL_ADC_GetValue(hadc);
```
