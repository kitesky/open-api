# 关于 Open API
实用在线工具箱( https://www.idcd.com )，提供大量在线工具服务，自网站上线以来，稳定运行服务5年时间。该文档为对外提供的API开发文档，在这里您可以找到多种资源API并与之集成，我们提供基于 HTTP协议 的 API，访问 API 将使用 Header Sign 方式 进行身份验证，您必须先注册帐号并创建应用程序客户端，生成 API 密钥信息。

### API 密钥
使用以下步骤填写开发者资料：
1. 注册会员帐号
2. 进入控制台，选择 [API 密钥]。
3. 选择添加新的应用程序客户端，并填写相关信息资料。
4. 创建应用程序客户端成功后请保存client_id和client_secret凭证信息。

### API 目录
我们自有API服务均为免费，当前API速率限制60次/分钟。

- 开发测试API
- IP查询API
- IP批量查询API
- 万年历查询API
- 福利彩票开奖查询API
- 全国行政区域查询API
- 成语查询API
- 成语接龙查询API
- 域名whois信息查询API
- 域名ssl证书信息查询API
- 网址安全检测API
- 国家法定节日放假安排查询API
- 历史上的今天查询AP
- 全国高校查询API(不含军校)
- 外汇牌价查询
- 更多 API 待更新...

更多文档资料请移步 https://www.idcd.com/docs/open-api

### 公共参数
公共参数是用于标识用户和接口签名的参数，如非必要，在每个接口单独的文档中不再对这些参数进行说明，但每次请求均需要携带这些参数，才能正常发起请求。

##### 使用签名方法的公共参数

使用签名方法时，公共参数需要统一放到 HTTP Header 请求头部中，如下表所示：

| 参数名称  |  类型 | 必选  | 描述 |
| ------------ | ------------ | ------------ | ------------ |
| ClientID  | String  | 是  | 客户端ID。  |
| SignatureMethod  | String  | 是  | 签名加密方式，当前仅支持 HmacSHA256 方式。 |
| Nonce  | String  | 是 | 随机字符串，方式重放攻击。  |
| Timestamp | Int | 是 | 当前 UNIX 时间戳，可记录发起 API 请求的时间。例如:1716085926，如果与当前时间相差过大，会引起签名过期错误。 |
| Signature  | String  | 是 | 请求签名，用来验证此次请求的合法性，需要用户根据实际的输入参数计算得出。具体计算方法参见签名方式文档。  |

##### 请求结构示例

    curl --location --request GET 'https://www.idcd.com/api/test' \
    --header 'ClientID: df77f2de-2924-4499-adda-1c4cc243625a' \
    --header 'Nonce: v0j38hHHUEqFwoh0Gc8Rbfi737xtIpLL' \
    --header 'Timestamp: 1716085926' \
    --header 'Signature: 624eef34b453ac74ae5109a921d20959cc066a674346277a29c92230ae7f5991' \
    --header 'SignatureMethod: HmacSHA256' \

##### 签名方法

1、拼接待签名字符串，该数据由 clientID， nonce，timestamp，signatureMethod 按照固定顺序拼接得出。

    plainText = clientID + nonce + timestamp + signatureMethod


2、计算签名

计算派生签名密钥，伪代码如下：

    hash_hmac('sha256', plainText, clientSecret))

### 接口请求

1. 输入示例
```
curl --location --request POST 'https://www.idcd.com/api/test' \
--header 'ClientID: df77f2de-2924-4499-adda-1c4cc243625a' \
--header 'Nonce: v0j38hHHUEqFwoh0Gc8Rbfi737xtIpLL' \
--header 'Timestamp: 1716085926' \
--header 'Signature: 5b1230f42bad2ffd5ad09890a8ebb47c02d74668be0cf7bb54a0f6a14996117b' \
--header 'SignatureMethod: HmacSHA256' \
--header 'Content-Type: application/json' \
--data-raw '{
    "input_text": "test content"
}'
```

2. 输出示例

```json
{
    "status": true,
    "message": "Success",
    "request_id": "d5d21011-5e37-435b-abca-296ce9d51cc9",
    "data": {
        "output_text": "test content"
    }
}
```


### 响应结果

##### 正确返回结果
若调用成功，其可能的返回如下为：

```json
{
    "status": true,
    "message": "Success",
    "data": {
        "content": "test"
    },
    "request_id": "91a31c6d-4f2f-49db-97e4-bbe9826017da"
}
```

request_id 是固定的字段，用于一个 API 请求的唯一标识，无论请求成功与失败，只要 API 处理了，则必定会返回。如果 API 出现异常，可以联系 在线客服 或 提交工单，并提供该 ID 来解决问题。

status 是固定字段，用于标注 API 处理结果，响应为布尔值， true-成功；false-失败。

message 用于描述 API 处理结果的说明信息。

data API 返回的结果对象。

###### 错误返回结果

```json
{
    "status": false,
    "code": "signature_error",
    "message": "签名数据无效",
    "request_id": "91a31c6d-4f2f-49db-97e4-bbe9826017da"
}
```
