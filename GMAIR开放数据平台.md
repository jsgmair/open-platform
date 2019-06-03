# GMAIR 数据开放平台

版本 V1.0.1


## 介绍

数据开放平台向第三方平台开放果麦新风设备的部分数据以及操作权限，包括GM320，GM420，GM520。具体包括：

+ 查询设备运行状态数据
+ 查询设备所处城市空气数据
+ 控制设备运行/关闭
+ 调节设备运行风量

这些接口通过HTTP GET/POST请求来实现。

## 接口描述

当第三方平台提交相关资料后给本公司后，经过资质审核，果麦将向该平台方提供appid用于访问以上功能。

### 1. 设备订阅
当第三方平台获取到appid后，为实现对于设备的数据查询和控制，需要首先订阅设备。

#### 请求URL
`HTTP POST` `https://microservice.gmair.net/openplatform/machine/subscribe`

#### 请求参数

|参数名称|类型|说明|
|:----------|:----------|:----------|
|appid|字符串|必需|
|qrcode|字符串|必需|

qrcode为设备的唯一识别码，在每一台果麦新风设备上均有标识。

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": "", 
    "description": ""
}
```

其中，responseCode有三种可能的取值，分别为：`RESPONSE_OK`, `RESPONSE_NULL`, `RESPONSE_ERROR`。

##### (1) RESPONSE_OK：订阅成功

```
{
    "responseCode": "RESPONSE_OK",
    "data": null,
    "description": "订阅成功"
}
```

##### (2) RESPONSE_NULL：重复订阅
```
{
    "responseCode": "RESPONSE_NULL",
    "data": null,
    "description": "已订阅该机器"
}
```

##### (3) RESPONSE_ERROR：订阅失败

"description":

- 未提供appid和qrcode："请提供正确的appid和qrcode"
- appid不合法："请提供正确的appid"
- qrcode不合法："请提供正确的qrcode"
- 查询与该设备的订阅关系失败："查询与该设备的订阅关系失败"
- 插入数据失败："订阅失败"

### 2. 设备取关

若某一台设备不再需要平台获取数据时，应通过该接口将设备从订阅历史中移除。

#### 请求URL
`HTTP POST` `https://microservice.gmair.net/openplatform/machine/unsubscribe`

#### 请求参数

|参数名称|类型|说明|
|:----------|:----------|:----------|
|appid|字符串|必需|
|qrcode|字符串|必需|

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": "", 
    "description": ""
}
```

其中，responseCode有两种可能的取值，分别为：`RESPONSE_OK`,  `RESPONSE_ERROR`。

##### (1) RESPONSE_OK：取关成功

```
{
    "responseCode": "RESPONSE_OK",
    "data": null,
    "description": "取消订阅设备成功"
}
```

##### (2) RESPONSE_ERROR：取关失败

"description":

- 未提供appid和qrcode："请提供正确的appid和qrcode"
- appid不合法："请提供正确的appid"
- 查询与该设备的订阅关系失败："查询与该设备的订阅关系失败"
- 未订阅该设备："未订阅该设备"
- 删除数据失败："取消订阅设备失败"

### 3. 获取订阅设备列表

平台可根据appid查询当前所有订阅的设备二维码列表。

#### 请求URL
`HTTP GET` `https://microservice.gmair.net/openplatform/machine/subscriptions`

#### 请求参数

|参数名称|类型|说明|
|:----------|:----------|:----------|
|appid|字符串|必需|

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": "", 
    "description": ""
}
```

其中，responseCode有两种可能的取值，分别为：`RESPONSE_OK`,  `RESPONSE_ERROR`。

##### (1) RESPONSE_OK：获取成功

```
{
    "responseCode": "RESPONSE_OK",
    "data":[JSON（参见下表）,...](JSONArray),
    "description": null
}
```

| 参数名称 | 类型   | 说明               |
| -------- | ------ | ------------------ |
| qrcode   | 字符串 | 已订阅设备的qrcode |
| createAt | 长整型 | 订阅该设备的时间戳 |

##### (2) RESPONSE_ERROR：获取失败

"description":

- 未提供appid或appid不存在："请提供正确的appid"
- 未查询到设备订阅信息："未查询到设备订阅信息"

### 4. 获取单台设备运行状态

#### 请求URL
`HTTP GET` `https://microservice.gmair.net/openplatform/machine/indoor`

#### 请求参数

|参数名称|类型|说明|
|:----------|:----------|:----------|
|appid|字符串|必需|
|qrcode|字符串|必需|

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": "", 
    "description": ""
}
```

其中，responseCode有两种可能的取值，分别为：`RESPONSE_OK`,  `RESPONSE_ERROR`。

##### (1) RESPONSE_OK：获取成功

```
{
    "responseCode": "RESPONSE_OK",
    "data": [JSON（参见下表）,...](JSONArray),
    "description": null
}
```

| 参数名称    | 类型   | 说明                                            |
| ----------- | ------ | :---------------------------------------------- |
| volume      | 整型   | 设备运行风速                                    |
| mode        | JSON | 设备运行模式(action_name, action_operator)          |
| heat        | 字符串 | GM320: 开/关, GM420: 关/500W/1000W |
| light       | 整型   | 设备运行亮度                                    |
| qrcode      | 字符串 | 该设备qrcode                                    |
| pm2_5       | 浮点型 | 室内PM2.5数值                                   |
| co2         | 浮点型 | 室内二氧化碳数值                                |
| temperature | 浮点型 | 室内温度                                        |
| humidity    | 浮点型 | 室内湿度                                        |
| lock        | 整型   | 童锁，仅GM420，GM520。0-关，1-开                |
| power       | 整型   | 0-关，1-开                                      |

##### (2) RESPONSE_ERROR：获取失败

"description":

- 未提供appid或qrcode："请提供正确的appid和qrcode"
- appid未订阅该设备："请确保该appid有效，且已订阅该设备二维码"
- 查询设备状态信息失败："获取设备状态信息失败"
- ...(详见具体的"description")

### 5. 获取单台设备所处城市空气质量数据

#### 请求URL
`HTTP GET` `https://microservice.gmair.net/openplatform/machine/outdoor`

#### 请求参数

|参数名称|类型|说明|
|:----------|:----------|:----------|
|appid|字符串|必需|
|qrcode|字符串|必需|

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": "", 
    "description": ""
}
```

其中，responseCode有两种可能的取值，分别为：`RESPONSE_OK`,  `RESPONSE_ERROR`。

##### (1) RESPONSE_OK：获取成功

```
{
    "responseCode": "RESPONSE_OK",
    "data": [JSON（参见下表）,...](JSONArray),
    "description": null
}
```

| 参数名称       | 类型   | 说明                     |
| -------------- | ------ | ------------------------ |
| o3             | 浮点型 | 设备所在城市二氧化碳数值 |
| pm10           | 浮点型 | 设备所在城市PM10数值     |
| cityId         | 字符串 | 设备所在城市city_id      |
| co             | 浮点型 | 设备所在城市一氧化碳数值 |
| createAt       | 长整型 | 城市数据更新时间时间戳   |
| no2            | 浮点型 | 设备所在城市二氧化氮数值 |
| cityName       | 字符串 | 设备所在城市名称         |
| aqiLevel       | 字符串 | 设备所在城市aqi等级      |
| pm2_5          | 浮点型 | 设备所在城市PM2.5数值    |
| so2            | 浮点型 | 设备所在城市二氧化硫数值 |
| aqi            | 浮点型 | 设备所在城市aqi数值      |
| primePollution | 字符串 | 设备所在城市主要污染物   |

##### (2) RESPONSE_ERROR：获取失败

"description":

- 未提供appid或qrcode："请提供正确的appid和qrcode"
- appid未订阅该设备："请确保该appid有效，且已订阅该设备二维码"
- 获取设备所处位置失败："获取设备所处位置失败"
- 查询该城市空气数据失败："查询该城市空气数据失败"
- 未查询到该设备城市的空气数据："未查询到该设备城市的空气数据"

### 6. 开启/关闭单台设备

#### 请求URL
`HTTP POST` `https://microservice.gmair.net/openplatform/machine/power/{value}`

#### 请求参数

|参数名称|类型|说明|
|:----------|:----------|:----------|
|value|字符串|必需，开启(on)，关闭(off)|
|appid|字符串|必需|
|qrcode|字符串|必需|

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": "", 
    "description": ""
}
```

其中，responseCode有两种可能的取值，分别为：`RESPONSE_OK`,  `RESPONSE_ERROR`。

##### (1) RESPONSE_OK：操作成功

```
{
    "responseCode": "RESPONSE_OK",
    "data": null,
    "description": "操作成功"
}
```

##### (2) RESPONSE_ERROR：获取失败

"description":

- 未提供appid或qrcode："请提供正确的appid和qrcode"
- appid未订阅该设备："请确保该appid有效，且已订阅该设备二维码"
- 开机失败："开机失败"
- 关机失败："关机失败"
- 输入value值不为on或off："无法操作，请输入合法值"

### 7. 调节单台设备运行风量

#### 请求URL
`HTTP POST` `https://microservice.gmair.net/openplatform/machine/speed`

#### 请求参数

|参数名称|类型|说明|
|:----------|:----------|:----------|
|appid|字符串|必需|
|qrcode|字符串|必需|
|speed|整型|必需|

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": "", 
    "description": ""
}
```

其中，responseCode有两种可能的取值，分别为：`RESPONSE_OK`,  `RESPONSE_ERROR`。

##### (1) RESPONSE_OK：操作成功

```
{
    "responseCode": "RESPONSE_OK",
    "data": null,
    "description": "已调节风量"
}
```

##### (2) RESPONSE_ERROR：获取失败

"description":

- 未提供appid或qrcode："请提供正确的appid和qrcode"
- appid未订阅该设备："请确保该appid有效，且已订阅该设备二维码"
- ...(详见具体的"description")

### 8. 获取单台设备可运行风量

#### 请求URL

`HTTP GET` `https://microservice.gmair.net/openplatform/machine/speed/range`

#### 请求参数

| 参数名称 | 类型   | 说明 |
| :------- | :----- | :--- |
| appid    | 字符串 | 必需 |
| qrcode   | 字符串 | 必需 |

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": "", 
    "description": ""
}
```

其中，responseCode有两种可能的取值，分别为：`RESPONSE_OK`,  `RESPONSE_ERROR`。

##### (1) RESPONSE_OK：操作成功

```
{
    "responseCode": "RESPONSE_OK",
    "data": [JSON（参见下表）,...](JSONArray),
    "description": null
}
```

| 参数名称  | 类型   | 说明               |
| --------- | ------ | ------------------ |
| qrcode    | 字符串 | 该设备qrcode       |
| minVolume | 整型   | 该设备可调最小风量 |
| maxVolume | 整型   | 该设备可调最大风量 |

##### (2) RESPONSE_NULL：查询结果为空

```
{
    "responseCode": "RESPONSE_NULL",
    "data": null,
    "description": "未查询到风量可调范围"
}
```

##### (3) RESPONSE_ERROR：获取失败

"description":

- 未提供appid或qrcode："请提供正确的appid和qrcode"
- appid未订阅该设备："请确保该appid有效，且已订阅该设备二维码"
- 未查询到该设备的model_id："未查询到该设备的model_id"
- 查询该设备model_id失败："查询该设备model_id失败"
- 查询风量可调范围失败："查询风量可调范围失败"

### 9. 转换单台设备运行模式

#### 请求URL
`HTTP POST` `https://microservice.gmair.net/openplatform/machine/mode`

#### 请求参数

|参数名称|类型|说明|
|:----------|:----------|:----------|
|appid|字符串|必需|
|qrcode|字符串|必需|
|mode|字符串|必需()|

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": "", 
    "description": ""
}
```

其中，responseCode有两种可能的取值，分别为：`RESPONSE_OK`,  `RESPONSE_ERROR`。

##### (1) RESPONSE_OK：操作成功

```
{
    "responseCode": "RESPONSE_OK",
    "data": null,
    "description": "已转换成...模式"
}
```

##### (2) RESPONSE_ERROR：获取失败

"description":

- 未提供appid或qrcode："请提供正确的appid和qrcode"
- appid未订阅该设备："请确保该appid有效，且已订阅该设备二维码"
- ...(详见具体的"description")
