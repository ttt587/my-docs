# 实践课4：Pi Agent模型调用与运行流程

姓名：谭灿英  
GitHub：`ttt587`  
小组源码仓库：<https://github.com/tri-queens-code-lab/pi-hunnu-2026>  
实践4源码分支：`practice-4-tancanying`  
实验基础提交：`c3cc91632d0a1950fb122605b190233f2aa0006e`

## 一、实践目标

本次实践通过在 Agent 循环中加入两处跟踪日志，对比“直接问答”和“调用本地工具读取文件”两种会话，理解模型请求、工具执行、工具结果写入上下文以及下一轮模型请求之间的关系。

## 二、仓库与分支

- 本地实验路径：`C:\pi-hunnu-2026-build`
- remote：`https://github.com/tri-queens-code-lab/pi-hunnu-2026.git`
- 实践分支：`practice-4-tancanying`
- 小组 `main` 分支未修改

![S1 仓库与个人分支](screenshots/S1-repository-branch.png)

## 三、源码修改

修改文件：`packages/agent/src/agent-loop.ts`。

### 1. 工具结果写入上下文后

在遍历每个工具结果时，先将当前 `result` 写入 `currentContext.messages`，随后立即输出日志，再写入 `newMessages`：

```ts
for (const result of toolResults) {
    currentContext.messages.push(result);
    console.error("[P4] 工具结果已加入上下文");
    newMessages.push(result);
}
```

![S2a 工具结果日志位于循环内部并紧跟上下文写入](screenshots/S2a-tool-result-log.png)

### 2. 请求模型前

在调用 `streamFunction` 前输出：

```ts
console.error("[P4] Agent 即将请求模型");
const response = await streamFunction(config.model, llmContext, {
    // ...
});
```

![S2b 模型请求日志紧邻 streamFunction](screenshots/S2b-model-request-log.png)

实验文件 `practice4.txt` 的内容为：

```text
实验代号：P4-TANCANYING-2026
```

## 四、检查、构建与产物

源码修改后先执行检查。在线构建在更新第三方模型目录时遇到 Vercel AI Gateway `403`，该步骤与本次 Agent 源码修改无关，因此改用仓库自带的离线完整构建脚本：

```powershell
npm.cmd run check
npm.cmd run build:offline
$LASTEXITCODE
Test-Path -LiteralPath '.\packages\coding-agent\dist\bundle\cli.js'
```

最终结果：

- 完整代码检查通过。
- 11个模块通过仓库自带的 `build:offline` 完整重新构建，`$LASTEXITCODE` 为 `0`。
- `coding-agent` bundle 共生成 `84 files, 9.4 MiB`。
- `Test-Path` 返回 `True`，确认 `packages/coding-agent/dist/bundle/cli.js` 存在。
- 本地源码 CLI 版本为 `1.5.2`。

`build:offline` 仍会重新编译包括 `agent` 和 `coding-agent` 在内的全部模块，只复用仓库中已生成并校验通过的模型数据，避免构建结果受第三方目录接口影响；未通过修改小组源码规避检查或编译步骤。

![S3 完整构建与产物核验](screenshots/S3-build-verify.png)

## 五、会话A：直接问答

配置：

- 启动入口：本地编译产物 `packages/coding-agent/dist/bundle/cli.js`
- 模型：`gpt-6.1-terra`
- thinking：`medium`
- 工具：禁用
- 提问：用一句话解释 Git 分支是什么

真实日志和回答：

```text
[P4] Agent 即将请求模型
Git 分支是指向某个提交的可移动指针，让你可以独立开发而不影响其他分支。
```

截图顶部显示 Pi `v1.5.2`，底部显示本地项目路径 `C:\pi-hunnu-2026-build`，证明运行的是当前仓库重新构建的本地 CLI。会话A没有工具调用，只请求模型一次，模型直接给出最终回答。

![S4 会话A](screenshots/S4-session-a.png)

## 六、会话B：读取文件

使用 `/new` 创建新会话，配置与会话A保持相同，并要求 Pi 使用 `read` 工具读取 `practice4.txt` 后只回答实验代号。

真实 Pi 界面中清楚显示 `read practice4.txt` 工具记录、工具结果写入上下文日志、下一次模型请求日志和最终回答：

```text
[P4] Agent 即将请求模型
[P4] 工具结果已加入上下文
[P4] Agent 即将请求模型
P4-TANCANYING-2026
```

会话B的执行顺序为：

1. 第一次请求远程模型；
2. 模型返回 `read` 工具调用；
3. 本地工具读取文件，结果作为 `toolResult` 加入上下文；
4. 携带更新后的上下文第二次请求模型；
5. 模型根据工具结果给出正确实验代号。

![S5 会话B](screenshots/S5-session-b.png)

## 七、会话对比

| 项目 | 会话A | 会话B |
|---|---:|---:|
| 工具调用次数 | 0 | 1（`read`） |
| 模型请求次数 | 1 | 2 |
| 工具结果写入上下文 | 否 | 是 |
| 最终结果 | 直接解释 Git 分支 | 正确返回文件实验代号 |

会话B比会话A多一次模型请求，原因是第一次模型请求只决定调用工具；本地工具执行完成后，Agent 将 `toolResult` 加入上下文，第二次模型请求才可以看到文件内容并生成最终回答。

## 八、运行流程图

箭头表示实际信息流和调用方向：

```mermaid
flowchart LR
    U[User prompt] --> CA[coding-agent]
    CA --> A[agent: runLoop]
    A --> AI[ai: streamFunction]
    AI --> RM[Remote model service]
    RM -->|Direct answer| A
    RM -->|toolCall: read| A
    A --> RT[Local read tool]
    RT --> TR[toolResult]
    TR --> A
    A -->|Next model request| AI
    A --> CA
    CA --> U
```

可编辑源文件：[practice4-flow.mmd](practice4-flow.mmd)。

![S6 运行流程与会话对比](screenshots/S6-runtime-flow.png)

## 九、结论

`coding-agent` 负责接收用户输入并展示结果；`agent` 维护循环、执行工具并更新上下文；`ai` 将规范化后的消息发送给远程模型服务。工具在本地执行，远程模型不会直接读取本机文件。只有当工具结果以 `toolResult` 进入上下文并发起下一轮模型请求后，模型才能利用该文件内容生成答案。

本报告及截图不包含 API Key、token、`auth.json` 或其他登录凭据。
