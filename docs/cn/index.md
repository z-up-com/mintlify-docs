# ToAPIs — OpenAI 兼容 API 网关（GPT-5、Claude、Gemini）
Source: https://docs.toapis.com/docs/cn/index

OpenAI 兼容 API，支持 GPT-5、Claude、Gemini。多供应商路由、透明定价、低延迟。企业 SLA、SDK 支持、按需付费。

统一的 OpenAI 兼容端点，支持 GPT-5、Claude 和 Gemini。只需将 Base URL 更改为 `https://toapis.com/v1`，即可在几分钟内从 OpenAI 迁移——保留现有 SDK，无需重写代码。多供应商路由确保低延迟和高可用性。透明定价、企业 SLA 和全球 CDN 加速。

## 快速开始

<Card title="立即开始" icon="rocket" href="/docs/cn/api-reference/texts/general/chat-completions">
  OpenAI 兼容的聊天 API，支持 GPT-5、Claude Sonnet 4.5、Gemini 2.0。切换 Base URL，无需修改代码。
</Card>

## OpenAI 兼容端点

ToAPIs 提供 OpenAI 风格的端点，实现无缝迁移。只需更改 Base URL 并继续使用现有 SDK。所有接口遵循 OpenAI 标准，扩展支持 Claude、Gemini、Sora 和 VEO 模型。

<CardGroup>
  <Card title="聊天补全 API" icon="comments" href="/docs/cn/api-reference/texts/general/chat-completions">
    流式支持和低延迟
  </Card>

  <Card title="图像生成 API" icon="image" href="/docs/cn/api-reference/images/gpt-4o/generation">
    文本到图像生成
  </Card>

  <Card title="视频生成 API" icon="video" href="/docs/cn/api-reference/videos/veo3/generation">
    异步任务管理，支持 Webhook
  </Card>
</CardGroup>

## 平台优势

### 透明定价

按需付费，无需订阅。所有模型的每 Token 价格清晰透明，比官方提供商价格更实惠，同时保持相同的质量和低延迟。

* 所有模型的每 Token 价格清晰透明
* 企业用量享受批量折扣
* 无隐藏费用
* 只为实际使用付费

### 多供应商路由与企业 SLA

内置智能路由跨多个 LLM 供应商，确保高可用性和低延迟。当一个供应商出现问题时，请求会自动路由到备用供应商，服务不中断。

### 99.9% 可用性 SLA

企业级可靠性，自动故障转移。多供应商路由确保您的请求始终以最低延迟得到服务。

### 全球 CDN 加速

全球边缘节点提供最低延迟。优化路由减少响应时间，全球范围内改善用户体验。

### 速率限制管理

跨供应商自动处理速率限制。智能请求分配防止节流，确保平稳运行。

### 实时状态监控

监控端点健康状态和性能。通过 Webhook 通知跟踪图像和视频创建的异步任务进度。

<Card title="查看状态 →" href="/docs/cn/api-reference/tasks/status" />

## SDK 与代码示例

Python、Node.js 和 Java 官方 SDK。所有 SDK 与 OpenAI 兼容，只需更改 Base URL 配置即可无缝迁移。

### Python SDK

使用 `pip install openai` 安装。设置 Base URL 并使用现有 OpenAI 代码，无需更改。

```python theme={null}
from openai import OpenAI
client = OpenAI(
    base_url="https://toapis.com/v1",
    api_key="your-ToAPIs-key"
)
```

### Node.js SDK

使用 `npm install openai` 安装。将 baseURL 参数配置为 ToAPIs 端点，保持现有集成。

```javascript theme={null}
import OpenAI from 'openai';
const client = new OpenAI({
  baseURL: 'https://toapis.com/v1',
  apiKey: 'your-ToAPIs-key'
});
```

### Java SDK

OpenAI 兼容的 Java 客户端。简单的配置更改即可访问所有 ToAPIs 模型和供应商。

## 从 OpenAI 迁移

5 分钟内切换到 ToAPIs。无需重写代码——只需更改 Base URL 和 API Key。

<Steps>
  <Step title="获取 API Key">
    注册 ToAPIs 并从 [控制台 Dashboard](https://toapis.com/console/token) 获取您的 API Key。
  </Step>

  <Step title="更新 Base URL">
    将 OpenAI Base URL 更改为 ToAPIs 端点。您现有的 SDK 集成无需修改即可工作。
  </Step>

  <Step title="测试端点">
    使用新的 Base URL 运行现有代码。所有 OpenAI 兼容端点以相同格式响应。在控制台 Dashboard 中检查延迟和速率限制。
  </Step>
</Steps>

## 统一端点支持的模型

通过一个 OpenAI 兼容网关访问领先的 LLM 供应商。所有模型均以一致的请求/响应格式提供。

### 聊天补全模型

用于聊天、补全和代码任务的领先语言模型：

* **GPT-5**：OpenAI 旗舰模型，增强推理能力
* **GPT-4o 和 GPT-4o Mini**：平衡性能和成本的多模态模型
* **Claude Sonnet 4.5 和 Haiku 4.5**：Anthropic 复杂推理模型
* **Gemini 2.0 Flash 和 Flash Thinking**：Google 多模态模型

### 图像生成模型

从文本提示生成图像：

* **GPT-4o Image**：OpenAI 图像生成模型
* **Gemini 2.5 Flash Image**：Google 高效图像模型

### 视频生成模型

视频生成，支持异步任务跟踪和 Webhook 回调：

* **OpenAI Sora2**：OpenAI 视频生成模型
* **Google VEO3**：Google 视频生成模型

### 语音模型

通过 OpenAI 兼容端点实现语音转文本和文本转语音：

* **Whisper-1**：OpenAI 转录模型
* **TTS**：多种声音的文本转语音

## 常见问题

<AccordionGroup>
  <Accordion title="如何从 OpenAI 迁移？">
    只需将 Base URL 更改为 `https://toapis.com/v1` 并使用您的 ToAPIs API Key。保留现有 SDK——无需重写代码。迁移通常在 5 分钟内完成。
  </Accordion>

  <Accordion title="SLA 包含什么？">
    我们的企业 SLA 包括 99.9% 可用性保证、全球 CDN 加速、多供应商路由自动故障转移以及实时状态监控。速率限制管理和 Webhook 支持均已包含。
  </Accordion>

  <Accordion title="价格与 OpenAI 相比如何？">
    ToAPIs 提供透明的每 Token 定价，比官方供应商价格更实惠。按需付费无需订阅，企业用量享受批量折扣，无隐藏费用。
  </Accordion>

  <Accordion title="延迟和性能如何？">
    通过全球 CDN 加速和智能多供应商路由保证低延迟。全球边缘节点确保最佳响应时间。实时状态监控让您跟踪性能。
  </Accordion>
</AccordionGroup>
