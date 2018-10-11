---
title: sentry日志管理
date: 2018-10-11 03:17:17
tags: [sentry]
categories: 日志管理
copyright: true
---

> Sentry是一个实时事件日志记录和汇集的平台。其专注于错误监控以及提取一切事后处理所需信息;支持几乎所有主流开发语言和平台, 并提供了现代化UI;是一款精致的Django应用，在刚接触使用时，可以先在其官方提供的sentry平台提供的UI查看日志，也可以在自己的服务器上搭建sentry。

# 官网上的sentry环境

步骤：
+ Sign up for an account

> 即注册自己的账户，官方介绍地址：https://sentry.io/signup/ ，可以用自己的github帐号登录

+ Install your SDK

> 即安装自己的sdk，sdk支持很多语言，包括node、php、C#、Rust、Python、java等等，官方介绍地址：https://docs.sentry.io/quickstart/?platform=javascript#pick-a-client-integration

+ Configure it

> 即需要配置一个DNS，它看起来很像一个标准的URL，但是它实际上只是哨兵SDK所需要的配置的一个表示。它由几个部分组成，包括协议、公钥、服务器地址和项目标识符。通过github账户登录sentry后，创建并进入到项目，找到右上角Settings =>Client Keys(DNS)，找到DNS类似为https://17a242f43fde4b9e8898e22d27300fd1:596820d3913a4cb890c3cb6f45ef3fe3@sentry.io/1297905。官方介绍地址：https://docs.sentry.io/quickstart/?platform=javascript#configure-the-sdk

+ 创建token掉取接口

> 通过创建token，使用sentry提供的API操作在平台上的功能（比如项目创建、查询等）。

<!-- more -->

# 搭建本地的sentry环境

+ 按张docker和docker-compose

```
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```
> 参考地址：https://docs.docker.com/compose/compose-file/#reference-and-guidelines
注释：由于之前安装的docker-compose版本是1.0 导致后面生成秘钥的时候报错，目前为止sentry可以支持的compose版本有"2.0", "2.1", "3.0"，下载下来后需要根据自身安装的docker-compose版本修改docker-compose.yml文件中的version: '2.3'的值

+ 从git上拉取源配置文件

```
$ git clone https://github.com/getsentry/onpremise.git
```

+ 然后进入onpremise 并执行命令

```
$ mkdir -p data/{sentry,postgres}
```

+ 执行命令创建项目的key，最后一行的值便是要获取的key

```
$ docker-compose run --rm web config generate-secret-key
Digest: sha256:ac3946556bca5f9699d8578f615dd411a38fe0d45ab166ddef5f83b1af6a822e
Status: Downloaded newer image for sentry:9.0-onbuild
# Executing 4 build triggers
 ---> Running in adaf5f1f7215
Removing intermediate container adaf5f1f7215
 ---> Running in a259f702fc57
Removing intermediate container a259f702fc57
 ---> Running in cbdf91773490
Removing intermediate container cbdf91773490
 ---> 5dc481ceddef

Successfully built 5dc481ceddef
Successfully tagged onpremise_web:latest
WARNING: Image for service web was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
*2i+01h4f2+(g%z&#yh+81jz7-h@)%j_x-y-r19v(bj@ehrda=
```

+ 把key赋值到docker-compose.yml 文件中的SENTRY_SECRET_KEY对应的值中。（端口号设置也在该配置文件中，默认9000）

+ 创建数据库、帐号、密码(不知道是不是自己电脑配置低，这步有点费时间还特别的卡，最后会让你属于一个邮箱+密码)

```
$ docker-compose run --rm web upgrade
...
Running migrations for nodestore:
 - Migrating forwards to 0001_initial.
 > nodestore:0001_initial
 - Loading initial data for nodestore.
Installed 0 object(s) from 0 fixture(s)
```

+  开启sentry服务

```
$ docker-compose up -d
...
Successfully built 5dc481ceddef
Successfully tagged onpremise_worker:latest
WARNING: Image for service worker was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
onpremise_redis_1_5052f121b1ef is up-to-date
onpremise_postgres_1_33982ab2b77b is up-to-date
onpremise_memcached_1_1c252ac88346 is up-to-date
onpremise_smtp_1_b74290ac5ca9 is up-to-date
Creating onpremise_web_1_945b0b215653    ... done
Creating onpremise_cron_1_65fb8f27fe15   ... done
Creating onpremise_worker_1_7b8dcfd2c027 ... done
```
+ 浏览器打开 http://localhost:9000 并完成部分内容填写

> 这部分填写一个url和邮箱，url填写自己的域名，也可以是http://ip:port，创建之后在项目里的DNS的部分内容会默认是他，当然使用的时候也可以自己改。

+ DNS获取

> 进入到要设置sentry日志的项目，点击设置 Settings =》Client Keys(DNS) =>https://59529140225f46f7b96d61621fea8d01:100cd995309f45c28f6741909bf99cb0@sentry.zehuihuang.com/2
其中 sentry.zehuihuang.com是在第一次登录时设置的，用的时候该URL的sentry.zehuihuang.com也可以自定义修改，比如https://59529140225f46f7b96d61621fea8d01:100cd995309f45c28f6741909bf99cb0@localhost:9000/2
https也可以改成http
格式：'{PROTOCOL}://{PUBLIC_KEY}:{SECRET_KEY}@{HOST}/{PATH}{PROJECT_ID}'
解释：PROTOCOL 通常会是 http 或者 https，HOST 为 Sentry 服务的主机名和端口，PATH 通常为空。 每个项目都会生成一个 DSN。

+ Java项目应用(pom文件依赖)

```
<dependency>
			<groupId>com.getsentry.raven</groupId>
			<artifactId>raven-logback</artifactId>
			<version>8.0.2</version>
</dependency>
```

+ 使用java的logback日志框架

> 首先使用springboot的方式在resources下创建logback.xml文件如下：

```
<configuration>
    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex"
                    converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx"
                    converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>
    <!-- 彩色日志格式 -->
    <property name="CONSOLE_LOG_PATTERN"
              value="${CONSOLE_LOG_PATTERN:-%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(--){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>/>
    <appender name="Console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!--<pattern> %d{YYYY-MM-dd HH:mm:ss.SSS} [%-5level] -&#45;&#45; %logger{36} [%thread] \t- %msg%n%nopex</pattern>-->
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
        </encoder>
    </appender>

    <appender name="Sentry" class="com.getsentry.raven.logback.SentryAppender">
        <!--每个项目生成不通的key-->
        <dsn>http://59529140225f46f7b96d61621fea8d01:100cd995309f45c28f6741909bf99cb0@localhost:9000/2</dsn>
        <!-- 设置拦截的最低级别为warn 警告-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>WARN</level>
        </filter>
    </appender>
    <logger name="logback.SentryAppenderIT" level="INFO">
        <appender-ref ref="Sentry"/>
    </logger>

    <root level="INFO">
        <appender-ref ref="Console"/>
        <appender-ref ref="Sentry"/>
    </root>
</configuration>
```

+ Create New Token 调用sentry接口

> 找到用户的API keys
点击按钮 Create New Token  创建token 并且选择该token在其项目和人员上的权限

+ 调用项目列表API接口调式(可以通过Postman测试调用)

> GET请求地址：
http://localhost:9000/api/0/projects/ 
参数token：92022dd4d77d4d37b5a69fdce291eb9b97659b7a34674040a7d39f826efe2c99
返回值如下：

```
[
    {
        "status": "active",
        "features": [
            "data-forwarding",
            "rate-limits"
        ],
        "color": "#bf403f",
        "isInternal": false,
        "isPublic": false,
        "dateCreated": "2018-10-10T09:24:12.931Z",
        "id": "3",
        "slug": "b26c9a8c0376",
        "name": "川商地产",
        "hasAccess": true,
        "isBookmarked": true,
        "platform": "java",
        "firstEvent": null,
        "avatar": {
            "avatarUuid": null,
            "avatarType": "letter_avatar"
        },
        "isMember": true,
        "organization": {
            "status": {
                "id": "active",
                "name": "active"
            },
            "require2FA": false,
            "avatar": {
                "avatarUuid": null,
                "avatarType": "letter_avatar"
            },
            "name": "Sentry",
            "dateCreated": "2018-10-10T03:30:30.513Z",
            "id": "1",
            "isEarlyAdopter": false,
            "slug": "sentry"
        }
    },
    {
        "status": "active",
        "features": [
            "data-forwarding",
            "rate-limits",
            "releases"
        ],
        "color": "#943fbf",
        "isInternal": false,
        "isPublic": false,
        "dateCreated": "2018-10-10T05:42:20.614Z",
        "id": "2",
        "slug": "java",
        "name": "Java",
        "hasAccess": true,
        "isBookmarked": false,
        "platform": "java",
        "firstEvent": "2018-10-10T06:43:07Z",
        "avatar": {
            "avatarUuid": null,
            "avatarType": "letter_avatar"
        },
        "isMember": true,
        "organization": {
            "status": {
                "id": "active",
                "name": "active"
            },
            "require2FA": false,
            "avatar": {
                "avatarUuid": null,
                "avatarType": "letter_avatar"
            },
            "name": "Sentry",
            "dateCreated": "2018-10-10T03:30:30.513Z",
            "id": "1",
            "isEarlyAdopter": false,
            "slug": "sentry"
        }
    },
    {
        "status": "active",
        "features": [
            "data-forwarding",
            "rate-limits"
        ],
        "color": "#bf873f",
        "isInternal": true,
        "isPublic": false,
        "dateCreated": "2018-10-10T03:30:30.601Z",
        "id": "1",
        "slug": "internal",
        "name": "Internal",
        "hasAccess": true,
        "isBookmarked": false,
        "platform": null,
        "firstEvent": "2018-10-10T09:45:46Z",
        "avatar": {
            "avatarUuid": null,
            "avatarType": "letter_avatar"
        },
        "isMember": true,
        "organization": {
            "status": {
                "id": "active",
                "name": "active"
            },
            "require2FA": false,
            "avatar": {
                "avatarUuid": null,
                "avatarType": "letter_avatar"
            },
            "name": "Sentry",
            "dateCreated": "2018-10-10T03:30:30.513Z",
            "id": "1",
            "isEarlyAdopter": false,
            "slug": "sentry"
        }
    }
]
```

可以看出有三个项目
其他具体的接口参考官网地址：https://docs.sentry.io/api/

+ 补充

> 1、官方和本地环境都发现了一个问题，把日志刷新成自动的不好使，必须刷新浏览器才行，不知道是不是源码的bug;2、本地的sentry环境无法验证邮箱

+ 参考地址

https://docs.sentry.io/server/installation/
https://docs.sentry.io/
