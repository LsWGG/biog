---
title: Django美多
date: 2020-03-02 16:48:08
tags: web
---

# Django美多商城

## 一、登录注册

- 创建用户（注册用户）的方法

  `user = User.objects.create_user(username, email, password, **extra_fields)`

- 用户认证（用户登录）的方法

```python
from django.contrib.auth import authenticate
user = authenticate(username=username, password=password, **kwargs)
```

<!--more-->

- 判断用户是否登录：
  - `user.is_authenticated()`
  - `LoginRequiredMixin`

- 处理密码的方法：
  
  - 设置密码：`set_password(raw_password)`
  - 校验密码：`check_password(raw_password)`
  
- pipeline操作redis数据库（解决请求过多，网络延迟问题）

  1. 创建redis管道

     `pl = redis_conn.pipeline()`

  2. 将redis请求添加到管道

     `pl.setex()`

  3. 执行请求

     `pl.execute()`

### QQ登录

OAuth：OAuth（开放授权）是一个开放标准，允许用户授权用第三方网站访问登录

1. 获取Authorization Code
2. 通过Authorization Code获取Access Token
3. 通过Access Token获取openid

## 二、用户中心

- 模板数据传递到Vue对象
  
- 通过Javascript做中间桥梁
  
- 验证邮箱序列化加密

  ```python
  from itsdangerous import TimedJSONWebSignatureSerializer as TJWSSerializer
  # 序列化
  serializer = TJWSSerializer('SECRET_KEY', expires_in=3600)
  data = {'user_id': self.id, 'email': self.email}
  token = serializer.dumps(data).decode()
  # 反序列化
  serializer = TJWSSerializer('SECRET_KEY', expires_in=3600)
  # 过期会报错
  data = serializer.loads(token)
  ```


- 省市区三级联动

  cache和Django-redis的区别

  - Django-redis：缓存数据需要分库、区分类型，取出来都是bytes类型
  - cache：对分库类型没有要求，存什么取什么
  
- fastDFS组成


    - Client：客户端，对接fastDFS
    - Tracker：调度
    - Storage：物理存储，时刻向Tracker上传空闲的计算机IP

  美多商城用Storage中挂载Nginx做下载
