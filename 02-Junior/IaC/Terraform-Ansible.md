# IaC: Terraform и Ansible

## Две модели

```text
Terraform: desired infrastructure → provider/API → state/plan/apply
Ansible: desired host configuration → SSH/modules → idempotent changes
```

Terraform отвечает, какие ресурсы существуют и как связаны. Ansible отвечает, какое состояние внутри host/service должно быть. Они пересекаются в provisioning, но не являются заменами друг друга.

## Terraform

```text
HCL → provider schema → dependency graph → refresh state → plan → apply
```

State — mapping config address к remote object и attributes. Backend/locking защищают concurrent changes. Drift — различие remote reality и state/config; plan является evidence, а не разрешением на apply.

## Ansible

```text
inventory → playbook → play(hosts) → task(module) → handler
```

Idempotency означает сходимость к одному состоянию при повторном запуске. `changed` должен означать реальную mutation. Handler выполняет restart только после изменения уведомившей задачи. Facts и variable precedence влияют на результат.

## Объединённый workflow

```text
Terraform создаёт VM/network
↓
ожидание доступного SSH
↓
Ansible создаёт users/packages/config
↓
проверка service
↓
развёртывание application/container
```

Используй explicit dependencies только когда graph не может вывести их сам. Не превращай provisioner в полноценное configuration management. Защищай state: sensitive values могут сохраняться даже при `sensitive=true`.

## Вопросы

- **Зачем Terraform, если Ansible может создать объект?** Terraform отслеживает resource lifecycle через graph/state/plan, Ansible в основном сходится к конфигурации host.
- **Зачем idempotency?** Безопасный повтор после partial failure и исправление drift.
- **Что делать после потери state?** Восстановить backup или импортировать существующие addresses до plan; не делать blind apply.

## Как объяснить за 30–60 секунд

«IaC — это repeatable change с review и convergence. Terraform владеет infrastructure graph и state, Ansible конфигурирует host через idempotent modules. Их contract: Terraform выдаёт доступные outputs, Ansible меняет только согласованное состояние и безопасно повторяется».

[[04-Middle/Architecture/State-Consistency-and-Scaling]], [[05-Production-Troubleshooting/Terraform]], [[04-Middle/Security/Security-Model-and-Secrets]].

## Термины Terraform

Provider — адаптер к API. Resource — объект, которым управляет Terraform. Data source — чтение внешнего объекта. Dependency graph — порядок операций. State — mapping addresses к remote objects. Drift — ручное изменение remote reality. Lifecycle rules определяют replace/update/protection.

Перед `apply` читай plan как diff: `+` create, `~` update, `-` destroy, `-/+` replace. Не принимай `-/+` без понимания, какая attribute immutable и есть ли backup.

```bash
terraform fmt -check
terraform validate
terraform plan -out=tfplan
terraform show tfplan
terraform state list
terraform state show ADDRESS
```

## Термины Ansible

Inventory определяет hosts/groups. Play связывает hosts и tasks. Module реализует одну операцию. Role структурирует переиспользование. Handler запускается по notify. Fact — автоматически собранное состояние host. Idempotency — повторный запуск приводит к тому же состоянию, а не каждый раз создаёт mutation.

```bash
ansible-inventory --graph
ansible all -m ping
ansible-playbook site.yml --check --diff
ansible-playbook site.yml --limit HOST -vvv
```

`--check` — прогноз, не полная гарантия; module может не уметь dry-run. `--diff` не включай для секретов.

## Практическое задание

1. Создай VM resource, измени variable и прочитай plan до apply.
2. Удали/исправь state только на копии и восстанови mapping через import.
3. Напиши Ansible role с user, package, template и handler.
4. Запусти её дважды и добейся отсутствия изменений во втором запуске.
5. Сломай переменную precedence и найди фактическое значение.
6. Вынеси secret в Vault/Ansible Vault и проверь, что он не попадает в log/diff.
