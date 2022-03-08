# kerberos 协议

## Kerberos是做什么的？

一种**不用网络传输密码**的**认证**协议。

> Kerberos是希腊神话中地狱之神哈迪斯(Hades)的三头守护犬的名字。这很贴合KDC(key distribution center),请求客户端，请求服务端三方在认证场景下的角色。英魂之刃里面哈迪斯的大招抓人很强的。哈哈哈


## Kerberos Realm

域(Realm)是包含所有请求客户端,KDC,请求服务端主机。


## Kerberos认证特点

客户端要访问服务端，需要完成三步交互：

1. 访问AS(Authentication Server)
2. 访问TGS(Ticket Granting Server)
3. 访问服务端

kerberos认证有如下特点：

- 每次交互都会获得两条信息，一个信息可以解密，一个信息不可解密
- 服务端不会直接访问KDC
- KDC存储所有密钥(secret keys)
- 密码不会经过网络传输


## KDC



## 认证过程

![kerberos-step1to4](images/kerberos_step_1to4.svg)

## Reference List

1. [Explain like I’m 5: Kerberos](https://www.roguelynn.com/words/explain-like-im-5-kerberos/)