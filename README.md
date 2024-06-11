# 价格配置器基本逻辑以及安装指导


## 目录
1. [文件夹内容总览](#文件夹内容总览)
2. [价格计算逻辑](#价格计算逻辑)
	- [exchange_rate](#exchange_rate) 设置逻辑
	- [sea freight](#sea_freight) 计算逻辑
	- [insurance](#insurance) 计算逻辑
	- [storage](#storage) 计算逻辑
	- [inbound](#inbound) 计算逻辑
3. [config](#coinfig)
4. [data ](#data )



## 文件夹内容总览
`price_cal.exe` 为小程序启动入口 
`config.ini` 为配置文件， [config](#config) 
`data/` 为所有excel文件存储位置，储存了各个地区的价格，成本，海运adder等。[data](#data) 

## 价格计算逻辑
#### 成本计算公式

>**本地成本 = 虚拟成本/exchange rate**
>
>**本地成本_FOB = 本地成本**
>
>**本地成本_CIF =  本地成本 + (sea freight + insurance) * SHIPPING_SAFETY_INDEX**
>
>**本地成本_DAP = 本地成本 + (sea freight + insurance + distribution) * SHIPPING_SAFETY_INDEX**
>
>**本地成本_FCA = 本地成本 + (sea freight + insurance) * SHIPPING_SAFETY_INDEX + (inbound + storage) * INBOUND_SAFETY_INDEX**

[exchange_rate](#exchange_rate) 设置逻辑
[sea freight](#sea_freight) 计算逻辑
[insurance](#insurance) 计算逻辑
[storage](#storage) 计算逻辑
[inbound](#inbound) 计算逻辑

在每一行产品型号计算中，会根据用户选择的贸易条款，有且只有一个成本会被使用。 

如果是非FOB的贸易条款，则需要额外的数据支持，如果所需要的Excel不在指定位置，则无法计算，表现为按键点击后无效果。

### 毛利率计算公式
>**gross margin = (售价-本地成本_{贸易条款})/售价**

售价均为未税售价

### exchange_rate
汇率在 [config](#coinfig)【地区】中的 `EXCHANGE_RATE` 项。

>**例子：**
澳元的汇率为 7.09 表现在
>
>【AUS】
EXCHANGE_RATE = 7.09

汇率影响到成本。 因为 `PRICE_LIST_PATH` 指向的文件中，默认是 price_list.xlsx 中的成本是人民币的成本。如果要在中国以外的国家地区定价，则
>地区成本=虚拟成本/汇率

### sea_freight
参数设置在[config](#coinfig)

**= INVERTER_SHIPPING/{40HQ}**

Battery 同理。
>**例子：**
【EU】
INVERTER_SHIPPING = 3000
BATTERY_SHIPPING = 4500
SHIPPING_SAFETY_INDEX = 1.5
>
一集装箱逆变器海运价格为 3000 欧，电池海运价格为 4500，海运安全指数为 150%
那么假如`40HQ` 有 1000 个的话（维护在【all】[config](#coinfig) 中 `ADDER_LIST_PATH` 路径指向的[data/](#data) excel文件在 `40HQ` 这一列），单个的运费的成本为： 3000/40

### insurance
保险相关的参数以及海运设置在[config](#coinfig),
{40_HQ} 和*售价\_FOB 维护在 [data/](#data) 文件夹中

 **=（INVERTER_SHIPPING+{40_HQ}\*售价_FOB）\*SHIPPING_BUFFER_RATE*INVERTER_INSURANCE_RATE/{40_HQ}**
Battery 同理。
>**例子：**
【EU】
INVERTER_INSURANCE_RATE = 0.00035
INVERTER_SHIPPING = 3000
SHIPPING_BUFFER_RATE = 1.1
{40_HQ} =1000
售价FOB = 500 
>
单机保费 =（3000+500*1000）\* 1.1  \* 0.00035 / 1000


### storage
仓储费用参数存储在[config](#coinfig)
>**例子：**
【EU】
INVERTER_STORAGE_COST = 130
BATTERY_STORAGE_COST = 260

如果一个电池 1 pallet(维护在【all】 `ADDER_LIST_PATH`  路径指向的Excel，所选大区对应的sheet 在 `1 pallet` 这一列) 为 32，则存储费为 260/32

### inbound
**= INVERTER_INBOUND /{40HQ}**
Battery 同理，出库费用参数存储在[config](#coinfig)，{40HQ}储存在excel 文件中
>**例子：**
【EU】
INVERTER_INBOUND =  1243.55 
BATTERY_INBOUND = 1373.60
{40_HQ} =1000

单机出库费用就是1243/1000

### distribution
DAP派送费参数存储在[config](#coinfig)
>**例子：**
【EU】
BATTERY_DISTRIBUTION_FEE = 1000
INVERTER_DISTRIBUTION_FEE = 1000
{40_HQ} =1000

派送费为 1000/1000


## config
#### 内容
储存了
 1. 所有外部excel数据xlsx的文件路径 
 2. 不常变化的参数，比如海运指数，汇率，仓储费用，保险汇率
.ini 中所有文件路径修改

在使用 exe 之前必须修改 config.ini 中所有需求的外置文件的路径，建议使用全部替换的功能，修改到 data 的路径

>**例子：**
在同事的电脑 XiaoluXu 上 data 文件夹存在
`C:\Users\XiaoluXu\data`
在新同事 A 的电脑上，如果要存在 D 盘上，则需要，查找全选替换为新路径，大概需要修改 20 处
`D:\Users\A\data`

## data 
#### 内容
此文件夹的结构为
> -All
 -EU
 -IND
 ...
 
*All* 文件夹中包含：
 1. `adder_list.xlsx`包含所有地区所有大洲的海运运费以及仓储运费，每个大区单独一个sheet,sheet的名字对应各个文件夹
 2. `product_list.xlsx` 包含了产品信息，比如说是逆变器还是电池
	 - `Product`列记录了产品型号
	 -  `Sales Text`如果包含Inverter则按照逆变器相关运费计算， 电池则同理，查看关键词Battery

特定地区文件夹内包含： 
 1. `price_list.xlsx` 包含人民币成本以及各个TIER的指导价格
 2. `local_hist.xlsx`的路径无法设置，默认为对应地区文件夹中
文件夹中资料维护
所有国家的额外信息，比如说成本，价格，



**成本需要维护在每个不同文件夹的 price_list 里面，的固定一列 叫虚拟成本，是人民币成本，而不是当地货币成本**
