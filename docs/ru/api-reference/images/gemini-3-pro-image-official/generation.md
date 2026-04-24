---
title: "Gemini-3-Pro Official Генерация изображений"
---
> Генерация изображений с использованием модели Google Gemini 3 Pro с прямым подключением к Vertex AI

* Прямое подключение к Google Vertex AI через нативный API Gemini 3 Pro Image
* Выбор модели `gemini-3-pro-image-preview-official` через параметр model
* Поддержка генерации из текста и из изображения
* Поддержка нативных параметров генерации: temperature, topP, maxOutputTokens
* Поддержка resolution (1K/2K/4K), personGeneration, imageOutputOptions — конфигурация изображений
* Поддержка thinkingConfig — режим мышления для улучшения генерации сложных сцен
* Поддержка safetySettings — настройки безопасности контента
* Асинхронное управление задачами через ID задачи

<Warning>
  **Важное изменение**: Для улучшения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 непосредственно в `image_urls`. Пожалуйста, сначала используйте [эндпоинт загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений, а затем используйте полученный URL в этом API.
</Warning>

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все эндпоинты требуют аутентификации Bearer Token

  Получить API Key: Перейдите на [страницу управления API Key](https://toapis.com/console/token) для получения вашего API Key

  Добавьте в заголовки запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="model" type="string" default="gemini-3-pro-image-preview-official" required>
  Название модели генерации изображений

  Пример: `"gemini-3-pro-image-preview-official"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации изображения
</ParamField>

<ParamField body="size" type="string">
  Соотношение сторон изображения

  Поддерживаемые форматы:

  * `1:1` - Квадрат
  * `3:2` / `2:3`
  * `3:4` / `4:3`
  * `4:5` / `5:4`
  * `9:16` / `16:9`
  * `21:9`
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Количество генерируемых изображений

  Фиксировано: 1
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL справочных изображений для генерации из изображения или редактирования

  **⚠️ Поддерживается только формат URL (base64 больше не поддерживается)**

  * Публично доступные URL изображений (http\:// или https\://)
  * Используйте [эндпоинт загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений

  **Ограничения:**

  * Максимум 14 изображений
  * Максимальный размер файла: 10MB
  * Поддерживаемые форматы: .jpeg, .jpg, .png, .webp
</ParamField>

<ParamField body="metadata" type="object">
  Нативные параметры расширения Vertex AI

  <Expandable title="Показать поля metadata">
    <ParamField body="temperature" type="number">
      Температура генерации, контролирует случайность вывода

      Диапазон: `0.0` - `2.0`
    </ParamField>

    <ParamField body="topP" type="number">
      Параметр Top-P сэмплирования

      Диапазон: `0.0` - `1.0`, по умолчанию `0.95`
    </ParamField>

    <ParamField body="maxOutputTokens" type="integer">
      Максимальное количество выходных токенов

      По умолчанию `32768`
    </ParamField>

    <ParamField body="resolution" type="string">
      Разрешение выходного изображения, автоматически преобразуется в imageSize Vertex AI

      Варианты: `1K`, `2K`, `4K`, по умолчанию `1K`
    </ParamField>

    <ParamField body="personGeneration" type="string">
      Управление генерацией людей

      Варианты:

      * `ALLOW_ALL` — разрешить генерацию всех людей (включая взрослых и детей)
      * `ALLOW_ADULT` — разрешить только взрослых
      * `ALLOW_NONE` — запретить генерацию людей
    </ParamField>

    <ParamField body="imageOutputOptions" type="object">
      Конфигурация формата выходного изображения

      <Expandable title="Поля imageOutputOptions">
        <ParamField body="mimeType" type="string">
          Формат выходного изображения

          Варианты: `image/png`, `image/jpeg`, `image/webp`
        </ParamField>

        <ParamField body="compressionQuality" type="integer">
          Качество сжатия (только для JPEG)
        </ParamField>
      </Expandable>
    </ParamField>

    <ParamField body="thinkingConfig" type="object">
      Конфигурация режима мышления. При включении модель сначала рассуждает, а затем генерирует изображение

      <Expandable title="Поля thinkingConfig">
        <ParamField body="thinkingBudget" type="integer">
          Бюджет токенов мышления, контролирует глубину рассуждений модели

          Диапазон: `0` - `24576`, по умолчанию определяется моделью
        </ParamField>

        <ParamField body="thinkingLevel" type="string">
          Уровень мышления

          Варианты: `LOW`, `MEDIUM`, `HIGH`, `MINIMAL`
        </ParamField>
      </Expandable>
    </ParamField>

    <ParamField body="safetySettings" type="array">
      Массив настроек безопасности для фильтрации контента

      <Expandable title="Элементы safetySettings">
        <ParamField body="category" type="string">
          Категория безопасности

          Варианты: `HARM_CATEGORY_HATE_SPEECH`, `HARM_CATEGORY_DANGEROUS_CONTENT`, `HARM_CATEGORY_SEXUALLY_EXPLICIT`, `HARM_CATEGORY_HARASSMENT`
        </ParamField>

        <ParamField body="threshold" type="string">
          Порог фильтрации

          Варианты: `OFF`, `BLOCK_LOW_AND_ABOVE`, `BLOCK_MEDIUM_AND_ABOVE`, `BLOCK_ONLY_HIGH`
        </ParamField>
      </Expandable>
    </ParamField>
  </Expandable>
</ParamField>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для запроса статуса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Используемое название модели
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди на обработку
  * `in_progress` - Обрабатывается
  * `completed` - Успешно завершена
  * `failed` - Ошибка
</ResponseField>

<ResponseField name="progress" type="integer">
  Процент выполнения задачи (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Время создания задачи (Unix timestamp)
</ResponseField>

<ResponseField name="metadata" type="object">
  Метаданные задачи
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gemini-3-pro-image-preview-official",
      "prompt": "Футуристический городской горизонт с неоновыми огнями в стиле киберпанк",
      "size": "16:9",
      "n": 1,
      "metadata": {
        "temperature": 1.0,
        "topP": 0.95,
        "resolution": "2K",
        "personGeneration": "ALLOW_ALL",
        "thinkingConfig": {
          "thinkingLevel": "HIGH"
        }
      }
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/images/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "gemini-3-pro-image-preview-official",
          "prompt": "Футуристический городской горизонт с неоновыми огнями в стиле киберпанк",
          "size": "16:9",
          "n": 1,
          "metadata": {
              "temperature": 1.0,
              "topP": 0.95,
              "resolution": "2K",
              "personGeneration": "ALLOW_ALL",
              "thinkingConfig": {
                  "thinkingLevel": "HIGH"
              }
          }
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/images/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'gemini-3-pro-image-preview-official',
      prompt: 'Футуристический городской горизонт с неоновыми огнями в стиле киберпанк',
      size: '16:9',
      n: 1,
      metadata: {
        temperature: 1.0,
        topP: 0.95,
        resolution: '2K',
        personGeneration: 'ALLOW_ALL',
        thinkingConfig: {
          thinkingLevel: 'HIGH'
        }
      }
    })
  });

  const task = await response.json();
  console.log(`ID задачи: ${task.id}`);
  console.log(`Статус: ${task.status}`);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "gemini-3-pro-image-preview-official",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
