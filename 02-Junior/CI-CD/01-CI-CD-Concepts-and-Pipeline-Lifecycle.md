# CI/CD: концепции и lifecycle pipeline

## Цель

Понять, как pipeline превращает commit в проверенный и безопасно продвигаемый artifact.

## Prerequisites

Git, shell, structured configuration, tests и Docker image model.

## In scope

CI автоматически проверяет изменение: lint, unit/integration tests, build и security checks. CD доставляет immutable artifact в environment и предоставляет promotion, verification и rollback.

```text
commit
  ↓
validate → test → build → scan
  ↓
artifact registry
  ↓
deploy staging → verify → promote production
```

Pipeline должен быть repeatable, observable и fail fast там, где дальнейшие стадии бессмысленны. Build один раз и promote тот же digest безопаснее, чем пересобирать artifact для каждой среды.

## Типичные ошибки

- pipeline проверяет только syntax;
- production собирается отдельно от staging;
- секреты печатаются в log;
- deployment success означает только принятие команды;
- нет rollback и owner.

## Практика

Создайте pipeline для небольшого приложения: lint → test → image build → scan → staging deploy → health verification. Намеренно сломайте test и build.

## Следующие темы

`02-Jobs-Stages-Runners-and-Executors.md`, artifacts и cache.
