# Remotes, fetch, pull и push

## Цель

Понимать границу между локальной и удалённой историей и управлять синхронизацией без случайного переписывания чужих изменений.

## Prerequisites

Objects, refs, branches и merge.

## In scope

Remote — именованное описание удалённого repository URL. Remote-tracking branch вроде `origin/main` — локальная ссылка на состояние, известное после последнего fetch; это не живое подключение к серверу.

`fetch` получает objects и обновляет remote-tracking refs, не меняя текущую branch. `pull` обычно сочетает fetch с merge или rebase. `push` отправляет достижимые objects и просит сервер переместить remote ref согласно fast-forward policy.

```bash
git fetch origin
git log main..origin/main
git push origin feature/example
git pull --rebase origin main
```

Разделяйте fetch и merge в критических изменениях: сначала изучите входящую историю, затем выберите стратегию объединения. Protected branches и review process являются частью delivery safety, а не препятствием для Git.

## Типичные ошибки

- считать `origin/main` актуальным до fetch;
- использовать `pull` без понимания merge/rebase;
- push в shared branch без проверки divergence;
- хранить секреты в remote URL;
- игнорировать подписанные tags и protected branch policy.

## Практика

Создайте bare remote, клонируйте его двумя клиентами, внесите расходящиеся commit, исследуйте через fetch, затем объедините изменения безопасным способом.

## Следующие темы

`06-Rebase-Reset-Restore-and-Revert.md`, затем cherry-pick и reflog.
