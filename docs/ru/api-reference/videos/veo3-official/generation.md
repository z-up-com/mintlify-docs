# Veo3 Official Генерация видео

> Генерация высококачественного видео с использованием моделей Google Veo3 Official с прямым подключением, поддержка генерации аудио, управления ключевыми кадрами и других расширенных функций

* Асинхронный режим обработки, возвращает ID задачи для последующего запроса статуса
* Прямое подключение к Google Vertex AI с поддержкой всех нативных параметров Veo
* Поддержка генерации видео из текста, из изображения и интерполяции ключевых кадров
* Сгенерированные ссылки на видео действительны в течение 24 часов, пожалуйста, сохраните их заблаговременно

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

<ParamField body="model" type="string" default="Veo3.1-quality-official" required>
  Название модели генерации видео

  Доступные модели:

  * `Veo3.1-quality-official` - Модель высококачественной генерации, идеальна для финального производства
  * `Veo3.1-fast-official` - Модель быстрой генерации, идеальна для быстрого предпросмотра и итераций

  Пример: `"Veo3.1-quality-official"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовое описание для генерации видео
</ParamField>

<ParamField body="duration" type="integer">
  Длительность видео в секундах

  Поддерживаемые значения:

  * `4` - 4 секунды
  * `6` - 6 секунд
  * `8` - 8 секунд (по умолчанию)
</ParamField>

<ParamField body="size" type="string">
  Соотношение сторон видео

  Поддерживаемые форматы:

  * `16:9` (горизонтальное, по умолчанию)
  * `9:16` (вертикальное)
</ParamField>

<ParamField body="resolution" type="string">
  Разрешение видео

  Поддерживаемые значения:

  * `720p` (по умолчанию)
  * `1080p`
</ParamField>

<ParamField body="image_urls" type="string[]">
  **Первый кадр** для генерации видео из изображения. Видео будет начинаться с этого изображения. Используется только первый URL из массива.

  **⚠️ Поддерживается только формат URL (base64 больше не поддерживается)**

  * Публично доступные URL изображений (http\:// или https\://)
  * Используйте [эндпоинт загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

  **Ограничения:**

  * Максимальный размер файла: 10MB
  * Поддерживаемые форматы: .jpeg, .jpg, .png, .webp

  <Warning>
    `image_urls` и `metadata.referenceImages` **нельзя использовать одновременно**. Если указаны оба, `image_urls` (первый кадр) имеет приоритет, а `referenceImages` будет проигнорирован.
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Специальные параметры расширения Veo3 Official

  <Expandable title="Показать поля metadata">
    <ParamField body="generateAudio" type="boolean">
      Генерировать ли аудио. По умолчанию: `false`

      При включении видео будет содержать сгенерированные ИИ звуковые эффекты и окружающий звук
    </ParamField>

    <ParamField body="negativePrompt" type="string">
      Негативный промпт, описывающий контент, который вы не хотите видеть в видео

      Пример: `"размытый, низкое качество, искажённый"`
    </ParamField>

    <ParamField body="personGeneration" type="string">
      Настройка безопасности генерации людей

      Поддерживаемые значения:

      * `allow_adult` - Разрешить генерацию взрослых людей
      * `dont_allow` - Не разрешать генерацию людей
    </ParamField>

    <ParamField body="lastFrame" type="string">
      URL изображения последнего кадра для генерации видео с интерполяцией ключевых кадров

      **Обязательно** используется вместе с `image_urls` (первый кадр) для генерации переходного видео от первого кадра к последнему. Без `image_urls` не имеет эффекта.
    </ParamField>

    <ParamField body="referenceImages" type="string[]">
      Массив URL справочных изображений стиля/ассетов. Видео будет учитывать визуальный стиль и элементы из этих изображений (они НЕ используются как начальный кадр).

      **Ограничение: максимум 3 изображения**

      **⚠️ Нельзя использовать вместе с `image_urls`** — они взаимоисключающие в Veo API. Используйте `image_urls` для управления первым кадром.
    </ParamField>

    <ParamField body="compressionQuality" type="string">
      Качество сжатия видео

      Поддерживаемые значения:

      * `optimized` (по умолчанию) - Оптимизированное сжатие
      * `lossless` - Сжатие без потерь
    </ParamField>

    <ParamField body="resizeMode" type="string">
      Режим изменения размера изображения при генерации видео из изображения

      Поддерживаемые значения:

      * `pad` - Режим заполнения, сохраняет исходные пропорции
      * `crop` - Режим обрезки, заполняет весь кадр
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
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "Veo3.1-quality-official",
      "prompt": "Дельфин прыгает через кристально чистые голубые воды океана, брызги воды, солнечный свет блестит на поверхности",
      "duration": 8,
      "size": "16:9",
      "resolution": "1080p",
      "metadata": {
        "generateAudio": true,
        "negativePrompt": "размытый, низкое качество"
      }
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "Veo3.1-quality-official",
          "prompt": "Дельфин прыгает через кристально чистые голубые воды океана, брызги воды, солнечный свет блестит на поверхности",
          "duration": 8,
          "size": "16:9",
          "resolution": "1080p",
          "metadata": {
              "generateAudio": True,
              "negativePrompt": "размытый, низкое качество"
          }
      }
  )

  task = response.json()
  print(f"ID задачи: {task['id']}")
  print(f"Статус: {task['status']}")
  ```

  ```javascript JavaScript theme={null}
  const response = await fetch('https://toapis.com/v1/videos/generations', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'Veo3.1-quality-official',
      prompt: 'Дельфин прыгает через кристально чистые голубые воды океана, брызги воды, солнечный свет блестит на поверхности',
      duration: 8,
      size: '16:9',
      resolution: '1080p',
      metadata: {
        generateAudio: true,
        negativePrompt: 'размытый, низкое качество'
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
    "model": "Veo3.1-quality-official",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
