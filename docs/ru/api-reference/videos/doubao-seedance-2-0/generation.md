# Seedance 2.0 Генерация видео

> Генерация видео с помощью моделей ByteDance Seedance 2.0 и Seedance 2.0 Fast

* Видео-модели нового поколения от ByteDance
* Поддерживаются `doubao-seedance-2-0` и `doubao-seedance-2-0-fast`
* Поддерживаются text-to-video, генерация по первому кадру, по первому и последнему кадру, а также multimodal reference-to-video
* Поддерживаются референсные изображения, видео и аудио
* Поддерживаются генерация синхронного аудио, web search tools и возврат последнего кадра
* Асинхронный рабочий процесс с проверкой статуса по ID задачи

## Authorizations

<ParamField header="Authorization" type="string" required>
  Все запросы требуют аутентификацию Bearer Token

  Получите API Key на странице [API Key Management](https://toapis.com/console/token)

  Добавьте в заголовки запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Тело запроса

<ParamField body="model" type="string" default="doubao-seedance-2-0" required>
  Название модели генерации видео

  Доступные модели:

  * `doubao-seedance-2-0` - стандартная версия с упором на более высокое качество, поддерживает `4-15` секунд
  * `doubao-seedance-2-0-fast` - ускоренная версия для preview и iteration, поддерживает `4-12` секунд
</ParamField>

<ParamField body="prompt" type="string">
  Текстовое описание видео

  Поддерживаются запросы на китайском и английском. Чем точнее описание сцены, движения камеры, действий, стиля и аудио-атмосферы, тем лучше результат.

  Рекомендации:

  * Китайский текст лучше держать в пределах `500` символов
  * Английский текст лучше держать в пределах `1000` слов
  * Для ссылок на загруженные материалы используйте обозначения вида "image 1", "video 1", "audio 1"

  Пример: `"Используй video 1 для POV-композиции, начни с image 1, заверши image 2 и сохрани ритм и настроение из audio 1"`
</ParamField>

<ParamField body="duration" type="integer" default={5}>
  Длительность видео в секундах

  Допустимые значения:

  * `doubao-seedance-2-0`: `4-15`
  * `doubao-seedance-2-0-fast`: `4-12`
  * `-1`: автоматическая длительность, выбранная моделью

  <Warning>
    `doubao-seedance-2-0-fast` не поддерживает длительность больше `12` секунд.
  </Warning>
</ParamField>

<ParamField body="aspect_ratio" type="string" default="adaptive">
  Соотношение сторон видео

  Варианты:

  * `21:9`
  * `16:9`
  * `4:3`
  * `1:1`
  * `3:4`
  * `9:16`
  * `adaptive`

  Поведение `adaptive`:

  * Text-to-video: модель выбирает наиболее подходящее соотношение сторон по prompt
  * Генерация по первому кадру или по первому и последнему кадру: ориентируется на первый кадр
  * Multimodal reference-to-video: обычно приоритет у reference video, затем у reference image
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL изображений в режиме совместимости

  Для явного контроля ролей рекомендуется использовать `image_with_roles`.

  `image_urls` и `image_with_roles` не следует использовать одновременно.
</ParamField>

<ParamField body="image_with_roles" type="array">
  Массив изображений с ролями

  Поддерживаемые сценарии:

  * Генерация по первому кадру: один `first_frame`
  * Генерация по первому и последнему кадру: один `first_frame` и один `last_frame`
  * Multimodal reference-to-video: элементы `reference_image`, от `1` до `9`

  <Expandable title="Описание полей">
    <ParamField body="url" type="string" required>
      URL изображения, Base64-данные или URI загруженного ассета

      Поддерживаются:

      * `https://...`
      * `data:image/<format>;base64,...`
      * `asset://<ASSET_ID>`
    </ParamField>

    <ParamField body="role" type="string" required>
      Роль изображения

      Варианты:

      * `first_frame`
      * `last_frame`
      * `reference_image`
    </ParamField>
  </Expandable>

  Требования к изображениям:

  * Форматы: `jpeg`, `png`, `webp`, `bmp`, `tiff`, `gif`
  * Размер одного файла: меньше `30MB`
  * Общий размер запроса: рекомендуется не более `64MB`
  * Соотношение сторон: примерно от `0.4` до `2.5`
  * Размеры: примерно от `300px` до `6000px`

  <Warning>
    - Режимы first-frame и first-and-last-frame нельзя смешивать с `reference_image`, `reference_video` или `reference_audio`
    - Разрешен только один `first_frame` и один `last_frame`
    - В multimodal reference mode все изображения должны использовать роль `reference_image`
  </Warning>
</ParamField>

<ParamField body="video_with_roles" type="array">
  Массив видео с ролями

  Сейчас поддерживается только `reference_video` для multimodal reference mode.

  <Expandable title="Описание полей">
    <ParamField body="url" type="string" required>
      URL видео или URI загруженного ассета

      Поддерживаются:

      * `https://...`
      * `asset://<ASSET_ID>`
    </ParamField>

    <ParamField body="role" type="string" required>
      Фиксированное значение:

      * `reference_video`
    </ParamField>
  </Expandable>

  Требования к видео:

  * Форматы: `mp4`, `mov`
  * Разрешение: `480p` или `720p`
  * Длительность одного видео: `2-15` секунд
  * Максимум: `3` референсных видео
  * Суммарная длительность reference video: не более `15` секунд
  * Размер одного файла: меньше `50MB`
  * Частота кадров: примерно `24-60 FPS`
</ParamField>

<ParamField body="audio_with_roles" type="array">
  Массив аудио с ролями

  Сейчас поддерживается только `reference_audio` для multimodal reference mode.

  <Expandable title="Описание полей">
    <ParamField body="url" type="string" required>
      URL аудио, Base64-данные или URI загруженного ассета

      Поддерживаются:

      * `https://...`
      * `data:audio/<format>;base64,...`
      * `asset://<ASSET_ID>`
    </ParamField>

    <ParamField body="role" type="string" required>
      Фиксированное значение:

      * `reference_audio`
    </ParamField>
  </Expandable>

  Требования к аудио:

  * Форматы: `wav`, `mp3`
  * Длительность одного файла: `2-15` секунд
  * Максимум: `3` аудиореференса
  * Суммарная длительность reference audio: не более `15` секунд
  * Размер одного файла: меньше `15MB`

  <Warning>
    `audio_with_roles` нельзя использовать отдельно. Нужен хотя бы один image reference или video reference.
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Дополнительные параметры

  <Expandable title="Описание полей">
    <ParamField body="resolution" type="string" default="720p">
      Разрешение видео

      Варианты:

      * `480p`
      * `720p`
    </ParamField>

    <ParamField body="generate_audio" type="boolean" default={true}>
      Генерировать ли синхронное аудио

      * `true`: вернуть видео со звуком
      * `false`: вернуть немое видео

      Модель может синтезировать речь, звуковые эффекты и фоновую музыку по prompt и визуальным подсказкам.
    </ParamField>

    <ParamField body="return_last_frame" type="boolean" default={false}>
      Возвращать ли дополнительно последний кадр в результате
    </ParamField>

    <ParamField body="tools" type="array">
      Конфигурация инструментов

      Сейчас поддерживается:

      * `[{ "type": "web_search" }]`

      Примечания:

      * Лучше всего подходит для text-to-video
      * Модель ищет только при необходимости, что может повысить актуальность, но добавить задержку
    </ParamField>

    <ParamField body="seed" type="integer">
      Random seed для управления генерацией
    </ParamField>
  </Expandable>
</ParamField>

## Правила комбинации входных данных

Типовые поддерживаемые комбинации:

* Только текст: text-to-video
* Текст + один first-frame image: генерация по первому кадру
* Текст + first-frame image + last-frame image: генерация по первому и последнему кадру
* Текст + reference images: multimodal reference-to-video
* Текст + reference videos: reference-guided video generation
* Текст + reference images + reference audio: multimodal reference-to-video
* Текст + reference images + reference videos + reference audio: multimodal reference-to-video

<Warning>
  Эти три режима взаимоисключающие:

  * Генерация по первому кадру
  * Генерация по первому и последнему кадру
  * Multimodal reference-to-video

  Если нужен строгий контроль первого и последнего кадра, используйте `first_frame` и `last_frame`. Если важнее общее референсное управление, используйте `reference_image`, `reference_video` и `reference_audio`.
</Warning>

## Соответствие разрешения и соотношения сторон

| Разрешение | Соотношение сторон | Размер в пикселях |
| ---------- | ------------------ | ----------------- |
| `480p`     | `16:9`             | `864x496`         |
| `480p`     | `4:3`              | `752x560`         |
| `480p`     | `1:1`              | `640x640`         |
| `480p`     | `3:4`              | `560x752`         |
| `480p`     | `9:16`             | `496x864`         |
| `480p`     | `21:9`             | `992x432`         |
| `720p`     | `16:9`             | `1280x720`        |
| `720p`     | `4:3`              | `1112x834`        |
| `720p`     | `1:1`              | `960x960`         |
| `720p`     | `3:4`              | `834x1112`        |
| `720p`     | `9:16`             | `720x1280`        |
| `720p`     | `21:9`             | `1470x630`        |

## Возможности и ограничения

| Параметр         | Seedance 2.0                                     | Seedance 2.0 Fast                                |
| ---------------- | ------------------------------------------------ | ------------------------------------------------ |
| Позиционирование | Более высокое качество                           | Более быстрая генерация и ниже стоимость         |
| Длительность     | `4-15` секунд или `-1` auto                      | `4-12` секунд или `-1` auto                      |
| Разрешение       | `480p` / `720p`                                  | `480p` / `720p`                                  |
| Роли изображений | `first_frame` / `last_frame` / `reference_image` | `first_frame` / `last_frame` / `reference_image` |
| Роли видео       | `reference_video`                                | `reference_video`                                |
| Роли аудио       | `reference_audio`                                | `reference_audio`                                |
| Генерация аудио  | `metadata.generate_audio`                        | `metadata.generate_audio`                        |
| Tools            | `metadata.tools`                                 | `metadata.tools`                                 |
| Последний кадр   | `metadata.return_last_frame`                     | `metadata.return_last_frame`                     |

<Info>
  Тарификация идет по секундам. Актуальная цена может отличаться в зависимости от версии модели, разрешения и стратегии отображения платформы. Проверяйте [страницу цен моделей](https://toapis.com/models).
</Info>

## Response

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для проверки статуса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Использованная модель
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди
  * `in_progress` - В обработке
  * `completed` - Успешно завершена
  * `failed` - Ошибка
</ResponseField>

<ResponseField name="progress" type="integer">
  Процент выполнения задачи (`0-100`)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Время создания задачи (Unix timestamp)
</ResponseField>

<ResponseField name="metadata" type="object">
  Метаданные задачи
</ResponseField>

<Info>
  Генерация видео выполняется асинхронно. При создании задачи вы получаете task ID, а затем можете использовать [Get Video Task Status](/ru/api-reference/tasks/video-status) для опроса прогресса и результата.
</Info>

<RequestExample>
  ```bash cURL (Multimodal reference-to-video) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0",
      "prompt": "Используй video 1 для POV-композиции, audio 1 как музыкальную подложку, стартуй с image 1 и завершайся на image 2 в стиле свежего рекламного ролика.",
      "duration": 11,
      "aspect_ratio": "16:9",
      "image_with_roles": [
        {"url": "https://example.com/ref-image-1.jpg", "role": "reference_image"},
        {"url": "https://example.com/ref-image-2.jpg", "role": "reference_image"}
      ],
      "video_with_roles": [
        {"url": "https://example.com/ref-video-1.mp4", "role": "reference_video"}
      ],
      "audio_with_roles": [
        {"url": "https://example.com/ref-audio-1.mp3", "role": "reference_audio"}
      ],
      "metadata": {
        "resolution": "720p",
        "generate_audio": true
      }
    }'
  ```

  ```bash cURL (Генерация по первому и последнему кадру) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0",
      "prompt": "Камера медленно уходит от букета у окна к центру стола, с теплым и мягким освещением.",
      "duration": 5,
      "aspect_ratio": "adaptive",
      "image_with_roles": [
        {"url": "https://example.com/first-frame.png", "role": "first_frame"},
        {"url": "https://example.com/last-frame.png", "role": "last_frame"}
      ],
      "metadata": {
        "resolution": "720p",
        "generate_audio": false,
        "return_last_frame": true
      }
    }'
  ```

  ```bash cURL (Text-to-video с web search) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0",
      "prompt": "Макросъемка стеклянной лягушки на листе, фокус плавно смещается к прозрачному брюшку и видимому биению сердца, стиль документального кино.",
      "duration": 11,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720p",
        "generate_audio": true,
        "tools": [{"type": "web_search"}]
      }
    }'
  ```

  ```bash cURL (Fast preview) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-2-0-fast",
      "prompt": "Сиба-ину бежит по неоновой улице после дождя, а мокрый асфальт отражает цветные огни.",
      "duration": 5,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720p"
      }
    }'
  ```

  ```python Python theme={null}
  import requests

  response = requests.post(
      "https://toapis.com/v1/videos/generations",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json",
      },
      json={
          "model": "doubao-seedance-2-0",
          "prompt": "Плавный проезд камеры из интерьера кафе на городскую улицу, с сохранением ритма речи и атмосферы из референса",
          "duration": -1,
          "aspect_ratio": "adaptive",
          "image_with_roles": [
              {"url": "https://example.com/ref.png", "role": "reference_image"}
          ],
          "metadata": {
              "resolution": "720p",
              "generate_audio": True,
              "tools": [{"type": "web_search"}],
          },
      },
  )

  print(response.json())
  ```
</RequestExample>
