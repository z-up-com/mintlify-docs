# GPT-Image-2 Генерация изображений

> Генерация изображений с помощью gpt-image-2, поддержка text-to-image и generation по reference_images

* Унифицированный API генерации изображений
* Выбор модели `gpt-image-2` через параметр `model`
* Поддержка text-to-image, single-reference и multi-reference generation
* Асинхронное управление задачами с получением результата по task ID

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат base64 больше не поддерживается в `image_urls` и `reference_images`. Сначала используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images), затем передайте полученный URL.
</Warning>

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все API требуют аутентификации Bearer Token

  Получите API Key на [странице управления API ключами](https://toapis.com/console/token)

  Добавьте в заголовок:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="model" type="string" default="gpt-image-2" required>
  Название модели генерации изображений

  Пример: `"gpt-image-2"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовый промпт для генерации изображения

  Максимум 4000 символов
</ParamField>

<ParamField body="size" type="string" default="1024x1024">
  Размер выходного изображения

  Популярные значения:

  * `1024x1024`
  * `1536x1024`
  * `1024x1536`
</ParamField>

<ParamField body="n" type="integer" default={1}>
  Количество изображений

  По умолчанию: 1
</ParamField>

<ParamField body="response_format" type="string" default="url">
  Формат ответа

  API возвращает URL изображения, рекомендуемое значение: `url`
</ParamField>

<ParamField body="reference_images" type="string[]">
  URL эталонных изображений для image-to-image

  **⚠️ Только URL (base64 больше не поддерживается)**

  * Публично доступные URL (`http://` или `https://`)
  * Можно использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных файлов
  * Поддерживаются одиночные и множественные reference images
</ParamField>

<ParamField body="image_urls" type="string[]">
  Поле обратной совместимости

  В ToAPIs автоматически нормализуется в `reference_images`
</ParamField>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Используемая модель
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued`
  * `in_progress`
  * `completed`
  * `failed`
</ResponseField>

<ResponseField name="progress" type="integer">
  Прогресс задачи (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Время создания задачи (Unix timestamp)
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-image-2",
      "prompt": "Сгенерируй постер ночного города будущего с неоном и кинематографичной композицией",
      "n": 1,
      "size": "1024x1024",
      "response_format": "url"
    }'
  ```

  ```bash cURL (Image-to-Image) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/images/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "gpt-image-2",
      "prompt": "Сохрани основную композицию и стилизуй изображение под киберпанк, усили свет и детали",
      "reference_images": [
        "https://example.com/source.png"
      ],
      "response_format": "url"
    }'
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "task_img_abc123def456",
    "object": "generation.task",
    "model": "gpt-image-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
