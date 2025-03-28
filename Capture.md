# 输入捕获测量占空比实验
## 原理
- 图1
![2](https://github.com/user-attachments/assets/a9d46509-b00d-4608-9a49-7d9f414a2628)
- 图2
![ebce30bc23b85fc676f790036bc4134e](https://github.com/user-attachments/assets/d1d26b0f-12e9-40b7-8d99-9f41b155eacc)
## 配置
![capture](https://github.com/user-attachments/assets/f3c17abe-a1ac-44d1-a521-a1c54f05af93)
![capture2](https://github.com/user-attachments/assets/ce6f1abf-56c2-4cad-8d3c-7883e5b0ac83)
![capture3](https://github.com/user-attachments/assets/fbce7016-853e-4b31-83ba-4b41ae07a69f)
## 代码
setup.c
```C
void setup()
{
    //打开通道1和通道2
    HAL_TIM_IC_Start(&htim15,TIM_CHANNEL_1);
    HAL_TIM_IC_Start(&htim15,TIM_CHANNEL_2);
}
```
loop.c
```C
void loop()
{
    float frq1=0;//频率
    float duty1=0;//占空比

    //求频率和占空比
    frq1=1000000.0f/(HAL_TIM_ReadCapturedValue(&htim15,TIM_CHANNEL_1)+1);
    duty1=(HAL_TIM_ReadCapturedValue(&htim15,TIM_CHANNEL_2)+1)*100.0f/(HAL_TIM_ReadCapturedValue(&htim15,TIM_CHANNEL_1)+1);
}
```
## 关键API
- 开启TIM_CHANNEL_1该通道输入捕获函数
```C
HAL_TIM_IC_Start(&htim2,TIM_CHANNEL_1);
HAL_TIM_IC_Start(&htim2,TIM_CHANNEL_2);
```
- 读取对应通道CCR值函数
```C
HAL_TIM_ReadCapturedValue(&htim2,TIM_CHANNEL_1)
HAL_TIM_ReadCapturedValue(&htim2,TIM_CHANNEL_2)
```
