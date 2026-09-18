# Refs, `HEAD`, branches и tags

## Цель

Понять, что branch — это изменяемая ссылка на commit, а `HEAD` определяет текущий контекст работы.

## Prerequisites

Git objects и commits.

## In scope

Ref хранит имя и object ID. Branch ref обычно указывает на последний commit ветки и перемещается при новом commit. Tag предназначен для именования важной точки истории; lightweight tag — простая ссылка, annotated tag — отдельный объект с подписью и сообщением.

`HEAD` обычно указывает на текущую branch через symbolic ref. Detached HEAD означает, что `HEAD` указывает прямо на commit. Работа в таком состоянии допустима для просмотра или эксперимента, но новый commit может стать недостижимым после переключения, если его не закрепить branch или tag.

```bash
git show-ref
git symbolic-ref HEAD
git log --decorate --oneline --graph --all
git switch -c feature/example
git tag -a v1.0.0 -m 'Release 1.0.0'
```

Удаление branch не удаляет commit мгновенно: объект может оставаться доступным через другие refs или reflog.

## Типичные ошибки

- считать branch отдельной копией repository;
- работать detached HEAD и потерять новый commit;
- переписывать tag после публикации без политики;
- использовать moving branch как immutable release marker.

## Практика

Создайте branch, detached HEAD и annotated tag. Найдите все refs через `show-ref`, сделайте commit в detached состоянии и закрепите его новой branch.

## Следующие темы

`04-Merge-Fast-Forward-and-Conflicts.md`, remotes и rebase.
