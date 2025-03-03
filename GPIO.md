# GPIO
## GPIO原理图
![GPIO原理图](https://github.com/fortunate-ouyanghui/LQB/blob/main/GPIO.png)
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
![LED原理图](https://github.com/fortunate-ouyanghui/LQB/blob/main/LED%E5%8E%9F%E7%90%86%E5%9B%BE.png)
## STM32cubemax配置
- SYS,Debug选择serial Wire
  ![SYS](https://github.com/fortunate-ouyanghui/LQB/blob/main/sys.png)
- RCC,HSE选择第三个
  ![RCC](https://github.com/fortunate-ouyanghui/LQB/blob/main/RCC.png)
- clock，选择24和80
  ![clock](https://github.com/fortunate-ouyanghui/LQB/blob/main/clock.png)
- GPIO_OutPut，选择PC8-PC15为GPIO输出，注意PD2也要选择GPIO输出(***LED亮的前提是置1->PD2***)
  ![GPIO配置](https://github.com/fortunate-ouyanghui/LQB/blob/main/GPIO_output.png)
## Keil5配置
- first
  ![first](https://github.com/fortunate-ouyanghui/LQB/blob/main/1.png)
- second
  ![second](https://github.com/fortunate-ouyanghui/LQB/blob/main/2.png)
## LED代码
```C
int main(void)
```


