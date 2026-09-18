# Bash для DevOps

## Модель выполнения

Shell разбирает командную строку, выполняет expansion, redirections и pipelines, затем запускает процесс через `exec`. `PATH` влияет на поиск программы, environment наследуется потомком, а обычная переменная без `export` — нет.

```text
исходная команда → parse → expansion → redirection → fork/exec → exit status
```

## Безопасный скрипт

```bash
#!/usr/bin/env bash
set -euo pipefail

log() { printf '%s %s\n' "$(date -Is)" "$*"; }
trap 'printf "ошибка в строке %s\n" "$LINENO" >&2' ERR

input=${1:?"использование: $0 FILE"}
if [ ! -r "$input" ]; then
  echo "файл недоступен" >&2
  exit 2
fi
while IFS= read -r line; do
  printf '%s\n' "$line"
done < "$input"
```

- заключай переменные в кавычки;
- используй `$(...)` для подстановки команды;
- для тестов предпочитай конструкцию Bash `test`/двойные квадратные скобки;
- разделяй stdout и stderr;
- критический exit status проверяй явно;
- `set -e` имеет исключения, поэтому не заменяет проектирование ошибок;
- `pipefail` не даёт скрыть ошибку внутри pipeline;
- `trap` делает cleanup и обработку сигналов предсказуемыми;
- `mktemp` и atomic rename защищают от частичных файлов.

## Текст и циклы

`grep` фильтрует, `sed` преобразует, `awk` обрабатывает поля, `find` ищет. Не парси `ls`; для имён с пробелами используй `-print0` и `xargs -0` или `-exec`. Массивы сохраняют границы аргументов.

```bash
mapfile -t files < <(find /var/log -type f -name '*.log' -print)
for file in "${files[@]}"; do
  grep -H 'ERROR' "$file" || true
done
```

## Production workflow

```text
контракт входа → проверка → lock/concurrency policy
→ временный результат → команда → exit code/логи
→ cleanup → метрики/alert → безопасный повторный запуск
```

Для systemd timer нужны timeout, absolute paths, journal-friendly output и `flock`, если параллельные запуски запрещены.

## Частые ошибки

- word splitting/globbing → кавычки и массивы;
- pipeline скрывает failure → `pipefail` и явные проверки;
- cron не видит PATH → absolute paths и Environment;
- частичный файл после сбоя → временный файл и `mv`;
- повторный timer → `flock` или политика systemd;
- секрет в истории shell → stdin, файл с правами или secret manager.

## Вопросы

- **stdout и stderr?** Разные потоки позволяют передавать данные через pipe и отдельно видеть диагностику.
- **Subshell и текущий shell?** `( ... )` изолирует изменения, `{ ...; }` выполняется в текущем shell.
- **Зачем `xargs -0`?** Null-разделитель безопасен для пробелов и переводов строк в именах.

## Как объяснить за 30–60 секунд

«Production Bash должен иметь явный контракт входа и выхода, проверять exit codes, обрабатывать сигналы и быть безопасным к пробелам и частичным результатам. `set -euo pipefail` — baseline, но критические операции всё равно проверяются явно; результат пишется атомарно, а повторный запуск контролируется».

[[01-Fundamentals/Linux/systemd-Users-SSH]], [[05-Production-Troubleshooting/CI-CD]].

## Полная цепочка выполнения команды

```text
строка команды
→ parsing
→ quoting/expansion
→ redirection
→ создание process
→ pipeline и file descriptors
→ fork/exec
→ signal/exit status
```

### Quoting и expansion

Без кавычек shell выполняет word splitting и pathname expansion. Поэтому `rm $FILE` может превратиться в несколько аргументов, а `rm "$FILE"` сохраняет один аргумент. Обычные кавычки не выполняют expansion, двойные разрешают `$var` и `$(command)`, backslash экранирует следующий символ.

```bash
name='report 2026.txt'
printf '%s\n' "$name"
printf '%s\n' $name
```

Первая команда печатает одну строку-аргумент, вторая может разделить её на слова. Это не косметика: неправильное quoting может удалить чужие файлы или раскрыть secret в log.

### File descriptors и pipeline

У процесса обычно есть `0` stdin, `1` stdout, `2` stderr. `>` создаёт/перезаписывает файл, `>>` дописывает, `2>` перенаправляет ошибки, `|` соединяет stdout одной команды со stdin другой.

```bash
command >result.txt 2>error.txt
command >result.txt 2>&1
command 2>&1 | tee result.txt
```

Если pipeline показывает неполный успех, `set -o pipefail` возвращает failure, когда падает любая команда. `tee` сохраняет output и показывает его дальше.

### Exit status

`0` означает success по соглашению Unix, ненулевое значение — failure/особое состояние. Это контракт между process и parent.

```bash
if curl --fail --silent --show-error https://example.test/health; then
  echo 'проверка успешна'
else
  rc=$?
  echo "проверка неуспешна: $rc" >&2
  exit "$rc"
fi
```

`curl --fail` превращает HTTP 4xx/5xx в ненулевой status. Без него TCP/HTTP transport может завершиться успешно, хотя приложение вернуло ошибку.

### Signals и cleanup

```bash
tmp=$(mktemp)
cleanup() { rm -f -- "$tmp"; }
trap cleanup EXIT
trap 'exit 130' INT TERM
```

`EXIT` срабатывает при завершении shell, а `INT/TERM` позволяют остановить работу безопасно. Не перехватывай `SIGKILL`: kernel его не передаёт обработчику.

### Практическое задание

1. Напиши script, который принимает directory, проверяет её существование и считает файлы по extension.
2. Добавь `set -euo pipefail`, quoting и обработку пустого результата `find`.
3. Направь normal output в stdout, ошибки в stderr.
4. Создай временный файл, заверши script через `SIGTERM`, проверь cleanup.
5. Запусти две копии одновременно и добавь `flock`.
6. Намеренно сломай одну команду в pipeline и сравни результат с/без `pipefail`.

### Production implications

- script должен быть идемпотентным: повторный запуск не портит уже готовое состояние;
- destructive actions требуют `--dry-run`, allowlist и явного подтверждения;
- secrets нельзя передавать в аргументах, потому что их видны через process list;
- log должен содержать timestamp, operation, target и exit result, но не secret;
- длительные scripts нуждаются в timeout, lock, metrics и alert.

### Дополнительные вопросы

- Почему `set -e` не гарантирует остановку при каждой ошибке?
- Чем environment отличается от shell variable?
- Как безопасно обработать filename с newline?
- Как сделать запись результата атомарной?
