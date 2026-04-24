# Doubao SeeDance Генерация видео

> Генерация видео с помощью модели ByteDance Doubao SeeDance

* Модель генерации видео ByteDance Doubao
* Модели: `doubao-seedance-1-0-pro-fast` или `doubao-seedance-1-0-pro-quality`
* Поддержка контроля первого/последнего/референсного кадра
* Асинхронное управление задачами

<Warning>
  **Важное изменение**: Для повышения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 напрямую в `image_urls`. Пожалуйста, сначала используйте [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений и получения URL, а затем вызывайте этот эндпоинт.
</Warning>

## Тело запроса

<ParamField body="model" type="string" default="doubao-seedance-1-0-pro-fast" required>
  Модели:

  * `doubao-seedance-1-0-pro-fast` - быстрая версия
  * `doubao-seedance-1-0-pro-quality` - качественная версия
</ParamField>

<ParamField body="prompt" type="string" required>
  Описание содержимого видео

  Подробно опишите сцены, действия, стили для лучших результатов генерации

  Пример: `"Закат на пляже, золотой солнечный свет на море, волны мягко ударяются о песок"`
</ParamField>

<ParamField body="duration" type="integer" default={5}>
  Продолжительность видео (секунды)

  Поддерживаемый диапазон: `2` \~ `12` секунд

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

<ParamField body="resolution" type="string" default="720p">
  Разрешение видео

  Варианты:

  * `480p` - Стандартное разрешение
  * `720p` - Высокое разрешение
  * `1080p` - Full HD

  По умолчанию: `720p`

  **Ограничение 1080p**: При использовании референсных изображений (`role: reference` в `image_with_roles`) разрешение 1080p не поддерживается
</ParamField>

## Комбинации разрешения и соотношения сторон

| Разрешение | Поддерживаемые соотношения      | Примечания                                |
| ---------- | ------------------------------- | ----------------------------------------- |
| 480p       | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | Все поддерживаются                        |
| 720p       | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | Все поддерживаются                        |
| 1080p      | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | Референсное изображение не поддерживается |

<ParamField body="image_urls" type="string[]">
  Массив URL-адресов первого кадра для генерации изображения в видео

  **⚠️ Только формат URL (base64 больше не поддерживается)**

  * Публично доступный URL изображения (http\:// или https\://)
  * Вы можете использовать [API загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений и получения URL

  Пример: `["https://example.com/cat.png"]`

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

      * `first_frame` - Первый кадр, как начальный кадр видео (только одно изображение)
      * `last_frame` - Последний кадр, как конечный кадр видео (**только версия quality**, только одно изображение)
      * `reference` - Референсное изображение для управления стилем видео (только одно изображение)
    </ParamField>
  </Expandable>

  Пример:

  ```json theme={null}
  [
    {"url": "https://example.com/start.png", "role": "first_frame"},
    {"url": "https://example.com/end.png", "role": "last_frame"}
  ]
  ```

  <Warning>
    * `image_urls` и `image_with_roles` нельзя использовать вместе
    * Для каждой роли поддерживается только одно изображение
    * `last_frame` поддерживается только версией `doubao-seedance-1-0-pro-quality`, fast версия не поддерживает одновременное использование первого и последнего кадра
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Дополнительные параметры (опционально)

  <Expandable title="Описание полей">
    <ParamField body="seed" type="integer">
      Целое число seed для контроля случайности генерации

      Диапазон: `-1` \~ `2^32-1`

      Одинаковое значение seed дает похожие результаты, но не гарантирует идентичность
    </ParamField>
  </Expandable>
</ParamField>

<RequestExample>
  ```bash cURL (Быстрый просмотр горизонтального видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-0-pro-fast",
      "prompt": "Закат на пляже, золотой солнечный свет на море, волны мягко ударяются о песок",
      "duration": 5,
      "aspect_ratio": "16:9",
      "resolution": "720p"
    }'
  ```

  ```bash cURL (Высококачественное вертикальное короткое видео) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-0-pro-quality",
      "prompt": "Девушка кружится под цветущими вишневыми деревьями, лепестки падают с ветром",
      "duration": 5,
      "aspect_ratio": "9:16",
      "resolution": "1080p"
    }'
  ```

  ```bash cURL (Эффект динамического перехода - Первый/Последний кадр) theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "doubao-seedance-1-0-pro-quality",
      "prompt": "Сцена переходит от дня к ночи, городские огни постепенно включаются",
      "duration": 5,
      "image_with_roles": [
        {"url": "https://example.com/day.png", "role": "first_frame"},
        {"url": "https://example.com/night.png", "role": "last_frame"}
      ]
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
          "model": "doubao-seedance-1-0-pro-fast",
          "prompt": "Закат на пляже, золотой солнечный свет на море, волны мягко ударяются о песок",
          "duration": 5,
          "aspect_ratio": "16:9",
          "resolution": "720p"
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
      model: 'doubao-seedance-1-0-pro-fast',
      prompt: 'Закат на пляже, золотой солнечный свет на море, волны мягко ударяются о песок',
      duration: 5,
      aspect_ratio: '16:9',
      resolution: '720p'
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
    "model": "doubao-seedance-1-0-pro-fast",
    "status": "queued",
    "progress": 0,
    "created_at": 1703884800,
    "metadata": {}
  }
  ```
</ResponseExample>
