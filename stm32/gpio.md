### 模式

```c
1. GPIO_Mode_AIN //模拟输入, IO信号直接读到片上外设模块(ADC)
2. GPIO_Mode_IN_FLOATING //浮空输入, 引脚悬空时电平不确定
3. GPIO_Mode_IPD //下拉输入, IO悬空时保持低电平
4. GPIO_Mode_IPU //上拉输入, IO悬空时保持高电平
5. GPIO_Mode_Out_OD //开漏输出, 若低则低，若高看外部上拉下拉
6. GPIO_Mode_Out_PP //推挽输出, IO电平一定是输出电平
7. GPIO_Mode_AF_OD //复用开漏输出
8. GPIO_Mode_AF_PP //复用推挽输出
        //开漏输出需要上拉，带负载能力强；推挽输出带负载能力不强，但不需要上拉。一般都是使用推挽输出。
```







### 使用步骤

```c
GPIO_InitTypeDef  GPIO_InitStructure; //定义初始化结构体
RCC_AHB1PeriphClockCmd(RCC_AHB1Periph_GPIOF, ENABLE);  //开启外设时钟
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9 | GPIO_Pin_10;  //选择控制引脚
GPIO_InitStructure.GPIO_Mode = GPIO_Mode_OUT;  //选择输出模式
        1. GPIO_Mode_IN  //输入
        2. GPIO_Mode_OUT //输出
        3. GPIO_Mode_AF  //复合
        4. GPIO_Mode_AN  //模拟
GPIO_InitStructure.GPIO_OType = GPIO_OType_PP
        1. GPIO_OType_PP  //推挽输出
        2. GPIO_OType_OD  //开漏输出
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_100MHz;
        1. GPIO_Speed_2MHz,
        2. GPIO_Speed_25MHz
        3. GPIO_Speed_50MHz
        4. GPIO_Speed_100MHZ
            //设计采样精度和传输速度
GPIO_InitStructure.GPIO_PuPd = GPIO_PuPd_UP;
        1. GPIO_PuPd_NOPULL  //悬空
        2. GPIO_PuPd_UP  //上拉
        3. GPIO_PuPd_DOWN //下拉
GPIO_Init(GPIOF, &GPIO_InitStructure); //初始化
```

### GPIO库函数

```c
void  GPIO_Init  (GPIO_TypeDef* GPIOx，GPIO_InitTypeDef* GPIO_InitStruct) //初始化函数
void GPIO_DeInit(GPIO_TypeDef* GPIOx) //取消初始化函数
uint8_t  GPIO_ReadInputDataBit  (GPIO_TypeDef* GPIOx , uint16_t GPIO_Pin) //读取输入管脚的值，返回为Bit_SET 或 Bit_RESET
uint16_t  GPIO_ReadInputData (GPIO_TypeDef* GPIOx ) //读取输入IO状态(16位)
uint8_t  GPIO_ReadOutputDataBit  (GPIO_TypeDef* GPIOx , uint16_t GPIO_Pin) //读取输出管脚的值
uint16_t  GPIO_ReadOutputData (GPIO_TypeDef* GPIOx ) //读取输出管脚(16位)
void  GPIO_SetBits(GPIO_TypeDef* GPIOx，uint16_t  GPIO_Pin) //置1
void  GPIO_ResetBits(GPIO_TypeDef* GPIOx，uint16_t  GPIO_Pin) //置0
void  GPIO_WriteBit(GPIO_TypeDef* GPIOx，uint16_t  GPIO_Pin，BitActionBitVal)  //置0或1, BitActionBitVal取Bit_SET或Bit_RESET
void  GPIO_Write(GPIO_TypeDef* GPIOx，uint16_t  PortVal) //统一对某一个端口写入, PortVal取Bit_SET或Bit_RESET
void  GPIO_ToggleBits(GPIO_TypeDef* GPIOx，uint16_t  GPIO_Pin)  //翻转某一端口的电平
```

