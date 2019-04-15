---
title: SpringBoot2-OAuth2.0
date: 2018-12-21 11:56:27
update: 2018-12-21 11:56:27
categories: OAUTH
tags: [security,oauth]
---

### OAuth2.0在SpringBoot2框架中的使用

OAuth2工作原理请参考： [认识OAuth2工作原理，以登录网站时用qq授权登录为例][1]

<!-- more -->

### SpringBoot2.0整合OAuth2.0

![springboot2_oauth2_a](https://volc1612.gitee.io/blog/images/springboot2oauth2/springboot2_oauth2_a.png)

[代码][2]

### 使用

* 认证登录入口类

```java
org.springframework.security.oauth2.provider.endpoint.TokenEndpoint#getAccessToken
```

* 用户密码校验类

```java
org.springframework.security.authentication.dao.AbstractUserDetailsAuthenticationProvider#authenticate
```

#### 使用密码模式认证

![springboot2_oauth2_b](https://volc1612.gitee.io/blog/images/springboot2oauth2/springboot2_oauth2_b.gif)

postman示例说明
```json
{
	"info": {
		"_postman_id": "e063908d-2b53-4b38-a1a3-a9079e0cb380",
		"name": "springboot2.0-oauth2",
		"schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
	},
	"item": [
		{
			"name": "登录认证授权密码模式=>获取token使用jwt或redis存储",
			"request": {
				"auth": {
					"type": "noauth"
				},
				"method": "POST",
				"header": [
					{
						"key": "Authorization",
						"type": "text",
						"value": "Basic c3lzdGVtOnN5c3RlbQ=="
					}
				],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "http://localhost:8888/oauth/token?grant_type=password&username=admin&password=123456&scope=all",
					"protocol": "http",
					"host": [
						"localhost"
					],
					"port": "8888",
					"path": [
						"oauth",
						"token"
					],
					"query": [
						{
							"key": "grant_type",
							"value": "password"
						},
						{
							"key": "username",
							"value": "admin"
						},
						{
							"key": "password",
							"value": "123456"
						},
						{
							"key": "scope",
							"value": "all"
						}
					]
				},
				"description": "grant_type password 使用密码模式认证\n请求头 Authorization 户端id和客户端密码例如\nAuthorization : Basic Base64(client_id:client_secret)\nAuthorization : Basic Base64(\"system:system\")"
			},
			"response": []
		},
		{
			"name": "使用token获取资源服务器",
			"request": {
				"method": "POST",
				"header": [
					{
						"key": "Authorization",
						"type": "text",
						"value": "bearer 06ff04ba-e06d-4827-8037-bd8d44f2a7f3"
					}
				],
				"body": {
					"mode": "raw",
					"raw": ""
				},
				"url": {
					"raw": "http://localhost:8889/api",
					"protocol": "http",
					"host": [
						"localhost"
					],
					"port": "8889",
					"path": [
						"api"
					]
				},
				"description": "grant_type password 使用密码模式认证"
			},
			"response": []
		}
	]
}
```

[1]: https://www.cnblogs.com/flashsun/p/7424071.html
[2]: https://gitee.com/volc1612/springboot2.0-oauth2.git
