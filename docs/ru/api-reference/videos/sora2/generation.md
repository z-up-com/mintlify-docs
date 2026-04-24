# Sora2 Генерация видео

> Генерация высококачественного видео с помощью модели OpenAI Sora2

* Модель генерации видео OpenAI Sora2
* Используйте параметр model для выбора `sora-2` или `sora-2-pro`
* Поддержка текст-в-видео, изображение-в-видео и ссылок на персонажей
* Асинхронное управление задачами, запрос результатов по ID задачи

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 напрямую в `image_urls`. Пожалуйста, сначала используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений и получения URL, а затем вызывайте этот эндпоинт.
</Warning>

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

<ParamField body="model" type="string" default="sora-2" required>
  Название модели генерации видео

  Поддерживаемые модели:

  * `sora-2` - Стандартная версия
  * `sora-2-pro` - Профессиональная версия, поддерживает большую длительность
  * `sora-2-vip` - VIP версия, более высокий приоритет

  Пример: `"sora-2"` или `"sora-2-pro"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации видео

  **💡 Функция ссылки на персонажей:**

  Вы можете использовать формат `@username` в промпте для ссылки на ранее созданных персонажей и включения их в генерируемое видео.

  * `username` персонажа можно получить через [API запроса персонажей](/ru/api-reference/videos/sora2/get-personas)
  * Поддерживается одновременная ссылка на несколько персонажей

  Пример: `"Кошка и собака вместе едут на машине @duksvfkf.cruisingki @zdqwahgj.baronbarki"`
</ParamField>

<ParamField body="duration" type="integer">
  Длительность видео в секундах

  * `sora-2`: Поддерживает 10 или 15 секунд
  * `sora-2-pro`: Поддерживает 15 секунд (HD) или 25 секунд

  Пример: `10`
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Соотношение сторон видео

  Поддерживаемые форматы:

  * `16:9` (Горизонтальный)
  * `9:16` (Вертикальный)
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL референсных изображений для генерации изображение-в-видео

  **⚠️ Только формат URL (base64 больше не поддерживается)**

  * Публично доступный URL изображения (http\:// или https\://)
  * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL
</ParamField>

<ParamField body="thumbnail" type="boolean">
  Генерировать ли миниатюру видео

  * `true` - Генерировать миниатюру видео для предварительного просмотра и отображения

  Если миниатюра не нужна, можно опустить этот параметр
</ParamField>

<ParamField body="metadata.n" type="integer" default="1">
  Количество генерируемых различных вариантов

  Диапазон: 1-4

  По умолчанию: `1`
</ParamField>

<ParamField body="metadata.watermark" type="boolean" default="false">
  Добавлять ли водяной знак к генерируемому видео

  * `false` - Без водяного знака
  * `true` - Добавить официальный водяной знак Sora к видео

  По умолчанию: `false`
</ParamField>

<ParamField body="metadata.hd" type="boolean" default="false">
  Генерировать ли видео высокой четкости

  * `true` - Генерировать HD видео
  * `false` - Стандартное разрешение

  **⚠️ Примечание:** Требуется модель `sora-2-pro` и длительность не может быть 25 секунд

  По умолчанию: `false`
</ParamField>

<ParamField body="metadata.private" type="boolean" default="false">
  Включить ли режим конфиденциальности

  * `true` - Видео не будет опубликовано и не может быть переделано (отредактировано повторно)
  * `false` - Видео может быть опубликовано и переделано

  По умолчанию: `false`
</ParamField>

<ParamField body="metadata.style" type="string">
  Стиль видео

  Поддерживаемые значения:

  * `thanksgiving` - Стиль День благодарения
  * `comic` - Комикс стиль
  * `news` - Новостной стиль
  * `selfie` - Селфи стиль
  * `nostalgic` - Ностальгический/Ретро стиль
  * `anime` - Аниме стиль

  Пример: `"anime"`
</ParamField>

<ParamField body="metadata.storyboard" type="boolean">
  Использовать ли раскадровку для более точного контроля деталей генерации видео

  * `true` - Включить функцию раскадровки
  * `false` - Не использовать раскадровку

  Пример: `true`
</ParamField>

<ParamField body="metadata.character_url" type="string">
  URL референсного видео для извлечения персонажа

  Используется для повторного использования персонажей из референсного видео в генерируемом видео

  Пример: `"https://filesystem.site/cdn/20251030/javYrU4etHVFDqg8by7mViTWHlMOZy.mp4"`

  **⚠️ Примечание:** При использовании этого параметра длительность генерируемого видео уменьшится на 1 секунду
</ParamField>

<ParamField body="metadata.character_timestamps" type="string">
  Временные метки появления персонажа

  Указывает временной диапазон в референсном видео, где появляется персонаж, поддерживает только 2-секундные сегменты

  Формат: `"начальная_секунда,конечная_секунда"`

  Пример: `"1,3"` означает персонажа с 1-й по 3-ю секунду в референсном видео

  **⚠️ Примечание:** При использовании этого параметра длительность генерируемого видео уменьшится на 1 секунду
</ParamField>

<ParamField body="metadata.character_create" type="boolean" default="false">
  Автоматически создать персонажа из сгенерированного видео после завершения

  * `true` - Автоматически создать персонажа
  * `false` - Не создавать персонажа

  По умолчанию: `false`
</ParamField>

<ParamField body="metadata.character_from_task" type="string">
  Создать персонажа из уже сгенерированного ID задачи

  Ссылка на ранее сгенерированный ID задачи видео для извлечения персонажей из этого видео

  Пример: `"video_01K8SGYNNNVBQTXNR4MM964S7K"`
</ParamField>

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для запроса статуса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Используемое имя модели
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди на обработку
  * `in_progress` - Обработка
  * `completed` - Успешно завершено
  * `failed` - Не удалось
</ResponseField>

<ResponseField name="progress" type="integer">
  Процент выполнения задачи (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Временная метка создания задачи (временная метка Unix)
</ResponseField>

<ResponseField name="metadata" type="object">
  Метаданные задачи
</ResponseField>

<RequestExample>
  ```bash cURL (Текст-в-видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "Золотистый ретривер бежит по траве, солнечный день",
      "duration": 10,
      "aspect_ratio": "16:9"
    }'
  ```

  ```bash cURL (Изображение-в-видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "Оживите эту собаку, бегущую по траве",
      "image_urls": ["https://example.com/dog.jpg"],
      "duration": 10,
      "aspect_ratio": "16:9"
    }'
  ```

  ```bash cURL (Со стилем) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "Милый мультяшный слон",
      "aspect_ratio": "16:9",
      "duration": 10,
      "metadata": {
        "style": "anime",
        "watermark": false
      }
    }'
  ```

  ```bash cURL (Со ссылкой на персонажа) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2",
      "prompt": "Кошка и собака вместе едут на машине @duksvfkf.cruisingki @zdqwahgj.baronbarki",
      "duration": 10,
      "aspect_ratio": "16:9"
    }'
  ```

  ```python Python (с metadata) theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2",
          "prompt": "Золотистый ретривер бежит по траве, солнечный день",
          "duration": 10,
          "aspect_ratio": "16:9",
          "metadata": {
              "style": "anime",
              "watermark": False,
              "n": 2
          }
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```javascript JavaScript (с metadata) theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2',
      prompt: 'Золотистый ретривер бежит по траве, солнечный день',
      duration: 10,
      aspect_ratio: '16:9',
      metadata: {
        style: 'anime',
        watermark: false,
        n: 2
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
    "id": "video_01K8SGYNNNVBQTXNR4MM964S7K",
    "object": "generation.task",
    "model": "sora-2",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {
      "size": "720x720"
    }
  }
  ```
</ResponseExample>
