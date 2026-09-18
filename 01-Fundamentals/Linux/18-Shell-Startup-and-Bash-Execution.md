# Запуск shell и выполнение Bash

## Цель

Различать login, interactive и non-interactive shell и писать команды, поведение которых не зависит от случайного состояния терминала.

## Prerequisites

CLI, `PATH`, процессы и environment.

## In scope

Shell может быть interactive или non-interactive, login или non-login. Эти свойства определяют, какие startup files читаются. Bash обычно использует профили для login shell и `.bashrc` для interactive shell, но точная цепочка зависит от способа запуска и дистрибутива.

При запуске команды Bash выполняет parsing, expansion, redirection, создание pipeline и запуск. Порядок важен: переменные и command substitution разворачиваются до выполнения, а redirection подключает descriptors к запуску процесса.

```bash
printf 'shell=%s flags=%s\n' "$0" "$-"
echo "$BASH_VERSION"
bash -c 'printf "non-interactive: %s\n" "$-"'
```

Скрипт должен явно задавать рабочий каталог, `PATH`, ожидаемые переменные и режим ошибок. Не следует полагаться на aliases, цветной вывод и настройки пользовательского профиля.

## Quoting

Двойные кавычки сохраняют пробелы и разрешают подстановку переменных. Одинарные кавычки отключают почти все подстановки. Без кавычек shell выполняет word splitting и pathname expansion. Это частая причина удаления или обработки не тех файлов.

## Типичные ошибки

- проверять скрипт только в интерактивной сессии;
- размещать обязательную настройку только в `.bashrc`;
- использовать alias в automation;
- забывать очищать чувствительные переменные;
- выполнять deployment из неожиданного working directory.

## Практика

Создайте скрипт, который выводит `PATH`, working directory, shell flags и environment. Запустите его через терминал, SSH, cron и `systemd`, сравните результаты.

## Следующие темы

`19-Pipes-Redirection-and-Exit-Codes.md` и `20-Bash-Scripting-Quoting-Functions-and-Traps.md`.
