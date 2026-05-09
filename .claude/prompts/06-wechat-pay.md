# Prompt: 微信支付 V3 集成

## 使用阶段
项目第 16-18 天

## Prompt

> 基于微信支付 JSAPI V3 API 实现预支付下单和支付回调：
> 
> **预支付下单：**
> - POST /api/user/order/payment
> - 构建 WxPayV3Request（outTradeNo, description, notifyUrl, amount.total, payer.openid）
> - 调用 WechatPayHttpClient 发送 POST 到 /v3/pay/transactions/jsapi
> - 返回 prepay_id，拼装小程序调起支付参数：
>   { appId, timeStamp, nonceStr, package: "prepay_id=xxx", signType: "RSA", paySign }
> - 返回 OrderPaymentVO
> 
> **支付回调：**
> - POST /api/notify/pay-success
> - 验签：从 HTTP 头取 Wechatpay-* 五个字段，用微信平台证书验签
> - 解密：AES-GCM 解密 Resource.ciphertext
> - 幂等处理：根据 outTradeNo 查询订单，状态已是 PAID 则直接返回 200
> - 更新订单状态为 PAID
> 
> **约束：**
> - 微信支付商户号 / APIv3 密钥 / 证书序列号从配置文件读取
> - 回调接口不需要登录拦截
> - 签名和加密使用官方 SDK（wechatpay-apache-httpclient 0.4.8）

## 关键 Review 点
- 幂等处理：回调可能重复到达
- 验签失败需要记录完整日志（用于排查）
- notifyUrl 使用内网穿透暴露的 HTTPS 地址
- 签名算法 WECHATPAY2-SHA256-RSA2048
