# JDBC и Hibernate: конспект для собеседования

---

## Часть 1: JDBC

### Что такое JDBC?

**JDBC (Java Database Connectivity)** — стандартный Java API для работы с реляционными базами данных. Позволяет подключаться к БД, выполнять SQL-запросы, читать и изменять данные без привязки к конкретной СУБД.

**Зачем нужен:**
- Единый интерфейс для разных СУБД (PostgreSQL, MySQL, Oracle и т.д.)
- Драйвер меняется — код остаётся тем же
- Низкоуровневый контроль над запросами и транзакциями

---

### Основные классы и интерфейсы

| Класс/интерфейс | Назначение |
|-----------------|------------|
| **DriverManager** | Получение соединения по URL, логину и паролю |
| **DataSource** | Предпочтительный способ: пул соединений, настройка через JNDI или конфиг |
| **Connection** | Соединение с БД. Создание Statement, управление транзакциями |
| **Statement** | Выполнение статического SQL. Уязвим к SQL Injection |
| **PreparedStatement** | SQL с параметрами `?`. Защита от injection, переиспользование плана |
| **CallableStatement** | Вызов stored procedures |
| **ResultSet** | Результат SELECT. Итерация по строкам, чтение колонок |

---

### Жизненный цикл: от подключения до результата

```
DriverManager/DataSource → Connection → Statement/PreparedStatement → ResultSet
         ↑                      ↑                    ↑                      ↑
   Получение соединения   Управление TX        Выполнение SQL          Чтение данных
```

---

### Простые примеры кода

#### 1. Подключение и простой SELECT

```java
// Ручное подключение (для понимания — в реальности обычно DataSource)
try (Connection conn = DriverManager.getConnection(
        "jdbc:postgresql://localhost:5432/mydb",
        "user",
        "password")) {

    try (Statement stmt = conn.createStatement();
         ResultSet rs = stmt.executeQuery("SELECT id, name FROM users")) {

        while (rs.next()) {
            long id = rs.getLong("id");
            String name = rs.getString("name");
            System.out.println(id + ": " + name);
        }
    }
}
```

#### 2. PreparedStatement — защита от SQL Injection

```java
String login = request.getParameter("login");  // Может быть злонамеренным

// Плохо: Statement — уязвим к injection
// "'; DROP TABLE users; --" сломает запрос
// Statement stmt = conn.createStatement();
// stmt.executeQuery("SELECT * FROM users WHERE login = '" + login + "'");

// Хорошо: PreparedStatement — параметры экранируются
try (PreparedStatement ps = conn.prepareStatement(
        "SELECT * FROM users WHERE login = ?")) {
    ps.setString(1, login);

    try (ResultSet rs = ps.executeQuery()) {
        if (rs.next()) {
            return mapToUser(rs);
        }
    }
}
```

#### 3. INSERT с параметрами

```java
try (PreparedStatement ps = conn.prepareStatement(
        "INSERT INTO users (name, email) VALUES (?, ?)")) {
    ps.setString(1, "Ivan");
    ps.setString(2, "ivan@mail.ru");
    int rows = ps.executeUpdate();  // Для INSERT/UPDATE/DELETE
    // rows — количество затронутых строк
}
```

#### 4. Транзакция

```java
conn.setAutoCommit(false);  // Выключаем автокоммит
try {
    // несколько операций
    updateBalance(conn, fromId, -100);
    updateBalance(conn, toId, 100);
    conn.commit();
} catch (Exception e) {
    conn.rollback();
    throw e;
} finally {
    conn.setAutoCommit(true);
}
```

#### 5. Получение сгенерированного ID (INSERT ... RETURNING / getGeneratedKeys)

```java
try (PreparedStatement ps = conn.prepareStatement(
        "INSERT INTO users (name) VALUES (?)",
        Statement.RETURN_GENERATED_KEYS)) {
    ps.setString(1, "Anna");
    ps.executeUpdate();

    try (ResultSet keys = ps.getGeneratedKeys()) {
        if (keys.next()) {
            long id = keys.getLong(1);
            // используем id
        }
    }
}
```

---

### Важные методы

| Метод | Где | Назначение |
|-------|-----|------------|
| `getConnection(url, user, pass)` | DriverManager | Получение соединения |
| `createStatement()` / `prepareStatement(sql)` | Connection | Создание Statement |
| `executeQuery()` | Statement | SELECT → ResultSet |
| `executeUpdate()` | Statement | INSERT/UPDATE/DELETE → int |
| `execute()` | Statement | Любой SQL, boolean (есть ResultSet или нет) |
| `next()` | ResultSet | Сдвиг на следующую строку |
| `getLong(1)`, `getString("name")` | ResultSet | Чтение значений по индексу или имени |
| `setString(1, value)`, `setInt(2, value)` | PreparedStatement | Установка параметров |
| `setAutoCommit(false)` | Connection | Ручное управление транзакциями |
| `commit()` / `rollback()` | Connection | Фиксация или откат |

---

### try-with-resources — обязательно

`Connection`, `Statement`, `ResultSet` реализуют `AutoCloseable`. Всегда используй try-with-resources, иначе соединения не освободятся и будет утечка:

```java
try (Connection conn = dataSource.getConnection();
     PreparedStatement ps = conn.prepareStatement(sql);
     ResultSet rs = ps.executeQuery()) {
    // работа с rs
}  // close вызывается автоматически
```

---

## Часть 2: ORM и Hibernate

### Что такое ORM?

**ORM (Object-Relational Mapping)** — технология маппинга объектов Java на таблицы БД и обратно. Вместо ручного `ResultSet` и SQL пишешь Java-объекты, а ORM генерирует SQL и заполняет объекты.

**Зачем:**
- Меньше boilerplate (нет тысяч строк JDBC)
- Типобезопасность, работа с объектами
- Кроссплатформенность (HQL/JPQL вместо диалекта SQL)
- Кеширование, lazy loading, управление жизненным циклом сущностей

**Минусы:** сложность, риски N+1, «магия», производительность при незнании внутренностей.

---

### Что такое Hibernate?

**Hibernate** — самая популярная ORM для Java. Реализует JPA (Java Persistence API) — стандарт, описывающий аннотации и поведение ORM. Spring Data JPA поверх Hibernate добавляет репозитории и удобные методы.

### JPA vs Hibernate: спецификация и реализация

| | JPA | Hibernate |
|---|-----|-----------|
| **Что это** | Спецификация (API, контракт) | Реализация этой спецификации |
| **Пакет** | `javax.persistence` (JPA 2) / `jakarta.persistence` (JPA 3) | `org.hibernate` |
| **Кто определяет** | Java EE / Jakarta EE | Red Hat / JBoss |
| **Альтернативы** | EclipseLink, OpenJPA, DataNucleus | — |

**Суть:** JPA — это «интерфейс»: аннотации `@Entity`, `@Id`, `@OneToMany` и т.д. определены в спецификации. Hibernate — конкретная «имплементация», которая эти аннотации обрабатывает и генерирует SQL.

Писать код лучше против JPA (импорты из `javax.persistence` / `jakarta.persistence`), тогда при смене провайдера (Hibernate → EclipseLink) код не ломается. Hibernate-специфичные фичи (`@BatchSize`, `@Filter`, нативные API) привязывают к Hibernate.

---

### Основные аннотации JPA/Hibernate

| Аннотация | Назначение |
|-----------|------------|
| `@Entity` | Класс — сущность, маппится на таблицу |
| `@Table(name = "users")` | Имя таблицы (по умолчанию — имя класса) |
| `@Id` | Первичный ключ |
| `@GeneratedValue(strategy = ...)` | Автогенерация ID (IDENTITY, SEQUENCE, TABLE) |
| `@Column(name = "email", nullable = false)` | Маппинг колонки, ограничения |
| `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany` | Связи между сущностями |
| `@JoinColumn` | FK-колонка для связи |
| `@Fetch(FetchType.LAZY)` / `FetchType.EAGER` | Стратегия загрузки связанных сущностей |
| `@Version` | Оптимистичная блокировка |
| `@Transactional` | Транзакционность (Spring) |

---

### Пример сущности

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 255)
    private String name;

    @Column(unique = true)
    private String email;

    @OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
    private List<Order> orders = new ArrayList<>();

    @Version
    private Integer version;

    // getters, setters
}
```

---

### Связи: mappedBy, cascade, orphanRemoval

#### mappedBy

Указывает, кто **владеет** связью. Владеющая сторона хранит FK. `mappedBy` ставится на **обратной** стороне — там, где FK нет.

```java
// Order — владелец (есть колонка user_id)
@Entity
public class Order {
    @ManyToOne
    @JoinColumn(name = "user_id")  // FK здесь
    private User user;
}

// User — обратная сторона (FK в Order)
@Entity
public class User {
    @OneToMany(mappedBy = "user")  // "user" — имя поля в Order
    private List<Order> orders = new ArrayList<>();
}
```

Без `mappedBy` Hibernate создаст две FK-колонки (в обеих таблицах). `mappedBy` говорит: «связь уже описана в Order.user».

#### cascade

Определяет, какие операции с родителем **каскадно** применяются к детям.

```java
@OneToMany(mappedBy = "user", cascade = CascadeType.PERSIST)
private List<Order> orders;

// Теперь:
User u = new User();
u.getOrders().add(new Order());  // Order ещё не в БД
userRepository.save(u);          // Сохранит и User, и Order — без явного save(order)
```

| CascadeType | Эффект |
|-------------|--------|
| PERSIST | persist(parent) → persist(children) |
| MERGE | merge(parent) → merge(children) |
| REMOVE | remove(parent) → remove(children) |
| REFRESH | refresh(parent) → refresh(children) |
| ALL | Все вышеперечисленные |

**Осторожно:** `CascadeType.ALL` на `@OneToMany` — удаление User удалит все Order. Используй только если это ожидаемое поведение.

#### orphanRemoval

При `orphanRemoval = true` удаление ссылки на ребёнка **удалит** запись из БД (ребёнок стал «сиротой»).

```java
@OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
private List<Order> orders = new ArrayList<>();

// ...
user.getOrders().remove(order);  // order отвязан от user
// При flush — DELETE FROM orders WHERE id = ?
// Без orphanRemoval — только UPDATE orders SET user_id = NULL (если nullable)
```

Типичное использование: родитель «владеет» списком (например, заказ и позиции заказа). Удаление позиции из списка = удаление из БД.

---

### @Transactional: propagation, readOnly, сессия

`@Transactional` (Spring) открывает транзакцию и привязывает к ней Hibernate-сессию. Без транзакции сессия либо не открывается, либо сразу закрывается — отсюда LazyInitializationException.

#### Propagation (распространение)

Как вести себя, если транзакция уже есть:

| Значение | Поведение |
|----------|-----------|
| **REQUIRED** (по умолчанию) | Использовать текущую транзакцию или создать новую |
| **REQUIRES_NEW** | Всегда создать новую транзакцию. Текущую приостановить. Полезно для логирования в отдельной TX — rollback основной не откатит лог |
| **SUPPORTS** | Работать в транзакции, если она есть; иначе — без неё |
| **MANDATORY** | Обязательно должна быть транзакция, иначе исключение |
| **NOT_SUPPORTED** | Выполнять без транзакции (приостановить текущую, если есть) |
| **NEVER** | Выполнять без транзакции; если есть — исключение |
| **NESTED** | Вложенная savepoint внутри текущей транзакции |

**Пример REQUIRES_NEW:**
```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void logAudit(String action) {
    auditRepository.save(new AuditLog(action));
}
// Вызов из метода с @Transactional — лог сохранится даже при rollback вызывающего
```

#### readOnly

`@Transactional(readOnly = true)` — подсказка: только чтение. Плюсы:
- БД может оптимизировать (реплики для чтения)
- Hibernate не делает dirty checking — меньше overhead
- Защита от случайной записи: flush вызовет ошибку

Для read-only операций (отчёты, получение сущностей) лучше указывать `readOnly = true`.

#### Когда открывается сессия

Сессия Hibernate привязана к транзакции. При входе в `@Transactional` метод:
1. Открывается транзакция (если ещё нет при REQUIRED)
2. Открывается/привязывается EntityManager (сессия)
3. При выходе из метода — commit/rollback, закрытие сессии

Поэтому lazy-поля доступны только **внутри** `@Transactional` метода. После return сессия закрыта — обращение к lazy-полю даст LazyInitializationException.

---

### Проблема N+1

**Суть:** При загрузке сущностей со связями для каждой родительской строки выполняется отдельный запрос к связанной таблице. 1 запрос для списка + N запросов для связей = N+1 запросов.

**Пример:**
```java
List<User> users = userRepository.findAll();  // 1 SELECT * FROM users
for (User u : users) {
    u.getOrders().size();  // N SELECT * FROM orders WHERE user_id = ?
}
// Итого: 1 + N запросов
```

**Решения:**

1. **JOIN FETCH** (один запрос с JOIN):
```java
@Query("SELECT u FROM User u JOIN FETCH u.orders")
List<User> findAllWithOrders();
```

2. **EntityGraph**:
```java
@EntityGraph(attributePaths = {"orders"})
@Query("SELECT u FROM User u")
List<User> findAllWithOrders();
```

3. **Batch size** (снизить количество запросов):
```java
@BatchSize(size = 10)
@OneToMany(mappedBy = "user")
private List<Order> orders;
// Загрузит orders для 10 users за один запрос
```

4. **Проверять через логи** `spring.jpa.show-sql=true` и смотреть количество запросов.

---

### Уровни кеша в Hibernate

Hibernate использует кеширование, чтобы не ходить в БД при повторном доступе к тем же данным.

| Уровень | Область | Жизненный цикл |
|---------|---------|----------------|
| **1L / Session cache** | В рамках одной сессии (транзакции) | Пока открыт `EntityManager` / `Session` |
| **2L / SessionFactory cache** | Общая для всех сессий | Пока живёт приложение |
| **Query cache** | Кеш результатов запросов (JPQL/HQL) | Пока живёт 2L, инвалидируется при изменениях |

#### Первый уровень (1L)

- По умолчанию включён, отключить нельзя.
- Одна сессия = одна транзакция (при `@Transactional`).
- Один и тот же `EntityManager` вернёт один и тот же объект по ID без повторного SELECT.
- После `flush()`/`commit()` сессия закрывается, кеш очищается.

```java
User u1 = userRepository.findById(1L);  // SELECT
User u2 = userRepository.findById(1L);  // Без SELECT — из 1L
// u1 == u2 (тот же экземпляр)
```

#### Второй уровень (2L)

- Нужно включить явно и подключить провайдер (Ehcache, Hazelcast, Caffeine).
- Объекты кешируются между разными транзакциями.
- Настраивается через `@Cacheable`, `@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)`.

```java
@Entity
@Cacheable
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
public class User { ... }
```

- **Query cache** кеширует результаты запросов по параметрам. Используется редко, т.к. легко инвалидируется.

---

### Жизненный цикл сущности (Entity states)

| Состояние | Описание |
|-----------|----------|
| **Transient** | Создан через `new`, не связан с persistence context |
| **Managed** | Привязан к EntityManager, изменения отслеживаются |
| **Detached** | Раньше был managed, сессия закрыта |
| **Removed** | Помечен на удаление (`remove()`) |

```java
User u = new User();           // Transient
em.persist(u);                 // Managed
em.detach(u);                  // Detached
em.merge(u);                   // Снова Managed
em.remove(u);                  // Removed
em.flush();                    // SQL DELETE
```

---

### Lazy vs Eager

| Тип | Когда загружается | Плюсы | Минусы |
|-----|-------------------|-------|--------|
| **LAZY** | При первом обращении к полю (в рамках сессии) | Меньше данных, меньше запросов | LazyInitializationException вне сессии |
| **EAGER** | Сразу при загрузке родителя | Всегда доступны | Риск N+1, лишние JOIN |

**Рекомендация:** по умолчанию `LAZY` для `@OneToMany`, `@ManyToMany`. Загружать связи явно через `JOIN FETCH` там, где нужны.

---

### LazyInitializationException

Возникает, когда обращаешься к lazy-полю **вне транзакции** (сессия уже закрыта):

```java
@Transactional
public User getUser(Long id) {
    return userRepository.findById(id);  // orders — LAZY
}
// После выхода из метода сессия закрыта

User u = userService.getUser(1L);
u.getOrders().size();  // LazyInitializationException!
```

**Решения:** загрузить orders в рамках транзакции (`JOIN FETCH`), или использовать `@Transactional(readOnly = true)` на методе, который обращается к orders, или DTO без lazy-полей.

---

### Dirty Checking

Hibernate отслеживает изменения managed-сущностей. При `flush()`/`commit()` генерируются UPDATE только для изменённых полей — без явного `save()`:

```java
User u = userRepository.findById(1L);  // Managed
u.setName("New Name");
// userRepository.save(u) не нужен!
// При flush/commit — UPDATE users SET name = ? WHERE id = 1
```

---

### Типичные вопросы на собеседовании

**Чем PreparedStatement лучше Statement?**  
Защита от SQL Injection, переиспользование плана запроса, типобезопасность параметров.

**Что такое ORM?**  
Маппинг объектов на таблицы БД. Плюсы: меньше кода, типобезопасность. Минусы: N+1, сложность, «магия».

**Что такое N+1 и как решать?**  
N+1 запросов при загрузке связанных сущностей. Решение: JOIN FETCH, EntityGraph, BatchSize.

**Уровни кеша Hibernate?**  
1L — в рамках сессии, 2L — между сессиями (нужен провайдер), Query cache — кеш запросов.

**LazyInitializationException — когда и почему?**  
Обращение к lazy-полю вне транзакции. Решение: загружать связи в транзакции или через JOIN FETCH.

**Dirty Checking?**  
Hibernate сам отслеживает изменения managed-сущностей и генерирует UPDATE при flush.

**JPA и Hibernate — в чём разница?**  
JPA — спецификация (API), Hibernate — реализация. Код пишем против JPA, работает Hibernate.

**Что такое mappedBy?**  
Указывает обратную сторону связи. FK хранится у владельца (сторона с @JoinColumn). mappedBy — на стороне без FK.

**Когда нужен REQUIRES_NEW?**  
Когда логика должна закоммититься в отдельной транзакции, даже если основная откатится (например, аудит).

**Зачем readOnly в @Transactional?**  
Оптимизация: нет dirty checking, можно читать с реплики. Защита от случайной записи.

uide/html_single/Hibernate_User_Guide.html)
- [Spring Data JPA Reference](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
