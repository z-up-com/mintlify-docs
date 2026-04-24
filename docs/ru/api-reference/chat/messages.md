> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Anthropic Messages API

> Общайтесь с моделями Claude в нативном формате Anthropic Messages API

* Нативный формат Anthropic Messages API
* Полная совместимость с официальным Anthropic SDK (Python / JavaScript) — нужно лишь изменить `base_url`
* Поддержка потоковой передачи (SSE)
* Поддержка многоходовых диалогов, системных подсказок, визуального ввода и вызова инструментов

<Note>
  Если вы уже используете OpenAI SDK, рекомендуем [эндпоинт в формате OpenAI](/docs/ru/api-reference/chat/chat).
  Если вы используете Anthropic SDK или Claude Code, рекомендуется этот эндпоинт.
</Note>

## Авторизация

<ParamField header="Authorization" type="string">
  Bearer-токен для прямых HTTP-запросов

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

<ParamField header="x-api-key" type="string">
  API-ключ, совместимый с Anthropic SDK

  ```
  x-api-key: YOUR_API_KEY
  ```
</ParamField>

<ParamField header="anthropic-version" type="string" default="2023-06-01">
  Версия Anthropic API. Устанавливается автоматически при использовании Anthropic SDK.

  Рекомендуемое значение: `2023-06-01`
</ParamField>

## Тело запроса

<ParamField body="model" type="string" required>
  Название модели

  Поддерживаются все модели серии Claude, например:

  * `claude-opus-4-6`
  * `claude-sonnet-4-6`
  * `claude-haiku-4-5`
</ParamField>

<ParamField body="messages" type="object[]" required>
  Список сообщений в хронологическом порядке. Допустимы только роли `user` и `assistant`. Системные подсказки задаются в поле `system` верхнего уровня.

  <Expandable title="messages[n]">
    <ParamField body="role" type="string" required>
      Роль: `user` или `assistant`
    </ParamField>

    <ParamField body="content" type="string | object[]" required>
      Содержимое в виде строки или массива блоков контента

      <Expandable title="Блок контента">
        <ParamField body="type" type="string" required>
          Тип контента: `text` или `image`
        </ParamField>

        <ParamField body="text" type="string">
          Текстовое содержимое. Обязательно при `type: text`.
        </ParamField>

        <ParamField body="source" type="object">
          Источник изображения. Обязательно при `type: image`.

          <Expandable title="source">
            <ParamField body="type" type="string" required>
              Тип источника: `base64` или `url`
            </ParamField>

            <ParamField body="media_type" type="string">
              MIME-тип, обязателен для `base64`. Например: `image/jpeg`, `image/png`
            </ParamField>

            <ParamField body="data" type="string">
              Изображение в кодировке Base64. Обязательно при `type: base64`.
            </ParamField>

            <ParamField body="url" type="string">
              URL изображения. Обязательно при `type: url`.
            </ParamField>
          </Expandable>
        </ParamField>
      </Expandable>
    </ParamField>
  </Expandable>
</ParamField>

<ParamField body="max_tokens" type="integer" required>
  Максимальное количество токенов для генерации

  * Claude Sonnet 4-6: до `64000`
  * Claude Opus 4-6: до `32000`
</ParamField>

<ParamField body="system" type="string | object[]">
  Системная подсказка на верхнем уровне (не внутри `messages`)

  Принимает строку или массив блоков контента.
</ParamField>

<ParamField body="stream" type="boolean" default={false}>
  Включить потоковую передачу (Server-Sent Events)

  * `true`: токены передаются по мере генерации в формате SSE Anthropic
  * `false`: полный ответ возвращается одним блоком
</ParamField>

<ParamField body="temperature" type="number" default={1}>
  Температура семплирования

  Диапазон: `0` – `1`
</ParamField>

<ParamField body="top_p" type="number">
  Порог ядерного семплирования

  Диапазон: `0` – `1`. Не рекомендуется задавать одновременно с `temperature`.
</ParamField>

<ParamField body="stop_sequences" type="string[]">
  Стоп-последовательности — генерация прекращается при появлении любой из них
</ParamField>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор запроса с префиксом `msg_`
</ResponseField>

<ResponseField name="type" type="string">
  Тип объекта, всегда `message`
</ResponseField>

<ResponseField name="role" type="string">
  Роль ответа, всегда `assistant`
</ResponseField>

<ResponseField name="content" type="object[]">
  Список сгенерированных блоков контента

  * `content[].type`: тип контента, обычно `text`
  * `content[].text`: сгенерированный текст
</ResponseField>

<ResponseField name="model" type="string">
  Модель, обработавшая запрос
</ResponseField>

<ResponseField name="stop_reason" type="string">
  Причина остановки генерации

  * `end_turn`: модель завершила ответ естественным образом
  * `max_tokens`: достигнут лимит `max_tokens`
  * `stop_sequence`: сработала стоп-последовательность
</ResponseField>

<ResponseField name="usage" type="object">
  Статистика использования токенов

  * `usage.input_tokens`: количество входных токенов
  * `usage.output_tokens`: количество выходных токенов
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/messages \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "claude-sonnet-4-6",
      "max_tokens": 1024,
      "messages": [
        {
          "role": "user",
          "content": "Привет, расскажи о себе"
        }
      ]
    }'
  ```

  ```bash cURL (системная подсказка) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/messages \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "claude-sonnet-4-6",
      "max_tokens": 1024,
      "system": "Ты профессиональный помощник по программированию, специализирующийся на Python и Go.",
      "messages": [
        {
          "role": "user",
          "content": "Напиши быструю сортировку на Python"
        }
      ]
    }'
  ```

  ```bash cURL (потоковая передача) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/messages \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "claude-sonnet-4-6",
      "max_tokens": 1024,
      "stream": true,
      "messages": [
        {
          "role": "user",
          "content": "Объясни, что такое рекурсия"
        }
      ]
    }'
  ```

  ```python Python (Anthropic SDK) theme={null}
  import anthropic

  client = anthropic.Anthropic(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com"
  )

  message = client.messages.create(
      model="claude-sonnet-4-6",
      max_tokens=1024,
      messages=[
          {"role": "user", "content": "Привет, расскажи о себе"}
      ]
  )

  print(message.content[0].text)
  ```

  ```python Python (потоковая передача) theme={null}
  import anthropic

  client = anthropic.Anthropic(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com"
  )

  with client.messages.stream(
      model="claude-sonnet-4-6",
      max_tokens=1024,
      messages=[
          {"role": "user", "content": "Объясни, что такое рекурсия"}
      ]
  ) as stream:
      for text in stream.text_stream:
          print(text, end="", flush=True)
  ```

  ```javascript JavaScript (Anthropic SDK) theme={null}
  import Anthropic from "@anthropic-ai/sdk";

  const client = new Anthropic({
    apiKey: "your-ToAPIs-key",
    baseURL: "https://toapis.com",
  });

  const message = await client.messages.create({
    model: "claude-sonnet-4-6",
    max_tokens: 1024,
    messages: [{ role: "user", content: "Привет, расскажи о себе" }],
  });

  console.log(message.content[0].text);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "msg_01XFDUDYJgAACzvnptvVoYEL",
    "type": "message",
    "role": "assistant",
    "content": [
      {
        "type": "text",
        "text": "Привет! Я Claude — ИИ-ассистент от Anthropic. Я помогу ответить на вопросы, проанализировать информацию, написать код и многое другое. Чем могу помочь?"
      }
    ],
    "model": "claude-sonnet-4-6",
    "stop_reason": "end_turn",
    "usage": {
      "input_tokens": 10,
      "output_tokens": 32
    }
  }
  ```

  ```json 400 theme={null}
  {
    "type": "error",
    "error": {
      "type": "invalid_request_error",
      "message": "messages: field required"
    }
  }
  ```

  ```json 401 theme={null}
  {
    "type": "error",
    "error": {
      "type": "authentication_error",
      "message": "Invalid API key"
    }
  }
  ```
</ResponseExample>
