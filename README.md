# 苍穹外卖（sky-take-out）

基于 Spring Boot 的餐饮外卖系统后端，包含**管理端**与**用户端（微信小程序）**两套接口。项目源自《苍穹外卖》实战课程，用于练习 Spring Boot 分层开发、MyBatis、Redis 缓存、JWT 鉴权、对象存储与微信登录等。

## 技术栈

| 分类 | 选型 |
| --- | --- |
| 框架 | Spring Boot 2.7.3、Spring MVC、Spring AOP |
| 持久层 | MyBatis 2.2.0、MySQL、Druid 连接池、PageHelper 分页 |
| 缓存 | Redis |
| 鉴权 | JWT（jjwt 0.9.1） |
| 接口文档 | Knife4j 3.0.2（Swagger） |
| 文件存储 | 阿里云 OSS |
| 其他 | Lombok、fastjson、Apache POI、微信支付 APIv3 |
| 构建 | Maven 多模块 |

## 模块结构

```
sky-take-out
├── pom.xml              父工程，统一管理依赖版本
├── sky-common           公共模块：常量、上下文、枚举、异常、JSON、返回结果、工具类、配置属性
├── sky-pojo             数据模型：entity / dto / vo
└── sky-server           业务服务
    └── src/main/java/com/sky
        ├── controller/admin   管理端接口
        ├── controller/user    用户端（小程序）接口
        ├── service / mapper   业务逻辑与持久层
        ├── config             配置类（Swagger、MyBatis、OSS、Redis 等）
        ├── interceptor        JWT 登录拦截器
        ├── aspect             公共字段自动填充
        └── handler            全局异常处理
```

## 已实现功能

- **员工管理**：登录、新增、分页查询、启用/禁用、编辑
- **分类管理**：新增、分页查询、删除、修改、列表查询
- **菜品管理**：新增（含口味）、分页查询、修改、批量删除、起售/停售
- **套餐管理**：新增、分页查询、修改、删除、起售/停售
- **店铺状态**：营业状态设置与查询
- **用户端**：微信登录、分类/菜品/套餐浏览
- **通用能力**：JWT 登录校验、公共字段自动填充、全局异常处理、Redis 缓存

> 购物车、订单、支付等模块仍在开发中。

## 环境要求

| 依赖 | 版本 |
| --- | --- |
| JDK | 8 及以上 |
| Maven | 3.6+ |
| MySQL | 5.7 / 8.0 |
| Redis | 5.0+ |

## 快速开始

1. **准备数据库**：创建 `sky_take_out` 库，导入课程资料中的建表与初始化 SQL。
2. **启动 Redis**（默认 `localhost:6379`）。
3. **准备配置文件**：仓库里只有占位模板，需要复制一份本地配置并填入真实参数：

   ```bash
   cd sky-server/src/main/resources
   cp application-dev.example.yml application-dev.yml
   ```

4. **填写配置**：按 `application-dev.yml` 里的注释填好 MySQL、Redis、阿里云 OSS、微信小程序的参数。
5. **启动项目**：用 IDEA 打开根目录，等待 Maven 依赖下载完成后运行 `com.sky.SkyApplication`（默认端口 `8080`）。
6. **查看接口文档**：<http://localhost:8080/doc.html>

## 配置文件说明

`application-dev.yml` 里包含数据库密码、阿里云 AccessKey、小程序 AppSecret 等敏感信息，因此已加入 `.gitignore`，**不会被提交**。仓库中只保留占位模板 [`application-dev.example.yml`](sky-server/src/main/resources/application-dev.example.yml)，完整内容如下（全部为占位符）：

```yaml
# 苍穹外卖开发环境配置模板
#
# 使用方法：
#   1. 复制本文件为同目录下的 application-dev.yml
#   2. 按下面注释填入自己的真实参数
#   3. application-dev.yml 已被 .gitignore 忽略，不会被提交

sky:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    host: localhost
    port: 3306
    database: sky_take_out
    username: your-mysql-username             # TODO: 数据库用户名
    password: your-mysql-password             # TODO: 数据库密码

  alioss:
    endpoint: oss-cn-hangzhou.aliyuncs.com    # TODO: 按自己 bucket 所在地域修改
    access-key-id: your-access-key-id         # TODO: 阿里云 AccessKey ID（建议用 RAM 子账号）
    access-key-secret: your-access-key-secret # TODO: 阿里云 AccessKey Secret
    bucket-name: your-bucket-name             # TODO: OSS Bucket 名称

  redis:
    host: localhost
    port: 6379
    database: 0

  wechat:
    appid: your-wechat-appid                  # TODO: 微信小程序 AppID
    secret: your-wechat-secret                # TODO: 微信小程序 AppSecret
```

clone 下来后必须先复制模板并填写，否则 `spring.profiles.active: dev` 找不到配置，项目启动会失败：

```bash
cd sky-server/src/main/resources
cp application-dev.example.yml application-dev.yml
```

如果不想把密钥写进文件，也可以把上面的 `access-key-id` / `access-key-secret` 两行改成 `${ALIYUN_OSS_ACCESS_KEY_ID}`、`${ALIYUN_OSS_ACCESS_KEY_SECRET}`，然后在 IDEA 的 Run Configuration → Environment variables 里设置这两个变量。

## 注意事项

- 请勿把真实密钥提交到仓库。GitHub 的 push protection 会直接拒绝包含密钥的推送，即使密钥属于你自己。
- `.gitignore` 已忽略 `target/`、`.idea`、`*.iml`、`*.class`、测试类以及 `application-dev.yml`。
- 本项目为课程练习代码，未做生产级加固，请勿直接用于线上环境。
