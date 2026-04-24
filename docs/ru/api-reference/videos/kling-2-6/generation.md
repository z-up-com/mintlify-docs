---
title: "Генерация видео Kling v2.6"
---
> Генерация высококачественных видео с помощью модели Kling v2.6, поддержка текст-в-видео и изображение-в-видео

* Асинхронный режим обработки, возвращает ID задачи для последующих запросов
* Поддержка текст-в-видео, изображение-в-видео (первый кадр / первый-последний кадр)
* Поддержка стандартного режима (720P) и профессионального режима (1080P)
* Профессиональный режим поддерживает автоматическую генерацию аудио

## Авторизация

<ParamField header="Authorization" type="string" required>
  Все конечные точки API требуют аутентификации с помощью Bearer Token

  Получите API Key: Посетите [страницу управления API Key](https://toapis.com/console/token)

  Добавьте в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Параметры запроса

<ParamField body="model" type="string" required>
  Название модели генерации видео, фиксированное значение `kling-2-6`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовый промпт, максимум **2500 символов**

  Подробно описывайте сцены, действия и стиль для лучших результатов

  Пример: `"Золотой кот бежит по залитому солнцем лугу, замедленная съёмка, кинематографическое качество"`
</ParamField>

<ParamField body="mode" type="string" default="std">
  Режим генерации

  Варианты:

  * `std` - Стандартный режим (720P, только видео без звука)
  * `pro` - Профессиональный режим (1080P, поддержка автоматической генерации аудио)

  По умолчанию: `std`

  <Warning>
    **Ограничение стандартного режима**: `std` поддерживает только видео без звука. Параметр `audio` требует режима `pro`.
  </Warning>
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Длительность видео (секунды)

  Варианты: `5` или `10`

  По умолчанию: `5`
</ParamField>

<ParamField body="aspect_ratio" type="string" default="16:9">
  Соотношение сторон видео

  Варианты:

  * `16:9` - Альбомная ориентация (по умолчанию)
  * `9:16` - Портретная ориентация
  * `1:1` - Квадрат

  По умолчанию: `16:9`
</ParamField>

<ParamField body="negative_prompt" type="string">
  Негативный промпт для исключения нежелательного контента

  Пример: `"размытый, низкое качество, искажённый"`
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL изображений для генерации изображение-в-видео

  * **1 изображение**: используется как первый кадр
  * **2 изображения**: автоматически назначаются как первый + последний кадр (требуется `mode: "pro"`)

  Максимум 2 изображения

  Пример: `["https://example.com/first.jpg"]`

  <Warning>
    * Максимум 2 изображения
    * Последний кадр (2 изображения) требует только режима `pro`; `std` поддерживает только первый кадр (1 изображение)
    * **Последний кадр и аудио взаимоисключающие**: в режиме `pro` нельзя использовать одновременно последний кадр и аудио
    * В режиме изображение-в-видео `aspect_ratio` может быть переопределён реальным соотношением сторон изображения
  </Warning>
</ParamField>

<ParamField body="audio" type="boolean" default="false">
  Автоматически генерировать аудио

  По умолчанию: `false`

  <Warning>
    * Доступно только в `mode: "pro"`
    * **Взаимоисключающее с последним кадром**: аудио нельзя использовать вместе с последним кадром (2 изображения)
  </Warning>
</ParamField>

<ParamField body="watermark" type="boolean">
  Добавить водяной знак
</ParamField>

## Тарификация

Оплата посекундная, цена зависит от режима и настройки аудио:

| Режим | Разрешение | Аудио             | Цена           |
| ----- | ---------- | ----------------- | -------------- |
| `std` | 720P       | Не поддерживается | \$0.0368 / сек |
| `pro` | 1080P      | Выкл              | \$0.0625 / сек |
| `pro` | 1080P      | Вкл               | \$0.125 / сек  |

Пример: режим `pro`, 10-секундное видео без звука = 10 × $0.0625 = $0.625

## Матрица поддержки функций

| Тип                 | Функция        | std 5s        | std 10s       | pro 5s | pro 10s |
| ------------------- | -------------- | ------------- | ------------- | ------ | ------- |
| Текст-в-видео       | Генерация      | ✅ (без звука) | ✅ (без звука) | ✅      | ✅       |
| Текст-в-видео       | Авто аудио     | -             | -             | ✅      | ✅       |
| Изображение-в-видео | Генерация      | ✅ (без звука) | ✅ (без звука) | ✅      | ✅       |
| Изображение-в-видео | Первый кадр    | ✅             | ✅             | ✅      | ✅       |
| Изображение-в-видео | Последний кадр | -             | -             | ✅      | ✅       |
| Изображение-в-видео | Авто аудио     | -             | -             | ✅      | ✅       |

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для запроса статуса
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, фиксированное значение `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Название использованной модели
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди на обработку
  * `in_progress` - Обрабатывается
  * `completed` - Успешно завершено
  * `failed` - Ошибка
</ResponseField>

<ResponseField name="progress" type="integer">
  Процент выполнения задачи (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Unix-временная метка создания задачи
</ResponseField>

## Примеры использования

### Сценарий 1: Текст-в-видео (стандартный режим)

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "Золотой кот бежит по залитому солнцем лугу, замедленная съёмка, кинематографическое качество",
  "mode": "std",
  "duration": 5,
  "aspect_ratio": "16:9"
}
```

### Сценарий 2: Текст-в-видео (профессиональный режим + негативный промпт)

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "Перекрёсток Сибуя в Токио ночью, неоновые огни отражаются на мокрой земле, люди идут с зонтами",
  "negative_prompt": "размытый, низкое качество, искажённый",
  "mode": "pro",
  "duration": 10,
  "aspect_ratio": "16:9"
}
```

### Сценарий 3: Изображение-в-видео (первый кадр)

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "Человек в кадре поворачивается и улыбается",
  "image_urls": ["https://example.com/portrait.jpg"],
  "mode": "std",
  "duration": 5
}
```

### Сценарий 4: Профессиональный режим + авто аудио

```json theme={null}
{
  "model": "kling-2-6",
  "prompt": "Волны разбиваются о скалы, чайки кружат в небе, вдали маяк",
  "mode": "pro",
  "duration": 10,
  "audio": true,
  "aspect_ratio": "16:9"
}
```

<Note>
  **Запрос результатов задачи**

  Генерация видео является асинхронной задачей, после отправки возвращается ID задачи. Используйте конечную точку [Получить статус задачи](/ru/api-reference/tasks/status) для запроса прогресса и результатов генерации.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "kling-2-6",
      "prompt": "Золотой кот бежит по залитому солнцем лугу, замедленная съёмка",
      "mode": "std",
      "duration": 5,
      "aspect_ratio": "16:9"
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
          "model": "kling-2-6",
          "prompt": "Золотой кот бежит по залитому солнцем лугу, замедленная съёмка",
          "mode": "std",
          "duration": 5,
          "aspect_ratio": "16:9"
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
      model: 'kling-2-6',
      prompt: 'Золотой кот бежит по залитому солнцем лугу, замедленная съёмка',
      mode: 'std',
      duration: 5,
      aspect_ratio: '16:9'
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
    "id": "video_01J9HA7JPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "kling-2-6",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
