# Git: object model и операции

## Модель

```text
Working Tree → Index → Commit → HEAD → Branch → Remote
```

Git хранит content-addressed objects: blob — содержимое файла, tree — снимок каталога, commit — tree, parents и metadata, tag — именованная ссылка. Branch — изменяемый ref на commit, HEAD указывает на текущую branch или detached commit.

## Зачем это нужно

Распределённая история даёт локальные commits, reviewable changes, branching и восстановление без постоянного центрального сервера. Remote — отдельный набор refs, а не «сама история Git».

## Операции

- `add` помещает снимок в index;
- `commit` создаёт immutable object;
- `fetch` обновляет remote-tracking refs без изменения working tree;
- `merge` создаёт join commit и сохраняет topology;
- `rebase` переносит commits на новую base и создаёт новые hashes;
- `cherry-pick` применяет один commit как новый;
- `reset` двигает ref/index/worktree согласно mode;
- `revert` создаёт inverse commit и безопаснее для public history;
- `stash` временно сохраняет незакоммиченные изменения;
- tag закрепляет release reference.

```bash
git status
git diff
git add -p
git commit -m 'change'
git log --graph --decorate --all
git fetch --prune
git switch -c feature/x
git rebase main
git reflog
git show HEAD^{tree}
```

## Conflict workflow

```text
merge/rebase
↓
conflict markers
↓
выбрать корректный результат
↓
git add
↓
continue
↓
test
```

Не делай rebase общей опубликованной branch без согласованной force-with-lease policy. Не используй `reset --hard` без понимания потери working tree; reflog помогает восстановить refs, но не является долгосрочным backup.

## Вопросы

- **Почему hash меняется после rebase?** Commit содержит parent hash; новая цепочка родителей создаёт новый object.
- **fetch и pull?** Fetch получает refs, pull добавляет merge/rebase policy.
- **reset и revert?** Reset меняет pointer истории, revert добавляет обратный commit.
- **Почему tag не абсолютно immutable?** Его можно перепривязать на server; release policy должна защищать tags.

## Как объяснить за 30–60 секунд

«Git — content-addressed DAG, а не просто каталог файлов. Working Tree, Index и Commit разделяют подготовку и фиксацию, Branch — ref, Remote — копия refs. Merge, rebase, reset и revert выбираются по тому, public ли история и нужен ли audit trail».

[[02-Junior/CI-CD/GitLab-Pipeline-Runner]], [[04-Middle/Architecture/Delivery-Architecture-and-Tradeoffs]].

## От commit до remote: подробная цепочка

1. Working Tree содержит текущие файлы.
2. `git add` помещает выбранные snapshots в Index.
3. `git commit` создаёт tree/blob/commit object и двигает current branch.
4. `HEAD` указывает, где находится пользователь.
5. `git fetch` получает remote objects и обновляет `origin/main` — remote-tracking ref.
6. `git push` просит remote обновить branch ref.
7. Remote разрешает fast-forward, если новый commit содержит старый commit в history.
8. Non-fast-forward отклоняется, если обновление затёрло бы чужую историю.

### Fast-forward и force-with-lease

Fast-forward не создаёт merge commit: branch pointer просто движется вперёд. При non-fast-forward история разошлась, и remote отказывается уничтожать commits.

`--force` безусловно перезаписывает remote ref и может удалить чужую работу. `--force-with-lease` разрешает переписывание только если remote ref всё ещё совпадает с последним известным состоянием client. Lease не делает операцию безопасной абсолютно: обнови fetch, проверь diff и предупреди участников.

```bash
git fetch origin
git log --oneline --graph --decorate HEAD origin/main
git push --force-with-lease origin feature/name
```

## Merge, rebase и revert

- Merge сохраняет ветвление и создаёт join commit.
- Rebase переносит собственные commits на новую base, переписывая hashes.
- Revert добавляет inverse commit и безопасен для public branch.
- Reset двигает ref и может изменить index/worktree; `--hard` удаляет незакоммиченные изменения.

## Практическое задание

1. Создай branch, два commit и remote-tracking branch.
2. Выполни fast-forward merge и найди отличие в graph.
3. Создай divergent history, реши conflict через merge.
4. Повтори через rebase и объясни новые hashes.
5. Откати опубликованный commit через revert.
6. Намеренно получи non-fast-forward и безопасно разберись через fetch/log.
7. Выполни `--force-with-lease` только после проверки lease.

## Security и production implications

- credentials и tokens не должны попадать в remote, commit или reflog без rotation;
- protected branches/tags и review уменьшают риск destructive push;
- signed commits/tags повышают доверие к происхождению, но не заменяют проверку build;
- `.gitignore` предотвращает новые утечки, но не удаляет secret из history;
- release должен ссылаться на immutable commit/tag и artifact digest.

## Дополнительные вопросы

- Что именно проверяет `--force-with-lease`?
- Почему rebase public branch опасен?
- Может ли reflog восстановить удалённый commit навсегда?
- Чем remote-tracking branch отличается от local branch?
- Почему commit hash зависит от parent и tree?
