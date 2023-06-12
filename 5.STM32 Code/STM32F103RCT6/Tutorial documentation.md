# Tutorial documentation

##### Preparations

Connect the dual-channel motor drive board and stm32 according to the wiring diagram, and the battery is connected to the power input interface above the dual-channel motor drive board, and the input voltage range is 5~12V. (The bottom of the board is the power output interface, don't connect it wrong!) ）

The picture is the wiring diagram of STM32F103C8T6, STM32F103RCT6 can be wired according to the picture pin.

![image-20230612171950832](C:\Users\W_ML\Desktop\STM32F103RCT6\英文教程\310 motor\Wiring diagram.jpg)

**Note: The motor interface line sequence of the dual-way motor drive board must correspond to the motor pin! The drive board motor interface line order does not correspond to the motor pins will burn the drive board!!! **

##### Main code

```c
#include "stm32f10x.h"
#include "delay.h"
#include "gpio.h"
#include "moto.h"
#include "pwm.h"
#include "adc.h"
#include "usart.h"
#include "encoder.h"
 
int main(void)
 {	
	 u16 encoder_A,encoder_B;
	 int Velocity_PWM1,Velocity_PWM2;
	 u16 adcx;
	 float vcc;
   SystemInit(); //配置系统时钟为72Mhz  
   delay_init();    //延时函数初始化
   uart_init(9600);		//串口初始化
	 
   adc_Init();				//ADC1初始化  
	 
   PWM_Int(7199,0);      //初始化pwm输出 72000 000 /7199+1=10000 
   Encoder_Init_Tim2();
   Encoder_Init_Tim4();
  while(1)
	{
//	    moto(0);                //moto=0正转
//	    moto(1);                //moto=1反转

		adcx=Get_adc_Average(ADC_Channel_2,10);  //获取ADC的值
		vcc=(float)adcx*(3.3*11/4096);     		 //求当前电压
		encoder_A=Read_Encoder(2);
		encoder_B=Read_Encoder(4);               //读取编码器数值
		Velocity_PWM1=Velocity_A(encoder_A);
		Velocity_PWM2=Velocity_B(encoder_B);
		Set_PWM(Velocity_PWM1,Velocity_PWM2);
		printf("µ±Ç°µçÑ¹=%6.2f V  Encoder_A = %d  Encoder_B=%d\r\n",vcc,encoder_A,encoder_B);				//打印当前工作电压及转速	
	}
 }


```

##### Experimental results						

The code is burned into the STM32 core board, and the two-way motor starts to turn. Open serial port software such as serial port debugging assistant, set the baud rate to 9600, you can see the current voltage value of the serial port output, and the coding value of the dual-channel motor speed.