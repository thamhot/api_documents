# 接入指南

1. 登录商户后台获取商户号和密钥
2. 联系运营客服加ip白名单
2. 为了保证双方的资金安全，接入创建代付订单接口完成之后，若接口返回异常，切记不要擅自驳回或者将订单切至其他通道，因上游可能出现异常，在驳回之前，先调用查询代付订单接口查询订单状态，当订单状态为失败时再进行驳回。违规操作造成的损失，自行承担。



# 接口网关

1. 联系运营客服获取网关地址



# 接口签名

MD5签名算法

1、将发送或接收到的所有参数按照参数名ASCII码从小到大排序（a-z），sign和空值不参与签名！

2、将排序后的参数拼接成URL键值对的格式，例如 a=b&c=d&e=f，参数值不要进行url编码。

3、再将拼接好的字符串与商户密钥KEY进行MD5加密得出sign签名参数，sign = md5 ( a=b&c=d&e=f&key=KEY ) ，md5结果为小写。

```php
php示例:
		/**
     * 生成签名
     * @param array $arr_param
     * @param string $secret_key
     * @return string
     */
    public static function getSign(array $arr_param, $secret_key)
    {
        ksort($arr_param); //根据键值升序排列
        $str_sign = '';
        foreach ($arr_param as $key => $value) {
            if (!empty($value)) {
                $str_sign .= $key . '=' . $value . '&';
            }
        }
        $str_sign = $str_sign . 'key=' . $secret_key;
        return md5($str_sign);
    }
```

##### 



# API列表

## 1、创建代付订单

### 简要描述
· 创建代付订单

### 请求URL
· /api/order/behalf

### 请求方式
· POST

### 参数

| **参数名**   | **必选** | **类型** | **说明**                                                     |
| ------------ | -------- | -------- | ------------------------------------------------------------ |
| shop_no      | 是       | string   | 商家唯一标识订单号                                           |
| shop_id      | 是       | int      | 平台注册的商家id                                             |
| notify_url   | 是       | string   | 付款完成通知商家的回调url                                    |
| pay_type     | 是       | int      | 请咨询业务                                                   |
| amount       | 是       | float    | 代付总金额                                                   |
| account_no   | 是       | string   | 代付支付账号                                                 |
| account_name | 是       | string   | 代付支付账号姓名                                             |
| account_code | 是       | string   | 代付支付银行代码<br>注：如果是转到银行账户，代码不能为空，如果是转到支付宝，值为Alipay |
| remark       | 是       | string   | 代付备注                                                     |
| sign         | 是       | string   | 接口签名验证,算法详见上文                                    |

### 注意事项
如果此接口返回异常，切记不要擅自驳回。因上游可能出现异常，在驳回之前，先调用查询代付订单接口查询订单状态，当订单状态为失败时再进行驳回。违规操作造成的损失，自行承担。

### 备注
##### 请求示例

```
shop_no:2021092012042835812487933
notify_url:http://shop.com/order/notify
shop_id:20188
pay_type:1
amount:149.00
account_no:1388066525
account_name:牛毅
remark:还钱
sign:d8b055836e2281190bf4dae4c0acba35
```

##### 返回示例

```
{"code":1,"order_no":"73955776456155136","shop_id":"40175","shop_no":"73955776456155136","amount":"149.00","account_no":"92012042835812487933","create_at":"1689708339","sign":"6a76edf532240af4b84e1ad46a00aac0"}
```

##### 返回参数说明

| **参数名** | **类型** | **说明**                          |
| ---------- | -------- | --------------------------------- |
| code       | int      | 返回成功标示,1为成功               |
| order_no   | string   | 平台代付订单号                            |
| shop_id    | string   | 用户唯一标识id                    |
| shop_no    | string   | 商户订单号                        |
| amount      | string   | 订单金额                          |
| account_no       | string   | 代付支付账号        |
| create_at       | string   | 创建时间        |
| sign        | string   | 接口签名验证,算法详见上文 |




## 2、代付订单回调

##### 简要描述

· 代付成功异步通知商户接口

##### 请求URL

· 商户传入的notify_url

##### 请求方式

· POST 商户异步通知地址

##### 参数

| **参数名**    | **必选** | **类型** | **说明**                                                 |
| ------------- | -------- | -------- | -------------------------------------------------------- |
| order_no      | 是       | string   | 订单号                                            |
| shop_no       | 是       | string   | 商家唯一标识订单号                                       |
| amount         | 是       | float    | 支付定案总金额                                         |
| create_time | 是       | int      | 订单创建时间                                             |
| pay_time      | 是       | int      | 订单支付时间                                             |
| status        | 是       | int      | 支付状态 2已支付 3支付失败 |
| reason        | 是       | string      | 支付失败的时候，会有失败原因 |
| shop_id | 是       | int      | 平台注册的商家id                                         |
| sign          | 是       | string   | 接口签名验证,算法详见上文                               |

##### 请求示例

```
{"order_no":"6859783957299200","shop_no":"12jl12j3l12l3j213jl12328","amount":"1000.00","create_time":1616163499,"pay_time":1616163578,"status":2,"shop_id":40281,"sign":"b41b43b7bf97d4d57c6b36a6248bee98"}
```

##### 返回示例

```
success
```
##### 返回参数说明

##### 备注

回调header Content-Type:application/x-www-data-urlencoded




## 3、查询代付订单

##### 简要描述

· 查询代付订单

##### 请求URL

· /api/order/querybehalf

##### 请求方式

· GET

##### 参数

| **参数名** | **必选** | **类型** | **说明**                  |
| ---------- | -------- | -------- | ------------------------- |
| order_no   | 否       | string   | 平台返回的订单号          |
| shop_no    | 否       | string   | 商家唯一标识订单号        |
| shop_id    | 是       | int      | 平台注册的商家id          |
| sign       | 是       | string   | 接口签名验证,算法详见上文 |

order_no和shop_no不能全为空,必须传一个

##### 请求示例

```
order_no:73955776456155136
shop_id:40175
sign:1cfac1add3c3177d1f84a846d60a3600
```

##### 返回示例

```
{"code":1,"order_no":"73955776456155136","shop_id":40175,"shop_no":"2021092012042835812487933","amount":"149.000","pay_type":"1","create_time":1632160431,"trade_no":"73955776456155136","status":2,"sign":"6f73bc51aa12dd113dd9ef83ab19a6c0"}
```

##### 返回参数说明

| **参数名**  | **类型** | **说明**                                                     |
| ----------- | -------- | ------------------------------------------------------------ |
| 参数名      | 类型     | 说明                                                         |
| code        | int      | 返回成功标示,1为成功,-1为失败                                |
| msg         | string   | 接口返回提示文案                                             |
| order_no    | string   | 平台订单号                                                   |
| shop_id     | string   | 用户唯一标识id                                               |
| amount      | string   | 订单金额                                                     |
| pay_type    | Int      | 订单代付类型                                                 |
| create_time | int      | 订单创建时间戳                                               |
| trade_no    | string   | 商户订单号                                                   |
| status      | int      | 支付状态状态 <br/>0  未支付 <br/>1  等待下发支付 <br/>2  已支付<br/>3  支付失败 |
| sign        | string   | 验证签名,算法详见上文                                        |



## 4、查询商家余额

##### 简要描述

· 查询商家余额

##### 请求URL

· /api/getMerchantBalance

##### 请求方式

· GET

##### 参数

| **参数名** | **必选** | **类型** | **说明**                  |
| ---------- | -------- | -------- | ------------------------- |
| t          | 是       | string   | 当前时间戳，10位       |
| shop_id    | 是       | int      | 平台注册的商家id          |
| sign       | 是       | string   | 接口签名验证,算法详见上文 |

##### 请求示例

```
t:1693759296
shop_id:40175
sign:1a9b617bfcbc5f1ec45207575344492a
```

##### 返回示例

```
{"shop_id":"40175","balance":"3643.430","date":"2023-09-04 02:01:58"}
```

##### 返回参数说明

| **参数名** | **类型** | **说明**   |
| ---------- | -------- | ---------- |
| shop_id    | string   | 商户id     |
| balance    | string   | 订单金额   |
| date       | string   | 查询的时间 |







