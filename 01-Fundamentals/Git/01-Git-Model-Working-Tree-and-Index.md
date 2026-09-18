# Git: working tree и index

## Цель

Понять, какие состояния проекта хранит Git и почему `git add` не является «сохранением файла в commit».

## Prerequisites

Файлы, каталоги и базовый CLI.

## In scope

Git разделяет:

- working tree — текущие файлы на диске;
- index/staging area — снимок изменений, которые войдут в следующий commit;
- repository — история объектов и refs.

Изменение файла сначала существует только в working tree. `git add` помещает выбранное содержимое в index. `git commit` создаёт объект commit из состояния index и связывает его с parent commit.

```text
working tree
    ↓ git add
index
    ↓ git commit
repository object
```

Поэтому файл может быть одновременно staged и иметь unstaged изменения: index содержит одну версию, working tree — другую. `git diff` сравнивает working tree с index, а `git diff --cached` — index с последним commit.

## Практический контракт

Перед commit проверяйте не только список изменённых файлов, но и staged diff. Это предотвращает случайную публикацию секрета, debug-кода или несвязанного изменения.

```bash
git status --short
git diff
git diff --cached
git add path/to/file
git commit -m 'Describe the change'
```

## Типичные ошибки

- думать, что `git add .` создаёт commit;
- не проверять staged diff;
- добавлять build artifacts и secrets;
- смешивать несколько независимых изменений в одном commit.

## Практика

Создайте два файла, измените один после `git add`, удалите второй из index без удаления с диска и объясните результат `status` и двух видов `diff`.

## Следующие темы

`02-Git-Objects-Commits-Trees-and-Blobs.md`, затем refs и branches.
