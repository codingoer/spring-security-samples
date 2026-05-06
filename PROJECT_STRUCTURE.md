# spring-security-samples 项目结构说明

本文档按“职责分类”来整理仓库结构，重点说明：

- 哪些内容属于文档
- 哪些内容属于构建和 CI 基础设施
- 哪些内容是真正的 demo/sample 代码
- 哪些内容是辅助脚本或本地环境文件
- `reactive` 和 `servlet` 两大示例分区下的主要模块分布

## 1. 仓库整体定位

这是一个 **Spring Security 官方示例集合仓库**，使用 **Gradle 多模块工程** 组织大量样例。  
核心目标是按技术栈和功能主题展示 Spring Security 的典型用法，比如：

- Servlet / Reactive 两大编程模型
- Spring Boot / 纯 Java 配置 / XML 配置
- OAuth2、SAML2、JWT、CAS、LDAP、Method Security、Session Management 等专题

## 2. 按职责分类看顶层结构

### 2.1 真正的 demo/sample 代码

这部分是仓库最核心的内容，也就是你真正会进入阅读、运行、调试的示例代码区。

#### `reactive/`

响应式技术栈样例目录，聚焦 Spring WebFlux、WebFlux.fn、RSocket 等响应式场景。

- `rsocket/`：RSocket + Spring Security 示例
- `webflux/`：基于 Spring WebFlux 的响应式示例
- `webflux-fn/`：基于函数式路由的 WebFlux 示例

#### `servlet/`

Servlet 技术栈样例目录，聚焦 Spring MVC / Servlet Filter 链相关安全场景。

- `java-configuration/`：非 Boot 的 Java 配置风格示例
- `spring-boot/`：基于 Spring Boot 的 Servlet 示例
- `xml/`：基于 XML 配置的传统示例

### 2.2 文档相关

这部分主要帮助理解仓库和具体 sample 的用途、运行方式、主题分类。

#### `README.adoc`

仓库总览文档，列出全部样例分类与跳转入口，是理解整个仓库最重要的入口文档。

#### 各 sample 目录下的 `README.adoc`

不是根目录文件，但很重要。它们通常说明：

- 这个 demo 想演示什么
- 如何启动
- 需要哪些前置依赖
- 访问路径或验证方式

如果需要把某个 sample 的 `README.adoc` 转成 HTML 方便本地浏览，可以在对应目录执行：

```bash
npx --yes @asciidoctor/cli README.adoc
```

如果要批量处理某一类 sample，也可以在仓库根目录结合脚本循环执行这个命令。

### 2.3 构建、依赖管理与 CI 基础设施

这部分不是 demo 本身，而是支撑整个仓库能统一构建、统一校验、统一发版/验证的基础设施。

#### `build.gradle`

根 Gradle 构建脚本。负责定义全局插件、格式化/检查规则、公共编译参数，以及聚合测试任务。

#### `settings.gradle`

Gradle 多模块入口文件。负责：

- 配置插件仓库
- 定义构建插件解析策略
- 注册整个仓库下所有 sample 模块

#### `gradle.properties`

Gradle 全局属性文件。当前主要定义：

- 项目版本
- Spring Security 版本
- Gradle JVM 参数和缓存开关

#### `versions.properties`

辅助版本属性文件，当前主要记录 Spring Boot 版本信息。

#### `gradle/`

Gradle 基础配置目录，主要包含：

- `libs.versions.toml`：版本目录，统一管理插件和 BOM 版本
- `wrapper/`：Gradle Wrapper 文件

#### `gradlew`

Unix/macOS 下的 Gradle Wrapper 启动脚本，用于统一执行构建命令。

#### `gradlew.bat`

Windows 下的 Gradle Wrapper 启动脚本。

#### `config/`

项目级代码规范配置目录。当前主要是：

- `checkstyle/`：Checkstyle 规则、抑制规则、文件头模板

#### `.github/`

GitHub 平台配置目录，主要包含：

- `workflows/`：CI、自动触发、自动 forward-merge 等 GitHub Actions 工作流
- `dependabot.yml`：Dependabot 依赖升级策略

#### `spring-security-ci.gradle`

CI 专用 Gradle 配置。用于强制整个仓库依赖指定的 Spring Security 版本，并从本地仓库优先解析对应构件。

#### `spring-security-commercial-ci.gradle`

商业/企业仓库场景下的 CI Gradle 配置。与上一个文件类似，但增加了企业制品仓库地址和认证配置。

### 2.4 脚本和维护工具

这部分主要用于批量维护整个样例仓库，不是业务代码，也不是单个 demo 的核心逻辑。

#### `find-latest-minor-version.sh`

辅助脚本。根据当前版本号和 Maven 仓库地址，尝试探测下一个可用的 minor/patch 版本目录。

#### `includes.sh`

辅助生成 `settings.gradle` 中 `include` 列表的脚本，本质上是根据子模块里的 `build.gradle*` 自动推导模块路径。

#### `sync-boot-version.sh`

批量同步所有子模块中 Spring Boot Gradle 插件版本的脚本。

#### `sync-dependency-management.sh`

批量同步所有子模块中 `io.spring.dependency-management` 插件版本的脚本。

#### `sync-framework-bom.sh`

批量同步各模块使用的 Spring Framework BOM 版本的脚本。

#### `sync-gradle-properties.sh`

把根目录 `gradle.properties` 复制到各子模块目录，便于每个 sample 独立执行构建。

#### `sync-gradle.sh`

把根目录的 `gradlew`、`gradlew.bat` 和 `gradle/wrapper` 同步到各子模块，保证 sample 可以单独运行。

#### `sync-libs-symlinks.sh`

为各子模块的 `gradle/libs.versions.toml` 创建指向根目录版本目录文件的符号链接，统一版本来源。

#### `sync-nebula-integtest.sh`

批量同步 `nebula.integtest` 插件版本的脚本。

#### `sync-security-bom.sh`

批量同步各模块使用的 Spring Security BOM 版本的脚本。

### 2.5 本地开发环境 / 工具生成内容

这部分通常不是仓库真正关心的“业务内容”，更多是本地开发或工具运行产生的目录。

#### `.git/`

Git 元数据目录，保存分支、提交、索引、对象库等版本控制信息。

#### `.gradle/`

本地 Gradle 缓存和构建状态目录。通常是机器本地生成内容，不属于业务源码。

#### `.idea/`

IntelliJ IDEA 工程配置目录，保存 IDE 级别设置、检查规则、索引配置等。

### 2.6 本地运行依赖环境

这部分不是 demo 主体代码，但为某些 demo 提供配套运行环境。

#### `docker/`

本地辅助运行环境目录。当前主要提供：

- `cas/`：CAS 相关容器配置
- `cas/docker-compose.yml`：启动本地 CAS 服务，供 CAS 示例调试使用

### 2.7 根目录文件速查表

如果从“分类”角度快速看根目录，可以简单理解为：

- 文档：`README.adoc`
- 真正 demo：`reactive/`、`servlet/`
- 构建入口：`build.gradle`、`settings.gradle`、`gradle.properties`、`versions.properties`、`gradle/`、`gradlew`、`gradlew.bat`
- CI / 规则：`.github/`、`config/`、`spring-security-ci.gradle`、`spring-security-commercial-ci.gradle`
- 维护脚本：`find-latest-minor-version.sh`、`includes.sh`、`sync-*.sh`
- 本地环境：`docker/`
- 本地工具/缓存：`.git/`、`.gradle/`、`.idea/`
- 版本控制辅助：`.gitignore`

#### `.gitignore`

Git 忽略规则，过滤 IDE 文件、Gradle 缓存、构建产物、日志等不应提交的文件。

## 3. 代码目录结构

这两个目录承载了仓库中的绝大多数业务示例：

- `reactive/`：响应式安全示例
- `servlet/`：Servlet 安全示例

另外，几乎每个具体 sample 目录内部都会重复出现一套相似结构：

- `build.gradle` 或 `build.gradle.kts`：该示例自己的构建脚本
- `settings.gradle`：使该 sample 可以单独作为 Gradle 项目运行
- `gradle.properties`：该 sample 的构建属性
- `gradlew` / `gradlew.bat` / `gradle/`：独立运行所需的 Wrapper 文件
- `src/`：源码、测试、资源文件
- 部分目录带有 `README.adoc`：介绍该样例的启动方式和背景

## 4. `reactive/` 目录结构

### `reactive/rsocket/`

- `hello-security/`：RSocket 场景下的基础安全示例

### `reactive/webflux-fn/`

- `hello/`：函数式路由的基础 WebFlux 示例
- `hello-security/`：函数式路由 + Spring Security 的基础示例

### `reactive/webflux/`

#### `reactive/webflux/java/`

- `hello/`：最基础的响应式应用示例
- `hello-security/`：最基础的 WebFlux 安全示例
- `hello-security-explicit/`：显式配置方式的 WebFlux 安全示例
- `method/`：响应式方法安全示例

##### `authentication/`

- `one-time-token/magic-link/`：一次性令牌 / 魔法链接登录示例
- `username-password/form/`：表单用户名密码认证示例
- `x509/`：基于 X.509 客户端证书的认证示例

##### `oauth2/`

- `login/`：OAuth2 Login 示例
- `resource-server/`：OAuth2 Resource Server 示例
- `webclient/`：受 Spring Security 保护的 WebClient 示例

##### `session-management/`

- `maximum-sessions/`：最大并发会话控制示例

#### `reactive/webflux/kotlin/`

- `hello-security/`：Kotlin 版 WebFlux 安全基础示例

## 5. `servlet/` 目录结构

### `servlet/java-configuration/`

传统 Servlet/MVC 体系下，使用 Java 配置而不是 Spring Boot 自动配置的示例集合。

- `aspectj/`：AspectJ 相关安全集成示例
- `data/`：Spring Data 与 Security 集成示例
- `hello-mvc-security/`：Spring MVC + Security 基础示例
- `hello-security/`：基础 Servlet 安全示例
- `hello-security-explicit/`：显式安全配置示例
- `max-sessions/`：最大会话数限制示例

#### `authentication/`

- `preauth/`：预认证（Pre-Authentication）示例
- `remember-me/`：Remember-Me 示例
- `x509/`：X.509 认证示例

##### `username-password/`

- `form/`：表单登录示例
- `in-memory/`：基于内存用户的认证示例
- `jdbc/`：基于 JDBC 的认证示例
- `ldap/`：基于 LDAP 的认证示例

#### `saml2/`

- `login/`：SAML 2.0 登录示例

### `servlet/spring-boot/`

基于 Spring Boot 的 Servlet 样例集合，是仓库中覆盖面最广的一组示例。

#### `servlet/spring-boot/java/`

- `acl/`：ACL（访问控制列表）示例
- `aot/data/`：AOT 场景下的数据安全示例
- `data/`：Spring Data 集成示例
- `hello/`：基础 Boot Web 示例
- `hello-security/`：基础 Boot 安全示例
- `hello-security-explicit/`：显式配置版 Boot 安全示例
- `ldap/`：Spring Boot + LDAP 示例
- `observability/`：安全相关可观测性示例

##### `authentication/`

- `one-time-token/magic-link/`：魔法链接登录示例

###### `username-password/`

- `compromised-password-checker/`：弱口令/泄露密码检查示例
- `mfa/`：多因素认证示例
- `user-details-service/custom-user/`：自定义 `UserDetails` 示例

##### `cas/`

- `login/`：CAS 登录示例

##### `jwt/`

- `login/`：JWT 登录示例

##### `oauth2/`

- `authorization-server/`：OAuth2 授权服务器示例
- `login/`：OAuth2 登录示例
- `restclient/`：RestClient 场景下的 OAuth2 客户端示例
- `webclient/`：WebClient 场景下的 OAuth2 客户端示例

###### `resource-server/`

- `hello-security/`：基础资源服务器示例
- `jwe/`：JWE（加密 JWT）示例
- `multi-tenancy/`：多租户资源服务器示例
- `opaque/`：Opaque Token 资源服务器示例
- `restclient/`：资源服务器与 RestClient 结合示例
- `static/`：静态配置资源服务器示例

##### `saml2/`

- `identity-provider/`：SAML2 Identity Provider 示例
- `login/`：SAML2 登录示例
- `refreshable-metadata/`：可刷新元数据示例
- `saml-extension-federation/`：SAML 扩展联邦场景示例
- `saml-extension-urls/`：SAML 扩展 URL 场景示例

##### `session-management/`

- `maximum-sessions/`：最大会话数限制示例
- `maximum-sessions-prevent-login/`：超过会话上限时阻止再次登录的示例

#### `servlet/spring-boot/kotlin/`

- `hello-security/`：Kotlin 版 Spring Boot Servlet 安全示例

### `servlet/xml/`

基于 XML 配置的传统 Spring Security 示例，适合了解老式配置方式。

#### `servlet/xml/java/`

- `contacts/`：联系人管理示例
- `dms/`：文档管理系统（DMS）示例
- `helloworld/`：最基础 XML 配置示例
- `preauth/`：XML 风格预认证示例

##### `saml2/`

- `login-logout/`：SAML2 登录/登出示例

## 6. 快速理解这个仓库的方法

如果只想快速建立整体认知，建议按下面顺序阅读：

1. `README.adoc`：先看样例目录总表
2. `settings.gradle`：看所有模块如何被组织进一个多模块工程
3. `build.gradle`：看全局构建规则
4. `reactive/` 和 `servlet/`：按技术栈浏览具体样例
5. 进入任一 sample 目录查看其 `README.adoc`、`build.gradle` 和 `src/`

## 7. 一句话总结

这个仓库本质上是一个 **按技术栈 + 按安全主题分类的 Spring Security 样例库**；  
根目录负责统一构建、版本和 CI，真正的示例代码主要集中在 `reactive/` 与 `servlet/` 下的大量独立 sample 模块中。
