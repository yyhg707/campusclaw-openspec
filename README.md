# CampusClaw 规约：登录、权限、班级隔离、讲义入库与检索

《互联网软件开发技术与实践》第 2 课作业。用 OpenSpec 把 CampusClaw 首版的五块能力写成可验收的规约。
本仓库**只包含规约**，不包含实现代码。

## 为什么写这些

第 2 课的核心是：代码可以由模型生成，工程师的交付物变成「能把需求说清楚、并且可以判定是否达标」的规约。
CampusClaw 的关键约束不是页面长什么样，而是**谁是谁、能看什么、数据属于哪个班**。
这三点说不清，检索功能就无法验收——没有班级边界，检索结果为什么只能来自本班就无从判断。

## 目录结构

```
openspec/
├── config.yaml                                  # 项目上下文与写作约束
└── changes/
    └── add-campusclaw-mvp/
        ├── proposal.md                          # Why / What Changes / Non-goals
        ├── design.md                            # 关键决策与取舍
        ├── tasks.md                             # 分节任务，每项带 verify
        └── specs/
            ├── login/spec.md                    # 登录与会话
            ├── role-permission/spec.md          # 角色能力边界
            ├── class-isolation/spec.md          # 班级数据边界
            ├── material-upload/spec.md          # 讲义上传与入库状态
            └── knowledge-search/spec.md         # 班内检索与来源核对
```

一个 change 下按能力拆分 spec，与课程样板 `openspec/changes/<change-id>/specs/<capability>/spec.md` 一致。

## 五份规约分别回答什么

| 能力 | 回答的问题 | 关键约束 |
| --- | --- | --- |
| `login` | 你是谁 | 令牌由服务端签发，登出立即失效，失败提示不区分账号是否存在 |
| `role-permission` | 你能做什么 | 教师可上传，学生只读；默认拒绝；越权与不存在返回一致 |
| `class-isolation` | 你的数据边界在哪 | 班级只从会话取，所有查询强制过滤 |
| `material-upload` | 讲义怎么进来 | 仅教师、仅 txt/md、状态可追踪、失败可重试 |
| `knowledge-search` | 怎么查、凭什么信 | 班内检索、结果带来源、参数有上限 |

## 写法约定

- 结构标题与 `SHALL` / `MUST` 保持英文，正文用中文
- 每条要求配至少一个成功场景和一个失败场景，用 `WHEN / THEN` 描述可观察结果
- `tasks.md` 中每项都带 `verify:`，说明怎么判定完成
- 初稿不勾选任何复选框，勾选只发生在实现并验证之后

## 验收方式

结构按第 2 课样板组织，可用课程 Harness 的 OpenSpec 命令校验：

```
openspec validate add-campusclaw-mvp --strict
```

本地未安装该 CLI，因此本仓库暂未附 validate 输出。校验时如报结构问题，以 `openspec/changes/add-campusclaw-mvp/` 下的文件为准修正。

## 当前状态

- 已完成：五份规约初稿、设计决策、任务拆解
- 未开始：实现。勾选与实现证据在后续课程中补充

## 相关

- 课程入口：https://devops.hello1023.com/
- 个人练习项目（第 1 课作业）：https://github.com/yyhg707/todo-list
