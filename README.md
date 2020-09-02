你好！
很冒昧用这样的方式来和你沟通，如有打扰请忽略我的提交哈。我是光年实验室（gnlab.com）的HR，在招Golang开发工程师，我们是一个技术型团队，技术氛围非常好。全职和兼职都可以，不过最好是全职，工作地点杭州。
我们公司是做流量增长的，Golang负责开发SAAS平台的应用，我们做的很多应用是全新的，工作非常有挑战也很有意思，是国内很多大厂的顾问。
如果有兴趣的话加我微信：13515810775  ，也可以访问 https://gnlab.com/，联系客服转发给HR。
# Check Firewalled

Mini HTTP service that takes a JSON with server information and check
 if it's accessible from the application.

In our use-case, we want the tested server to **not** be accessible from this application.

An unaddressable server is considered a success.

## Env

| Key        | Requirement           | Explanation                                                                                   |
|------------|-----------------------|-----------------------------------------------------------------------------------------------|
| SHARED_KEY | mandatory              | Secret shared between main app and this one. (Needs to be sent in the header *Authorization*) |
| PORT       | optional (default 80)  | Port used for the application                                                                 |
| CHECK_SMTP | optional (default true)| Should the application check for an SMTP server when the server is available.                                                                |
| SMTP_CN | optional (default hostname)| Common Name to use for client certificate when doing a STARTTLS                                                                |
| SMTP_FROM | optional (default local@hostname)| Email to use to do the MAIL FROM smtp command                                                               |

## Data
```json
{
  "server": "example.com",
  "port": 25,
  "mx": false,
  "test_email": "test@example.com"
}
```


| Key    | Explanation                                                                    |
|--------|--------------------------------------------------------------------------------|
| server | Server to check                                                                |
| port   | Port to use to attempt the connection                                          |
| mx     | Instead of resolving the IP, resolve the MX of the server first then check IPs |
| test_email     | Used as RCPT TO when doing SMTP checks |
## Result
### Success
```json
{
    "request": {
        "server": "example.com",
        "port": 25,
        "mx": false
    },
    "success": true,
    "details": [
        {
            "server": "example.com",
            "ip": "0.0.0.0",
            "result": true,
            "smtp_check_msg": ""
        },
        {
            "server": "example.com",
            "ip": "0.0.0.1",
            "result": true,
            "smtp_check_msg": "[TLS VersionTLS12] Stop at RCPT TO"
        },
    ]
}
```


### Failure

#### All servers
```json
{
    "request": {
        "server": "example.com",
        "port": 25,
        "mx": false
    },
    "success": false,
    "details": [
        {
            "server": "example.com",
            "ip": "0.0.0.0",
            "result": false,
            "smtp_check_msg": "[TLS VersionTLS12] Can start a SMTP Transaction"
        },
        {
            "server": "example.com",
            "ip": "0.0.0.1",
            "result": false,
            "smtp_check_msg": "[TLS VersionTLS12] Can start a SMTP Transaction"
        },
    ]
}
```

#### One servers
```json
{
    "request": {
        "server": "example.com",
        "port": 25,
        "mx": false
    },
    "success": false,
    "details": [
        {
            "server": "example.com",
            "ip": "0.0.0.0",
            "result": false,
            "smtp_check_msg": "[TLS VersionTLS12] Can start a SMTP Transaction"
        },
        {
            "server": "example.com",
            "ip": "0.0.0.1",
            "result": true,
            "smtp_check_msg": ""
        },
    ]
}
```

#### Unknown host
```json
{
  "request": {
    "server": "mailcatcher",
    "port": 25,
    "mx": false,
    "testEmail": "test@test.com"
  },
  "success": false,
  "details": [
    {}
  ],
  "errorMessage": "lookup mailcatcher on 127.0.0.1: no such host"
}
```
