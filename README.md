# Yolanda WSP Mini API 对接文档

## 请求流程

### **1. `Scale Device` ----> `WSP Mini Server`**

+ 体脂秤设备（Scale Device）在WiFi模式下，通过HTTP请求上传测量数据到中转服务器（WSP Mini Server）；
+ 中转服务器（WSP Mini Server）解析体脂秤设备（Scale Device）上传的测量数据，并响应处理结果给体脂秤设备（Scale Device）——测量成功或失败。

### **2. `WSP Mini Server`  ---->  `Client Server`**

+ 中转服务器（WSP Mini Server）通过HTTP请求从客户服务器（Client Server）获取该体脂秤设备（Scale Device）绑定的用户列表；
+ 中转服务器（WSP Mini Server）根据体脂秤设备（Scale Device）上传的测量数据以及从客户服务器（Client Server）获取的用户信息生成详细的身体指标报告；
+ 中转服务器（WSP Mini Server）通过HTTP请求发送身体指标报告数据到客户服务器（Client Server），客户服务器（Client Server）再将该指标报告向用户展示。

> P.S. 以上过程是按照流水线方式一次性执行，其中WSP Mini Server不会存储任何测量数据或用户信息。

## 接口说明

### 一、获取秤设备用户列表

#### 请求端

```
WSP Mini Server
```

#### 响应端

```
Client Server
```

#### 请求方式

```
GET
```

#### 请求参数说明

| 名称 | 类型   | 是否必须 | 描述          | Mock值            |
| ---- | ------ | -------- | ------------- | ----------------- |
| mac  | string | 是       | 秤设备MAC地址 | 12:34:56:78:9A:BC |

#### 请求地址

```
Client Server提供URL，如：https://www.client-server-domain.com/users
```

#### 请求参数示例

```
https://www.client-server-domain.com/users?mac=A8:48:FA:35:14:56
```

#### 返回参数说明

| 名称         | 类型    | 是否必须 | 描述                     | Mock值     |
| ------------ | ------- | -------- | ------------------------ | ---------- |
| users        | array   | 是       | 秤设备绑定用户列表       | []         |
| - user_index | integer | 是       | 秤设备端用户占位(1-8)    | 1          |
| - gender     | integer | 是       | 性别(男为1 女为0)        | 1          |
| - height     | double  | 是       | 身高(cm)                 | 180.5      |
| - birthday   | string  | 是       | 生日(YYYY-mm-dd)         | 1995-06-30 |
| - weight     | double  | 是       | 用户最近一次测量体重(kg) | 78.6       |

#### 返回参数示例

```json
{
    "users": [{
        "user_index": 1,
        "gender": 1,
        "height": 180.5,
        "birthday": "1995-06-30",
        "weight": 78.6
    },{
        "user_index": 2,
        "gender": 0,
        "height": 168.5,
        "birthday": "1994-02-28",
        "weight": 54.0
    },{
        "user_index": 8,
        "gender": 1,
        "height": 174.3,
        "birthday": "1999-12-08",
        "weight": 67.0
    }]
}
```

### 二、接收测量指标报告

#### 请求端

```
WSP Mini Server
```

#### 响应端

```
Client Server
```

#### 请求方式

```
POST
```

#### 请求参数说明

| 名称                 | 类型    | 是否必须 | 描述                                                         | Mock值                                           |
| -------------------- | ------- | -------- | ------------------------------------------------------------ | ------------------------------------------------ |
| mac                  | string  | 是       | 秤设备MAC地址                                                | 12:34:56:78:9A:BC                                |
| measurements         | array   | 是       | 测量指标报告列表                                             | []                                               |
| - user_index         | integer | 是       | 秤设备端用户占位(1-8)                                        | 8                                                |
| - timestamp          | integer | 是       | 测量时间戳 (UTC)                                             | 1642063478                                       |
| -hmac                | string  | 是       | 签名密文                                                     | E8B40DEE51140E284782FDFC52E04962D0F71F09BCADD... |
| -definite_flag       | boolean | 是       | 数据归属是否明确(true表示该测量数据是当前user_index上的用户测量的，否则表示不一定，需要用户认领) | true                                             |
| - weight             | double  | 是       | 体重 (kg)                                                    | 64.8                                             |
| - heart_rate         | integer | 是       | 心率 (次/分)                                                 | 82                                               |
| - bmi                | double  | 是       | 身体质量指数                                                 | 22.4                                             |
| - bodyfat            | double  | 是       | 体脂率(%)                                                    | 19.3                                             |
| - fat_free_weight    | double  | 是       | 去脂体重(kg)                                                 | 52.3                                             |
| - subfat             | double  | 是       | 皮下脂肪率(%)                                                | 17.4                                             |
| - visfat             | double  | 是       | 内脏脂肪等级                                                 | 5                                                |
| - water              | double  | 是       | 身体水分率(%)                                                | 58.3                                             |
| - bmr                | integer | 是       | 基础代谢(kcal)                                               | 1499                                             |
| - muscle             | double  | 是       | 骨骼肌率(%)                                                  | 52.1                                             |
| - sinew              | double  | 是       | 肌肉重量(kg)                                                 | 49.7                                             |
| - bone               | double  | 是       | 骨量(kg)                                                     | 2.62                                             |
| - protein            | double  | 是       | 蛋白质占比(%)                                                | 18.4                                             |
| - score              | double  | 是       | 健康分数                                                     | 96.0                                             |
| - bodyage            | integer | 是       | 体年龄                                                       | 18                                               |
| - body_shape         | integer | 是       | 体型                                                         | 4                                                |
| - cardiac_index      | double  | 是       | 心脏指数                                                     | 3.3                                              |
| -fatty_liver_risk    | integer | 否       | 脂肪肝风险等级                                               | 0                                                |
| -fat_weight          | double  | 否       | 脂肪重量(kg)                                                 | 12.5                                             |
| -obesity             | double  | 否       | 肥胖度                                                       | 2.86                                             |
| -water_content       | double  | 否       | 身体水分含量(kg)                                             | 37.8                                             |
| -protein_quality     | double  | 否       | 蛋白质重量(kg)                                               | 11.9                                             |
| -inorganic_salt      | integer | 否       | 无机盐状况                                                   | 1                                                |
| -ideal_visual_weight | double  | 否       | 理想视觉体重(kg)                                             | 63.0                                             |
| -standard_weight     | double  | 否       | 标准体重(kg)                                                 | 63.0                                             |
| -weight_control      | double  | 否       | 体重控制量(kg)                                               | -2.78                                            |
| -fat_control         | double  | 否       | 脂肪控制量(kg)                                               | -2.78                                            |
| -muscle_control      | double  | 否       | 肌肉控制量(kg)                                               | 0.92                                             |
| -muscle_rate         | double  | 否       | 肌肉率(%)                                                    | 76.69                                            |

#### 请求地址

```
Client Server提供URL，如：https://www.client-server-domain.com/measurements
```

#### 请求参数示例

```json
{
  "mac": "12:34:56:78:9A:BC",
  "measurements": [{
      "user_index": 8,
      "hmac": "E8B40DEE51140E284782FDFC52E04962D0F71F09BCADD84CE44B6A3013BBB06AA6A9A7DD1970DD6A94D81F7CCE2C7D4A5338D5F8D64923C32E459BE7D33D190D15F7FC15BA30203CE10C654EAD8FD409BF5308AED7E1A1C9788156DE25B1D7007FABBB123AD3D6B058E84327BF97953C87558F57C804B7069B8B34B88889E48267560A3B803C4652C9966B9DE5D9BBD2",
      "timestamp": 1642063478,
      "weight": 64.8,
      "heart_rate": 82,
      "bmi": 20.1,
      "bodyfat": 14,
      "fat_free_weight": 50.1,
      "subfat": 12.7,
      "visfat": 3.4,
      "water": 62.2,
      "bmr": 1451,
      "muscle": 55.6,
      "sinew": 47.5,
      "bone": 2.51,
      "protein": 19.5,
      "score": 96.0,
      "bodyage": 18,
      "body_shape": 4,
      "cardiac_index": 3.3,
      "fatty_liver_risk": 0,
      "fat_weight": 12.5,
      "obesity": 2.86,
      "water_content": 37.8,
      "protein_quality": 11.9,
      "inorganic_salt": 1,
      "ideal_visual_weight": 63.0,
      "standard_weight": 63.0,
      "weight_control": -2.78,
      "fat_control": -2.78,
      "muscle_control": 0.92,
      "muscle_rate": 76.69,
      "definite_flag": true
  }]
}
```

#### 返回参数说明

| 名称    | 类型   | 是否必须 | 描述             | Mock值  |
| ------- | ------ | -------- | ---------------- | ------- |
| success | string | 是       | 测量报告接收成功 | success |

#### 返回参数示例

```text
success
```

## 鉴权方式

#### 令牌

WSP Mini Server向Client Server的接口每一次发起HTTP请求时，**请求头**中会携带JWT令牌作为鉴权凭证。

示例：

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsImFsZ29yaXRobSI6IkhTMjU2IiwidHlwIjoiSldUIn0.eyJpc3MiOiJMTzhkZnlLSXdVdGRva0E4NVJaRk5UTzhCeUF2aDI5QiIsImlhdCI6MTYzMDk5NzMzNSwidXNlcl9pZCI6NDY1fQ.kbskDz_gBbswKaiNWSiEhvozHdrzm7vzlKWEGQn7SXk
```

#### 加密方式：

JWT作为Token载体，加密算法为HS256，payload说明：

- appid：Client Server提供的小程序APPID
- iss：签发人
- iat：签发时间戳

#### 密钥：

```
Client Server提供secret
```

