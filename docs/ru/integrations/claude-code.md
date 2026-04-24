> ## Documentation Index
> Fetch the complete documentation index at: https://docs.toapis.com/llms.txt
> Use this file to discover all available pages before exploring further.

# Настройка Claude Code

> Используйте ToAPIs в качестве бэкенда API для Claude Code

## Подготовка

Перед началом убедитесь, что у вас есть:

1. **Установленный Claude Code**

   Установите Claude Code CLI через npm или pnpm:

   ```bash theme={null}
   # npm
   npm install -g @anthropic-ai/claude-code

   # pnpm
   pnpm install -g @anthropic-ai/claude-code
   ```

   Или посетите [официальную документацию Claude Code](https://docs.anthropic.com/claude-code) для получения актуальных инструкций.

2. **API-ключ ToAPIs**

   Войдите в [консоль ToAPIs](https://toapis.com/console/token) для получения API-ключа (начинается с `sk-`).

**Примечание:** Если у вас ещё нет аккаунта ToAPIs, сначала зарегистрируйтесь на [ToAPIs](https://toapis.com).

## Настройка

Claude Code поддерживает несколько способов настройки пользовательского API-эндпоинта. Вы можете указать ToAPIs в качестве альтернативного бэкенда для Anthropic API.

Claude Code использует два отдельных JSON-файла: **состояние onboarding** и **переменные окружения** должны находиться в соответствующем файле, чтобы настройки вступили в силу.

### Убедитесь, что onboarding завершён

В домашнем каталоге создайте или отредактируйте файл `~/.claude.json`:

```json theme={null}
{
  "hasCompletedOnboarding": true
}
```

Этот файл используется только для состояния CLI. API-токен и base URL задавайте в `settings.json` ниже.

### Способ 1: `settings.json` (рекомендуется)

Настройте переменные окружения в `~/.claude/settings.json` (глобально) или `.claude/settings.json` (уровень проекта). Изменять конфигурацию оболочки не требуется:

```json theme={null}
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "sk-xxxxxxxxxxxx",
    "ANTHROPIC_BASE_URL": "https://toapis.com"
  }
}
```

**Глобальная настройка** (применяется ко всем проектам — рекомендуется):

```bash theme={null}
mkdir -p ~/.claude
```

Затем запишите приведённый выше JSON в `~/.claude/settings.json`.

**Настройка уровня проекта** (применяется только к текущему проекту):

Создайте `.claude/settings.json` в корне проекта. Не включайте API-ключ в конфигурацию уровня проекта: храните `ANTHROPIC_AUTH_TOKEN` в глобальной конфигурации, на уровне проекта задавайте только `ANTHROPIC_BASE_URL`:

```json theme={null}
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://toapis.com"
  }
}
```

> **Примечание:** Поле `hasCompletedOnboarding` указывайте в `~/.claude.json`, блок `env` — в `~/.claude/settings.json` или `.claude/settings.json`. Не смешивайте их в одном файле.

### Способ 2: Переменные окружения

Подходит для временного использования или CI/CD-сред.

#### macOS / Linux

Временная настройка для текущей сессии:

```bash theme={null}
export ANTHROPIC_AUTH_TOKEN="sk-xxxxxxxxxxxx"
export ANTHROPIC_BASE_URL="https://toapis.com"
claude
```

Постоянная настройка — добавьте в `~/.bashrc` или `~/.zshrc`:

```bash theme={null}
export ANTHROPIC_AUTH_TOKEN="sk-xxxxxxxxxxxx"
export ANTHROPIC_BASE_URL="https://toapis.com"
```

Затем перезагрузите оболочку:

```bash theme={null}
source ~/.zshrc  # или source ~/.bashrc
```

#### Windows

Временная настройка в PowerShell:

```powershell theme={null}
$env:ANTHROPIC_AUTH_TOKEN = "sk-xxxxxxxxxxxx"
$env:ANTHROPIC_BASE_URL = "https://toapis.com"
claude
```

Постоянная настройка (уровень пользователя):

```powershell theme={null}
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", "sk-xxxxxxxxxxxx", "User")
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", "https://toapis.com", "User")
```

### Способ 3: Файл `.env`

Создайте файл `.env` в корне вашего проекта:

```bash theme={null}
ANTHROPIC_AUTH_TOKEN=sk-xxxxxxxxxxxx
ANTHROPIC_BASE_URL=https://toapis.com
```

**Важно:** Добавьте `.env` в `.gitignore`, чтобы не допустить утечки API-ключа.

### Справочник по настройке

| Переменная                 | Значение                                |
| -------------------------- | --------------------------------------- |
| **ANTHROPIC\_AUTH\_TOKEN** | Ваш API-ключ ToAPIs (`sk-xxxxxxxxxxxx`) |
| **ANTHROPIC\_BASE\_URL**   | `https://toapis.com`                    |

## Проверка настройки

Запустите следующую команду для проверки установки:

```bash theme={null}
claude --version
```

Затем запустите Claude Code:

```bash theme={null}
claude
```

Если всё настроено верно, Claude Code подключится через ToAPIs и будет отвечать в штатном режиме.

## Рекомендуемые модели

Следующие модели Claude доступны через ToAPIs:

| Название модели   | ID модели                   | Описание                                                                           |
| ----------------- | --------------------------- | ---------------------------------------------------------------------------------- |
| Claude Opus 4.6   | `claude-opus-4-6`           | Наиболее мощная, лучше всего для сложных задач                                     |
| Claude Sonnet 4.6 | `claude-sonnet-4-6`         | Баланс производительности и скорости — рекомендуется для ежедневного использования |
| Claude Haiku 4.5  | `claude-haiku-4-5-20251001` | Самая быстрая, лучше всего для лёгких задач                                        |

Для смены модели используйте команду `/model` внутри Claude Code или параметр `--model` при запуске:

```bash theme={null}
claude --model claude-sonnet-4-6
```

## Устранение неполадок

### `Authentication error` или `401 Unauthorized`?

1. Убедитесь, что `ANTHROPIC_AUTH_TOKEN` задан корректно и начинается с `sk-`
2. Проверьте активность ключа в [консоли ToAPIs](https://toapis.com/console/token)
3. Убедитесь, что на вашем счету достаточно средств

### `Connection refused` или невозможно подключиться?

1. Убедитесь, что `ANTHROPIC_BASE_URL` установлен как `https://toapis.com`
2. Проверьте сетевое подключение и доступность `https://toapis.com`

### Проверка текущих переменных окружения

```bash theme={null}
echo $ANTHROPIC_AUTH_TOKEN
echo $ANTHROPIC_BASE_URL
```

### Распространённые коды ошибок

| Ошибка                      | Причина                                     | Решение                         |
| --------------------------- | ------------------------------------------- | ------------------------------- |
| `401 Unauthorized`          | Недействительный или просроченный API-токен | Создайте новый API-ключ         |
| `429 Too Many Requests`     | Превышен лимит запросов                     | Подождите немного и повторите   |
| `500 Internal Server Error` | Временная проблема на сервере               | Повторите через несколько минут |
| `insufficient_quota`        | Недостаточно средств на счету               | Пополните баланс в консоли      |

## Советы

### 1. Запуск в директории проекта

Перейдите в папку проекта и запустите `claude` — он автоматически подхватит контекст проекта:

```bash theme={null}
cd /your/project
claude
```

### 2. Полезные команды

| Команда              | Описание                |
| -------------------- | ----------------------- |
| `/help`              | Показать справку        |
| `/model`             | Сменить модель          |
| `/clear`             | Очистить текущий диалог |
| `/exit` или `Ctrl+C` | Выйти из Claude Code    |

### 3. Работа с кодом

Claude Code умеет читать и изменять файлы проекта. Основные сценарии использования:

* **Код-ревью** — попросите Claude Code проверить ваш код и предложить улучшения
* **Генерация кода** — опишите задачу, Claude Code напишет код напрямую в файлы
* **Отладка** — вставьте сообщение об ошибке и получите конкретные рекомендации
* **Рефакторинг** — попросите Claude Code улучшить структуру кода

## Поддержка

Если у вас возникли проблемы:

* 📚 [Документация ToAPIs](https://docs.toapis.com)
* 📚 [Официальная документация Claude Code](https://docs.anthropic.com/claude-code)
* 💬 [Сообщество Discord](https://discord.gg/hvnszCrJ73)
* 🐦 [Twitter @toapisai](https://x.com/toapisai)

***

\[## Начните работу с ToAPIs

Зарегистрируйтесь в ToAPIs, получите API-ключ и ускорьте разработку с Claude Code!]\([https://toapis.com](https://toapis.com))
