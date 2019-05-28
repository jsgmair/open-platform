# GMAIR 数据开放平台

版本 V1.0.0


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

#### 返回结果

请求成功后，结果以JSON格式返回，格式为：

```
{
    "responseCode": "", 
    "data": “”, 
    "description": ""
}
```

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
    "data": “”, 
    "description": ""
}
```

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
    "data": “”, 
    "description": ""
}
```

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
    "data": “”, 
    "description": ""
}
```

### 5. 获取单台设备所处城市空气质量数据

#### 请求URL
`HTTP POST` `https://microservice.gmair.net/openplatform/machine/outdoor`

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
    "data": “”, 
    "description": ""
}
```

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
    "data": “”, 
    "description": ""
}
```

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
    "data": “”, 
    "description": ""
}
```
