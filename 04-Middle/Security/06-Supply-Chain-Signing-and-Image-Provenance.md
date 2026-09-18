# Supply chain, signing и image provenance

## Цель

Доказать происхождение deployable artifact и запретить неподтверждённые images на admission boundary.

## Prerequisites

Git, CI/CD, registry, SBOM, signatures, admission и threat modeling.

## In scope

Provenance связывает artifact с source commit, builder, dependencies и policy. Signature подтверждает integrity/identity подписавшего, но trust store и key lifecycle должны быть управляемыми. Admission проверяет digest, signature, source и vulnerability policy.

## Практика

Подпишите test image, настройте policy для signed digest, проверьте reject unsigned/unknown source и rotation signing key.

## Следующие темы

`07-Vulnerability-Management-and-Remediation.md`, StrongMiddle supply chain.
