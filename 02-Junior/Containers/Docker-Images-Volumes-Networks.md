# Docker: images, volumes, networks и Registry

## Image и build

Image — immutable manifest с ordered layers. Dockerfile instruction может создавать layer; cache зависит от instruction и inputs. Multi-stage build отделяет compiler/dependencies от runtime image.

```dockerfile
FROM eclipse-temurin:21-jdk AS build
WORKDIR /src
COPY pom.xml .
RUN ./mvnw -DskipTests package
COPY . .
RUN ./mvnw -DskipTests package
FROM eclipse-temurin:21-jre
COPY --from=build /src/target/app.jar /app.jar
USER 10001
ENTRYPOINT ["java","-jar","/app.jar"]
```

Build должен быть reproducible: pinned base/tag/digest, `.dockerignore`, non-root, отсутствие secret в layers, минимальные packages и vulnerability scan.

## Filesystem и данные

Writable container layer эфемерен. Volume — persistent path под управлением runtime, bind mount — явный host path. Жизненный цикл данных проектируется отдельно от жизненного цикла container.

## Network

Docker bridge создаёт virtual interfaces и embedded DNS. Containers в одной network находят service по имени. Published `HOST:CONTAINER` port не равен внутреннему listening port.

```bash
docker build -t app:1 .
docker run --rm --name app -p 8080:8080 app:1
docker inspect app
docker network inspect bridge
docker volume inspect data
docker stats app
docker history app:1
docker login REGISTRY
docker push REGISTRY/project/app:1
```

## Pull path

```text
DNS → TCP → TLS → authentication → manifest → layers → unpack → start
```

`unauthorized`, `x509`, `manifest unknown`, timeout и architecture mismatch относятся к разным слоям.

## Вопросы

- **Почему не `latest`?** Mutable tag ломает воспроизводимость и rollback; используй immutable version/digest.
- **Зачем cache?** Он ускоряет build, но stale dependencies могут дать неожиданный результат.
- **Volume и bind mount?** Volume управляется runtime, bind mount связывает container с host path.

## Как объяснить за 30–60 секунд

«Image — immutable layered artifact, container — runtime instance, volume — отдельный жизненный цикл данных. Build должен быть воспроизводимым и безопасным, pull проходит DNS/TCP/TLS/auth/manifest/layers. Service name и published port — разные abstractions».

[[02-Junior/Containers/Isolation-OCI-Runtime]], [[02-Junior/CI-CD/GitLab-Pipeline-Runner]], [[05-Production-Troubleshooting/Containers]].

## Как читать Dockerfile

Порядок instruction влияет на cache и итоговый attack surface. Сначала помещай редко меняющиеся dependency manifests, затем устанавливай dependencies, и только после этого копируй часто изменяющийся source. Но cache не должен закреплять уязвимые или секретные данные.

`ENTRYPOINT` задаёт основной executable, `CMD` — default arguments/command, `ENV` влияет на runtime, `ARG` доступен во время build и не должен использоваться для секретов, `EXPOSE` документирует порт, но не публикует его.

## Compose и DNS

В Compose service name разрешается встроенным DNS в общей network. `depends_on` задаёт порядок создания, но не readiness database. Поэтому application должна иметь retry/backoff или health-aware startup.

```bash
docker compose config
docker compose up -d --build
docker compose ps
docker compose logs -f SERVICE
docker compose exec SERVICE getent hosts db
docker compose down
```

Ожидаемый результат `config` — развёрнутый YAML, `ps` — state/ports, `getent` — IP service name. Если DNS работает, но connect refused, проблема уже в listener/readiness/port.

## Registry и digest

Pull проходит authentication, получение manifest, выбор platform и скачивание layers. Tag — удобное имя, digest — content identity. Production должен фиксировать digest или защищённый immutable tag.

## Практическое задание

1. Собери multi-stage image и сравни `docker history`.
2. Добавь секрет в build намеренно, найди его в layer и удали безопасным способом.
3. Создай Compose web+db с volume, затем удали container без удаления volume.
4. Сломай service name, published port и internal bind address по отдельности.
5. Ограничь memory, получи OOM и отличи его от application exit.
6. Опубликуй image под tag и digest, затем проверь, что deploy указывает на те же bytes.

## Production implications

Logging должен идти в stdout/stderr, иначе runtime не сможет централизованно собрать его. Volume должен иметь owner/backup/restore policy. Registry требует TLS, scoped credentials, retention и scan. `docker system prune` без понимания volumes может удалить нужные данные.
