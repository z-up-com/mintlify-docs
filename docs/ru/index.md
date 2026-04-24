> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# ToAPIs — OpenAI-совместимый API-шлюз (GPT-5, Claude, Gemini)

> OpenAI-совместимый API, поддержка GPT-5, Claude, Gemini. Маршрутизация между провайдерами, прозрачное ценообразование, низкая задержка. Корпоративный SLA, поддержка SDK, оплата по факту.

Единый OpenAI-совместимый эндпоинт с поддержкой GPT-5, Claude и Gemini. Просто измените Base URL на `https://toapis.com/v1` и мигрируйте с OpenAI за несколько минут — сохраните существующий SDK, без переписывания кода. Маршрутизация между провайдерами обеспечивает низкую задержку и высокую доступность. Прозрачное ценообразование, корпоративный SLA и глобальное CDN-ускорение.

## Быстрый старт

<Card title="Начать сейчас" icon="rocket" href="/docs/ru/api-reference/texts/general/chat-completions">
  OpenAI-совместимый Chat API, поддержка GPT-5, Claude Sonnet 4.5, Gemini 2.0. Смените Base URL без изменения кода.
</Card>

## OpenAI-совместимые эндпоинты

ToAPIs предоставляет эндпоинты в стиле OpenAI для бесшовной миграции. Просто измените Base URL и продолжайте использовать существующий SDK. Все интерфейсы соответствуют стандарту OpenAI с расширенной поддержкой Claude, Gemini, Sora и VEO.

<CardGroup cols={3}>
  <Card title="Chat Completions API" icon="comments" href="/docs/ru/api-reference/texts/general/chat-completions">
    Поддержка стриминга и низкая задержка
  </Card>

  <Card title="API генерации изображений" icon="image" href="/docs/ru/api-reference/images/gpt-4o/generation">
    GPT-4o-image, Seedream и другие модели
  </Card>

  <Card title="API генерации видео" icon="video" href="/docs/ru/api-reference/videos/veo3/generation">
    VEO3, Sora2 и другие модели
  </Card>
</CardGroup>

## Преимущества платформы

<CardGroup cols={2}>
  <Card title="🔄 OpenAI-совместимость" icon="rotate">
    Полная совместимость со стандартом OpenAI API, нулевые затраты на миграцию
  </Card>

  <Card title="🌐 Маршрутизация между провайдерами" icon="server">
    Автоматическое переключение между провайдерами для обеспечения высокой доступности
  </Card>

  <Card title="💰 Прозрачное ценообразование" icon="wallet">
    Оплата по факту использования, без скрытых платежей
  </Card>

  <Card title="⚡ Низкая задержка" icon="bolt">
    Глобальное CDN-ускорение, оптимизированный путь запросов
  </Card>

  <Card title="🔒 Корпоративный SLA" icon="shield-check">
    Гарантия доступности 99.9%, круглосуточная техническая поддержка
  </Card>

  <Card title="📊 Полная документация" icon="book">
    Подробная документация API, примеры на нескольких языках
  </Card>
</CardGroup>

## Примеры SDK

<Tabs>
  <Tab title="Python">
    ```python theme={null}
    from openai import OpenAI

    client = OpenAI(
        base_url="https://toapis.com/v1",
        api_key="your-ToAPIs-key"
    )

    response = client.chat.completions.create(
        model="gpt-5",
        messages=[
            {"role": "user", "content": "Привет!"}
        ]
    )

    print(response.choices[0].message.content)
    ```
  </Tab>

  <Tab title="Node.js">
    ```javascript theme={null}
    import OpenAI from 'openai';

    const client = new OpenAI({
      baseURL: 'https://toapis.com/v1',
      apiKey: 'your-ToAPIs-key'
    });

    const response = await client.chat.completions.create({
      model: 'gpt-5',
      messages: [{ role: 'user', content: 'Привет!' }]
    });

    console.log(response.choices[0].message.content);
    ```
  </Tab>
</Tabs>

## Миграция с OpenAI

<Steps>
  <Step title="Получите API Key">
    Зарегистрируйтесь на [ToAPIs](https://toapis.com) и получите ваш API Key
  </Step>

  <Step title="Измените Base URL">
    Замените `https://api.openai.com/v1` на `https://toapis.com/v1`
  </Step>

  <Step title="Замените API Key">
    Используйте ваш ToAPIs API Key вместо OpenAI API Key
  </Step>

  <Step title="Готово!">
    Ваше приложение теперь использует ToAPIs, без изменений в коде
  </Step>
</Steps>

## Поддерживаемые модели

| Тип             | Модели                                                           |
| --------------- | ---------------------------------------------------------------- |
| **Текст**       | GPT-5, Claude Sonnet 4.5, Gemini 2.5 Pro, DeepSeek V3            |
| **Изображения** | GPT-4o-image, Gemini-2.5-Flash-Image, Seedream-4.0, Seedream-4.5 |
| **Видео**       | VEO3, Sora2, MiniMax-Hailuo-02, Wan2.6                           |
| **Аудио**       | Whisper-1, TTS                                                   |

## Часто задаваемые вопросы

<AccordionGroup>
  <Accordion title="Нужно ли изменять код?">
    Нет. ToAPIs полностью совместим с OpenAI SDK. Вам нужно только изменить Base URL и API Key.
  </Accordion>

  <Accordion title="Какие модели поддерживаются?">
    Поддерживаются GPT-5, Claude, Gemini, DeepSeek и многие другие модели. Смотрите полный список моделей на странице [моделей](https://toapis.com/models).
  </Accordion>

  <Accordion title="Как оплачивать?">
    Оплата по факту использования, поддерживаются банковские карты, PayPal и криптовалюта.
  </Accordion>
</AccordionGroup>
