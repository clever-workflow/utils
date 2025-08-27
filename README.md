# Notification Workflow

Reusable GitHub Actions workflow для отправки уведомлений в Telegram о результатах деплоя.

## Использование

Просто добавьте этот workflow в ваш репозиторий:

```yaml
name: Example Notifications

on:
  push:
    branches: [ main, develop, master ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy step
        run: echo "Deploy completed"

  notification:
    needs: deploy
    if: always()
    uses: clever-workflow/utils/.github/workflows/notify-commit.yml@main
    with:
      HASHTAG: "YourProject"
      DEPLOY_STATUS: ${{ needs.deploy.result }}
```

## Параметры

### Inputs

- `DEPLOY_STATUS` (required): Статус деплоя (`success` или `failure`)
- `HASHTAG` (required): Хештег для уведомления

## Формат уведомления

Workflow отправляет POST запрос на `http://5.129.234.22:4000/notifications/commit` с JSON данными:

```json
{
  "repositoryName": "owner/repo",
  "author": "commit author",
  "commitMessage": "commit message",
  "branch": "branch name",
  "repositoryUrl": "https://github.com/owner/repo",
  "deployStatus": "✅ Деплой успешен" | "❌ Ошибка деплоя: [run_url]",
  "hashtag": "#YourProject"
}
```

## Особенности

- Не требует настройки секретов
- Автоматически отправляет уведомления при успешном и неуспешном деплое
- Логирует HTTP статус ответа
- Поддерживает кастомные хештеги для каждого проекта
