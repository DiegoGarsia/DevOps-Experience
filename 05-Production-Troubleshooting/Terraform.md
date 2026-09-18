# Terraform troubleshooting guide

## State lock

Проверь, жив ли другой apply. Не используй `force-unlock` без проверки owner/lock age и backup state. Одновременное изменение state может привести к неправильному plan.

## Drift и unexpected destroy

```text
изменение .tf
↓
refresh/state
↓
resource schema/lifecycle
↓
dependencies/provider behavior
↓
plan review
```

Не применяй план, который не можешь объяснить. Проверяй адрес ресурса, immutable attributes, `count/for_each`, import и provider version.

## Потеря state

Инфраструктура может продолжать работать, но Terraform потерял mapping. Восстанови backup remote state или аккуратно импортируй существующие objects; не создавай дубль blind apply.

## Prevention

Remote backend с locking/versioning, review plan, protected state, secret rotation, provider lock file, малые модули и регулярная проверка restore state.

[[05-Production-Troubleshooting/Universal-Method]], [[02-Junior/IaC/Terraform-Ansible]], [[04-Middle/Security/Security-Model-and-Secrets]].

## Сценарий: неожиданный destroy

Не выполняй apply. Сохрани plan. Проверь resource address, state, provider version, immutable attribute, `count/for_each`, dependencies и drift. Сравни с `terraform state show`; если object существует, рассмотри import, а не recreate.

## Сценарий: state lock

Узнай владельца и возраст lock, проверь активный CI/apply. Если процесс жив, не делай force-unlock. Если lock stale, сохрани evidence и только затем удали lock по policy. Prevention: remote backend, locking, state backup и serialised pipeline.
