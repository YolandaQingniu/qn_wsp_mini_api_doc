# Yolanda WSP Mini API 对接文档

## 请求流程

1. WSP Scale ----> WSP Mini Server

+ 体脂秤设备在WiFi模式下，通过HTTP请求上传测量数据到WSP Mini（中转服务器）；
+ WSP Mini（中转服务器）解析体脂秤设备上传的测量数据，响应处理结果给秤端设备。

2. WSP Mini Server  <---->  Client Server

+ WSP Mini（中转服务器）通过HTTP请求从Client Server（客户服务器）获取该体脂秤设备绑定用户列表；
+ WSP Mini（中转服务器）根据体脂秤设备上传的测量数据以及从Client Server（客户服务器）获取的用户信息生成详细的身体指标报告；
+ WSP Mini（中转服务器）通过HTTP请求发送身体指标报告数据到Client Server（客户服务器），客户服务器再将该指标报告向用户展示。

> P.S. 以上过程是按照流水线方式一次性执行，其中WSP Mini不会存储任何测量数据或用户信息。

## 接口说明

### 一、获取用户列表(客户服务器实现，中转服务器请求)

#### 请求方式

```
GET
```

#### 请求参数

| 字段名 | 类型   | 是否必填 | 介绍 | 额外说明                |
| ------ | ------ | -------- | ---- | ----------------------- |
| mac    | String | Y        | Mac  | mock: 12:34:56:78:9A:BC |

#### 请求参数示例

```
{
    "mac": "12:34:56:78:9A:BC"
}
```

#### 返回参数

| 字段名       | 类型    | 是否必填 | 介绍                 | 额外说明         |
| ------------ | ------- | -------- | -------------------- | ---------------- |
| users        | array   | Y        | 用户                 | 用户列表         |
| - user_index | integer | Y        | 秤端用户坑位         | mock: 1          |
| - gender     | integer | Y        | 性别(男为1 女为0)    | mock: 1          |
| - height     | number  | Y        | 身高(cm)             | mock: 180.5      |
| - birthday   | string  | Y        | 生日                 | mock: 1995-06-30 |
| - weight     | number  | Y        | 用户最近测量体重(kg) | mock: 78.6       |

#### 返回参数示例

```
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

### 二、上传测量数据(客户服务器实现，中转服务器请求)

#### 请求方式

```
POST
```

#### 请求参数

| 字段名               | 类型    | 是否必填 | 介绍                                                         | 额外说明                     |
| -------------------- | ------- | -------- | ------------------------------------------------------------ | ---------------------------- |
| mac                  | string  | Y        | Mac                                                          | mock: 12:34:56:78:9A:BC      |
| measurements         | array   | Y        | 记录列表                                                     |                              |
| - user_index         | integer | Y        | 用户在秤端的坑位                                             | mock: 1                      |
| - timestamp          | integer | Y        | 测量时间戳 (s)                                               | mock: 1582698882             |
| -hmac                | string  | Y        | 签名                                                         | mock: 183476B32E22B26989A... |
| -definite_flag       | boolean | Y        | 归属是否明确(true表示该测量数据是当前user_index上的用户测量的) | mock: true                   |
| - weight             | number  | Y        | 体重 (kg)                                                    | mock: 55.0                   |
| - heart_rate         | integer | Y        | 心率 (BPM)                                                   | mock: 70                     |
| - bmi                | number  | Y        | BMI                                                          | mock：20.1                   |
| - bodyfat            | number  | Y        | 体脂率                                                       | mock：14                     |
| - fat_free_weight    | number  | Y        | 去脂体重                                                     | mock：50.1                   |
| - subfat             | number  | Y        | 皮下脂肪                                                     | mock：12.7                   |
| - visfat             | number  | Y        | 内脏脂肪                                                     | mock：3.46                   |
| - water              | number  | Y        | 水分                                                         | mock：62.2                   |
| - bmr                | integer | Y        | 基础代谢                                                     | mock：1451                   |
| - muscle             | number  | Y        | 骨骼肌率                                                     | mock：55.6                   |
| - sinew              | number  | Y        | 肌肉量                                                       | mock：47.5                   |
| - bone               | number  | Y        | 骨量                                                         | mock：2.51                   |
| - protein            | number  | Y        | 蛋白质                                                       | mock：19.5                   |
| - score              | number  | Y        | 分数                                                         | mock：90.2                   |
| - body_age           | integer | Y        | 体年龄                                                       | mock：20                     |
| - body_shape         | integer | Y        | 体型                                                         | mock：4                      |
| - cardiac_index      | number  | Y        | 心脏指数                                                     | mock：0                      |
| -fatty_liver_risk    | integer | Y        | 脂肪肝风险等级                                               | mock：0                      |
| -fat_weight          | number  | Y        | 脂肪量                                                       | mock：0                      |
| -obesity             | number  | Y        | 肥胖度                                                       | mock：0                      |
| -water_content       | number  | Y        | 含水量                                                       | mock：0                      |
| -protein_quality     | number  | Y        | 蛋白质量                                                     | mock：0                      |
| -inorganic_salt      | integer | Y        | 无机盐状况                                                   | mock：0                      |
| -ideal_visual_weight | number  | Y        | 理想视觉体重                                                 | mock：0                      |
| -standard_weight     | number  | Y        | 标准体重                                                     | mock：0                      |
| -weight_control      | number  | Y        | 体重控制量                                                   | mock：0                      |
| -fat_control         | number  | Y        | 脂肪控制量                                                   | mock：0                      |
| -muscle_control      | number  | Y        | 肌肉控制量                                                   | mock：0                      |
| -muscle_rate         | number  | Y        | 肌肉率                                                       | mock：0                      |

#### 请求参数示例

```json
{
  "mac": "12:34:56:78:9A:BC",
  "measurements": [{
      "user_index": 1,
      "hmac": "70D57038CC39FEA98FBB9A4132B35422752520A027E97ADAE55BD57F9A6EB13A27AD4447D806316227EF96A5979C6F529D15DAF42D7ADD17436739ABE38820CEBF15D58ABAB1F30DAA1EC67362F463A412AAB8BCBA80A594F0B194C197B4C1620BFCF4F7BA5A3743825A7156AEB1F575E22CA4E5BED9237DF838365DFD3E88BA74EDB0AD7F0809DF7DCF4C2F3EA67387C8EE85C25E83044940675FB31BF693239A37E23247F60E651ABD3885BBEAE29CC8EE85C25E83044940675FB31BF69323DA10A0956A1709A0E895FA7740EEB9A1AC7AC7A1F0A2D8365BD261D0FA6C50398A2AF8A4D7003A75E5178E2B52A7502BAE53A619BDE39B1C8098C6A00D4220AF589D389ABEE16D80F5C7E2109E79B95BC7AE52DD296697750F6484B0AB29AD92B5FCABF2E71CDB5F15D89A04A5B01EE13EB4C3F4DB748ACFFBDF4F227478ED69A59A1592642C7420F52D6787F337ADAE9106A14693CEC5AD814D14CEACFC8005BD39289E27ECE343F4B0CF99BCB4DBC950F76FEFB0867ED0932DE5635872AA65CA97902A2898C323861883E1B51E9F5B",
      "timestamp": 1527855059,
      "weight": 55.0,
      "heart_rate": 70,
      "bmi": 20.1,
      "bodyfat": 14,
      "fat_free_weight": 50.1,
      "subfat": 12.7,
      "visfat": 3.4671535888517173,
      "water": 62.2,
      "bmr": 1451,
      "muscle": 55.6,
      "sinew": 47.5,
      "bone": 2.51,
      "protein": 19.5,
      "score": 90.2,
      "body_age": 20,
      "body_shape": 4,
      "heart_rate": 0,
      "cardiac_index": 0,
      "fatty_liver_risk": 2,
      "fat_weight": 8.1,
      "obesity": 1,
      "water_content": 2,
      "protein_quality": 2,
      "inorganic_salt": 2,
      "ideal_visual_weight": 60,
      "standard_weight": 22,
      "weight_control": 2,
      "fat_control": 2,
      "muscle_control": 2,
      "muscle_rate": 1,
      "definite_flag": true
  }]
}
```

#### 返回参数示例

```
"success"
```

> P.S. 实际请求参数根据客户定制指标内容会有所不同。
