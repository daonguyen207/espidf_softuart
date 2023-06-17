# espidf_softuart
Thư viện uart mềm cho espidf.

Hỗ trợ truyền song công nhiều uart mềm, hoạt động tốt nhất ở tốc độ 9600

# Sử dụng
Add thư viện: 
```
#include "softuart.h"
```
Đặt SoftUartHandler() vào ngắt timer, thời gian ngắt tùy theo tốc độ truyền. Tính theo công thức 0.2*(1/BR)

if BR=9600 then 0.2*(1/9600)=20.8333333 uS

Ví dụ khởi tạo timer
```
//timer 21 us
timer_config_t config = {
        .divider = 80,
        .counter_dir = TIMER_COUNT_UP,
        .counter_en = TIMER_PAUSE,
        .alarm_en = TIMER_ALARM_EN,
        .auto_reload = 1,
    }; // default clock source is APB
    timer_init(TIMER_GROUP_0, TIMER_0, &config);
    timer_set_counter_value(TIMER_GROUP_0, TIMER_0, 0);
    timer_set_alarm_value(TIMER_GROUP_0, TIMER_0, 21);
    timer_enable_intr(TIMER_GROUP_0, TIMER_0);
    timer_isr_callback_add(TIMER_GROUP_0, TIMER_0, SoftUartHandler, 0, 0);
    timer_start(TIMER_GROUP_0, TIMER_0);
```
Ví dụ sử dụng khởi tạo uart 0
```
SoftUartInit(0,17,16); // khởi tạo soft uart0, tx=17  rx=16
SoftUartEnableRx(0);   // cho phép nhận data trên soft uart0
```
Ví dụ gửi nhận
```

//nhận
if(SoftUartRxAlavailable(0) != 0) //neu co du lieu trong bo dem rx của uart 0
{
    vTaskDelay(50 / portTICK_PERIOD_MS); //wait data
    uint8_t rx[64] = {0};
    int len = SoftUartRxAlavailable(0);
    SoftUartReadRxBuffer(0,rx,len);
    ESP_LOGI("uart","%s",(char *)rx);
}

//gửi
SoftUartPuts(0,(uint8_t *)"xin chao\r\n",10);
vTaskDelay(200 / portTICK_PERIOD_MS);
```

## Cấu hình số lượng uart và kích cỡ bộ đệm
Trong file softuat.h
```
#define 	Number_Of_SoftUarts 	6 	// Max 8

#define 	SoftUartTxBufferSize	64
#define 	SoftUartRxBufferSize	64
```
