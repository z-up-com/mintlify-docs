---
title: "Генерация видео MiniMax-Hailuo-2.3"
---
> Генерация видео с поддержкой текст-в-видео и изображение-в-видео, 15 типов команд камеры, автооптимизация промпта

* Асинхронный режим обработки, возвращает ID задачи для последующих запросов
* Поддержка текст-в-видео и изображение-в-видео (первый кадр)
* Поддержка длительности 6 и 10 секунд, разрешения 768p и 1080p
* 15 типов команд камеры, автооптимизация промпта и управление водяным знаком

<Warning>
  **Важное изменение**: Для улучшения производительности и контроля затрат мы больше не поддерживаем передачу данных изображений в формате base64 непосредственно в параметрах. Пожалуйста, сначала используйте [эндпоинт загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки изображений, а затем используйте полученный URL в этом API.
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

## Параметры запроса

<ParamField body="model" type="string" required>
  Название модели генерации видео, фиксированное значение `MiniMax-Hailuo-2.3`
</ParamField>

<ParamField body="prompt" type="string" required>
  Описание содержимого видео (максимум **2000 символов**)

  Рекомендуется подробно описывать сцену, действия и стиль. Поддерживаются команды камеры (см. таблицу ниже).

  Пример: `"Милый кот бежит по лугу"`
</ParamField>

<ParamField body="duration" type="integer" default="6">
  Длительность видео (секунды)

  Варианты:

  * `6` - 6 секунд
  * `10` - 10 секунд

  По умолчанию: `6`

  <Warning>
    **Ограничение 1080p**: При разрешении 1080p поддерживается только длительность 6 секунд.
  </Warning>
</ParamField>

<ParamField body="metadata" type="object">
  Специальные параметры расширения

  <Expandable title="Поля metadata">
    <ParamField body="resolution" type="string" default="768p">
      Разрешение видео

      Варианты:

      * `768p` - HD (по умолчанию)
      * `1080p` - Full HD (только 6 секунд)

      По умолчанию: `768p`
    </ParamField>

    <ParamField body="first_frame_image" type="string">
      URL изображения первого кадра для режима изображение-в-видео

      Поддерживаемые форматы:

      * **Публичный URL**: `https://example.com/start.jpg`
      * Используйте [эндпоинт загрузки изображений](/docs/ru/api-reference/uploads/images) для загрузки локальных изображений

      Видео начнётся с этого изображения
    </ParamField>

    <ParamField body="prompt_optimizer" type="boolean" default="true">
      Автоматическая оптимизация промпта

      При включении система автоматически оптимизирует ваш промпт для лучшего результата

      По умолчанию: `true`
    </ParamField>

    <ParamField body="fast_pretreatment" type="boolean" default="false">
      Сократить время предварительной обработки промпта

      При включении ускоряет обработку, но может снизить качество оптимизации

      По умолчанию: `false`
    </ParamField>

    <ParamField body="watermark" type="boolean" default="false">
      Добавить водяной знак

      По умолчанию: `false`
    </ParamField>
  </Expandable>
</ParamField>

## Сочетание разрешения и длительности

| Разрешение | Поддерживаемая длительность | Примечание             |
| ---------- | --------------------------- | ---------------------- |
| 768p       | 6 с, 10 с                   | Все варианты           |
| 1080p      | 6 с                         | 10 с не поддерживается |

## Команды камеры

В `prompt` используйте синтаксис `[команда]` для управления камерой. Поддерживаются 15 типов:

| Категория    | Команда  | Описание               |
| ------------ | -------- | ---------------------- |
| Панорама     | `[左移]`   | Панорама влево         |
| Панорама     | `[右移]`   | Панорама вправо        |
| Поворот      | `[左摇]`   | Поворот камеры влево   |
| Поворот      | `[右摇]`   | Поворот камеры вправо  |
| Наезд/отъезд | `[推进]`   | Наезд (приближение)    |
| Наезд/отъезд | `[拉远]`   | Отъезд (отдаление)     |
| Подъём/спуск | `[上升]`   | Подъём камеры          |
| Подъём/спуск | `[下降]`   | Спуск камеры           |
| Наклон       | `[上摇]`   | Наклон вверх           |
| Наклон       | `[下摇]`   | Наклон вниз            |
| Зум          | `[变焦推近]` | Зум приближение        |
| Зум          | `[变焦拉远]` | Зум отдаление          |
| Прочее       | `[晃动]`   | Дрожание камеры        |
| Прочее       | `[跟随]`   | Следование за объектом |
| Прочее       | `[固定]`   | Фиксированный кадр     |

**Пример использования:**

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "[推进]Кот бежит по саду, камера медленно приближается к крупному плану",
  "duration": 6,
  "metadata": {
    "resolution": "768p"
  }
}
```

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

<ResponseField name="metadata" type="object">
  Метаданные задачи
</ResponseField>

## Примеры использования

### Сценарий 1: Быстрая генерация текст-в-видео

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "Милый кот бежит по лугу, солнечный день",
  "duration": 6,
  "metadata": {
    "resolution": "768p",
    "prompt_optimizer": true,
    "watermark": false
  }
}
```

### Сценарий 2: Высококачественное 1080p видео

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "Ночной город, неоновые огни, поток машин",
  "duration": 6,
  "metadata": {
    "resolution": "1080p",
    "prompt_optimizer": true,
    "watermark": false
  }
}
```

### Сценарий 3: Изображение-в-видео (первый кадр)

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "Котёнок бежит к камере, улыбается и моргает",
  "duration": 6,
  "metadata": {
    "first_frame_image": "https://example.com/cat.jpg",
    "resolution": "1080p"
  }
}
```

### Сценарий 4: С командами камеры

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "[推进]Кот бежит по саду, камера медленно приближается к крупному плану",
  "duration": 6,
  "metadata": {
    "resolution": "768p"
  }
}
```

### Сценарий 5: Режим быстрой предобработки

```json theme={null}
{
  "model": "MiniMax-Hailuo-2.3",
  "prompt": "Волны бьются о берег на закате",
  "duration": 10,
  "metadata": {
    "resolution": "768p",
    "prompt_optimizer": true,
    "fast_pretreatment": true
  }
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
      "model": "MiniMax-Hailuo-2.3",
      "prompt": "Милый кот бежит по лугу",
      "duration": 6,
      "metadata": {
        "resolution": "768p",
        "prompt_optimizer": true,
        "fast_pretreatment": false,
        "watermark": false
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
          "model": "MiniMax-Hailuo-2.3",
          "prompt": "Милый кот бежит по лугу",
          "duration": 6,
          "metadata": {
              "resolution": "768p",
              "prompt_optimizer": True,
              "fast_pretreatment": False,
              "watermark": False
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
      model: 'MiniMax-Hailuo-2.3',
      prompt: 'Милый кот бежит по лугу',
      duration: 6,
      metadata: {
        resolution: '768p',
        prompt_optimizer: true,
        fast_pretreatment: false,
        watermark: false
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
    "id": "video_01J9HA7JPQ9A0Z6JZ3V8M9W6PZ",
    "object": "generation.task",
    "model": "MiniMax-Hailuo-2.3",
    "status": "queued",
    "progress": 0,
    "created_at": 1768380224,
    "metadata": {}
  }
  ```
</ResponseExample>
