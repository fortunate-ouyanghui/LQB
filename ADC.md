# ADC模拟电压测量实验(单通道非扫描)
## 原理
![ADC](https://github.com/user-attachments/assets/580f3f3f-3e08-4ddb-be02-d29b44f1547f)
## 配置

## 代码
- My.h
```C
#ifndef __MY_H
#define __MY_H


#include "gpio.h"
#include "adc.h"


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
	HAL_ADCEx_Calibration_Start(&hadc2,ADC_SINGLE_ENDED);
}

void loop()
{
	float adc_data;
  adc_data =adc_read(&hadc2);
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
