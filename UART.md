# 串口通信实验
## 配置
![1](https://github.com/user-attachments/assets/cb370e70-1cbd-427f-9cf0-b0d17f46ff4a)
![2](https://github.com/user-attachments/assets/23e6be05-bc6d-4a84-aea1-42ab5a255352)
![3](https://github.com/user-attachments/assets/e71804d0-b7af-4e89-b467-67832bfe98e9)
## 代码
- My.h
```C
#ifndef __MY_H
#define __MY_H


#include "lcd.h"
#include "stdio.h"
#include "string.h"
#include "usart.h"
#include "stdbool.h"


void wait_Tx_complete(void);
void setup(void);
void loop(void);


#endif

```
- My.c
```C
#include "My.h"



#define Idle_IT 1
#define Ordnary_IT_Rx 2
#define SWITCH Idle_IT   //选择idle接收中断还是普通中断


bool Tx_complete_flag=false;
char Idle_Rx[10];//idle中断接收缓冲区
char UART_Rx[10];//中断接收缓冲区
uint8_t Rx_Byte;
uint16_t Rx_Index=0;
bool Ready=false;//数据包是否准备完成
char UART_Tx[10]="good\r\n";//中断发送缓冲区
char passwd[10]="123";//密码
char text[50];



void setup()
{
	LCD_Init();
	LCD_Clear(Black);
  LCD_SetBackColor(Black);
	LCD_SetTextColor(White);
	
	//开启发送中断
	//HAL_UART_Transmit_IT(&huart1,(uint8_t*)UART_Tx,strlen(UART_Tx));
	
	#if SWITCH == Idle_IT
		HAL_UARTEx_ReceiveToIdle_IT(&huart1,(uint8_t*)Idle_Rx,1024);//开启空闲接收中断
	#else
		HAL_UART_Receive_IT(&huart1,&Rx_Byte,1);//开启普通接收中断
	#endif
	
}


void loop()
{
	if(Ready==true)
	{
	  LCD_DisplayStringLine(Line2,(uint8_t*)UART_Rx);
		Ready=false;
	}
	
	wait_Tx_complete();

}


//等待发送完成函数
void wait_Tx_complete()
{
	while(Tx_complete_flag==false);
	Tx_complete_flag=false;
}


//发送中断处理函数
void HAL_UART_TxCpltCallback(UART_HandleTypeDef *huart)
{
	Tx_complete_flag=true;
	
	//再次开启发送中断
	HAL_UART_Transmit_IT(&huart1,(uint8_t*)UART_Tx,strlen(UART_Tx));
}


//Idle中断处理函数
void HAL_UARTEx_RxEventCallback(UART_HandleTypeDef *huart, uint16_t Size)
{
	char old_pwd[10];
	char new_pwd[10];
	sscanf(Idle_Rx,"%3s-%3s",old_pwd,new_pwd);//从uart_rx中以 %3s-%3s 的格式赋值给old_pwd和new_pwd
	
	//如果旧密码是对的，则允许更改新密码
	if(strcmp(old_pwd,passwd)==0)
	{
		for(int i=0;i<3;++i) passwd[i]=new_pwd[i];
		sprintf(text,"new pwd:%s",passwd);//将passwd以 new pwd: %s 的格式赋值给text
		LCD_DisplayStringLine(Line0,(uint8_t*)text);
	}
	else
	{
		LCD_DisplayStringLine(Line1,(uint8_t*)"failed     ");
	}
		
	//再次开启空闲中断
	HAL_UARTEx_ReceiveToIdle_IT(&huart1,(uint8_t*)Idle_Rx,1024);
}


//普通中断处理函数
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart)
{
	if(Rx_Byte=='\r' || Rx_Byte=='\n')//如果接收完一个数据包
	{
		if(Rx_Index>0)//如果数据包不是空的
		{
			UART_Rx[Rx_Index]='\0';
			Rx_Index=0;
			Ready=true;
		}
	}
	else
	{
		if(Rx_Index<1024)
			UART_Rx[Rx_Index++]=Rx_Byte;
		else
			Rx_Index=0;
	}
	
	//再次开启普通中断接收
	HAL_UART_Receive_IT(&huart1,&Rx_Byte,1);
}
```
