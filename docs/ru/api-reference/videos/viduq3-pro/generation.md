> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Генерация видео Vidu Q3 Pro

> Генерация высококачественных видео с помощью Vidu Q3 Pro, поддержка текст-в-видео, изображение-в-видео и контроля первого-последнего кадра

* Асинхронный режим обработки, возвращает ID задачи для последующих запросов
* Поддержка текст-в-видео, изображение-в-видео, генерация первого-последнего кадра
* Поддержка разрешений 540p / 720p / 1080p
* Диапазон длительности 1-16 секунд, аудио включено по умолчанию

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
  Название модели генерации видео, фиксированное значение `viduq3-pro`
</ParamField>

<ParamField body="prompt" type="string" required>
  Текстовый промпт, максимум **2000 символов**

  Обязателен для текст-в-видео. Необязателен для режимов изображение-в-видео и первый-последний кадр.

  Пример: `"Кот играет на пианино, камера медленно приближается"`
</ParamField>

<ParamField body="duration" type="integer" default="5">
  Длительность видео (секунды)

  Диапазон: `1` до `16`

  По умолчанию: `5`
</ParamField>

<ParamField body="resolution" type="string" default="720p">
  Разрешение видео

  Варианты:

  * `540p` - Стандартное разрешение
  * `720p` - Высокое разрешение (по умолчанию)
  * `1080p` - Полное высокое разрешение

  По умолчанию: `720p`
</ParamField>

<ParamField body="aspect_ratio" type="string">
  Соотношение сторон видео (только для режима текст-в-видео)

  Варианты:

  * `16:9` - Альбомная
  * `9:16` - Портретная
  * `4:3` - Традиционная альбомная
  * `3:4` - Традиционная портретная
  * `1:1` - Квадрат

  <Warning>
    Этот параметр доступен только в режиме текст-в-видео (когда `image_urls` не предоставлен). В режиме изображение-в-видео соотношение сторон определяется автоматически по изображению.
  </Warning>
</ParamField>

<ParamField body="image_urls" type="string[]">
  Массив URL изображений для режима изображение-в-видео

  Система автоматически определяет режим генерации по количеству изображений:

  * **0 изображений** (не предоставлено): Режим текст-в-видео
  * **1 изображение**: Режим изображение-в-видео (изображение как начальный кадр)
  * **2 изображения**: Режим первого-последнего кадра (первое = первый кадр, второе = последний кадр)

  Пример: `["https://example.com/photo.jpg"]`

  <Warning>
    * Максимум 2 изображения
    * Для режима первого-последнего кадра необходимо предоставить ровно 2 изображения
    * При предоставлении `image_urls` параметр `aspect_ratio` недоступен
  </Warning>
</ParamField>

<ParamField body="audio" type="boolean" default="true">
  Генерировать аудио (диалоги, звуковые эффекты)

  По умолчанию: `true`

  Установите `false` для видео без звука.
</ParamField>

<ParamField body="seed" type="integer">
  Случайное зерно для воспроизводимых результатов

  Использование одного и того же зерна с идентичными параметрами даст одинаковый результат.
</ParamField>

## Автоматическая маршрутизация

| Количество изображений | Режим                 | Описание                                      |
| ---------------------- | --------------------- | --------------------------------------------- |
| 0 (не предоставлено)   | Текст-в-видео         | Генерация только из текста                    |
| 1                      | Изображение-в-видео   | Изображение как начальный кадр                |
| 2                      | Первый-последний кадр | Первое = первый кадр, второе = последний кадр |

## Ответ

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, фиксированное значение `generation.task`
</ResponseField>

<ResponseField name="model" type="string">
  Название использованной модели
</ResponseField>

<ResponseField name="status" type="string">
  Статус задачи

  * `queued` - В очереди
  * `in_progress` - Обрабатывается
  * `completed` - Завершено
  * `failed` - Ошибка
</ResponseField>

<ResponseField name="progress" type="integer">
  Процент выполнения (0-100)
</ResponseField>

<ResponseField name="created_at" type="integer">
  Unix-временная метка создания задачи
</ResponseField>

## Примеры использования

### Сценарий 1: Текст-в-видео

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "Кот играет на пианино, камера медленно приближается",
  "duration": 8,
  "resolution": "1080p",
  "aspect_ratio": "16:9"
}
```

### Сценарий 2: Изображение-в-видео (одно изображение)

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "Человек медленно поворачивается и улыбается",
  "image_urls": ["https://example.com/photo.jpg"],
  "duration": 5,
  "resolution": "720p"
}
```

### Сценарий 3: Первый-последний кадр

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "Человек постепенно садится из положения стоя",
  "image_urls": [
    "https://example.com/first.jpg",
    "https://example.com/last.jpg"
  ],
  "duration": 8
}
```

### Сценарий 4: Видео без звука

```json theme={null}
{
  "model": "viduq3-pro",
  "prompt": "Таймлапс морского пейзажа на закате",
  "duration": 10,
  "resolution": "1080p",
  "audio": false
}
```

<Note>
  **Запрос результатов задачи**

  Генерация видео является асинхронной задачей, после отправки возвращается ID задачи. Используйте конечную точку [Получить статус задачи](/ru/api-reference/tasks/status) для запроса прогресса и результатов.
</Note>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/generations \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "viduq3-pro",
      "prompt": "Кот играет на пианино, камера медленно приближается",
      "duration": 8,
      "resolution": "1080p",
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
          "model": "viduq3-pro",
          "prompt": "Кот играет на пианино, камера медленно приближается",
          "duration": 8,
          "resolution": "1080p",
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
      model: 'viduq3-pro',
      prompt: 'Кот играет на пианино, камера медленно приближается',
      duration: 8,
      resolution: '1080p',
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
    "model": "viduq3-pro",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
