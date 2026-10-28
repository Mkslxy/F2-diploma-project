# РОЗДІЛ 2. АРХІТЕКТУРНЕ ТА АЛГОРИТМІЧНЕ ПРОЄКТУВАННЯ СИСТЕМИ

## 2.1. Контекстне та контейнерне моделювання системи (C4 Model: Levels 1–2)

### 2.1.1. C4 Рівень 1: Діаграма системного контексту (System Context Diagram)
Діаграма відображає межі медичної інформаційної системи, ролі користувачів та зовнішні інтеграційні шлюзи.

```mermaid
C4Context
    title C4 Level 1: System Context Diagram для розподіленої системи управління медцентром

    Person(patient, "Пацієнт", "Зовнішній користувач: перегляд розкладу, онлайн-бронювання слота")
    Person(doctor, "Лікар", "Фахівець клініки: ведення прийому, перехід статусів візиту")
    Person(registrar, "Реєстратор", "Оператор рецепції: оперативне керування графіком та слотами")
    Person(manager, "Керівник клініки", "Аналітика: моніторинг утилізації лікарів і звітність")

    Enterprise_Boundary(b0, "Контур медичного центру") {
        System(med_system, "Медична інформаційно-аналітична система", "Забезпечує транзакційне бронювання слотів без колізій та асинхронний розрахунок аналітики завантаженості")
    }

    System_Ext(sms_gw, "SMS / Email Gateway", "Сервіс сповіщень пацієнтів про статус прийому")
    System_Ext(ehealth, "eHealth Integration Bus", "Національний контур медичних даних (HL7 FHIR)")
    System_Ext(liqpay, "Payment Gateway (LiqPay)", "Обробка платежів за медичні послуги")

    Rel(patient, med_system, "Перегляд розкладу та бронювання", "HTTPS / JSON")
    Rel(doctor, med_system, "Керування прийомами та статусами", "HTTPS / JSON")
    Rel(registrar, med_system, "Диспетчеризація слотів розкладу", "HTTPS / JSON")
    Rel(manager, med_system, "Перегляд аналітичних зрізів", "HTTPS / JSON")

    Rel(med_system, sms_gw, "Відправка повідомлень", "REST / HTTPS")
    Rel(med_system, ehealth, "Синхронізація медичних записів", "HL7 FHIR / HTTPS")
    Rel(med_system, liqpay, "Ініціалізація та верифікація платежу", "REST / HTTPS")
```

**Текстовий опис та трасування Context рівня:**
* Система підтримує 4 типи акторів, взаємодіючи через протокол `HTTPS / JSON` із застосуванням JWT-авторизації згідно з [SRS-FUN-01](./srs.md#2-functional-requirements).
* Зовнішня інтеграція із шлюзами сповіщень реалізує вимогу асинхронного сповіщення [SRS-FUN-08](./srs.md#2-functional-requirements).

---

### 2.1.2. C4 Рівень 2: Контейнерна діаграма (Container Diagram)
Діаграма демонструє фізичний поділ системи на окремо розгорнуті виконувані модулі, сервіси, бази даних та брокери відповідно до технологічного стеку.

```mermaid
C4Container
    title C4 Level 2: Container Diagram

    Person(user, "Користувачі системи", "Пацієнти, Лікарі, Адміністрація")

    Container_Boundary(c1, "Інфраструктура програмного комплексу") {
        Container(spa, "Frontend SPA Web Client", "Next.js 14, React, RTK Query, Tailwind", "Забезпечує реактивний інтерфейс, кешування стану розкладу та візуалізацію графіків")
        Container(api_gateway, "Backend Core API", "Python, Django REST Framework, Gunicorn", "Обробляє бізнес-правила, RBAC-авторизацію, контролери бронювання та API розкладу")
        Container(celery_worker, "Async Analytics Worker", "Celery, Python", "Виконує важкі фонові обчислення утилізації персоналу та формування теплових карт")
        ContainerDb(redis_store, "Redis Broker & In-Memory Store", "Redis 7.2", "Зберігає тимчасові ключі Hold Slot (TTL 420с), кеш аналітики та черги повідомлень Celery")
        Container(pg_bouncer, "Connection Pooler", "PgBouncer", "Оптимізує пул активних клієнтських підключень до реляційної бази даних")
        ContainerDb(postgres_db, "Primary Relational DB", "PostgreSQL 16", "Зберігає облікові дані, розклад, прийоми пацієнтів та аудит-журнал із дотриманням ACID")
    }

    Rel(user, spa, "Використовує інтерфейс", "HTTPS / TLS 1.3")
    Rel(spa, api_gateway, "API запити (REST)", "REST / JSON (HTTPS)")
    Rel(api_gateway, redis_store, "Тимчасове блокування слота (Hold)", "TCP / RESP Protocol")
    Rel(api_gateway, celery_worker, "Постановка задач на аналітику", "AMQP / Redis Broker")
    Rel(celery_worker, redis_store, "Кешування зрізів аналітики", "TCP / RESP Protocol")
    Rel(api_gateway, pg_bouncer, "Транзакційне збереження записів", "SQL / TCP (Port 6432)")
    Rel(celery_worker, pg_bouncer, "Читання даних для агрегації", "SQL / TCP (Port 6432)")
    Rel(pg_bouncer, postgres_db, "Пул з'єднань до СУБД", "SQL / TCP (Port 5432)")
```

**Текстовий опис та трасування зв'язків контейнерів:**
* Контейнер **Frontend SPA** реалізує клієнтське кешування за допомогою RTK Query згідно з [SRS-FUN-07](./srs.md#2-functional-requirements).
* Контейнер **Redis Broker & Store** реалізує патерн Hold Slot згідно з архітектурним рішенням [ADR-001](./adr/0001-choice-of-technology.md) та вимогою [SRS-FUN-02](./srs.md#2-functional-requirements).
* Контейнер **Backend Core API** забезпечує транзакційну фіксацію бронювання у **PostgreSQL** із песимістичним блокуванням за вимогою [SRS-FUN-04](./srs.md#2-functional-requirements).
* Воркери **Celery** здійснюють асинхронний розрахунок аналітики згідно з [SRS-FUN-06](./srs.md#2-functional-requirements) без деградації Latency основного API ([SRS-NFR-PERF-03](./srs.md#3-non-functional-requirements)).

---

## 2.2. Компонентне моделювання критичного сервісу (C4 Model: Component)
Для деталізації обрано найвідповідальніший сервіс бекенду — **Scheduling & Appointment Service** (сервіс диспетчеризації розкладу та бронювання). Діаграма відображає дотримання принципів Clean Architecture (розділення Inbound, Core та Outbound шарів).

```mermaid
C4Component
    title C4 Level 3: Component Diagram (Scheduling & Appointment Core Service)

    Container_Boundary(c_backend, "Backend Core Container (DRF)") {
        
        Boundary(inbound_layer, "Inbound / Presentation Layer") {
            Component(jwt_guard, "JWT Auth Middleware", "Django Middleware", "Валідує RS256 JWT токен та призначає контекст ролі RBAC")
            Component(dto_validator, "DTO Validation Serializer", "DRF Serializer", "Валідує вхідні параметри, типи та ліміти запиту")
            Component(appointment_ctrl, "Appointment Controller", "DRF APIView / ViewSet", "Маршрутизує запити бронювання, зміни статусів та скасувань")
        }

        Boundary(core_layer, "Core Domain & Service Layer (Clean Architecture)") {
            Component(appointment_service, "Appointment Service", "Python Service Class", "Координує бізнес-логіку бронювання, фіксацію статусів та запуск транзакцій")
            Component(hold_slot_mgr, "Hold Slot Manager", "Domain Logic", "Керує життєвим циклом тимчасових блокувань слотів у Redis")
            Component(appointment_fsm, "Appointment State Machine", "Domain Entity Logic", "Контролює валідність переходів статусів (SCHEDULED -> IN_PROGRESS)")
            Component(audit_service, "Audit Logging Service", "Domain Service", "Формує незмінний слід аудиторських подій для дій користувачів")
        }

        Boundary(outbound_layer, "Outbound / Infrastructure Layer") {
            Component(redis_client, "Redis Cache & Lock Adapter", "redis-py client", "Виконує атомарні операції SET NX EX та перевірку ключів у Redis")
            Component(appointment_repo, "Appointment Repository", "Django ORM / PostgreSQL", "Виконує транзакційні запити до СУБД із SELECT FOR UPDATE")
            Component(task_producer, "Celery Event Producer", "Celery Task Dispatcher", "Публікує події для фонового перерахунку завантаженості персоналу")
        }
    }

    Rel(jwt_guard, dto_validator, "Передає валідований контекст", "Internal Call")
    Rel(dto_validator, appointment_ctrl, "Передає очищені DTO", "Internal Call")
    Rel(appointment_ctrl, appointment_service, "Викликає операції бронювання", "Service Interface")

    Rel(appointment_service, hold_slot_mgr, "Перевіряє та встановлює Hold", "Domain Method")
    Rel(appointment_service, appointment_fsm, "Валідує перехід статусу", "FSM Transition Check")
    Rel(appointment_service, audit_service, "Логує зміну стану", "Audit Event")

    Rel(hold_slot_mgr, redis_client, "Атомарний лок (SET NX)", "Redis Protocol")
    Rel(appointment_service, appointment_repo, "Транзакційне збереження", "ORM Query / SELECT FOR UPDATE")
    Rel(appointment_service, task_producer, "Відправка події в чергу", "Async Message Emit")
```

**Текстовий опис та трасування компонентного рівня:**
* **Inbound Layer:** Контролює безпеку запитів за допомогою `JWT Auth Middleware` ([SRS-NFR-SEC-01](./srs.md#3-non-functional-requirements)) та перевіряє цілісність даних через `DTO Validation Serializer`.
* **Core Layer:** Ізолює чисту бізнес-логіку. `Hold Slot Manager` взаємодіє з Redis відповідно до [ADR-001](./adr/0001-choice-of-technology.md), а `Appointment State Machine` забезпечує виконання вимоги [SRS-FUN-05](./srs.md#2-functional-requirements).
* **Outbound Layer:** `Appointment Repository` реалізує паттерн Repository та унеможливлює прямий доступ контролерів до бази даних в обхід бізнес-правил (відсутність Tight Coupling та Spaghetti Code).

---

## AI Insights (AI Disclosure)
* **Промпт архітектурного рев'ю C4:** Перевірка відповідності діаграм C4 вимогам SRS та відсутність втрачених компонентів.
* **Зауваження ШІ-Рев'юера:** Було підсвічено відсутність пулу з'єднань PgBouncer на C4 Container та брак шару валідації DTO / Middleware на C4 Component.
* **Внесені виправлення:** Додано PgBouncer у схему контейнерів між API та PostgreSQL; на рівні компонентів додано DTO Validation Serializer та явний аудит-сервіс.
