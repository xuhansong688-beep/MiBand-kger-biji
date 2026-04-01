## 一、Modbus RTU（RS485）帧示例

RTU 帧格式：`从站地址(1B) + 功能码(1B) + 数据(NB) + CRC校验(2B)`

默认从站地址：`0x01`（1 号从站），波特率 9600，8N1

---

### 1. 功能码 01：读线圈（Read Coils）

**场景**：读取从站 1 号，线圈地址`0x0000`（1 号线圈），连续读`4`个线圈

- **请求帧（Master → Slave）**：`01 01 00 00 00 04 7D CB`
    
    表格
    
    |字节|含义|解析|
    |---|---|---|
    |`01`|从站地址|目标从站为 1 号|
    |`01`|功能码|读线圈|
    |`00 00`|起始地址|线圈起始地址 0（对应 1 号线圈）|
    |`00 04`|读取数量|连续读 4 个线圈|
    |`7D CB`|CRC 校验|低字节`CB`在前，高字节`7D`在后|
    
- **响应帧（Slave → Master）**：`01 01 01 03 91 B8`
    
    表格
    
    |字节|含义|解析|
    |---|---|---|
    |`01`|从站地址|源从站为 1 号|
    |`01`|功能码|读线圈（与请求一致）|
    |`01`|字节数|数据占 1 字节（4 个线圈≤8 位，1 字节足够）|
    |`03`|数据|二进制`00000011`，表示线圈 1/2 为 ON，3/4 为 OFF|
    |`91 B8`|CRC 校验|帧完整性校验|
    

---

### 2. 功能码 02：读离散输入（Read Discrete Inputs）

**场景**：读取从站 1 号，离散输入地址`0x0000`（1 号输入），连续读`8`个输入

- **请求帧**：`01 02 00 00 00 08 B9 9E`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`02`|功能码（读离散输入）|
    |`00 00`|起始地址 0|
    |`00 08`|读 8 个输入|
    |`B9 9E`|CRC 校验|
    
- **响应帧**：`01 02 01 85 90 3F`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`02`|功能码|
    |`01`|字节数（8 位 = 1 字节）|
    |`85`|数据：二进制`10000101`，输入 1/3/8 为 ON，其余 OFF|
    |`90 3F`|CRC 校验|
    

---

### 3. 功能码 03：读保持寄存器（Read Holding Registers）

**场景**：读取从站 1 号，保持寄存器地址`0x0000`（40001），连续读`2`个寄存器

- **请求帧**：`01 03 00 00 00 02 C4 0B`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`03`|功能码（读保持寄存器）|
    |`00 00`|起始地址 0|
    |`00 02`|读 2 个寄存器（每个 2 字节）|
    |`C4 0B`|CRC 校验|
    
- **响应帧**：`01 03 04 12 34 56 78 7A 3F`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`03`|功能码|
    |`04`|字节数（2 个寄存器 ×2 字节 = 4 字节）|
    |`12 34`|寄存器 0 的值：`0x1234`（4660）|
    |`56 78`|寄存器 1 的值：`0x5678`（22136）|
    |`7A 3F`|CRC 校验|
    

---

### 4. 功能码 04：读输入寄存器（Read Input Registers）

**场景**：读取从站 1 号，输入寄存器地址`0x0000`（30001），读`1`个寄存器（温度传感器值）

- **请求帧**：`01 04 00 00 00 01 31 CA`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`04`|功能码（读输入寄存器）|
    |`00 00`|起始地址 0|
    |`00 01`|读 1 个寄存器|
    |`31 CA`|CRC 校验|
    
- **响应帧**：`01 04 02 01 90 BA 83`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`04`|功能码|
    |`02`|字节数（1 个寄存器 ×2 字节 = 2 字节）|
    |`01 90`|温度值：`0x0190`=400，对应 40.0℃（假设比例 1:10）|
    |`BA 83`|CRC 校验|
    

---

### 5. 功能码 05：写单个线圈（Write Single Coil）

**场景**：给从站 1 号，线圈地址`0x0000`（1 号线圈）写`ON`

- **请求帧**：`01 05 00 00 FF 00 8C 3A`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`05`|功能码（写单个线圈）|
    |`00 00`|线圈地址 0|
    |`FF 00`|写 ON（`00 00`表示 OFF）|
    |`8C 3A`|CRC 校验|
    
- **响应帧（原帧回显）**：`01 05 00 00 FF 00 8C 3A`
    
    > 从站原样返回请求帧，确认操作成功
    

---

### 6. 功能码 06：写单个寄存器（Write Single Register）

**场景**：给从站 1 号，保持寄存器地址`0x0001`（40002）写`0x1234`

- **请求帧**：`01 06 00 01 12 34 E9 7F`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`06`|功能码（写单个寄存器）|
    |`00 01`|寄存器地址 1|
    |`12 34`|写入值`0x1234`|
    |`E9 7F`|CRC 校验|
    
- **响应帧（原帧回显）**：`01 06 00 01 12 34 E9 7F`
    

---

### 7. 功能码 15：写多个线圈（Write Multiple Coils）

**场景**：给从站 1 号，线圈地址`0x0000`，连续写`4`个线圈（1/2 ON，3/4 OFF）

- **请求帧**：`01 0F 00 00 00 04 01 03 7E 8A`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`0F`|功能码（写多线圈，十进制 15）|
    |`00 00`|起始地址 0|
    |`00 04`|写 4 个线圈|
    |`01`|字节数（4 位 = 1 字节）|
    |`03`|数据：`00000011`，线圈 1/2 ON|
    |`7E 8A`|CRC 校验|
    
- **响应帧**：`01 0F 00 00 00 04 54 0B`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`0F`|功能码|
    |`00 00`|起始地址 0|
    |`00 04`|写入数量 4|
    |`54 0B`|CRC 校验|
    

---

### 8. 功能码 16：写多个寄存器（Write Multiple Registers）

**场景**：给从站 1 号，保持寄存器地址`0x0002`，连续写`2`个寄存器（`0x1111`、`0x2222`）

- **请求帧**：`01 10 00 02 00 02 04 11 11 22 22 F7 7B`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`10`|功能码（写多寄存器，十进制 16）|
    |`00 02`|起始地址 2|
    |`00 02`|写 2 个寄存器|
    |`04`|字节数（2×2=4 字节）|
    |`11 11`|寄存器 2 的值|
    |`22 22`|寄存器 3 的值|
    |`F7 7B`|CRC 校验|
    
- **响应帧**：`01 10 00 02 00 02 41 7E`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`10`|功能码|
    |`00 02`|起始地址 2|
    |`00 02`|写入数量 2|
    |`41 7E`|CRC 校验|
    

---

## 二、Modbus TCP 帧示例

TCP 帧格式：`MBAP头(7B) + 功能码(1B) + 数据(NB)`，**无 CRC 校验**（由 TCP 层保证可靠性），默认端口`502`

MBAP 头结构：`事务标识符(2B) + 协议标识符(2B, 固定0x0000) + 长度(2B) + 单元标识符(1B, 从站地址)`

### 1. 功能码 03：读保持寄存器（对应 RTU 示例）

**场景**：读取从站 1 号，保持寄存器地址`0x0000`，读`2`个寄存器

- **请求帧**：`00 01 00 00 00 06 01 03 00 00 00 02`
    
    表格
    
    |字节|含义|
    |---|---|
    |`00 01`|事务标识符（主站自增，用于匹配请求 / 响应）|
    |`00 00`|协议标识符（固定 0，代表 Modbus）|
    |`00 06`|后续数据长度（6 字节：1B 地址 + 1B 功能码 + 4B 数据）|
    |`01`|单元标识符（从站地址 1，对应 RTU 的地址）|
    |`03`|功能码（读保持寄存器）|
    |`00 00`|起始地址 0|
    |`00 02`|读 2 个寄存器|
    
- **响应帧**：`00 01 00 00 00 09 01 03 04 12 34 56 78`
    
    表格
    
    |字节|含义|
    |---|---|
    |`00 01`|事务标识符（与请求一致）|
    |`00 00`|协议标识符|
    |`00 09`|后续长度 9 字节|
    |`01`|单元标识符|
    |`03`|功能码|
    |`04`|字节数（2 个寄存器 ×2 字节 = 4 字节）|
    |`12 34`|寄存器 0 的值|
    |`56 78`|寄存器 1 的值|
    

---

### 2. 功能码 06：写单个寄存器（对应 RTU 示例）

**场景**：给从站 1 号，寄存器地址`0x0001`写`0x1234`

- **请求帧**：`00 02 00 00 00 06 01 06 00 01 12 34`
- **响应帧**：`00 02 00 00 00 06 01 06 00 01 12 34`（原帧回显）

---

## 三、异常响应帧示例

**场景**：主站请求读不存在的寄存器（非法地址）

- **RTU 异常帧**：`01 83 02 91 B0`
    
    表格
    
    |字节|含义|
    |---|---|
    |`01`|从站地址|
    |`83`|异常功能码（原功能码`03` + `0x80`）|
    |`02`|异常码（02 = 非法数据地址）|
    |`91 B0`|CRC 校验|
    
- **TCP 异常帧**：`00 01 00 00 00 03 01 83 02`
    
    表格
    
    |字节|含义|
    |---|---|
    |`00 01`|事务标识符|
    |`00 00`|协议标识符|
    |`00 03`|长度 3 字节|
    |`01`|单元标识符|
    |`83`|异常功能码|
    |`02`|异常码|

| Solution                 |   Applicable    | Justification/remarques (理由 / 备注)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| :----------------------- | :-------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **MODBUS TCP**           | **✅ Oui (适用)**  | **理由**：<br><br>1. **硬件兼容**：PC 配备 Ethernet 接口，直接支持 TCP/IP 协议。<br><br>2. **距离与速率**：Ethernet 传输距离远（远超 RS232/RS485），速率高（100Mbps），适合连接 PC 与远程设备。<br><br>3. **设备特性**：现代工业网关（如 Papouch TQS3 的配套网关）普遍支持 Modbus TCP，可实现以太网直接通信。                                                                                                                                                                                                                                                                                                                                                                                                     |
| **MODBUS RTU sur RS232** | **❌ Non (不适用)** | **理由**：<br><br>1. **物理层限制**：RS232 标准规定最大传输距离约 **15 米**，题目中 “salle 307”（307 教室 / 房间）通常超过此距离，无法覆盖。<br><br>2. **点对点**：RS232 仅支持**一对一**通信，无法同时连接 2 个温度传感器。<br><br>3. **抗干扰**：RS232 是单端信号，抗电磁干扰能力弱，不适合工业现场。                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **MODBUS RTU sur RS485** | **✅ Oui (适用)**  | **理由**：<br><br>1. **总线拓扑**：RS485 支持**主从式总线结构**，可轻松连接 2 个传感器，满足 “deux capteurs” 的需求。<br><br>2. **距离**：传输距离可达 **1200 米**，完全覆盖室内 / 厂区范围。<br><br>3. **抗干扰**：差分信号传输，抗干扰性极强，适合工业环境。<br><br>4. **成本**：接线简单（A/B 线），硬件成本低，是连接传感器最标准的方案。                                                                                                                                                                                                                                                                                                                                                                                             |
| Solution                 |   Applicable    | Justification / remarques                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **MODBUS TCP**           |      ✅ Oui      | 1. Compatibilité matérielle : le PC est équipé d'une interface Ethernet, qui prend directement en charge le protocole TCP/IP.<br><br>2. Distance et débit : l'Ethernet offre une grande portée de transmission (beaucoup supérieure à RS232/RS485) et un débit élevé (100 Mbps), adapté à la connexion entre le PC et des équipements distants.<br><br>3. Caractéristiques des équipements : les passerelles industrielles modernes (comme celles compatibles avec les capteurs Papouch TQS3) prennent généralement en charge Modbus TCP, permettant une communication directe via Ethernet.                               |
| **MODBUS RTU sur RS232** |      ❌ Non      | 1. Limites de la couche physique : la norme RS232 impose une distance de transmission maximale d'environ 15 mètres. Dans le sujet, la « salle 307 » dépasse généralement cette distance, rendant la connexion impossible.<br><br>2. Point à point : RS232 ne prend en charge que la communication un-à-un, il ne permet pas de connecter simultanément 2 capteurs de température.<br><br>3. Immunité aux perturbations : RS232 utilise un signal asymétrique, avec une faible résistance aux perturbations électromagnétiques, il n'est pas adapté à un environnement industriel.                                          |
| **MODBUS RTU sur RS485** |      ✅ Oui      | 1. Topologie bus : RS485 supporte une architecture maître-esclave sur bus, permettant de connecter facilement 2 capteurs et satisfaisant l'exigence de « deux capteurs ».<br><br>2. Distance : la portée de transmission peut atteindre 1200 mètres, couvrant entièrement une zone intérieure ou d'usine.<br><br>3. Immunité aux perturbations : transmission par signal différentiel (symétrique), très résistante aux perturbations électromagnétiques, adaptée à un environnement industriel.<br><br>4. Coût : câblage simple (fils A/B), faible coût matériel, c'est la solution standard pour connecter des capteurs. |
### Traduction française

- **Asymétrique (monofilaire)** : un fil par rapport à la masse → sensible aux perturbations, courte portée.
- **Symétrique (différentiel)** : comparaison entre deux fils → insensible aux perturbations, longue portée.
# 1. Série 串行 vs Parallèle 并行
（区分：**数据是一根一根发，还是多根一起发**）
## Série 串行
- **解释**：数据**一位一位**依次传输，只使用**1 根或 2 根数据线**。
- **优点**：线路少、成本低、距离远、干扰小。
- **缺点**：速度慢一点。
- **例子**：
    
    - **RS232 / RS485**
    - **UART**
    - **USB**
    - **Modbus RTU**
    - 以太网
    
## Parallèle 并行

- **解释**：数据**多位同时传输**（比如 8 位、16 位一起发），需要**多根数据线**。
- **优点**：速度快。
- **缺点**：线多、干扰大、**距离极短**。
- **例子**：
    
    - 老式电脑 **并口 LPT**
    - 早期硬盘线
    - CPU 内部地址总线、数据总线
# 2. Asynchrone 异步 vs Synchrone 同步

（区分：**收发双方有没有共同时钟，靠什么对齐数据**）
## Asynchrone 异步

- **解释**：
    
    收发双方**没有共用时钟线**，只靠**起始位 + 停止位**来同步每帧数据。
    
    双方必须提前约定相同**波特率**。
- **特点**：
    
    - 简单、线路少
    - 不需要时钟线
    - 效率稍
- **例子**：
    
    - **UART**
    - **RS232 / RS485**
    - **Modbus RTU**
    - 串口调试
    
## Synchrone 同步

- **解释**：
    
    收发双方使用**同一时钟信号（CLK）**，时钟同步后连续传输数据，不需要起始位停止位。
    
- **特点**：
    
    - 速度快
    - 效率高
    - 必须有时钟线
    
- **例子**：
    
    - **SPI**
    - **I2C**
    - CAN 高速部分
    - 存储器（Flash / SDRAM）
    

### 串行 Série

一位一位发 → 线少、距离远 → **RS485、UART、Modbus**
### 并行 Parallèle

多位一起发 → 线多、速度快、距离短 → **并口、总线**
### 异步 Asynchrone

无时钟，靠起始位 / 停止位，波特率一致 → **UART、RS232、Modbus RTU**

### 同步 Synchrone

有时钟线 CLK，连续高速传输 → **SPI、I2C**

- **Communication série** : transmission des données bit par bit sur un seul fil.
- **Communication parallèle** : transmission de plusieurs bits simultanément sur plusieurs fils.
- **Communication asynchrone** : pas de signal d’horloge commun ; synchronisation par bit de départ et bit d’arrêt.
- **Communication synchrone** : synchronisation par un signal d’horloge (CLK) commun entre émetteur et récepteur.