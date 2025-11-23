# stm32-HAL-SimpleUARTRX
stm32接收外部信号，控制板载LED点亮熄灭。电脑端输入字符0，LED熄灭；输入字符1，LED点亮。  
## 具体实现
![电路接线图](https://github.com/wahtcanisay/stm32-SimpleUARTTX/blob/main/%E6%8E%A5%E7%BA%BF%E5%9B%BE.png)
### cubemx部分

首先再`SYS`中`Debug`选择`Serial Wire`  
再才`Connectivity`选择``USART1``,选择异步工作模式,参数设为115200,8 Bits, None, 1，收发双向。  
再在电脑的串口调试中选用相同的参数，打开串口。  
将PC13设为输出，初始设为高电压，开漏状态。  

### keil5部分

**新接口：**
```c
HAL_StatusTypeDef HAL_UART_Receive(UART_HandleTypeDef *huart,
                                   uint8_t *pData,
                                   uint16_t Size,
                                   uint32_t Timeout)
```
作用：通过串口接受一定量数据
参数：      
  *huart 串口句柄指针  
  *pData 指向接受缓冲区的指针  
  Size 要接收的数据数量，以字节为单位  
  Timeout 超时时间，单位ms，填写HAL_MAX_Delay代表无限期等待下去直到接收完成为止  

返回：返回程序执行结果   
HAL_OK - 成功  
HAL_ERROR - 接收出错  
HAL_BUSY - 串口忙  
HAL_TIMEOUT - 接收超时  

```c
while(1){
    //数据接收地址
    uint8_t dataRcvd;

    HAL_UART_Receive(&huart, &dataRcvd, 1, HAL_MAX_Delay);

    if(dataRcvd == '0'){
        //PC13输出1熄灭板载LED
        HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_SET);
    }
    else if(dataRcvd == '1'){
        //PC13输出0点亮板载LED
        HAL_GPIO_WritePin(GPIOC, GPIO_PIN_13, GPIO_PIN_RESET);
    }

}
```