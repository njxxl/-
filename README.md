```markdown
## 安装指导

### 1.1 config.ini 中所有文件路径修改
在使用 exe 之前必须修改 config.ini 中所有需求的外置文件的路径，建议使用全部替换的功能，修改到 data 的路径

**例子：**
在同事的电脑 XiaoluXu 上 data folder 存在
`C:\Users\XiaoluXu\data`
在新同事 A 的电脑上，如果要存在 D 盘上，则需要，查找全选替换为，大概需要修改 20 处
`D:\Users\A\data`

你可以打开 data 这个文件夹，是按照区域分散在不同文件夹的，文件夹名字不可更改

### 1.2 汇率维护 exchange rate
汇率在【地区】中的 EXCHANGE_RATE 项。

**例子：**
澳元的汇率为 7.09 表现在
【AUS】
EXCHANGE_RATE = 7.09
汇率影响到成本。 因为 PRICE_LIST_PATH 指向的文件中，默认是 price_list.xlsx 中的成本是人民币的成本。如果要在中国以外的国家地区定价，则成本为=虚拟成本/汇率

### 1.3 海运费用维护 sea freight

**例子：**
【EU】
INVERTER_SHIPPING = 3000
BATTERY_SHIPPING = 4500
SHIPPING_SAFETY_INDEX = 1.5
一集装箱逆变器海运价格为 3000 欧，电池海运价格为 4500，海运安全指数为 150%
那么假如一个逆变器一个 Pallet 有 1000 个的话（维护在【all】ADDER_LIST_PATH 路径指向的文件在 40HQ 这一列），单个的运费的成本为： 3000/40

### 1.4 仓储费用维护 storage

**例子：**
【EU】
INVERTER_STORAGE_COST = 130
BATTERY_STORAGE_COST = 260
如果一个电池 1 pallet(维护在【all】 ADDER_LIST_PATH 路径指向的文件在 1 pallet 这一列) 为 32，则存储费为 260/32

### 贸易条款对成本的影响
FIB ADDER = 0
CIF ADDER = (sea freight + insurance) * SHIPPING_SAFETY_INDEX
DAP ADDER = (sea freight + insurance + distribution) * SHIPPING_SAFETY_INDEX
FCA ADDER = CIF ADDER + (inbound_storage) * INBOUND_SAFETY_INDEX

### 2. data 文件夹中资料维护
所有国家的额外信息，比如说成本，价格，

**成本需要维护在每个不同文件夹的 price_list 里面，的固定一列 叫虚拟成本，是人民币成本，而不是当地货币成本**
```
