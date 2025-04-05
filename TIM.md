# TIM按键消抖实验
## TIM功能:
- 定时中断
- 输入捕获
- 输出比较
- 编码器
- 主从触发
## 按键原理图
![KEY](https://github.com/user-attachments/assets/491ea08c-321c-44cd-852d-f3fb184d532d)
## TIM定时原理  
![1](https://github.com/user-attachments/assets/16645adc-afc3-44a1-a143-dd1aba9cf807)
CK_PSC/(PSC+CNT)->每次触发中断的频率为CK_PSC/(PSC+CNT)或每(PSC+CNT)/CK_PSC秒触发一次中断
## TIM配置
- 开启定时器和NVIC中断
![tim2](https://github.com/user-attachments/assets/4805ce38-b433-4d40-a820-bebaf6ac484f)
- 配置参数
![tim1](https://github.com/user-attachments/assets/ebc4197d-05c1-4551-a6cd-6b22862b9485)
- 配置引脚
![key2](https://github.com/user-attachments/assets/9d388c78-0c13-49ab-b5fa-b34c627ac957)
- 配置模式
![key3](https://github.com/user-attachments/assets/0d37c661-99de-4499-8583-8e6beaa3982f)
## 代码编写
- 消抖原理
![消抖](https://github.com/user-attachments/assets/84b91d1a-23e8-4ae9-a2a4-54a2d3ea22cb)
- KEY.h
```C
#ifndef __KEY_H
#define __KEY_H


typedef struct Bkeys
{
    uint8_t age;//通过age来确定按键按下的时长
    uint8_t short_flag;//按键按下(短按)short_flag为1,用户操作后手动置0
    uint8_t long_flag;//按键按下(长按)long_flag为1,松开后自动归0（可以自己决定手动还是自动）
    uint8_t press;

    uint8_t double_ageEN;//双击两次按下中间是否要计时 1->开始计时 0->不计时
    uint8_t double_age;//双击中间计时时长
    uint8_t double_flag;//双击标志，用户自己配置归0
}Bkeys;


uint8_t key_read(void);//获取哪个按键按下
void key_serv(void);//短按
void key_serv_long(void);//长按
void key_serv_double();//双击

#endif
```
- KEY.c
```C
#include "KEY.h"


Bkeys bkeys[5]={0,0,0,0,0};


//读取按键返回按键值
uint8_t key_read()
{
    if(HAL_GPIO_ReadPin(GPIOB,GPIO_PIN_0)==0)      return 1;
    else if(HAL_GPIO_ReadPin(GPIOB,GPIO_PIN_1)==0) return 2;
    else if(HAL_GPIO_ReadPin(GPIOB,GPIO_PIN_2)==0) return 3;
    else if(HAL_GPIO_ReadPin(GPIOA,GPIO_PIN_0)==0) return 4;
    else return 0;
}


//短按
void key_serv()
{
    uint8_t key_sta=key_read();
    if(key_sta!=0)//按键按下
    {
        bkeys[key_sta].age++;
        if(bkeys[key_sta].age==2) bkeys[key_sta].short_flag=1;
    }
    else//按键松开
    {
        for(int i=0;i<5;++i) bkeys[i].age=0;
    }
}


//长按 执行流程：1->3->2
void key_serv_long()
{
    uint8_t key_sta=key_read();
    /*1.按键时长计时 */
    if(key_sta!=0)//按键按下
    {
        bkeys[key_sta].age++;
        if(bkeys[key_sta].age==2) bkeys[key_sta].press=1;//按键按下状态
    }
    /*2.按键松开各状态归0并判断是长按还是短按*/
    else//按键松开
    {
        for(int i=0;i<5;++i)
        {
            if(bkeys[i].press==1 && bkeys[i].long_flag==0)
                bkeys[i].short_flag=1;

            bkeys[i].press=0;
            bkeys[i].age=0;
            bkeys[i].long_flag=0;//用户自己决定手动还是松开后自动归0
        }
    }
    /*3.判断是否长按 */
    if(bkeys[key_sta].age>69) bkeys[key_sta].long_flag=1;//长按时长为700ms->长按
}


//按键双击
void key_serv_double()
{
    uint8_t key_sta=key_read();
    /*1.按键时长计时 */
    if(key_sta!=0)//按键按下
    {
        bkeys[key_sta].age++;
        if(bkeys[key_sta].age==2) bkeys[key_sta].press=1;//按键按下状态
    }
    /*2.按键松开各状态归0并判断是长按还是短按*/
    else//按键松开
    {
        for(int i=0;i<5;++i)
        {
            //判断是否是第二次按下并且是双击
            if(bkeys[i].double_ageEN==1 && bkeys[i].press==1)
            {
                bkeys[i].double_flag=1;
                bkeys[i].press=0;
                bkeys[i].double_ageEN=0;
            }

            //第一次按下
            if(bkeys[i].press==1 && bkeys[i].long_flag==0) bkeys[i].double_ageEN=1;//第一次短按松开后开始计时
            if(bkeys[i].double_ageEN==1) bkeys[i].double_age++;//计时
            if(bkeys[i].double_ageEN==1 && bkeys[i].double_age>20)//单击
            {
                bkeys[i].short_flag=1;
                bkeys[i].double_age=0;
                bkeys[i].double_ageEN=0;
            }
            bkeys[i].press=0;
            bkeys[i].age=0;
            bkeys[i].long_flag=0;//用户自己决定手动还是松开后自动归0
        }
    }
    /*3.判断是否长按 */
    if(bkeys[key_sta].age>69) bkeys[key_sta].long_flag=1;//长按时长为700ms->长按
}

```
- TIM中断函数
```C
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    if(htim->Instance==TIM6)
        key_serv();
}
```
- 初始化配置
```C
void setup()
{
    HAL_TIM_Base_Start_IT(&htim6);
}
```
- My.h
```C
#ifndef __MY_H
#define __MY_H

#include "tim.h"
#include "gpio.h"
#include "KEY.h"
#include "lcd.h"
extern Bkeys bkeys[5];

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
	HAL_TIM_Base_Start_IT(&htim6);
}

void loop()
{
	if(bkeys[1].double_flag==1)
	{
		LCD_DisplayStringLine(Line0,(uint8_t *)"Key1:on");
		bkeys[1].double_flag=0;
	}
}

void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
	if(htim->Instance==TIM6)
	{
		key_serv_double();
	}
}

```
## 关键API
- 开启中断定时器
```C
HAL_TIM_Base_Start_IT(&htim6);
```
- 定时器周期溢出中断服务函数
```C
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
```
