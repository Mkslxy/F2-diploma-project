# Інженерна документація кваліфікаційного проєкту (F2-diploma-project)

## Тема проєкту
* **Українською:** Розробка розподіленої інформаційно-аналітичної системи управління медичним центром із модулем оптимізації та аналізу завантаженості спеціалістів
* **English:** Engineering of a Distributed Healthcare Management System with Specialist Workload Optimization and Analytics
* **Спеціальність:** 121 «Інженерія програмного забезпечення» (освітня програма F2)
* **Технологічний стек:** Next.js (App Router), TypeScript, React, RTK Query, Python / Django REST Framework, Celery, Redis, PostgreSQL, Docker, GitHub Actions

---

## Навігаційний дашборд документації

| Розділ / Артефакт | Опис | Посилання | Статус |
| :--- | :--- | :--- | :--- |
| **Thesis Proposal** | Концепція проєкту, стек, актуальність та аналоги | [`proposal.md`](./proposal.md) | `Done` |
| **Розділ 1 (Аналітика)** | Аналіз предметної області (As-Is), To-Be модель, Core Features | [`1_analysis.md`](./1_analysis.md) | `Done` |
| **Розділ 1 (Моноліт .docx)** | Повний зведений академічний документ першого розділу (8 сторінок) | [`F2_Diploma_Chapter1.docx`](./F2_Diploma_Chapter1.docx) / [Notion PKM](https://app.notion.com/p/F2_Diploma_-_-3dedff09ecc68016a39dc7f487367050) | `Done` |
| **Специфікація вимог (SRS)** | Контракт вимог за міжнародним стандартом ISO/IEC/IEEE 29148 | [`srs.md`](./srs.md) | `Done` |
| **Архітектурні рішення (ADR)** | Реєстр рішень (ADR-001: Hold Slot vs Pessimistic DB Lock) | [`adr/0001-choice-of-technology.md`](./adr/0001-choice-of-technology.md) | `Done` |
| **Розділ 2 (Проєктування)** | C4 Model (Levels 1–3), наскрізне трасування вимог | [`2_design.md`](./2_design.md) | `In Progress` |
| **Розділ 3 (Реалізація)** | Архітектура коду, тестування k6, DevSecOps | [`3_implementation.md`](./3_implementation.md) | `Planned` |
| **Висновки** | Загальні результати кваліфікаційної роботи | [`4_conclusions.md`](./4_conclusions.md) | `Planned` |

---

## Зведена матриця нефункціональних вимог (NFRs Matrix)

| Індекс вимоги | Категорія | Технічний показник | Цільове значення (2026) | Метод валідації |
| :--- | :--- | :--- | :--- | :--- |
| **SRS-NFR-PERF-01** | Продуктивність | Latency p95 (читання розкладу) | **<= 120 мс** при 300 RPS | k6 Spike / Load Testing |
| **SRS-NFR-PERF-02** | Продуктивність | Latency p95 (транзакція бронювання) | **<= 250 мс** при 100 RPS | k6 Concurrent Stress Test |
| **SRS-NFR-PERF-03** | Продуктивність | Час розрахунку аналітики (Async) | **<= 3.0 с** (фонова черга Celery) | Prometheus / Celery Flower |
| **SRS-NFR-SCAL-01** | Масштабованість | Архітектура бекенду | **Stateless**, до 10 реплік pods | Docker Compose / Kube HPA |
| **SRS-NFR-SCAL-02** | Масштабованість | Пул підключень до PostgreSQL | **До 1000 клієнтів** (PgBouncer) | PgBouncer pool profiling |
| **SRS-NFR-SEC-01** | Безпека | Автентифікація та авторизація | **OAuth 2.0 / JWT (RS256)** | OWASP ZAP / JWT Inspector |
| **SRS-NFR-SEC-02** | Безпека | Шифрування даних | **TLS 1.3** (трафік), **AES-256** (БД) | Trivy / SonarQube Scanner |
| **SRS-NFR-SEC-03** | Безпека | Мережевий захист та ліміти | **Rate Limiting 100 req/min** | Nginx / Django Ratelimit |
| **SRS-NFR-REL-01** | Надійність | Доступність системи (Uptime SLA) | **SLA >= 99.9%** | Uptime Kuma / Health Check |
| **SRS-NFR-REL-02** | Надійність | Disaster Recovery (PostgreSQL WAL) | **RPO <= 15 хв, RTO <= 60 хв** | pg_dump / WAL backup scripts |
