> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# ​Quick Start

# [​](#quick-start)Quick Start

Welcome to our API services! This guide helps you quickly get started with image and video generation.

## [​](#step-1:-get-an-api-key)Step 1: Get an API Key

1. Visit the [API Key Management page](https://toapis.com/console/token)
2. Sign in to your account
3. Create a new API key
4. Save your key securely (it is shown only once)

## [​](#step-2:-choose-a-model)Step 2: Choose a Model

We provide multiple AI models to choose from.

### [​](#text-generation-models)Text Generation Models

* **GPT-4o**: Powerful dialogue and text generation capabilities
* **Claude**: High-performance conversational model by Anthropic
* **Gemini**: Google’s multimodal large language model

### [​](#image-generation-models)Image Generation Models

* **GPT-4o-image**: High‑quality image generation
* **gpt-image-2**: OpenAI-compatible image generation with `reference_images`

### [​](#video-generation-models)Video Generation Models

* **Sora2**: Professional video generation

## [​](#step-3:-send-a-request)Step 3: Send a Request

### [​](#text-generation-example)Text generation example

Copy

```shiki theme={null}
curl -X POST https://toapis.com/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4o",
    "messages": [
      {
        "role": "user",
        "content": "Hello, please introduce yourself"
      }
    ]
  }'

```

### [​](#image-generation-example)Image generation example

Copy

```shiki theme={null}
curl -X POST https://toapis.com/v1/images/generations \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4o-image",
    "prompt": "A cute panda",
    "size": "1:1",
    "n": 1
  }'

```

### [​](#video-generation-example)Video generation example

Copy

```shiki theme={null}
curl -X POST https://toapis.com/v1/videos/generations \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "sora-2",
    "prompt": "Waves crashing against the shore",
    "duration": 15,
    "aspect_ratio": "16:9"
  }'

```

## [​](#step-4:-check-task-status)Step 4: Check Task Status

Because we use asynchronous processing, you need to query task status to obtain results.
Copy

```shiki theme={null}
curl -X GET https://toapis.com/v1/tasks/YOUR_TASK_ID \
  -H "Authorization: Bearer YOUR_API_KEY"

```

## [​](#what’s-next)What’s Next

\[## View API docs

Learn more about all available API endpoints.]\(/en/api-reference/introduction)
\[## Development Guide

Learn how to integrate the API into your application.]\(/en/development)[Development Guide](/en/development)⌘I
