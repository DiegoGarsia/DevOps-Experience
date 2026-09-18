# Git objects: commits, trees и blobs

## Цель

Понять внутреннюю модель Git, чтобы уверенно читать историю, находить потерянные commits и не путать имя файла с объектом данных.

## Prerequisites

Working tree, index и базовый commit workflow.

## In scope

Git хранит content-addressed objects:

- blob — содержимое файла без имени;
- tree — соответствие имени, mode и object ID;
- commit — ссылка на tree, parent commit, author, committer и message;
- annotated tag — объект, указывающий на другой объект с metadata.

Имя файла находится в tree, поэтому один blob может быть доступен под разными именами, а одинаковые содержимое файлов имеют один объект в пределах repository. Commit образует directed acyclic graph, а не линейный журнал.

```text
commit
  └── tree
       ├── blob: file content
       └── tree: directory
```

Объекты идентифицируются hash. Современный Git может использовать SHA-256 repository format, но привычные репозитории используют SHA-1 с защитными механизмами. В operational работе важнее принцип адресации и ссылки, а не алгоритм как таковой.

## Диагностика

```bash
git cat-file -t HEAD
git cat-file -p HEAD
git rev-parse HEAD^{tree}
git ls-tree -r HEAD
```

Эти команды помогают понять, что commit указывает на дерево, а tree — на blobs и вложенные trees.

## Типичные ошибки

- считать blob отдельным файлом с именем;
- считать branch копией всей истории;
- изменять объект вместо перемещения ref;
- ожидать, что удалённый ref немедленно уничтожит объект.

## Практика

Найдите tree и blob конкретного файла, сравните его object ID до и после изменения и объясните, почему новый commit не изменяет старый объект.

## Следующие темы

`03-Refs-HEAD-Branches-and-Tags.md` и merge workflow.
