# API Document to MCP

`api-doc-to-mcp` 用于将 OpenAPI、Swagger、Postman、Markdown、Word、PDF 等 API 文档，转换为可评审、可测试、可部署的 MCP 集成。

它也可以审计已有的 API-to-MCP 封装，检查接口映射、权限、租户隔离、敏感数据、错误处理和高风险操作。

## 主要能力

- 解析和整理 API 接口文档
- 建立 API 到 MCP Tool、Resource、Prompt 的映射
- 根据业务任务设计 MCP 能力，而不是机械暴露所有端点
- 识别认证、权限、租户和敏感数据缺口
- 对删除、支付、审批、退款、外部发送等高风险操作进行审查
- 按现有项目的技术栈生成或修改 MCP Server
- 设计分页、异步任务、错误映射、超时和重试策略
- 运行类型检查、构建和聚焦测试
- 输出 `ready`、`conditional` 或 `blocked` 状态

## 适用场景

- 根据 API 文档构建 MCP Server
- 将已有 API 客户端或 SDK 封装为 MCP
- 评审 API 到 MCP 的标准化方案
- 审计已有 MCP Server 的安全和功能完整性
- 检查高风险接口是否适合暴露给模型
- 为 MCP 集成生成测试清单和发布检查项

## 使用方式

在请求中显式触发：

```text
$api-doc-to-mcp
