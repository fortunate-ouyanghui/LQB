# systick定时器
说明：系统内置定时器无需cubemax配置。每1ms产生一次中断，uwTick++。
# 实验：每500ms翻转一次LED0
# 代码
- setup.c
```C
static uint32_t 500msTime;
void setup()
{
  500msTime=uwTick;
}
```
- loop.c
```C
void loop()
{
  if(uwTick-500msTime>500)
  {
    LED0_TOGGLE();
    500msTime=uwTick;
  }
}
```
