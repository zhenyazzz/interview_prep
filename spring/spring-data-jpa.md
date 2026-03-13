# Spring Data и Spring Data JPA — руководство для подготовки к собеседованию

Подробное описание Spring Data, JPA, работы с сущностями, транзакциями и производительностью для подготовки к собеседованиям Java Backend.

---

## Table of Contents (оглавление)

- [Введение в Spring Data](#введение-в-spring-data)
- [Что такое ORM](#что-такое-orm)
- [Что такое JPA](#что-такое-jpa)
- [Hibernate](#hibernate)
- [Spring Data JPA](#spring-data-jpa)
- [Entities](#entities)
- [Entity Lifecycle](#entity-lifecycle)
- [Persistence Context](#persistence-context)
- [Dirty Checking](#dirty-checking)
- [Репозитории](#репозитории)
- [Query Methods](#query-methods)
- [Custom Queries](#custom-queries)
- [JPQL](#jpql)
- [Pagination](#pagination)
- [Fetch Strategies](#fetch-strategies)
- [Lazy vs Eager Loading](#lazy-vs-eager-loading)
- [N+1 Problem](#n1-problem)
- [Transactions](#transactions)
- [@Transactional](#transactional)
- [Propagation](#propagation)
- [Isolation Levels](#isolation-levels)
- [Locking](#locking)
- [Optimistic Locking](#optimistic-locking)
- [Pessimistic Locking](#pessimistic-locking)
- [Performance Optimization](#performance-optimization)
- [Краткий конспект для собеседования](#краткий-конспект-для-собеседования)

---

# Введение в Spring Data

## Что такое Spring Data

**Spring Data** — семейство проектов Spring, которое унифицирует и упрощает доступ к данным: репозитории, абстракция над хранилищем, генерация запросов по соглашениям об именах методов. Подмодули: Spring Data JPA (реляционные БД через JPA), Spring Data MongoDB, Spring Data Redis, Spring Data Elasticsearch и др. Общая идея — один стиль работы с разными хранилищами через интерфейсы репозиториев.

## Какую проблему решает

- **Дублирование кода** — типовые CRUD и простые запросы не нужно писать вручную; достаточно объявить интерфейс с нужными методами.
- **Разные технологии доступа** — JPA, JDBC, NoSQL; Spring Data даёт общую модель репозиториев и конвенции (имена методов, пагинация, сортировка).
- **Интеграция с Spring** — транзакции, конфигурация, тесты; репозитории — обычные бины в контексте.

## Как упрощает работу с БД

- **Репозитории** — интерфейс, наследующий JpaRepository/CrudRepository; реализация создаётся Spring Data во время выполнения (прокси). Методы типа `findByEmail`, `findByNameAndStatus` реализуются автоматически по имени.
- **Меньше boilerplate** — не нужно писать EntityManager, типовые запросы, открытие/закрытие транзакций для простых операций.
- **Единый стиль** — пагинация (Pageable, Page), сортировка (Sort), кастомные запросы (@Query) в одном API.

---

# Что такое ORM

## Object Relational Mapping

**ORM (Object-Relational Mapping)** — техника отображения **объектной модели** (классы Java) на **реляционную модель** (таблицы, столбцы, связи). Позволяет работать с данными как с объектами (поля, связи, коллекции), а не с SQL и ResultSet. ORM сам генерирует SQL (или использует написанный разработчиком) и маппит результаты в объекты.

## Какие проблемы решает

- **Ручной маппинг** — не нужно вручную писать SELECT и заполнять объекты из ResultSet.
- **Различие моделей** — объекты и таблицы имеют разную структуру (наследование, коллекции, связи); ORM скрывает эту разницу через маппинг и стратегии загрузки.
- **Портативность** — один и тот же код (JPA) работает с разными СУБД при смене диалекта и драйвера.
- **Управление жизненным циклом** — кэш первого уровня (persistence context), отслеживание изменений (dirty checking), каскады.

Минусы: сложность отладки SQL, риск N+1 и лишних запросов, необходимость понимания того, как ORM генерирует запросы.

---

# Что такое JPA

## Java Persistence API

**JPA (Java Persistence API)** — **спецификация** (набор интерфейсов и правил) для ORM в Java. Описывает, как маппить сущности, как работать с EntityManager, как писать JPQL, как настраивать транзакции и т.д. JPA не содержит реализации — это контракт, которому следуют **провайдеры** (Hibernate, EclipseLink, OpenJPA и др.).

## JPA как спецификация

- Определяет аннотации: @Entity, @Table, @Id, @GeneratedValue, @OneToMany, @ManyToOne и т.д.
- Определяет API: EntityManager, EntityManagerFactory, Query, Criteria API.
- Определяет язык запросов JPQL (Java Persistence Query Language).
- Реализация (Hibernate) интерпретирует спецификацию и генерирует SQL, управляет кэшем и жизненным циклом сущностей.

Приложение пишет код против JPA (и опционально Spring Data JPA); смена провайдера при сохранении совместимости со спецификацией не требует переписывания кода.

---

# Hibernate

## Что такое Hibernate

**Hibernate** — популярная **реализация JPA** (провайдер). Помимо требований JPA, предоставляет расширения: дополнительные аннотации, критерии, фильтры, поддержку специфичных возможностей БД. В экосистеме Spring обычно используют JPA с провайдером Hibernate (через spring-boot-starter-data-jpa).

## Как Hibernate реализует JPA

- Реализует интерфейсы JPA: EntityManager → Session (внутренняя абстракция Hibernate), EntityManagerFactory → SessionFactory.
- Интерпретирует аннотации JPA и свои (@Entity, маппинг связей, каскады, fetch type).
- Генерирует SQL по операциям (persist, merge, remove) и по JPQL/Criteria; использует диалект для конкретной СУБД.
- Управляет persistence context (кэш первого уровня), dirty checking, flush, загрузкой связей (lazy/eager).

Spring создаёт EntityManagerFactory (часто через LocalContainerEntityManagerFactoryBean), инжектит EntityManager в репозитории и сервисы; транзакции управляются через Spring @Transactional.

---

# Spring Data JPA

## Как Spring Data работает поверх JPA

Spring Data JPA добавляет слой **репозиториев** поверх JPA:

- Вы объявляете интерфейс, наследующий JpaRepository<Entity, Id> (или CrudRepository, PagingAndSortingRepository).
- Во время выполнения Spring Data создаёт **прокси** (реализацию этого интерфейса), который использует EntityManager и свои компоненты (QueryLookupStrategy, конвертеры).
- Вызовы методов типа findById, findAll, findByEmail делегируются в реализацию: построение запроса по имени метода или по @Query, выполнение через JPA, возврат сущностей или DTO.

То есть Spring Data JPA не заменяет JPA/Hibernate, а упрощает типовые сценарии (CRUD, запросы по соглашению об именах, пагинация) и интегрирует их с транзакциями и контекстом Spring.

## Как упрощает работу с репозиториями

- Не нужно писать класс репозитория и внедрять EntityManager — достаточно интерфейса.
- **Query methods** — имя метода определяет запрос: findByStatusAndCreatedAtAfter → WHERE status = ? AND created_at > ?.
- **@Query** — JPQL или нативный SQL для сложных запросов.
- **Пагинация и сортировка** — Pageable, Sort, Page из коробки.
- **Специфичные расширения** — @EntityGraph, @Modifying, проекции, спецификации (Specification).

---

# Entities

## @Entity

Класс, помеченный **@Entity**, отображается на таблицу в БД. JPA/Hibernate управляет его жизненным циклом в рамках persistence context. У сущности должен быть **первичный ключ** (поле с @Id). По умолчанию имя таблицы совпадает с именем класса; имя можно переопределить через **@Table**.

## @Table

**@Table(name = "users", schema = "app")** — задаёт имя таблицы и опционально схему. Без аннотации используется имя класса (в snake_case в зависимости от настроек).

## @Id

**@Id** — поле (или свойство) является **первичным ключом**. Тип обычно Long, UUID или составной ключ (@EmbeddedId / @IdClass).

## @GeneratedValue

**@GeneratedValue** — значение ключа генерируется БД или провайдером. Стратегии:

| Стратегия | Описание |
|-----------|----------|
| **IDENTITY** | БД генерирует при INSERT (AUTO_INCREMENT, SERIAL). |
| **SEQUENCE** | Последовательность БД (SEQUENCE в Oracle, PostgreSQL). |
| **TABLE** | Отдельная таблица для генерации id (редко). |
| **AUTO** | Провайдер выбирает стратегию (часто SEQUENCE или IDENTITY). |

Пример:

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String email;
    private String name;
    // getters, setters, конструктор по умолчанию
}
```

---

# Entity Lifecycle

## Состояния сущности (Entity states)

Сущность в JPA может находиться в одном из состояний:

| Состояние | Описание |
|-----------|----------|
| **Transient (новый)** | Объект создан (new), не связан с persistence context. После persist() переходит в managed. |
| **Managed (управляемый)** | Сущность привязана к persistence context (загружена через find/getReference или после persist). Изменения отслеживаются (dirty checking); при flush отправляются в БД. |
| **Detached (отсоединённый)** | Ранее была managed, но контекст закрыт или сущность отвязана (detach). Изменения не отслеживаются. Чтобы снова сохранить — merge(). |
| **Removed (удаляемый)** | Помечена на удаление (remove()). При flush выполнится DELETE. |

Переходы: new → **persist** → managed; managed → **close/clear/detach** → detached; detached → **merge** → managed; managed → **remove** → removed; после flush removed выходит из контекста.

## Схема переходов

```
  [Transient]  ──persist──►  [Managed]  ──remove──►  [Removed]
                    │              ▲
                    │              │ merge
                    │ detach       │
                    ▼              │
              [Detached]  ─────────┘
```

В типичном Spring-приложении: в одной транзакции сущность загружается (managed), меняется, при commit делается flush → изменения пишутся в БД. После выхода из транзакции контекст закрывается — сущность становится detached. При следующем запросе загрузка снова даёт managed в новом контексте.

---

# Persistence Context

## Что такое persistence context

**Persistence context** — кэш (набор) сущностей, которыми управляет EntityManager в рамках одной транзакции (или одной сессии). В нём хранятся сущности в состоянии **managed**. Один контекст привязан к одному EntityManager; при JPA с транзакционным scope (по умолчанию в Spring) контекст живёт столько же, сколько транзакция: при открытии транзакции создаётся/используется контекст, при commit/rollback он закрывается.

## Как работает EntityManager

- **persist(entity)** — помещает сущность в контекст (managed); при flush будет INSERT.
- **find(id)** — ищет в контексте; если нет — запрос в БД, результат помещается в контекст.
- **merge(entity)** — если сущность detached, создаётся копия в состоянии managed и возвращается; при flush будет UPDATE.
- **remove(entity)** — помечает сущность на удаление; при flush — DELETE.
- **flush()** — синхронизация контекста с БД (INSERT/UPDATE/DELETE), без commit транзакции.
- **clear()** — очистка контекста; все сущности становятся detached.

## Как Spring управляет persistence context

- Spring создаёт **EntityManager** на транзакцию (по умолчанию): при входе в метод с @Transactional открывается транзакция и при первом обращении к EntityManager (через репозиторий или @PersistenceContext) получается EntityManager, привязанный к этой транзакции. При выходе из метода (commit/rollback) транзакция и контекст закрываются.
- Один контекст на одну транзакцию — все репозитории и сервисы в одной транзакции видят один и тот же набор managed-сущностей; изменения, сделанные в одном месте, видны в другом и при flush попадут в БД.

---

# Dirty Checking

## Что такое dirty checking

**Dirty checking** — механизм JPA/Hibernate: для сущностей в состоянии **managed** провайдер отслеживает изменения полей. При **flush** (явном или перед выполнением запроса, при commit) провайдер сравнивает текущее состояние сущностей с тем, что было при загрузке (или при последнем flush), и для изменившихся сущностей генерирует UPDATE. Разработчику не нужно вызывать update вручную — достаточно изменить поле у загруженной сущности в той же транзакции.

## Как Hibernate отслеживает изменения

- Для сущностей, загруженных в текущем контексте, Hibernate хранит «снимок» состояния при загрузке (или при последнем flush).
- При flush (или при запросах, которые требуют синхронизации) для каждой managed-сущности сравниваются текущие значения полей со снимком. При отличии выполняется UPDATE.
- Требования: сущность должна быть в одном контексте (managed), не отвязана (detached). Для полей с lazy-коллекциями при обращении к коллекции она подгружается и тоже может участвовать в dirty checking.

Ограничения: не срабатывает для **detached** сущностей (их нужно merge); для очень больших графов объектов dirty checking может быть затратным (можно отключить для сущности через @DynamicUpdate и т.п., но это уже тонкая настройка).

---

# Репозитории

## Иерархия интерфейсов

| Интерфейс | Назначение |
|-----------|------------|
| **CrudRepository<T, ID>** | Базовые CRUD: save, findById, findAll, count, delete, existsById. |
| **PagingAndSortingRepository<T, ID>** | Расширяет CrudRepository: findAll(Pageable), findAll(Sort). |
| **JpaRepository<T, ID>** | Расширяет PagingAndSortingRepository; добавляет flush, saveAndFlush, deleteInBatch, getOne (getReference), методы для работы с пагинацией и сортировкой в стиле JPA. |

В приложениях обычно наследуют **JpaRepository**, чтобы иметь полный набор методов и интеграцию с JPA (пагинация, batch-операции, getReference).

Пример:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    List<User> findByStatusOrderByCreatedAtDesc(Status status);
}
```

Реализацию писать не нужно — Spring Data создаёт прокси, который выполняет запросы через JPA.

---

# Query Methods

## Query methods (методы запросов)

**Query methods** — методы репозитория, реализация которых **выводится из имени метода** по соглашению Spring Data. Парсер разбирает префикс (find…By, get…By, query…By, count…By, delete…By) и часть после By — условия (поля сущности + ключевые слова And, Or, Between, LessThan, Like, IgnoreCase и т.д.). По ним строится JPQL (или запрос провайдера).

## Naming conventions

- **findBy…**, **getBy…**, **queryBy…** — выборка; возврат Entity, List, Optional, Stream, Page (с Pageable).
- **countBy…** — подсчёт.
- **deleteBy…**, **removeBy…** — удаление (часто в отдельной транзакции или с @Modifying).
- После **By** перечисляются **поля** и **модификаторы**: And, Or, Between, LessThan, GreaterThan, Like, Containing, StartingWith, EndingWith, IgnoreCase, OrderBy…

Примеры:

```java
Optional<User> findByEmail(String email);
List<User> findByNameAndStatus(String name, Status status);
List<User> findByCreatedAtBetween(LocalDateTime from, LocalDateTime to);
Page<User> findByStatus(Status status, Pageable pageable);
long countByStatus(Status status);
```

Поля в имени метода должны соответствовать полям сущности (или вложенным полям через _).

---

# Custom Queries

## @Query

**@Query** — задаёт запрос вручную: JPQL (по умолчанию) или нативный SQL (nativeQuery = true). Параметры: по имени (`:name`) или по позиции (?1). Для нативного запроса важен порядок столбцов и маппинг на сущность (или DTO через конструктор/интерфейс).

Пример JPQL:

```java
@Query("SELECT u FROM User u WHERE u.status = :status AND u.createdAt > :after")
List<User> findActiveUsers(@Param("status") Status status, @Param("after") LocalDateTime after);
```

Пример нативного запроса:

```java
@Query(value = "SELECT * FROM users WHERE status = ?1", nativeQuery = true)
List<User> findByStatusNative(int status);
```

## JPQL

**JPQL** — язык запросов JPA. Оперирует **сущностями и полями**, а не таблицами. Синтаксис похож на SQL: SELECT u FROM User u WHERE u.email = :email. Поддерживаются join, fetch join, подзапросы, агрегаты. Провайдер транслирует JPQL в SQL с учётом диалекта БД.

---

# Pagination

## Page и Pageable

- **Pageable** — интерфейс запроса пагинации: номер страницы (0-based), размер страницы, Sort. Создаётся через `PageRequest.of(page, size)` или `PageRequest.of(page, size, Sort.by("name"))`.
- **Page<T>** — результат запроса с элементами страницы, общим количеством элементов (totalElements), количеством страниц (totalPages), флагами hasNext/hasPrevious. Репозиторий принимает Pageable и возвращает Page<T> при методе типа `Page<User> findByStatus(Status status, Pageable pageable)`.

Пример:

```java
Page<User> page = userRepository.findByStatus(Status.ACTIVE, PageRequest.of(0, 20, Sort.by("name").asc()));
List<User> content = page.getContent();
long total = page.getTotalElements();
```

Для подсчёта total Spring Data выполняет дополнительный COUNT-запрос; если общее количество не нужно, можно использовать **Slice<T>** (только следующая страница есть/нет, без COUNT).

---

# Fetch Strategies

## LAZY и EAGER

**FetchType** у связей (@OneToMany, @ManyToOne и т.д.) задаёт, когда загружать связанные сущности:

| Тип | Описание |
|-----|----------|
| **LAZY** | Связь не загружается при загрузке владельца; загрузка при первом обращении к полю/коллекции (в той же сессии/транзакции). По умолчанию для @OneToMany, @ManyToMany. |
| **EAGER** | Связь загружается вместе с владельцем (дополнительный запрос или join). По умолчанию для @ManyToOne, @OneToOne. |

Рекомендация: по возможности использовать **LAZY** и подгружать связи явно (fetch join, @EntityGraph) только там, где нужно, чтобы избежать лишних запросов и N+1.

Пример:

```java
@OneToMany(mappedBy = "order", fetch = FetchType.LAZY)
private List<OrderItem> items;

@ManyToOne(fetch = FetchType.LAZY)  // переопределить дефолт EAGER при необходимости
private User user;
```

---

# Lazy vs Eager Loading

- **Lazy** — меньше запросов при загрузке корневой сущности; риск **N+1**, если обходить коллекции вне управляемого контекста или без fetch join. Нужно либо инициализировать связи в транзакции (fetch join, @EntityGraph), либо принимать возможные дополнительные запросы.
- **EAGER** — всегда тянет связь; при нескольких таких связях или цепочках — много join’ов или запросов, риск лишней нагрузки и «забытых» lazy-исключений при отключённом контексте. Для коллекций EAGER обычно не рекомендуют.

На собеседовании важно: по умолчанию коллекции — LAZY; при доступе к коллекции в транзакции без fetch join выполняется отдельный запрос на каждую такую коллекцию (N+1). Решение — явная загрузка в одном запросе (JOIN FETCH, @EntityGraph) или batch size.

---

# N+1 Problem

## Что такое N+1

**N+1** — ситуация, когда для выборки N корневых сущностей выполняется **1 запрос** на саму выборку и **N дополнительных запросов** для загрузки связей (например, коллекций) у каждой из N сущностей. Итого 1 + N запросов. Происходит при LAZY-связях: при обходе коллекции у каждой сущности провайдер подгружает её в отдельном запросе.

## Когда возникает

- Загружаются сущности (findAll или по критерию); у каждой есть LAZY-коллекция.
- В цикле или при сериализации (JSON) происходит обращение к этой коллекции → для каждой сущности выполняется отдельный SELECT по связи.
- Аналогично для цепочки связей (order → items → product).

## Как обнаружить

- Включить логирование SQL (например, Hibernate: show_sql, format_sql) и увидеть множество однотипных запросов к одной связи после одного основного запроса.
- Профилирование БД и мониторинг количества запросов на один HTTP-запрос.

## Как решить

### JOIN FETCH

В одном запросе (JPQL) загрузить корневую сущность и связь через **JOIN FETCH**. Коллекция заполняется в том же запросе, дополнительные запросы не выполняются.

```java
@Query("SELECT o FROM Order o JOIN FETCH o.items WHERE o.id = :id")
Optional<Order> findByIdWithItems(@Param("id") Long id);

@Query("SELECT DISTINCT o FROM Order o JOIN FETCH o.items")
List<Order> findAllWithItems();
```

DISTINCT нужен, когда один корневой объект дублируется из-за join по коллекции (одна строка на элемент коллекции).

### @EntityGraph

Указывает, какие связи загружать **eagerly** в этом запросе, не меняя глобальный fetch type.

```java
@EntityGraph(attributePaths = {"items", "items.product"})
@Query("SELECT o FROM Order o")
List<Order> findAllWithItems();
```

Или через метод репозитория:

```java
@EntityGraph(attributePaths = "items")
List<Order> findAllByStatus(Status status);
```

### Batch fetching

Настройка в маппинге: **@BatchSize(size = 10)**. При обращении к LAZY-коллекциям провайдер подгружает их не по одной, а пачками (например, для 100 заказов — 10 запросов по 10 коллекций). N+1 остаётся по смыслу, но количество запросов снижается (N/ batch_size + 1).

```java
@OneToMany(mappedBy = "order", fetch = FetchType.LAZY)
@BatchSize(size = 10)
private List<OrderItem> items;
```

Итого: лучший контроль дают **JOIN FETCH** и **@EntityGraph** (один запрос); **batch size** — компромисс без изменения запроса.

---

# Transactions

## Зачем нужны транзакции

- **Атомарность** — группа операций выполняется целиком или откатывается целиком (commit/rollback).
- **Изоляция** — видимость изменений других транзакций (уровни изоляции).
- **Согласованность** — ограничения БД и приложения выполняются после commit.
- В контексте JPA транзакция также определяет границы **persistence context**: при commit выполняется flush и изменения уходят в БД; при rollback контекст откатывается.

## Как Spring управляет транзакциями

- **@Transactional** на методе или классе — при вызове метода открывается транзакция (или используется существующая, в зависимости от propagation), перед выходом — commit (при нормальном завершении) или rollback (при исключении). Управление делегируется менеджеру транзакций (PlatformTransactionManager); для JPA используется JpaTransactionManager, который привязывает EntityManager к текущей транзакции.
- Один EntityManager (и один persistence context) на транзакцию — все вызовы репозиториев и сервисов в одной транзакции видят одни и те же managed-сущности.

---

# @Transactional

## Основные параметры

| Параметр | Назначение |
|----------|------------|
| **propagation** | Поведение при наличии уже открытой транзакции (см. [Propagation](#propagation)). |
| **isolation** | Уровень изоляции (READ_COMMITTED и т.д.). |
| **timeout** | Таймаут транзакции в секундах. |
| **readOnly** | Подсказка: только чтение (оптимизация, запрет flush у Hibernate для чистого чтения). |
| **rollbackFor / noRollbackFor** | При каких исключениях делать rollback (по умолчанию — unchecked). |

Пример:

```java
@Transactional(readOnly = true, timeout = 5)
public User getById(Long id) { ... }

@Transactional(rollbackFor = Exception.class)
public void saveOrder(Order order) { ... }
```

---

# Propagation

## Режимы распространения транзакции

| Режим | Поведение |
|-------|-----------|
| **REQUIRED** (по умолчанию) | Если есть текущая транзакция — участвовать в ней; если нет — создать новую. |
| **REQUIRES_NEW** | Всегда создавать новую транзакцию; текущую приостановить. При выходе из метода — commit/rollback только своей транзакции. |
| **SUPPORTS** | Если есть транзакция — участвовать; если нет — выполнять без транзакции. |
| **NOT_SUPPORTED** | Выполнять без транзакции; текущую приостановить. |
| **MANDATORY** | Требуется существующая транзакция; иначе исключение. |
| **NEVER** | Транзакции быть не должно; иначе исключение. |
| **NESTED** | Вложенная «savepoint»-транзакция внутри текущей; откат вложенной не откатывает внешнюю (если провайдер поддерживает savepoints). |

Типичное использование: **REQUIRED** для большинства методов; **REQUIRES_NEW** для логирования/аудита, которое должно сохраниться даже при rollback основной операции; **READ_ONLY** + **SUPPORTS** для методов только чтения.

---

# Isolation Levels

## Уровни изоляции (кратко)

| Уровень | Описание |
|---------|----------|
| **READ_UNCOMMITTED** | Видны незакоммиченные изменения других транзакций (грязное чтение). |
| **READ_COMMITTED** | Видны только закоммиченные данные; повторное чтение той же строки может дать другое значение (non-repeatable read). |
| **REPEATABLE_READ** | В рамках транзакции повторное чтение той же строки даёт то же значение; возможны фантомные чтения (новые строки). |
| **SERIALIZABLE** | Полная изоляция; как правило, блокировки, наименьшая параллельность. |

По умолчанию в большинстве БД и в Spring — **READ_COMMITTED**. Задать в аннотации: `@Transactional(isolation = Isolation.REPEATABLE_READ)`.

---

# Locking

## Зачем нужен locking

- **Конкурентное обновление** — две транзакции не должны незаметно перезаписать изменения друг друга.
- **Чтение согласованных данных** — избежать грязного/неповторяемого чтения при необходимости (уровни изоляции и явные блокировки).

В JPA есть **оптимистическая** блокировка (по версии/дате, без блокировок в БД) и **пессимистическая** (SELECT FOR UPDATE и аналоги — блокировка строк в БД).

---

# Optimistic Locking

## @Version

**Оптимистическая блокировка** — сущность имеет поле **версии** (число или дата), аннотированное **@Version**. При UPDATE в WHERE добавляется условие version = :oldVersion. Если к этому моменту другая транзакция уже обновила строку (и увеличила version), условие не выполняется и обновляется 0 строк → провайдер выбрасывает **OptimisticLockException**. Приложение может повторить операцию или сообщить пользователю о конфликте.

```java
@Entity
public class Order {
    @Id
    @GeneratedValue
    private Long id;
    @Version
    private Long version;
    // ...
}
```

Плюсы: нет блокировок в БД, высокая параллельность. Минусы: конфликты обнаруживаются при commit; нужна стратегия повтора или обработки ошибки.

---

# Pessimistic Locking

## SELECT FOR UPDATE

**Пессимистическая блокировка** — явная блокировка строк в БД на время транзакции. В JPA задаётся через **LockModeType**: при запросе сущности указывают, например, **PESSIMISTIC_WRITE** (обычно SELECT ... FOR UPDATE). Другие транзакции, пытающиеся заблокировать те же строки, ждут или получают таймаут. После commit/rollback блокировка снимается.

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("SELECT o FROM Order o WHERE o.id = :id")
Optional<Order> findByIdForUpdate(@Param("id") Long id);
```

Или при find: entityManager.find(Order.class, id, LockModeType.PESSIMISTIC_WRITE);

Плюсы: гарантия, что между чтением и обновлением данные не изменятся. Минусы: блокировки, риск взаимных блокировок и снижения параллельности; нужно держать транзакции короткими.

---

# Performance Optimization

## Batching

- **Batch insert/update** — Hibernate может группировать INSERT/UPDATE в пачки (например, 50 за раз) через настройку `hibernate.jdbc.batch_size`. Снижает количество round-trip’ов к БД.
- Для batch-вставки лучше использовать **persist** в цикле и периодически **flush/clear**, чтобы не раздувать persistence context.

## Fetch joins

- **JOIN FETCH** в JPQL загружает связь в одном запросе, устраняя N+1. Использовать для нужных связей; не перегружать один запрос множеством fetch (получается декартово произведение и дубликаты без DISTINCT).

## Indexes

- Индексы по полям, участвующим в WHERE, JOIN, ORDER BY — ускоряют запросы. Уникальный индекс по полю версии при оптимистической блокировке. Составные индексы под конкретные запросы.

## Query optimization

- Выбирать только нужные поля (проекции, DTO) вместо целых сущностей, где достаточно.
- Пагинация на уровне БД (LIMIT/OFFSET или keyset) вместо загрузки всего набора.
- Избегать N+1: @EntityGraph, JOIN FETCH, batch size.
- readOnly = true для транзакций только чтения; при необходимости — отдельный read-only replica.

---

# Краткий конспект для собеседования

## Entity lifecycle

**Transient** — создан, не в контексте. **Managed** — в persistence context, изменения отслеживаются. **Detached** — контекст закрыт или отвязан. **Removed** — помечена на удаление. Переходы: persist → managed; detach/close → detached; merge → managed; remove → removed.

## Persistence context

Кэш сущностей у EntityManager; одна транзакция — один контекст в типичной настройке Spring. В контексте сущности в состоянии managed; flush синхронизирует с БД; при commit контекст закрывается, сущности становятся detached.

## Dirty checking

Для managed-сущностей провайдер отслеживает изменения полей; при flush генерируется UPDATE для изменившихся сущностей. Вызывать update вручную не нужно. Для detached не работает — нужен merge.

## N+1 problem

Один запрос на выборку N сущностей + N запросов на LAZY-связи при обращении к ним. Решения: **JOIN FETCH**, **@EntityGraph** (один запрос), **@BatchSize** (уменьшение числа запросов).

## Transactions

@Transactional задаёт границы транзакции; Spring открывает/закрывает транзакцию и привязывает EntityManager к ней. Один контекст на транзакцию; при commit — flush и commit в БД.

## Propagation

REQUIRED — участвовать в текущей или создать новую. REQUIRES_NEW — всегда новая, текущую приостановить. MANDATORY — только в существующей. SUPPORTS — с транзакцией или без. NOT_SUPPORTED / NEVER — без транзакции. NESTED — вложенная (savepoint).

## Isolation

READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE. По умолчанию обычно READ_COMMITTED. Задаётся в @Transactional(isolation = ...).

---

*Документ предназначен для подготовки к техническим собеседованиям Java Backend. Рекомендуется дополнять материалами по Spring Core, Hibernate и практикой с репозиториями и транзакциями.*
