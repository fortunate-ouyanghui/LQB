# TIM按键消抖实验
## TIM功能:
- 定时中断
- 输入捕获
- 输出比较
- 编码器
- 主从触发
## TIM定时原理  
![TIM定时原理](https://github.com/user-attachments/assets/9532a271-6d8e-4a73-bb88-0496cbefa915)
CK_PSC/(PSC+CNT)->每次触发中断的频率为CK_PSC/(PSC+CNT)或每(PSC+CNT)/CK_PSC秒触发一次中断
## TIM配置
![配置1](https://github.com/fortunate-ouyanghui/LQB/blob/main/TIM_NVIC.png)
![配置2](https://github.com/fortunate-ouyanghui/LQB/blob/main/TIM6.png)
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
}Bkeys;


uint8_t key_read(void);//获取哪个按键按下
void key_serv(void);//短按
void key_serv_long(void);//长按


#endif
```
- KEY.c
```C
#include "KEY.h"


Bkeys bkeys[5]={0,0,0,0};


//读取按键返回按键值
uint8_t key_read()
{
    if(HAL_GPIO_ReadPin(GPIOB,GPIO_PIN_0)==0)      return 1;
    else if(HAL_GPIO_ReadPin(GPIOB,GPIO_PIN_1)==0) return 2;
    else if(HAL_GPIO_ReadPin(GPIOB,GPIO_PIN_2)==0) return 3;
    else if(HAL_GPIO_ReadPin(GPIOA,GPIO_PIN_0)==0) return 4;
    else return -1;
}


//短按
void key_serv()
{
    uint8_t key_sta=key_read();
    if(key_sta!=-1)//按键按下
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
    if(key_sta!=-1)//按键按下
    {
        bkeys[key_sta].age++;
        if(bkeys[key_sta].age==2) bkeys[key_sta].press=1;//按键按下状态
    }
    /*2.按键松开各状态归0并判断是长按还是短按*/
    else//按键松开
    {
        for(int i=0;i<5;++i)
        {
            if(bkeys[i].press=1 && bkeys[i].long_flag==0)
                bkeys[i].short_flag=1;

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
    if(htim->Instance=TIM6)
        key_serv();
}
```
