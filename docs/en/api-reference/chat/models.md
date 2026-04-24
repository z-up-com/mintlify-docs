> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Supported Text Models

> All text models are fully compatible with the OpenAI Chat Completions API — just switch the model parameter

All models share the same API endpoint. Simply change the `model` parameter to switch between them:

```
POST https://toapis.com/v1/chat/completions
```

For the complete request parameters and response format, see [Chat Completions API](./chat).

***

## Anthropic / Claude

| Model ID            | Description                                                                               | Vision | Thinking |
| ------------------- | ----------------------------------------------------------------------------------------- | :----: | :------: |
| `claude-haiku-4-5`  | Fastest and most cost-effective Claude model, ideal for high-throughput lightweight tasks |    ✓   |          |
| `claude-sonnet-4-6` | Best balance of performance and cost, suitable for most business use cases                |    ✓   |          |
| `claude-opus-4-6`   | Claude flagship model, ideal for complex reasoning and high-quality generation            |    ✓   |          |

## OpenAI / GPT-5

| Model ID              | Description                                                                       | Vision | Thinking |
| --------------------- | --------------------------------------------------------------------------------- | :----: | :------: |
| `gpt-5`               | OpenAI flagship model with powerful general reasoning and multimodal capabilities |    ✓   |          |
| `gpt-5-codex`         | GPT-5 optimized for code — excels at code generation and debugging                |        |          |
| `gpt-5-codex-mini`    | Lightweight code model with ultra-fast response and low cost                      |        |          |
| `gpt-5.1`             | GPT-5 enhanced, with improved instruction following and reasoning                 |    ✓   |          |
| `gpt-5.1-codex`       | GPT-5.1 code-specialized variant                                                  |        |          |
| `gpt-5.1-codex-max`   | GPT-5.1 flagship code model, maximum coding capability                            |        |          |
| `gpt-5.1-codex-mini`  | GPT-5.1 lightweight code variant, fast and cost-efficient                         |        |          |
| `gpt-5.2`             | GPT-5 second generation with enhanced multimodal capabilities                     |    ✓   |          |
| `gpt-5.2-codex`       | GPT-5.2 code-specialized variant                                                  |        |          |
| `gpt-5.3-codex`       | Latest GPT-5 code model with continuously improving capabilities                  |        |          |
| `gpt-5.3-codex-spark` | GPT-5.3 Codex Spark variant, focused on high-speed code generation                |        |          |
| `gpt-5.4`             | GPT-5 generation 4, enhanced general reasoning and multimodal capabilities        |    ✓   |          |

## Google / Gemini

| Model ID              | Description                                                                                     | Vision | Thinking |
| --------------------- | ----------------------------------------------------------------------------------------------- | :----: | :------: |
| `gemini-3.1-fast`     | Google Gemini fast variant, high-value multimodal model                                         |    ✓   |          |
| `gemini-3.1-thinking` | Google Gemini thinking variant with built-in reasoning chain, ideal for complex problem solving |    ✓   |     ✓    |

## DeepSeek

| Model ID        | Description                                                                                  | Vision | Thinking |
| --------------- | -------------------------------------------------------------------------------------------- | :----: | :------: |
| `deepseek-v3.2` | DeepSeek flagship model with extended thinking support, excels at code and complex reasoning |        |     ✓    |

## Alibaba / Qwen

| Model ID        | Description                                                                                     | Vision | Thinking |
| --------------- | ----------------------------------------------------------------------------------------------- | :----: | :------: |
| `qwen3-max`     | Qwen flagship model with deep thinking support, excels at complex reasoning and code generation |        |     ✓    |
| `qwen3.5-plus`  | Qwen enhanced model, balanced performance and cost                                              |        |          |
| `qwen3.5-flash` | Qwen ultra-fast model, ideal for low-latency and high-throughput scenarios                      |        |          |

## Zhipu / GLM

| Model ID | Description                                                                         | Vision | Thinking |
| -------- | ----------------------------------------------------------------------------------- | :----: | :------: |
| `glm-5`  | Zhipu flagship model with outstanding Chinese language understanding and generation |        |          |

## Moonshot / Kimi

| Model ID    | Description                                                                              | Vision | Thinking |
| ----------- | ---------------------------------------------------------------------------------------- | :----: | :------: |
| `kimi-k2.5` | Moonshot flagship model with ultra-long context and strong Chinese language capabilities |        |          |

## MiniMax

| Model ID       | Description                                              | Vision | Thinking |
| -------------- | -------------------------------------------------------- | :----: | :------: |
| `MiniMax-M2.5` | MiniMax flagship model with excellent cost-effectiveness |        |          |
