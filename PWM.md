# PWM实验
## PWM基础知识
- PWM
![fc45c7e54da6bd00e317780f956c954f](https://github.com/user-attachments/assets/2754ffc5-ea6c-4c11-a5a3-eac9d11f7233)
- 输出比较
![7e45032e19224ed9787351320f08930a](https://github.com/user-attachments/assets/674585cc-c65a-4473-93da-97e1ce8c38da)
- 两种模式
![e05ac0ec7809a6a3fbf746ae1270b66c](https://github.com/user-attachments/assets/570be005-f258-49e5-be8f-e0057e51e641)
![87118d6bccbf9d57fab8f06ca81bfc6f](https://github.com/user-attachments/assets/5c1dd856-1bd0-4108-b736-e187e11ffe8e)
- PWM原理图
![3](https://github.com/user-attachments/assets/8bb57260-75bb-47f0-889c-b85eacf63e5a)
## PWM配置
![pwm](https://github.com/user-attachments/assets/b55fe523-cba8-4b0b-add7-380d48a0d824)
![pwm5](https://github.com/user-attachments/assets/6d38ed6a-8946-4715-bdd1-d8a4abac2466)
![pwm3](https://github.com/user-attachments/assets/6dad6298-a7da-4860-b1b7-f83ed17ffc6f)
![pwm4](https://github.com/user-attachments/assets/5df47de3-0d97-4f7d-bd68-8fc9cf64961f)
## 代码
- 初始化代码
```C
void setup()
{
  HAL_TIM_PWM_Start(&htim2,TIM_CHANNEL_2);
}
```
- 修改ARR
```C
__HAL_TIM_SET_AUTORELOAD(&htim2,250);
```
- 修改CCR
```C
__HAL_TIM_SetCompare(&htim2,TIM_CHANNEL_2,100);
```
- 修改CNT
```C
__HAL_TIM_SET_COUNTER();
```



