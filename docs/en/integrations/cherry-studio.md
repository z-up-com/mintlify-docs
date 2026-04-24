> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Cherry Studio Setup Guide

> Use ToAPIs as the API backend for Cherry Studio

## Prerequisites

Before getting started, make sure you have:

1. **Cherry Studio installed**

   Download and install Cherry Studio from the [official website](https://cherry-ai.com)

2. **A ToAPIs API key**

   Sign in to the [ToAPIs Console](https://toapis.com/console/token) to obtain your API key (starts with `sk-`)

**Note:** If you don't have a ToAPIs account yet, register at [ToAPIs](https://toapis.com) first.

## Setup Steps

### Step 1: Open Model Provider Settings

1. Launch Cherry Studio
2. Click the **Settings** icon (gear icon) in the bottom-left corner
3. Navigate to **Model Services** or **Providers**

### Step 2: Add ToAPIs as a Provider

1. Click the **+** button in the top-right of the provider list
2. In the dialog that appears:
   * **Name**: Enter `ToAPIs` (or any name you prefer)
   * **Provider Type**: Select **New API** (OpenAI-compatible)
3. Click **Confirm** to create the provider

### Step 3: Fill in API Configuration

On the ToAPIs provider configuration page, enter the following:

| Field           | Value                                   |
| --------------- | --------------------------------------- |
| **API Key**     | Your ToAPIs API key (`sk-xxxxxxxxxxxx`) |
| **API Address** | `https://toapis.com/v1/`                |

### Step 4: Add Models

1. On the provider config page, click **Add Model**
2. Fill in the model details:
   * **Model ID**: Enter the model ID you want to use (see recommended models below)
   * **Endpoint Type**: Select **OpenAI**
3. Click Save

**Recommended Models:**

| Model Name        | Model ID                                                                    | Description                                                |
| ----------------- | --------------------------------------------------------------------------- | ---------------------------------------------------------- |
| Claude Opus 4.6   | [`claude-opus-4-6`](/en/api-reference/chat/claude-opus-4-6/chat)            | Most capable, best for complex tasks                       |
| Claude Sonnet 4.6 | [`claude-sonnet-4-6`](/en/api-reference/chat/claude-sonnet-4-6/chat)        | Balanced performance and speed — recommended for daily use |
| Claude Haiku 4.5  | [`claude-haiku-4-5-20251001`](/en/api-reference/chat/claude-haiku-4-5/chat) | Fastest, best for lightweight tasks                        |
| GPT-5             | [`gpt-5`](/en/api-reference/chat/models)                                    | Latest OpenAI flagship model                               |
| GPT-4o            | [`gpt-4o`](/en/api-reference/chat/models)                                   | High-quality conversations and multimodal                  |
| GPT-4o Mini       | [`gpt-4o-mini`](/en/api-reference/chat/models)                              | Fast and cost-effective                                    |
| Gemini 2.5 Pro    | [`gemini-2.5-pro`](/en/api-reference/chat/models)                           | Google multimodal flagship                                 |

Repeat the above steps to add as many models as you need.

### Step 5: Enable and Verify

1. Make sure the toggle next to the provider is **enabled**
2. Click **Check Connection** or **Test** to verify the configuration
3. A success message confirms everything is working correctly

## Start Using

Once configured:

1. Return to the Cherry Studio main interface
2. Select your ToAPIs model from the model picker at the top of the chat window
3. Start chatting

## Troubleshooting

### Connection test fails?

1. Double-check the **API Key** — it must start with `sk-`
2. Confirm the **API Address** is `https://toapis.com/v1/` (note the trailing slash)
3. Verify the key is active in the [ToAPIs Console](https://toapis.com/console/token) and your account has sufficient balance
4. Check your network connection and ensure `https://toapis.com` is reachable

### Can't find my model in the list?

Cherry Studio uses manually added models. Simply type the model ID directly when adding a model. You can find all available model IDs in the [ToAPIs model list](https://toapis.com/console/models).

### Common error codes

| Error                       | Cause                        | Fix                     |
| --------------------------- | ---------------------------- | ----------------------- |
| `401 Unauthorized`          | Invalid or expired API key   | Generate a new API key  |
| `429 Too Many Requests`     | Rate limit exceeded          | Wait a moment and retry |
| `500 Internal Server Error` | Temporary server issue       | Retry in a few minutes  |
| `insufficient_quota`        | Insufficient account balance | Top up in the console   |

## Support

If you encounter any issues:

* 📚 [ToAPIs Documentation](https://docs.toapis.com)
* 📚 [Cherry Studio Official Docs](https://docs.cherry-ai.com)
* 💬 [Discord Community](https://discord.gg/hvnszCrJ73)
* 🐦 [Twitter @toapisai](https://x.com/toapisai)

***

\[## Get Started with ToAPIs

Sign up for ToAPIs, get your API key, and supercharge your AI workflow with Cherry Studio!]\([https://toapis.com](https://toapis.com))
