# Документація кваліфікаційної роботи (Docs-as-Code)

Навігаційний реєстр інженерних артефактів проєкту **MedCenter** (Розділи 1 та 2).

## Статус етапів проєктування

| № з/п | Етап життєвого циклу документації | Артефакти | Статус |
| :--- | :--- | :--- | :--- |
| 1 | Концепція проєкту та науковий апарат | [`proposal.md`](./proposal.md) | `Done` |
| 2 | Аналіз предметної області та порівняння аналогів (Розділ 1) | [`1_analysis.md`](./1_analysis.md) | `Done` |
| 3 | Специфікація вимог за ISO/IEC/IEEE 29148 | [`srs.md`](./srs.md) | `Done` |
| 4 | Журнал архітектурних рішень (ADR-001) | [`adr/0001-choice-of-technology.md`](./adr/0001-choice-of-technology.md) | `Done` |
| 5 | Проєктування архітектури, баз даних, API, Use Cases та алгоритмів (Розділ 2) | [`2_design.md`](./2_design.md), [`openapi.yaml`](./api/openapi.yaml), [`use_case_diagram.png`](./diagrams/use_case_diagram.png) | `Done` |
| 6 | Зведений моноліт пояснювальної записки (Розділи 1 та 2, `.docx`) | [`F2_Diploma_Chapter1_2_Petykhachnyi.docx`](./F2_Diploma_Chapter1_2_Petykhachnyi.docx) | `Done` |
| 7 | Програмна реалізація та верифікація (Розділ 3) | [`3_implementation.md`](./3_implementation.md) | `Planned` |
| 8 | Загальні висновки кваліфікаційної роботи | [`4_conclusions.md`](./4_conclusions.md) | `Planned` |

## Трасування артефактів

1. Постановка задачі та NFR-метрики: [`1_analysis.md`](./1_analysis.md) $\rightarrow$ [`srs.md`](./srs.md)
2. Консолідований Розділ 2 (C4, AppSec, API Topology, Use Cases, ERD, Алгоритми): [`2_design.md`](./2_design.md)
3. Обґрунтування гібридного блокування слотів: [`adr/0001-choice-of-technology.md`](./adr/0001-choice-of-technology.md)
4. Міжсервісний REST-контракт OpenAPI 3.0.3: [`api/openapi.yaml`](./api/openapi.yaml)
5. Поведінкова UML-діаграма прецедентів (StarUML + PNG): [`use_case_diagram.png`](./diagrams/use_case_diagram.png) / [`medcenter_use_case.mdj`](./diagrams/medcenter_use_case.mdj)
6. Зведений моноліт Розділів 1 та 2: [`F2_Diploma_Chapter1_2_Petykhachnyi.docx`](./F2_Diploma_Chapter1_2_Petykhachnyi.docx)
7. Головний інженерний дашборд репозиторію: [`../README.md`](../README.md)
