> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Чат-завершения

> Эндпоинт чат-завершений, совместимый с OpenAI, поддерживает все текстовые модели

* Совместимый с OpenAI API чат-завершений
* Укажите модель через параметр `model` — все доступные модели см. в [Списке моделей](./models)
* Поддержка потоковой передачи (SSE)
* Поддержка многоходовых диалогов и системных промптов
* Отдельные модели поддерживают визуальный ввод (изображения) и расширенное мышление

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все API требуют аутентификации Bearer Token

  Получите API Key на [странице управления API ключами](https://toapis.com/console/token)

  Добавьте в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="model" type="string" required>
  Название модели — все доступные модели см. в [Списке моделей](./models)

  Примеры: `"claude-sonnet-4-6"`, `"gpt-5"`, `"qwen3-max"`
</ParamField>

<ParamField body="messages" type="object[]" required>
  Список сообщений диалога в хронологическом порядке

  <Expandable title="messages[n]">
    <ParamField body="role" type="string" required>
      Роль — `system`, `user` или `assistant`
    </ParamField>

    <ParamField body="content" type="string | object[]" required>
      Содержимое сообщения в одном из двух форматов:

      * **Строка**: обычный текст, поддерживается всеми моделями
      * **Массив блоков контента**: смешанный текст и изображения, только для моделей с поддержкой зрения

      <Expandable title="Формат массива блоков (content[n])">
        <ParamField body="type" type="string" required>
          Тип блока — `text` или `image_url`
        </ParamField>

        <ParamField body="text" type="string">
          Текстовое содержимое, обязательно при `type` равном `text`
        </ParamField>

        <ParamField body="image_url" type="object">
          Данные изображения, обязательно при `type` равном `image_url`

          <Expandable title="image_url">
            <ParamField body="url" type="string" required>
              URL изображения или Base64 Data URI (формат: `data:image/jpeg;base64,...`)
            </ParamField>
          </Expandable>
        </ParamField>
      </Expandable>
    </ParamField>
  </Expandable>
</ParamField>

<ParamField body="stream" type="boolean" default={false}>
  Включить потоковую передачу (Server-Sent Events)

  * `true`: Передавать токены по мере генерации
  * `false`: Вернуть полный ответ сразу
</ParamField>

<ParamField body="max_tokens" type="integer">
  Максимальное количество токенов для генерации

  Использует лимит модели по умолчанию, если не задано
</ParamField>

<ParamField body="temperature" type="number" default={1}>
  Температура выборки, управляет случайностью вывода

  * Диапазон: `0` до `2`
  * Меньшие значения дают более детерминированный вывод, большие — более разнообразный
</ParamField>

<ParamField body="top_p" type="number" default={1}>
  Порог вероятности ядерной выборки

  Диапазон: `0` до `1`. Не рекомендуется одновременно изменять `temperature` и `top_p`
</ParamField>

<ParamField body="stop" type="string | string[]">
  Стоп-последовательности — генерация останавливается при встрече этих строк

  Максимум 4 стоп-последовательности
</ParamField>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор запроса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `chat.completion`
</ResponseField>

<ResponseField name="created" type="integer">
  Временная метка создания запроса (Unix)
</ResponseField>

<ResponseField name="model" type="string">
  Фактически использованная модель
</ResponseField>

<ResponseField name="choices" type="object[]">
  Список сгенерированных результатов

  * `choices[].message.role`: Роль сообщения, всегда `assistant`
  * `choices[].message.content`: Сгенерированный текст
  * `choices[].finish_reason`: Причина завершения — `stop` / `length` / `content_filter`
  * `choices[].index`: Индекс результата
</ResponseField>

<ResponseField name="usage" type="object">
  Статистика использования токенов

  * `usage.prompt_tokens`: Количество входящих токенов
  * `usage.completion_tokens`: Количество исходящих токенов
  * `usage.total_tokens`: Общее количество токенов
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/chat/completions \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "claude-sonnet-4-6",
      "messages": [
        {
          "role": "user",
          "content": "Привет, расскажи о себе"
        }
      ]
    }'
  ```

  ```bash cURL (Потоковый режим) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/chat/completions \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-5",
      "messages": [
        {
          "role": "system",
          "content": "Ты профессиональный помощник по коду"
        },
        {
          "role": "user",
          "content": "Напиши быструю сортировку на Python"
        }
      ],
      "stream": true
    }'
  ```

  ```python Python (openai SDK) theme={null}
  from openai import OpenAI

  client = OpenAI(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com/v1"
  )

  response = client.chat.completions.create(
      model="claude-sonnet-4-6",  # замените на любую поддерживаемую модель
      messages=[
          {"role": "user", "content": "Привет, расскажи о себе"}
      ]
  )

  print(response.choices[0].message.content)
  ```

  ```python Python (Потоковый режим) theme={null}
  from openai import OpenAI

  client = OpenAI(
      api_key="your-ToAPIs-key",
      base_url="https://toapis.com/v1"
  )

  stream = client.chat.completions.create(
      model="gpt-5",  # замените на любую поддерживаемую модель
      messages=[
          {"role": "system", "content": "Ты профессиональный помощник по коду"},
          {"role": "user", "content": "Напиши быструю сортировку на Python"}
      ],
      stream=True
  )

  for chunk in stream:
      if chunk.choices[0].delta.content:
          print(chunk.choices[0].delta.content, end="", flush=True)
  ```

  ```javascript JavaScript (openai SDK) theme={null}
  import OpenAI from "openai";

  const client = new OpenAI({
    apiKey: "your-ToAPIs-key",
    baseURL: "https://toapis.com/v1",
  });

  const response = await client.chat.completions.create({
    model: "claude-sonnet-4-6", // замените на любую поддерживаемую модель
    messages: [{ role: "user", content: "Привет, расскажи о себе" }],
  });

  console.log(response.choices[0].message.content);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "chatcmpl-abc123",
    "object": "chat.completion",
    "created": 1703884800,
    "model": "claude-sonnet-4-6",
    "choices": [
      {
        "index": 0,
        "message": {
          "role": "assistant",
          "content": "Привет! Я Claude, ИИ-ассистент от Anthropic. Я могу помогать отвечать на вопросы, анализировать информацию, писать код, создавать контент и многое другое. Чем могу помочь?"
        },
        "finish_reason": "stop"
      }
    ],
    "usage": {
      "prompt_tokens": 14,
      "completion_tokens": 40,
      "total_tokens": 54
    }
  }
  ```

  ```json 400 theme={null}
  {
    "error": {
      "code": "invalid_request_error",
      "message": "The 'messages' field is required.",
      "param": "messages",
      "type": "invalid_request_error"
    }
  }
  ```

  ```json 401 theme={null}
  {
    "error": {
      "code": "unauthorized",
      "message": "Invalid API key",
      "type": "authentication_error"
    }
  }
  ```
</ResponseExample>
