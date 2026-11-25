# Документація кваліфікаційної роботи (Docs-as-Code)

Навігаційний реєстр інженерних артефактів проєкту **MedCenter** (Розділи 1 та 2).

## Статус етапів проєктування

| № з/п | Етап життєвого циклу документації | Артефакти | Статус |
| :--- | :--- | :--- | :--- |
| 1 | Концепція проєкту та науковий апарат | [`proposal.md`](./proposal.md) | `Done` |
| 2 | Аналіз предметної області та порівняння аналогів (Розділ 1) | [`1_analysis.md`](./1_analysis.md) | `Done` |
| 3 | Специфікація вимог за ISO/IEC/IEEE 29148 | [`srs.md`](./srs.md) | `Done` |
| 4 | Журнал архітектурних рішень (ADR-001) | [`adr/0001-choice-of-technology.md`](./adr/0001-choice-of-technology.md) | `Done` |
| 5 | Проєктування архітектури, баз даних та алгоритмів (Розділ 2) | [`2_design.md`](./2_design.md), [`openapi.yaml`](./api/openapi.yaml) | `Done` |
| 6 | Програмна реалізація та верифікація (Розділ 3) | [`3_implementation.md`](./3_implementation.md) | `Planned` |
| 7 | Загальні висновки кваліфікаційної роботи | [`4_conclusions.md`](./4_conclusions.md) | `Planned` |

## Трасування артефактів

1. Постановка задачі та NFR-метрики: [`1_analysis.md`](./1_analysis.md) $\rightarrow$ [`srs.md`](./srs.md)
2. Архітектура C4 Model (Levels 1–3) та моделі даних/алгоритмів: [`2_design.md`](./2_design.md)
3. Обґрунтування гібридного блокування слотів: [`adr/0001-choice-of-technology.md`](./adr/0001-choice-of-technology.md)
4. Міжсервісний REST-контракт OpenAPI 3.0.3: [`api/openapi.yaml`](./api/openapi.yaml)
5. Головний інженерний дашборд репозиторію: [`../README.md`](../README.md)
