# Spec Delta: login

## Purpose

为教师和学生建立可信的登录身份，使后续权限与班级边界有唯一可信来源，并保证失败路径不泄露账号是否存在。

## ADDED Requirements

### Requirement: 账号密码登录

系统 SHALL 校验账号与密码，成功后返回 Bearer 令牌、到期时间和该账号的角色与班级；登录响应 MUST NOT 设置认证 Cookie。

#### Scenario: 有效账号登录
- **WHEN** 已启用账号提交正确密码
- **THEN** 返回 200、令牌、到期时间，以及服务端读取的角色与班级

#### Scenario: 密码错误
- **WHEN** 已存在账号提交错误密码
- **THEN** 返回 401，提示为通用错误，不说明密码错误还是账号不存在

### Requirement: 密码存储

系统 MUST 只保存密码的加盐哈希，不得保存或回显明文密码；日志与错误响应 MUST NOT 包含密码或令牌。

#### Scenario: 检查存储内容
- **GIVEN** 已存在若干账号
- **WHEN** 直接查看账号表的密码字段
- **THEN** 只能看到哈希值，无法还原明文

### Requirement: 会话校验与失效

系统 SHALL 在每次受保护请求上校验令牌，缺失、伪造、过期或已撤销的令牌返回 401；登出后同一令牌 MUST 立即失效。

#### Scenario: 登出后重放
- **WHEN** 用户登出后用同一令牌请求受保护接口
- **THEN** 返回 401，不返回任何业务数据

#### Scenario: 令牌过期
- **GIVEN** 令牌已超过有效期
- **WHEN** 携带该令牌请求接口
- **THEN** 返回 401，并提示需要重新登录

### Requirement: 不使用 Cookie 认证

系统 MUST NOT 仅凭 Cookie 授予认证访问权限。

#### Scenario: 只带 Cookie
- **WHEN** 请求只携带会话 Cookie、没有 Bearer 令牌
- **THEN** 返回 401
