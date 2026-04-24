> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Doubao SeeDance 1.5 Pro Генерация видео

> Генерация видео с помощью модели ByteDance Doubao SeeDance 1.5 Pro

* Модель генерации видео ByteDance Doubao версии 1.5 Pro
* Поддержка генерации текста в видео
* Поддержка контроля первого/последнего/многих референсных кадров (1-4 референсных изображения)
* Поддержка генерации аудио (эксклюзивная функция 1.5 Pro)
* Асинхронное управление задачами, запрос результатов по ID задачи

## Тело запроса

<ParamField body="model" type="string" default="doubao-seedance-1-5-pro" required>
  Модели:

  * `doubao-seedance-1-5-pro` - версия 1.5 Pro, поддерживает генерацию аудио и несколько референсных изображений
</ParamField>

<ParamField body="prompt" type="string" required>
  Описание содержимого видео

  Подробно опишите сцены, действия, стили для лучших результатов генерации

  Пример: `"Закат на пляже, золотой солнечный свет на море, волны мягко ударяются о песок"`
</ParamField>

<ParamField body="duration" type="integer" default={5}>
  Продолжительность видео (секунды)

  Поддерживаемый диапазон: `4` \~ `12` секунд

  По умолчанию: `5`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Соотношение сторон видео

  Варианты:

  * `16:9` - Горизонтальный
  * `9:16` - Вертикальный
  * `1:1` - Квадрат
  * `4:3` - Традиционное соотношение
  * `3:4` - Вертикальное традиционное соотношение
  * `21:9` - Ультраширокий

  По умолчанию: `16:9`
</ParamField>

## Комбинации разрешения и соотношения сторон

| Разрешение | Поддерживаемые соотношения      | Примечания                        |
| ---------- | ------------------------------- | --------------------------------- |
| 480p       | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | Все поддерживаются                |
| 720p       | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | Все поддерживаются (по умолчанию) |

<Warning>
  **Ограничение разрешения 1.5 Pro**: 1.5 Pro не поддерживает разрешение 1080p, максимум 720p
</Warning>

<ParamField body="image_urls" type="string[]">
  Массив URL-адресов изображений для генерации изображения в видео

  Правила автоматического назначения ролей:

  * 1 изображение = первый кадр
  * 2 изображения = первый кадр + последний кадр
  * 3+ изображений = первый кадр + последний кадр + референсные изображения

  Пример: `["https://example.com/first.png", "https://example.com/last.png"]`

  `image_urls` и `image_with_roles` нельзя использовать вместе
</ParamField>

<ParamField body="image_with_roles" type="array">
  Массив изображений с ролями для более точного контроля

  <Expandable title="Описание полей">
    <ParamField body="url" type="string" required>
      URL-адрес изображения
    </ParamField>

    <ParamField body="role" type="string" required>
      Роль изображения

      Варианты:

      * `first_frame` - Первый кадр, как начальный кадр видео (поддерживается только один)
      * `last_frame` - Последний кадр, как конечный кадр видео (поддерживается только один)
      * `reference_image` - Референсное изображение для управления стилем видео (поддерживает 1-4 изображения)
    </ParamField>
  </Expandable>

  Пример:

  ```json theme={null}
  [
    {"url": "https://example.com/start.png", "role": "first_frame"},
    {"url": "https://example.com/end.png", "role": "last_frame"},
    {"url": "https://example.com/ref1.png", "role": "reference_image"},
    {"url": "https://example.com/ref2.png", "role": "reference_image"}
  ]
  ```

  <Warning>
    * `image_urls` и `image_with_roles` нельзя использовать вместе
    * Первый и последний кадры поддерживают только одно изображение каждый
    * Референсные изображения используют роль `reference_image` (Примечание: версия 1.0 использует `reference`)
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Дополнительные параметры

  <Expandable title="Описание полей">
    <ParamField body="resolution" type="string" default="720p">
      Разрешение видео

      Варианты:

      * `480p` - Стандартное разрешение
      * `720p` - Высокое разрешение (по умолчанию)

      1.5 Pro не поддерживает 1080p
    </ParamField>

    <ParamField body="seed" type="integer">
      Целое число seed для контроля случайности генерации

      Диапазон: `-1` \~ `2^32-1`

      Одинаковое значение seed дает похожие результаты, но не гарантирует идентичность
    </ParamField>

    <ParamField body="audio" type="boolean" default={false}>
      Генерировать ли аудио

      При установке `true` видео будет содержать AI-генерированное сопровождающее аудио

      **Эксклюзивная функция 1.5 Pro**
    </ParamField>

    <ParamField body="camerafixed" type="boolean" default={false}>
      Фиксировать ли положение камеры

      При установке `true` положение камеры остается фиксированным
    </ParamField>
  </Expandable>
</ParamField>

## Отличия от версии 1.0

| Функция                       | 1.0 fast/quality       | 1.5 Pro                            |
| ----------------------------- | ---------------------- | ---------------------------------- |
| Разрешение по умолчанию       | 1080p                  | **720p**                           |
| Поддерживаемые разрешения     | 480p/720p/1080p        | **480p/720p**                      |
| Диапазон продолжительности    | 2-12с                  | **4-12с**                          |
| Генерация аудио               | Не поддерживается      | **Поддерживается**                 |
| Роль референсного изображения | `reference` (1 изобр.) | **`reference_image` (1-4 изобр.)** |

<RequestExample>
  ```bash cURL (Текст в видео с аудио) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-5-pro",
      "prompt": "Закат на пляже, золотой солнечный свет на море, волны мягко ударяются о песок",
      "duration": 5,
      "aspect_ratio": "16:9",
      "metadata": {
        "resolution": "720p",
        "audio": true
      }
    }'
  ```

  ```bash cURL (Высококачественное вертикальное короткое видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-5-pro",
      "prompt": "Девушка кружится под цветущими вишневыми деревьями, лепестки падают с ветром",
      "duration": 5,
      "aspect_ratio": "9:16",
      "metadata": {
        "resolution": "720p",
        "audio": true
      }
    }'
  ```

  ```bash cURL (Контроль стиля с несколькими референсами) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-5-pro",
      "prompt": "Генерация видео в стиле референсных изображений, сохранение единого визуального эффекта",
      "image_with_roles": [
        {"url": "https://example.com/first.png", "role": "first_frame"},
        {"url": "https://example.com/ref1.png", "role": "reference_image"},
        {"url": "https://example.com/ref2.png", "role": "reference_image"}
      ],
      "metadata": {
        "audio": true
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
          "model": "doubao-seedance-1-5-pro",
          "prompt": "Закат на пляже, золотой солнечный свет на море, волны мягко ударяются о песок",
          "duration": 5,
          "aspect_ratio": "16:9",
          "metadata": {
              "resolution": "720p",
              "audio": True
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
      model: 'doubao-seedance-1-5-pro',
      prompt: 'Закат на пляже, золотой солнечный свет на море, волны мягко ударяются о песок',
      duration: 5,
      aspect_ratio: '16:9',
      metadata: {
        resolution: '720p',
        audio: true
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
    "id": "task_vid_xyz789ghi012",
    "object": "generation.task",
    "model": "doubao-seedance-1-5-pro",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
