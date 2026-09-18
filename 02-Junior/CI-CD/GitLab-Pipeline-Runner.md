# CI/CD: pipeline, job, Runner и artifact

## Модель

```text
событие Git → pipeline → stages → jobs → Runner/executor
                                      ↓
                         workspace/image/variables
                                      ↓
                          artifact/cache/environment
```

CI проверяет change и создаёт artifact. CD доставляет immutable artifact, проверяет результат и выполняет promotion/rollback.

- stage — группа jobs с порядком;
- job — исполняемая единица;
- Runner — worker, который получает job;
- executor — shell, Docker, Kubernetes и другие среды;
- artifact — результат job и канал передачи;
- cache — ускорение, но не authoritative data channel;
- variables/secrets — runtime inputs с областью и защитой.

## Design pipeline

```text
lint/test → security → build → registry → deploy → health gate → promote/rollback
```

`rules` решают, создаётся ли job; `needs` строит DAG; `resource_group` сериализует production changes; environments записывают target и deployment state.

```yaml
stages: [test, build, deploy]

unit:
  stage: test
  script: ./test.sh
  artifacts:
    when: always
    reports:
      junit: report.xml

build:
  stage: build
  needs: [unit]
  script: ./build-and-push.sh "$CI_COMMIT_SHA"

deploy:
  stage: deploy
  resource_group: production
  script: ./deploy.sh "$CI_COMMIT_SHA"
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

Не печатай tokens, используй protected/masked variables, file variables для keys/certificates и least privilege для Runner.

## Strategies

Rolling меняет replicas постепенно. Blue-green держит две среды и переключает endpoint. Canary отправляет контролируемую долю traffic и требует health/metrics gates. GitOps отделяет создание artifact от declarative reconciliation; это delivery model, а не синоним одного инструмента.

## Вопросы

- **Artifact и cache?** Artifact — необходимый результат, cache — disposable optimization.
- **Зачем immutable tag/digest?** Deploy должен однозначно указывать на bytes и поддерживать deterministic rollback.
- **Почему проверять после `kubectl`?** API acceptance не равен пользовательскому успеху.

## Как объяснить за 30–60 секунд

«Pipeline — это управляемый путь от change к проверенному artifact. Runner задаёт execution boundary, artifacts передают результат, cache ускоряет, rules/needs формируют DAG, resource_group предотвращает параллельные production changes. CD завершается health gate и готовым rollback».

[[01-Fundamentals/Git/Git-Object-Model]], [[02-Junior/Containers/Docker-Images-Volumes-Networks]], [[04-Middle/Architecture/Delivery-Architecture-and-Tradeoffs]].

## Как выполняется job

1. GitLab создаёт pipeline по событию и применяет `rules`.
2. Scheduler ищет доступный Runner с подходящими tags.
3. Executor создаёт workspace и environment.
4. Job выполняет `before_script`, `script`, `after_script`.
5. Artifacts/cache сохраняются согласно policy.
6. Downstream job получает только объявленные dependencies/needs.

Artifact — контракт результата: его можно скачать и проверить. Cache — оптимизация, которую можно потерять; через cache нельзя передавать критическую версию или secret.

## Variables и безопасность

Protected variable доступна только protected branch/tag. Masked variable скрывается в log при подходящем формате, но команда может раскрыть её через debug/ошибку. Runner executor определяет boundary: privileged Docker или broad Kubernetes permissions повышают blast radius.

## Практическое задание

1. Сделай pipeline test → build → deploy.
2. Добавь artifact report и отдельный cache dependencies.
3. Удали `needs` намеренно и объясни, почему downstream не видит output.
4. Передай версию через dotenv artifact.
5. Добавь `resource_group` и запусти два deploy одновременно.
6. Разверни плохой digest, выполни health gate и автоматический rollback.
