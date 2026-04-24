# Azure Sora 2 Remix

> Ремикс существующего видео с использованием модели Azure Sora 2

Функция ремикса позволяет изменять определенные аспекты существующего видео, сохраняя при этом его основные элементы. Ссылаясь на ID предыдущего успешно созданного видео и предоставляя обновленный промпт, система сохраняет структуру оригинального видео, переходы между сценами и визуальную компоновку, одновременно реализуя запрошенные вами изменения.

<Tip>
  Для оптимальных результатов ограничьте свои изменения одной четко сформулированной корректировкой. Узкие, точные правки лучше сохраняют верность исходному материалу и минимизируют вероятность появления визуальных дефектов.
</Tip>

## Authorizations

<ParamField header="Authorization" type="string" required>
  Все конечные точки требуют аутентификации Bearer Token

  Получите API Key на [странице управления API Key](https://toapis.com/console/token)

  Добавьте в заголовок запроса:

  ```
  Authorization: Bearer YOUR_API_KEY
  ```
</ParamField>

## Path Parameters

<ParamField path="video_id" type="string" required>
  ID ранее завершенного видео для ремикса

  Пример: `"video_01K8SGYNNNVBQTXNR4MM964S7K"`
</ParamField>

## Body

<ParamField body="model" type="string" required>
  Название модели генерации видео

  Для ремикса Azure Sora 2 необходимо использовать `sora-2-official`

  Пример: `"sora-2-official"`
</ParamField>

<ParamField body="prompt" type="string" required>
  Обновленное текстовое описание для ремикшированного видео

  Опишите конкретные изменения, которые вы хотите внести в оригинальное видео. Будьте точны и сосредоточьтесь на одном четком изменении.

  Пример: `"Измените цветовую палитру на бирюзовый, песочный и ржавый с теплой контровой подсветкой"`
</ParamField>

## Response

<ResponseField name="id" type="string">
  Уникальный идентификатор задачи для ремикшированного видео
</ResponseField>

<ResponseField name="object" type="string">
  Тип объекта, всегда `video`
</ResponseField>

<ResponseField name="model" type="string">
  Используемое название модели
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
  Временная метка создания задачи (Unix timestamp)
</ResponseField>

<ResponseField name="remixed_from_video_id" type="string">
  ID оригинального видео, из которого был сделан ремикс
</ResponseField>

<ResponseField name="seconds" type="string">
  Продолжительность видео в секундах
</ResponseField>

<ResponseField name="size" type="string">
  Разрешение видео (например, "1280x720")
</ResponseField>

<RequestExample>
  ```bash cURL theme={null}
  curl --request POST \
    --url https://toapis.com/v1/videos/video_01K8SGYNNNVBQTXNR4MM964S7K/remix \
    --header 'Authorization: Bearer <token>' \
    --header 'Content-Type: application/json' \
    --data '{
      "model": "sora-2-official",
      "prompt": "Измените цветовую палитру на бирюзовый, песочный и ржавый с теплой контровой подсветкой"
    }'
  ```

  ```python Python theme={null}
  import requests

  video_id = "video_01K8SGYNNNVBQTXNR4MM964S7K"

  response = requests.post(
      f"https://toapis.com/v1/videos/{video_id}/remix",
      headers={
          "Authorization": "Bearer your-ToAPIs-key",
          "Content-Type": "application/json"
      },
      json={
          "model": "sora-2-official",
          "prompt": "Измените цветовую палитру на бирюзовый, песочный и ржавый с теплой контровой подсветкой"
      }
  )

  task = response.json()
  print(f"ID задачи ремикса: {task['id']}")
  print(f"Статус: {task['status']}")
  print(f"Ремикс из: {task.get('remixed_from_video_id')}")
  ```

  ```javascript JavaScript theme={null}
  const videoId = "video_01K8SGYNNNVBQTXNR4MM964S7K";

  const response = await fetch(`https://toapis.com/v1/videos/${videoId}/remix`, {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer your-ToAPIs-key',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      model: 'sora-2-official',
      prompt: 'Измените цветовую палитру на бирюзовый, песочный и ржавый с теплой контровой подсветкой'
    })
  });

  const task = await response.json();
  console.log(`ID задачи ремикса: ${task.id}`);
  console.log(`Статус: ${task.status}`);
  console.log(`Ремикс из: ${task.remixed_from_video_id}`);
  ```
</RequestExample>

<ResponseExample>
  ```json 200 theme={null}
  {
    "id": "video_68ff7cef76cc8190b7eab9395e936d9e",
    "object": "video",
    "model": "sora-2-official",
    "status": "queued",
    "progress": 0,
    "created_at": 1761574127,
    "remixed_from_video_id": "video_01K8SGYNNNVBQTXNR4MM964S7K",
    "seconds": "8",
    "size": "1280x720"
  }
  ```
</ResponseExample>
