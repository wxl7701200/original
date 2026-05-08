# Jeecg-Boot 低代码开发平台 — 详细分析报告

## 一、概述

**项目名称**: Jeecg-Boot  
**版本**: 2.2.1（发布日期：2020-07-13）  
**开源协议**: MIT License  
**作者/组织**: jeecg-boot / Scott  
**仓库地址**: https://github.com/wxl7701200/original.git  

Jeecg-Boot 是一个基于 Spring Boot 的低代码快速开发平台，采用前后端分离架构，提供代码生成器、Online 在线表单设计、工作流、大屏报表等功能，旨在降低企业级应用的开发成本，提高开发效率。

---

## 二、技术栈

### 后端技术

| 领域         | 技术                                      |
| ------------ | ----------------------------------------- |
| 基础框架     | Spring Boot 2.1.3.RELEASE                 |
| 持久层       | MyBatis-Plus 3.3.2                        |
| 安全框架     | Apache Shiro 1.4.0 + JWT 3.7.0（无状态） |
| 数据库连接池 | Alibaba Druid 1.1.17                      |
| 缓存         | Redis（Lettuce 客户端）                   |
| 动态数据源   | Baomidou Dynamic-Datasource 2.5.4         |
| 定时任务     | Quartz（JDBC 持久化）                     |
| 日志         | Logback + SLF4J                           |
| JSON         | Fastjson 1.2.72                           |
| API 文档     | Swagger 2.9.2 + Swagger Bootstrap UI      |
| Excel 工具   | AutoPOI 1.2                               |
| 模板引擎     | Freemarker                                |
| 代码生成     | Jeecg CodeGenerate 1.2.0                   |
| 文件存储     | MinIO 4.0.0 / Aliyun OSS / 本地           |
| 第三方登录   | JustAuth 1.3.2 (GitHub/WeChat/DingTalk)  |
| 工具库       | Lombok, Guava 26.0, Hutool 5.3.8          |
| 短信服务     | Aliyun SMS SDK                            |

### 前端技术

- 代码生成器内置 Vue 模板（`vue/*.vue`）
- NgAlain 管理后台模板集成
- 在线表单设计器（Online Form）

### 数据库支持

| 数据库         | 版本      | SQL 脚本文件                     |
| -------------- | --------- | -------------------------------- |
| MySQL          | 5.7+      | `jeecgboot-mysql-5.7.sql`       |
| Oracle         | 11g       | `jeecgboot-oracle11g.sql`       |
| SQL Server     | 2017      | `jeecgboot-sqlserver2017.sql`   |
| PostgreSQL     | 已配置驱动 | -                               |

### 运行环境

- **语言**: Java 8+
- **构建工具**: Maven
- **IDE**: Eclipse（需 Lombok 插件）或 IntelliJ IDEA
- **缓存**: Redis 5.0+
- **容器化**: Docker + Docker Compose

---

## 三、项目结构

```
original/
├── pom.xml                          # Maven 父 POM，管理依赖版本
├── README.md                        # 项目说明（中文）
├── LICENSE                          # MIT 许可证
├── docker-compose.yml               # Docker 容器编排（MySQL + Redis + App）
│
├── jeecg-boot-base-common/          # 【模块1】公共基础模块
│   └── src/main/java/org/jeecg/common/
│       ├── api/                     # 外部 API 接口定义（WPS等）
│       ├── aspect/annotation/       # 自定义注解（AutoLog/Dict/OnlineAuth/PermissionData）
│       ├── constant/                # 常量定义（缓存/通用/数据库/数据填充规则/WebSocket）
│       ├── es/                      # ElasticSearch 查询工具
│       ├── exception/               # 全局异常处理
│       ├── handler/                 # 数据填充规则处理器接口
│       ├── system/
│       │   ├── api/                 # 系统 API 接口
│       │   ├── base/                # 基础 Controller/Entity/Service 基类
│       │   ├── controller/          # 通用控制器（文件上传/下载等）
│       │   ├── query/               # 查询构造器（QueryGenerator/QueryRuleEnum）
│       │   ├── util/                # JWT 工具类/数据权限工具
│       │   └── vo/                  # 值对象（LoginUser/DictModel/ComboModel等）
│       └── util/                    # 大量工具类
│           ├── dynamic/db/          # 动态数据库操作
│           ├── jsonschema/          # JSON Schema 生成（在线表单用）
│           ├── oss/                 # 阿里云 OSS 工具
│           ├── security/            # 安全加解密工具
│           ├── superSearch/         # 高级查询工具
│           └── ...                  # 其他工具（Redis/MD5/IP/SQL注入过滤等）
│
├── jeecg-boot-module-system/        # 【模块2】系统业务模块（主模块）
│   ├── pom.xml
│   └── src/
│       ├── main/java/org/jeecg/
│       │   ├── JeecgApplication.java          # Spring Boot 入口
│       │   ├── JeecgOneGUI.java               # 代码生成器 GUI
│       │   ├── JeecgOneToMainUtil.java        # 代码生成器工具
│       │   ├── config/                         # 配置类
│       │   │   ├── ShiroConfig.java           # Shiro + JWT 安全配置
│       │   │   ├── Swagger2Config.java        # Swagger API 文档
│       │   │   ├── MybatisPlusConfig.java     # MyBatis-Plus 配置
│       │   │   ├── RedisConfig.java           # Redis 配置
│       │   │   ├── WebSocketConfig.java       # WebSocket 配置
│       │   │   ├── MinioConfig.java           # MinIO 文件存储
│       │   │   └── ...                        # 其他配置（静态资源/跨域/RestTemplate等）
│       │   └── modules/                        # 业务模块目录
│       │       ├── cas/                # CAS 单点登录模块
│       │       ├── demo/               # 示例/演示模块
│       │       │   └── test/           # 演示代码（单表/一对多/ERP风格）
│       │       ├── message/            # 消息中心（系统消息/模板消息）
│       │       ├── monitor/            # 系统监控（Redis监控/HTTP追踪等）
│       │       ├── ngalain/            # NgAlain 管理后台接口
│       │       ├── oss/                # 文件管理（OSS上传管理）
│       │       ├── quartz/             # 定时任务管理
│       │       ├── shiro/              # Shiro Realm + JWT Filter 认证授权
│       │       ├── system/             # 【核心】系统管理模块
│       │       │   ├── controller/     # REST 控制器
│       │       │   ├── entity/         # 数据库实体
│       │       │   ├── mapper/         # MyBatis Mapper 接口 + XML
│       │       │   ├── model/          # 数据模型（树形菜单等）
│       │       │   ├── rule/           # 数据填充规则实现
│       │       │   ├── service/        # 业务服务接口 + 实现
│       │       │   ├── util/           # 系统工具类
│       │       │   └── vo/             # 值对象
│       │       └── test/               # 自定义测试模块
│       └── main/resources/
│           ├── application.yml                # 主配置（激活dev）
│           ├── application-dev.yml            # 开发环境配置
│           ├── application-prod.yml           # 生产环境配置
│           ├── application-test.yml           # 测试环境配置
│           ├── logback-spring.xml             # 日志配置
│           ├── jeecg/
│           │   ├── jeecg_config.properties    # 代码生成器配置
│           │   ├── jeecg_database.properties  # 数据库连接配置
│           │   ├── code-template/             # 代码生成器模板（4种风格）
│           │   │   ├── one/                   # 单表模式
│           │   │   ├── one2/                  # 单表模式（风格2）
│           │   │   ├── onetomany/             # 一对多模式
│           │   │   └── onetomany2/            # 一对多模式（风格2）
│           │   └── code-template-online/       # Online 在线表单代码模板
│           │       ├── default/               # 默认风格
│           │       ├── erp/                   # ERP 风格
│           │       └── inner-table/           # 内嵌子表风格
│           └── static/generic/web/            # 文件预览静态资源
│
├── db/                               # 数据库脚本目录
│   ├── jeecgboot-mysql-5.7.sql       # MySQL 初始化脚本
│   ├── jeecgboot-oracle11g.sql       # Oracle 初始化脚本
│   ├── jeecgboot-sqlserver2017.sql   # SQL Server 初始化脚本
│   ├── Dockerfile                    # MySQL Docker 镜像构建
│   └── 增量SQL/                       # 版本升级 SQL 脚本
│       ├── 2.2.0升级到2.2.1mysql.sql
│       └── 版本升级说明.txt
│
└── D:/                               # 本地文件上传目录痕迹
    └── opt/upFiles/temp/
        └── ICON-西红柿_1598433538617.png
```

**统计数据**:
- 总文件数: 976
- Java 源文件: ~300+
- MyBatis Mapper XML: 30+
- 配置文件: 4 个 profile (dev/prod/test + 主配置)
- 代码生成模板: 100+ (覆盖 4 种代码风格 × 2 类模板)

---

## 四、核心功能模块详解

### 4.1 代码生成器（Code Generator）

Jeecg-Boot 的核心特色功能。支持一键生成完整的 CURD 代码：

- **生成范围**: Controller、Service、ServiceImpl、Mapper、Mapper.xml、Entity、Vue 前端页面
- **模板风格**:
  - `one` — 单表（标准）
  - `one2` — 单表（替代风格）
  - `onetomany` — 一对多（主子表）
  - `onetomany2` — 一对多（替代风格）
- **Online模板**:
  - `default` — 默认在线表单风格
  - `erp` — ERP 风格（列表+表单分离）
  - `inner-table` — 内嵌子表风格
- **配置方式**: `jeecg_config.properties` + `jeecg_database.properties`

### 4.2 系统管理（system 模块）

核心 RBAC 权限管理系统，包含以下实体：

- **用户管理**: SysUser（用户）
- **角色管理**: SysRole（角色）、SysUserRole（用户角色关联）
- **权限管理**: SysPermission（菜单/按钮权限）
- **部门管理**: SysDepart（组织机构）
- **数据权限**: SysPermissionDataRule（数据规则）、SysDepartPermission（部门权限）
- **字典管理**: SysDict + SysDictItem（数据字典）
- **日志管理**: SysLog（操作日志）、SysDataLog（数据变更日志）
- **公告管理**: SysAnnouncement + SysAnnouncementSend（系统公告）
- **分类管理**: SysCategory（分类树）
- **数据源管理**: SysDataSource（动态数据源）
- **职务管理**: SysPosition（岗位）
- **租户管理**: SysTenant（多租户）
- **填充规则**: SysFillRule（编码生成规则）
- **校验规则**: SysCheckRule（唯一性校验）

### 4.3 认证与授权

- **Shiro + JWT 无状态认证**: 不使用 Session，每次请求携带 JWT Token
- **ShiroRealm**: 自定义 Realm 进行认证和授权
- **JwtFilter**: 拦截所有请求校验 Token 有效性
- **Shiro-Redis**: 权限缓存存储在 Redis 中
- **数据权限**: 通过 `@PermissionData` 注解实现行级数据过滤
- **自动日志**: 通过 `@AutoLog` 注解记录操作日志

### 4.4 查询过滤器（QueryGenerator）

强大的动态查询构造器：
- 支持模糊查询（`*关键字*`）
- 支持取非查询（`!关键字`）
- 支持范围查询（`> >= < <=`）
- 支持 IN 查询（逗号分隔）
- 支持多字段模糊匹配
- 支持高级查询模式（QueryRuleEnum）

### 4.5 其他业务模块

| 模块        | 功能                                              |
| ----------- | ------------------------------------------------- |
| message     | 消息模板管理、消息发送（支持 websocket 推送）     |
| quartz      | 定时任务管理（启用/暂停/立即执行/CRON 表达式）     |
| oss         | 文件上传管理（本地/MinIO/Aliyun OSS）             |
| monitor     | Redis 监控、HTTP Trace 性能追踪                   |
| cas         | CAS 单点登录客户端集成                            |
| shiro       | Shiro Realm + JWT Filter 认证授权实现             |
| ngalain     | NgAlain 前端框架后端接口                          |
| test        | 自定义测试业务（TestWxlDemo）                     |

### 4.6 第三方集成

- **JustAuth**: 第三方登录（GitHub OAuth / 企业微信 / 钉钉扫码）
- **阿里云**: OSS 对象存储 + SMS 短信服务
- **MinIO**: 开源对象存储
- **七牛云**: 文件上传（Qiniu SDK）
- **WPS**: 在线文档预览编辑
- **ElasticSearch**: 6.x 集群搜索支持（可配置关闭）
- **CAS**: 单点登录服务
- **Freemarker**: 在线表单模板解析

---

## 五、部署方式

### 5.1 传统方式

```bash
mvn clean package
java -jar jeecg-boot-module-system.jar
```

需要预先安装 MySQL 5.7+ 和 Redis 5.0+。

### 5.2 Docker 方式

使用 `docker-compose.yml` 一键部署三个容器：

| 容器名           | 镜像           | 端口  | 说明             |
| ---------------- | -------------- | ----- | ---------------- |
| jeecg-boot-mysql | jeecg-boot-mysql | 3306 | MySQL 数据库     |
| jeecg-boot-redis | redis:5.0      | 6379  | Redis 缓存       |
| jeecg-boot-system | jeecg-boot-system | 8080 | Spring Boot 应用 |

```bash
# 构建镜像
docker-compose build
# 启动容器组
docker-compose up -d
# 访问 Swagger 文档
http://localhost:8080/jeecg-boot/doc.html
```

---

## 六、配置说明

### 多环境支持

通过 `spring.profiles.active` 切换环境：
- `dev`: 开发环境（端口 8082，MySQL 远程数据库，Redis 本地）
- `test`: 测试环境
- `prod`: 生产环境
- `docker`: Docker 部署环境

### 关键配置项

```yaml
# 文件上传类型: local / minio / alioss
jeecg.uploadType: local

# 文件上传根路径
jeecg.path.upload: D://opt//upFiles

# Shiro 白名单 URL
jeecg.shiro.excludeUrls: /test/jeecgDemo/demo3,...

# Swagger 开关
swagger.enable: true

# 第三方登录
justauth.enabled: true

# 大屏报表
jeecg.jmreport.is_verify_token: false
```

---

## 七、项目特点总结

### 优势

1. **低代码**: 代码生成器大幅减少手写 CURD 代码
2. **功能全面**: 权限管理、定时任务、消息中心、文件管理等功能开箱即用
3. **数据库兼容**: 支持 MySQL、Oracle、SQL Server、PostgreSQL 四种数据库
4. **容器化支持**: 提供完整的 Docker Compose 部署方案
5. **多数据源**: 内置动态数据源切换功能
6. **无状态认证**: JWT + Shiro 无 Session 设计，适合分布式部署
7. **丰富的模板**: 代码生成器提供多种前端风格模板
8. **在线表单**: Online 表单设计器支持动态表单配置

### 技术债务与风险

1. **Spring Boot 版本较旧**: 2.1.3.RELEASE 已 EOL，存在安全漏洞风险
2. **Fastjson 版本**: 1.2.72 存在已知安全漏洞
3. **Java 8**: 未迁移到更高版本
4. **部分配置硬编码**: 数据库密码直接写在 application-dev.yml 中（已暴露）
5. **D: 盘路径**: 文件上传路径使用了 Windows 盘符 `D://opt//upFiles`
6. **Shiro 版本**: 1.4.0 较为陈旧
7. **废弃模块**: 包含 `ngalain` 等前端框架后端接口，前端项目不在本仓库
8. **配置占位符**: 多处密码用 `??` 占位，实际部署需补充

### 适用场景

- 企业内部管理系统快速开发
- 中小型 ERP/OA/CRM 系统
- 需要权限管理、工作流、报表的项目
- 快速原型验证

---

## 八、安全建议

1. **立即修改**: application-dev.yml 中暴露的数据库密码
2. **升级 Fastjson**: 升级到最新安全版本
3. **升级 Spring Boot**: 迁移到 2.5+ 或 3.x
4. **升级 Shiro**: 升级到 1.7+
5. **配置管理**: 敏感配置迁移到环境变量或配置中心
6. **SQL 注入防护**: SqlInjectionUtil 已内置但需确保全面使用

---

*报告生成日期: 2026-05-08*
