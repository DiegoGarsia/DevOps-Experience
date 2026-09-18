# Python для DevOps-автоматизации

## Цель

Освоить минимальный Python, необходимый для glue scripts, API-проверок, преобразования данных и диагностики.

## Prerequisites

CLI, structured data, алгоритм idempotent automation и базовый Git.

## In scope

Нужный уровень включает variables, lists, dictionaries, functions, conditions, loops, exceptions, modules, file I/O, `json`, `subprocess`, `argparse`, `logging` и HTTP-клиент.

Пример безопасного запуска внешней команды:

```python
from subprocess import run

result = run(["systemctl", "is-active", "nginx"], text=True, capture_output=True)
if result.returncode != 0:
    raise SystemExit(result.stderr.strip() or "service is not active")
```

Список аргументов предпочтительнее `shell=True`: он не смешивает данные и shell syntax. Ошибки нужно обрабатывать явно, а результат — логировать без секретов.

Для JSON используйте parser, для конфигурации — schema validation. В automation разделяйте чтение состояния и изменение, добавляйте timeout, retry только для временных ошибок и понятный exit code.

## Out of scope

Не требуется изучать web-разработку, сложные алгоритмы, асинхронные фреймворки, data science или создавать полноценные приложения. Python здесь — инструмент автоматизации, а не отдельная профессия.

## Типичные ошибки

- `shell=True` с непроверенным вводом;
- отсутствие timeout в HTTP или subprocess;
- ловля `Exception` без контекста;
- печать токенов в log;
- изменение production без dry-run и проверки результата.

## Практика

Напишите CLI-скрипт, который получает URL и таймаут, проверяет HTTP status, пишет структурированный JSON-результат и возвращает разные exit codes для network error, timeout и application error.

## Следующие темы

Git, CI/CD, Terraform и Ansible уровня Junior.
