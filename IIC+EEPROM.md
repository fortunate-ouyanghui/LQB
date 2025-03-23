# IIC实验
## IIC基础
- knowledge
![1](https://github.com/user-attachments/assets/96884586-73ab-408f-99d2-bbf14f512fb4)
- 时序图
![2](https://github.com/user-attachments/assets/179c25a8-2569-4dc0-827b-8f23cd4484ae)
- 原理图
![4](https://github.com/user-attachments/assets/3a530fe1-219a-4307-94b1-d4f775a125cd)
## 配置
- 移植IIC.h和IIC.c文件。
- 注意：移植官方文件时，注意修改i2c-hal.c文件中#include "i2c.h"为#include "i2c-hal.h"
## 代码
- 时序图
![1](https://github.com/user-attachments/assets/369d01dc-c0eb-410f-ad7b-39e8508db06e)
- EEPROM

![2](https://github.com/user-attachments/assets/a3ec654c-93d1-4cc5-ab0f-2439abe5cbee)
- MCP4017-数字电位器
![1](https://github.com/user-attachments/assets/f072aa0f-2940-423d-be50-0f134d208690)

- 编写函数eeprom_write
```C
//向设备地址1010 000 的寄存器地址ad dr写入数据data
void eeprom_write(uint8_t addr,uint8_t data)
{
	I2CStart();
	I2CSendByte(0xa0);//设备地址
	I2CWaitAck();
	I2CSendByte(addr);//寄存器地址
	I2CWaitAck();
	I2CSendByte(data);
	I2CWaitAck();
	I2CStop();
}
```
- 编写函数eeprom_read
```C

//向设备地址1010 000的寄存器地址addr读入数据
uint8_t eeprom_read(uint8_t addr)
{
	uint8_t rec;
	I2CStart();
	I2CSendByte(0xa0);
	I2CWaitAck();
	I2CSendByte(addr);//寄存器地址
	I2CWaitAck();
	I2CStop();
	
	I2CStart();
	I2CSendByte(0xA1);
	I2CWaitAck();
	rec=I2CReceiveByte();
	I2CSendNotAck();
	I2CStop();
	return rec;
}
```
