# Тестирование в Spring и Spring Boot

Руководство по unit- и интеграционному тестированию для подготовки к собеседованиям Java Backend.

---

## Table of Contents (оглавление)

- [Введение в тестирование](#введение-в-тестирование)
- [Типы тестов](#типы-тестов)
- [Unit Tests](#unit-tests)
- [Integration Tests](#integration-tests)
- [JUnit](#junit)
- [Mockito](#mockito)
- [Mocking](#mocking)
- [Spring Boot Testing](#spring-boot-testing)
- [@SpringBootTest](#springboottest)
- [Slice Tests](#slice-tests)
- [MockMvc](#mockmvc)
- [Testcontainers](#testcontainers)
- [Best Practices тестирования](#best-practices-тестирования)
- [Краткий конспект для собеседования](#краткий-конспект-для-собеседования)

---

# Введение в тестирование

## Зачем нужны тесты

- **Регрессии** — изменения в коде не ломают уже работающую функциональность; падающий тест сразу указывает на проблему.
- **Документация** — тесты показывают, как должен вести себя код при заданных входных данных и условиях.
- **Уверенность при рефакторинге** — можно менять реализацию, опираясь на зелёные тесты.
- **Раннее обнаружение ошибок** — дешевле найти баг в тестах до продакшена.

## Какие проблемы решают

- **Ручная проверка** — автоматические тесты выполняются за секунды при каждом коммите или в CI.
- **Сложность воспроизведения** — сценарии зафиксированы в коде; любой разработчик может запустить тесты в той же среде.
- **Изоляция** — unit-тесты проверяют один класс без реальной БД и сети; интеграционные — взаимодействие компонентов в контролируемых условиях.

## Роль тестирования в backend-разработке

В backend тесты проверяют бизнес-логику (сервисы, расчёты), доступ к данным (репозитории, транзакции), API (контроллеры, сериализация, коды ответов) и интеграцию с БД и внешними сервисами. Типичный стек: JUnit 5, Mockito, Spring Test (контекст, MockMvc), Testcontainers для реальной БД в интеграционных тестах.

---

# Типы тестов

## Основные типы

| Тип | Масштаб | Что проверяет | Зависимости |
|-----|---------|----------------|-------------|
| **Unit (юнит)** | Один класс/метод | Логику в изоляции | Зависимости подменяются моками |
| **Integration (интеграционные)** | Несколько компонентов или слой | Взаимодействие (сервис + репозиторий, контроллер + сервис, приложение + БД) | Реальные или тестовые (in-memory БД, контейнеры) |
| **End-to-End (E2E)** | Вся система | Сценарий от запроса до ответа (как пользователь) | Реальное окружение или максимально близкое |

## Таблица сравнения

| Критерий | Unit | Integration | E2E |
|----------|------|-------------|-----|
| **Скорость** | Очень быстро (мс) | Средне (секунды) | Медленно (минуты) |
| **Надёжность** | Высокая (мало внешних факторов) | Зависит от окружения | Зависит от инфраструктуры |
| **Цель** | Правильность логики | Правильность связки компонентов | Правильность сценария для пользователя |
| **Частота запуска** | Каждый коммит | В CI, перед релизом | Реже, smoke/sanity |
| **Сложность отладки** | Проще (малый объём кода) | Средняя | Сложнее (много слоёв) |

---

# Unit Tests

## Что такое unit-тест

**Unit-тест** — тест, проверяющий **одну единицу кода** (класс, метод) в **изоляции**. Все внешние зависимости (другие сервисы, репозитории, БД, сеть) подменяются **моками** или заглушками. Тест управляет только входными данными и ожидаемым результатом (и при необходимости поведением моков). Цель — проверить логику самого класса, а не интеграцию.

## Характеристики unit-тестов

- **Быстрые** — нет обращений к БД, сети, файловой системе.
- **Детерминированные** — один и тот же результат при повторном запуске.
- **Изолированные** — не зависят от порядка выполнения других тестов и от внешнего состояния.
- **Сфокусированные** — один тест проверяет один сценарий (один путь выполнения или одну граничную ситуацию).
- **Самодостаточные** — всё необходимое задаётся в тесте (arrange — act — assert).

Типичная структура: **Arrange** (подготовка данных и моков), **Act** (вызов тестируемого метода), **Assert** (проверка результата и при необходимости вызовов моков).

---

# Integration Tests

## Что такое integration-тест

**Интеграционный тест** проверяет **взаимодействие нескольких компонентов**: сервис + репозиторий, контроллер + сервис, приложение + реальная (или контейнерная) БД, вызов внешнего API. Зависимости либо реальные (БД в контейнере, in-memory БД), либо тестовые двойники с предсказуемым поведением. Цель — убедиться, что компоненты правильно работают вместе (маппинг, транзакции, SQL, сериализация).

## Какие компоненты тестируются

- **Слой доступа к данным** — репозитории (JPA, JDBC) против реальной или тестовой БД; корректность запросов и маппинга.
- **Сервисный слой** — сервис + репозиторий в одной транзакции; бизнес-правила и сохранение данных.
- **Веб-слой** — контроллеры (MockMvc или реальный сервер); маппинг URL, коды ответов, тело ответа, валидация.
- **Полный контекст** — @SpringBootTest с поднятым приложением и БД (например, Testcontainers); сквозной сценарий без реального HTTP извне.

---

# JUnit

## Что такое JUnit

**JUnit** — фреймворк для написания и запуска **повторяемых тестов** в Java. Позволяет объявлять тестовые методы, запускать их, проверять результаты (assertions) и организовывать жизненный цикл (до/после каждого теста или класса). Текущий стандарт — **JUnit 5 (Jupiter)**; в Spring Boot по умолчанию подключается через spring-boot-starter-test.

## Как используется в Java

- Тестовый класс содержит методы, помеченные **@Test**.
- Запуск: из IDE (Run Test) или из сборки (Maven: mvn test, Gradle: test). JUnit собирает все @Test-методы и выполняет их (по умолчанию в произвольном порядке; изоляция между методами).
- Проверки: **Assertions** (assertEquals, assertTrue, assertThrows и т.д.). При падении assertion тест помечается как failed с сообщением.

## @Test

Метод с аннотацией **@Test** считается тестовым и будет выполнен. Имя метода обычно описывает сценарий (например, `shouldReturnUserWhenExists`). Можно параметризовать тесты: **@ParameterizedTest** с источниками аргументов.

```java
@Test
void shouldAddTwoNumbers() {
    int result = calculator.add(2, 3);
    assertEquals(5, result);
}

@Test
void shouldThrowWhenDividingByZero() {
    assertThrows(ArithmeticException.class, () -> calculator.divide(1, 0));
}
```

## @BeforeEach и @AfterEach

- **@BeforeEach** — метод выполняется **перед каждым** @Test в данном классе. Используется для подготовки общих данных, сброса состояния, инициализации моков.
- **@AfterEach** — выполняется **после каждого** @Test. Используется для очистки ресурсов, сброса состояния.

Аналоги для уровня класса: **@BeforeAll** / **@AfterAll** (один раз на класс; метод должен быть static для JUnit 5, если не используется @TestInstance(Lifecycle.PER_CLASS))).

```java
@BeforeEach
void setUp() {
    repository = new InMemoryUserRepository();
    service = new UserService(repository);
}

@AfterEach
void tearDown() {
    repository.clear();
}
```

---

# Mockito

## Что такое Mockito

**Mockito** — библиотека для создания **моков** (mock), **шпионов** (spy) и проверки взаимодействий (сколько раз вызван метод, с какими аргументами). Позволяет подменять зависимости в тестах: задавать возвращаемые значения (when(...).thenReturn(...)), проверять вызовы (verify(mock).method(...)). Интегрируется с JUnit 5 через **@ExtendWith(MockitoExtension.class)** или в Spring — через **@MockBean** в тестовом контексте.

## Зачем используется mocking

- **Изоляция** — тестируемый класс не зависит от реальной БД, внешнего API или тяжёлых сервисов.
- **Контроль сценариев** — можно имитировать исключения, задержки, разные ответы (например, «пользователь не найден»).
- **Проверка взаимодействий** — убедиться, что метод вызвал зависимость с нужными аргументами и нужное число раз.

## @Mock

**@Mock** (или **Mockito.mock(Class)**) создаёт **mock-объект** указанного типа. Все методы по умолчанию возвращают null, 0 или false; поведение настраивается через **when(...).thenReturn(...)** и т.д. В JUnit 5 для активации @Mock нужен **@ExtendWith(MockitoExtension.class)** на классе.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    void shouldReturnUserWhenFound() {
        when(userRepository.findById(1L)).thenReturn(Optional.of(new User(1L, "test@example.com")));
        Optional<User> result = userService.findById(1L);
        assertTrue(result.isPresent());
        assertEquals("test@example.com", result.get().getEmail());
    }
}
```

## @InjectMocks

**@InjectMocks** помечает экземпляр класса, в который Mockito должен **внедрить** созданные в этом классе **@Mock** (по совпадению типов полей или конструктора). Создаётся один экземпляр тестируемого класса с подставленными моками; удобно для unit-тестов без Spring-контекста.

## @MockBean

**@MockBean** — аннотация **Spring Boot Test**: добавляет mock-бин в тестовый **ApplicationContext** (или подменяет существующий бин того же типа). Используется в интеграционных тестах (@SpringBootTest, @WebMvcTest и т.д.), когда нужно подменить реальный сервис или репозиторий в контексте. После теста подменённый бин удаляется из контекста.

```java
@WebMvcTest(UserController.class)
class UserControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    void shouldReturnUser() throws Exception {
        when(userService.findById(1L)).thenReturn(Optional.of(new User(1L, "a@b.com")));
        mockMvc.perform(get("/api/users/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.email").value("a@b.com"));
    }
}
```

---

# Mocking

## Что такое mock

**Mock (мок)** — объект-заглушка, реализующий тот же интерфейс (или расширяющий класс), что и реальная зависимость. Поведение методов задаётся в тесте: что возвращать, какое исключение бросать. Мок не содержит бизнес-логики; он только фиксирует ожидания и позволяет проверять вызовы (verify). Отличие от **stub**: стаб только подставляет ответы; мок часто используют и для проверки взаимодействий (был ли метод вызван, с какими аргументами).

## Когда использовать mock

- **Внешние зависимости** — БД, HTTP-клиенты, очереди сообщений, файловая система в unit-тестах.
- **Нестабильные или медленные компоненты** — внешний API, сложная инициализация.
- **Проверка взаимодействий** — «сервис должен один раз вызвать repository.save с этим объектом».
- **Граничные и ошибочные сценарии** — «если репозиторий выбрасывает исключение, сервис должен отдать определённый ответ».

Не стоит мокать всё подряд: если класс простой и без тяжёлых зависимостей, можно тестировать с реальными объектами. Моки уместны там, где нужна изоляция или контроль над зависимостью.

---

# Spring Boot Testing

## Что предоставляет Spring Boot для тестирования

- **spring-boot-starter-test** — подтягивает JUnit 5, Mockito, AssertJ, Hamcrest, Spring Test, JsonPath. Одной зависимостью подключается типичный тестовый стек.
- **Загрузка контекста** — **@SpringBootTest** поднимает полный или частичный ApplicationContext для интеграционных тестов.
- **Срезы тестов (slice)** — **@WebMvcTest**, **@DataJpaTest**, **@JsonTest**, **@RestClientTest** и др. поднимают только нужный слой и связанные бины, что ускоряет тесты и упрощает изоляцию.
- **MockMvc** — тестирование веб-слоя без запуска сервера: отправка HTTP-запросов к диспетчеру, проверка статуса и тела ответа.
- **Тестовые утилиты** — **TestRestTemplate**, **WebTestClient** для запросов к поднятому приложению; **@MockBean** для подмены бинов в контексте.
- **Автоконфигурация тестов** — отдельные свойства (application-test.yml), профиль test, встроенная БД по умолчанию для срезов (H2 при наличии).

---

# @SpringBootTest

## Как работает аннотация

**@SpringBootTest** загружает **ApplicationContext** для тестов. По умолчанию ищет класс с @SpringBootApplication (вверх по пакетам) и создаёт контекст, максимально близкий к реальному: поднимаются все компоненты, автоконфигурация, свойства из application.properties/yml. Можно сузить конфигурацию: **classes** — только указанные классы конфигурации; **webEnvironment** — тип веб-окружения (MOCK, RANDOM_PORT, DEFINED_PORT, NONE).

- **webEnvironment = WebEnvironment.MOCK** — контекст с MockServletContext; сервер не запускается. Подходит для тестов с MockMvc или тестов бинов без HTTP.
- **webEnvironment = WebEnvironment.RANDOM_PORT** — запускается реальный встроенный сервер на случайном порту; тесты могут обращаться к нему через TestRestTemplate или WebTestClient.

Контекст кэшируется между тестами (в пределах одного набора тестов) по одинаковой конфигурации, чтобы ускорить запуск.

## Когда использовать

- **Интеграционные тесты** — проверка взаимодействия нескольких слоёв (сервис + репозиторий + транзакции) или полный сценарий (запрос через TestRestTemplate к работающему приложению).
- **Тесты с реальной БД** — например, с Testcontainers: контекст поднимается с подставленным DataSource на контейнер.
- Когда **недостаточно среза** (@WebMvcTest не поднимет сервисы и репозитории; для проверки только контроллера с моками срез удобнее и быстрее).

Пример:

```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
class FullIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldReturnUsers() {
        ResponseEntity<List> response = restTemplate.getForEntity("/api/users", List.class);
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
    }
}
```

---

# Slice Tests

## Идея срезов

**Slice-тесты** поднимают только **часть** приложения: один слой (веб, JPA, клиент REST) и минимально необходимые для него бины. Остальное не загружается или подменяется моками. Тесты быстрее и проще настроить; изоляция выше.

## @WebMvcTest

Загружает только слой **Spring MVC**: контроллеры, WebMvc-конфигурацию, конвертеры. **Не** загружает сервисы, репозитории, полную безопасность (при необходимости подключают только нужные компоненты безопасности). Подменять зависимости контроллера нужно через **@MockBean**. Подходит для тестов маппинга, сериализации, кодов ответов контроллеров.

```java
@WebMvcTest(UserController.class)
class UserControllerTest {
    @Autowired
    private MockMvc mockMvc;
    @MockBean
    private UserService userService;
    // тесты через mockMvc.perform(...)
}
```

## @DataJpaTest

Загружает слой **JPA**: репозитории, EntityManager, конфигурацию БД. По умолчанию использует встроенную БД (H2) и откатывает транзакцию после каждого теста. Полный контекст приложения и сервисы **не** загружаются. Подходит для тестов репозиториев (запросы, маппинг). При необходимости подменяют DataSource (например, на Testcontainers) через @DynamicPropertySource или тестовую конфигурацию.

```java
@DataJpaTest
class UserRepositoryTest {
    @Autowired
    private UserRepository userRepository;

    @Test
    void shouldFindByEmail() {
        userRepository.save(new User("test@example.com"));
        assertThat(userRepository.findByEmail("test@example.com")).isPresent();
    }
}
```

## @RestClientTest

Загружает только бины, связанные с **REST-клиентом**: указанный RestTemplate, конвертеры, при необходимости моки для HTTP. Используется для тестов клиентских бинов (вызов внешнего API) без поднятия всего приложения.

---

# MockMvc

## Что такое MockMvc

**MockMvc** — абстракция Spring для тестирования **сервлетного слоя** без запуска HTTP-сервера. Имитирует HTTP-запросы (GET, POST, заголовки, тело) и передаёт их в **DispatcherServlet**; ответ обрабатывается в памяти. Позволяет проверять статус, заголовки, тело ответа (JSON через JsonPath), вызовы и исключения. Подходит для тестов контроллеров (маппинг, валидация, сериализация) с подменёнными сервисами (@MockBean).

## Как тестировать REST API

- **Создание запроса** — **mockMvc.perform(get(...).post(...).put(...)** с URL, параметрами, заголовками, телом (contentType, content(JSON)).
- **Проверки** — **.andExpect(status().isOk())**, **.andExpect(jsonPath("$.field").value(...))**, **.andExpect(header().exists(...))** и т.д.
- **Асинхронные запросы** — **MockMvcRequestBuilders.asyncDispatch(...)** при тестировании асинхронных контроллеров.

Пример:

```java
@WebMvcTest(UserController.class)
class UserControllerTest {
    @Autowired
    private MockMvc mockMvc;
    @MockBean
    private UserService userService;

    @Test
    void getUsers_ReturnsOk() throws Exception {
        when(userService.findAll()).thenReturn(List.of(new User(1L, "a@b.com")));
        mockMvc.perform(get("/api/users").accept(MediaType.APPLICATION_JSON))
            .andExpect(status().isOk())
            .andExpect(content().contentType(MediaType.APPLICATION_JSON))
            .andExpect(jsonPath("$[0].email").value("a@b.com"));
    }

    @Test
    void createUser_Returns201() throws Exception {
        String json = "{\"email\":\"new@example.com\",\"name\":\"New\"}";
        when(userService.save(any())).thenReturn(new User(1L, "new@example.com"));
        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(json))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.id").value(1));
    }
}
```

---

# Testcontainers

## Что такое Testcontainers

**Testcontainers** — библиотека, запускающая **Docker-контейнеры** во время тестов. В тесте объявляют контейнер (например, PostgreSQL, Redis); библиотека скачивает образ (если нужно), стартует контейнер и предоставляет хост/порт. Приложение в тесте подключается к этой БД как к реальной. После тестов контейнер останавливается. Так интеграционные тесты выполняются против **настоящей** СУБД, а не in-memory, что снижает расхождение с продакшеном (диалект SQL, особенности типов, блокировки).

## Зачем использовать реальную БД в тестах

- **Совместимость** — тот же диалект и поведение, что в production; запросы и маппинг JPA проверяются на реальной СУБД.
- **Надёжность** — H2 и другие in-memory БД могут вести себя иначе (типы, функции, ограничения); тесты на реальной БД ловят больше проблем до деплоя.
- **Один способ запуска** — не нужны отдельные настройки для «тестовой» и «продакшен» БД в тестах; контейнер поднимается автоматически.

## Пример: PostgreSQL container

Зависимость (Maven): `org.testcontainers:postgresql` (и общая `testcontainers`). JUnit 5: контейнер как поле с **@Container** и **@ServiceConnection** (Spring Boot 3.1+) или ручная настройка DataSource через **@DynamicPropertySource**.

```java
@Testcontainers
@SpringBootTest
class UserRepositoryIntegrationTest {
    @Container
    @ServiceConnection
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15-alpine");

    @Autowired
    private UserRepository userRepository;

    @Test
    void shouldSaveAndFindUser() {
        User user = new User("test@example.com");
        user = userRepository.save(user);
        assertThat(user.getId()).isNotNull();
        assertThat(userRepository.findByEmail("test@example.com")).isPresent();
    }
}
```

С **@ServiceConnection** (Spring Boot 3.1+) Spring автоматически подставляет URL, имя пользователя и пароль из контейнера в DataSource. Без неё — вручную:

```java
@DynamicPropertySource
static void props(DynamicPropertyRegistry registry) {
    registry.add("spring.datasource.url", postgres::getJdbcUrl);
    registry.add("spring.datasource.username", postgres::getUsername);
    registry.add("spring.datasource.password", postgres::getPassword);
}
```

---

# Best Practices тестирования

## Писать unit-тесты для бизнес-логики

- Сервисы, расчёты, правила валидации — тестировать в изоляции с моками зависимостей.
- Покрывать основные сценарии и граничные случаи (пустой список, null, исключения). Это даёт быструю обратную связь и стабильную документацию поведения.

## Использовать integration-тесты для инфраструктуры

- Репозитории (JPA, запросы), транзакции, контроллеры с реальной сериализацией и маппингом — проверять интеграционными тестами.
- Слой доступа к данным — против реальной или контейнерной БД (Testcontainers), чтобы не расходиться с продакшеном.

## Не использовать реальную БД в unit-тестах

- Unit-тест не должен обращаться к БД, сети или файлам. Зависимости от репозиториев подменять моками.
- Если тест ходит в БД — это уже интеграционный тест; его стоит помечать и запускать в подходящем окружении (например, с контейнером).

## Использовать Testcontainers

- Для интеграционных тестов с БД предпочтительно поднимать ту же СУБД, что и в production (PostgreSQL, MySQL и т.д.) через Testcontainers.
- Один образ/версия для всех разработчиков и CI — воспроизводимая среда и меньше «у меня работает».

Дополнительно: осмысленные имена тестов (shouldReturnUserWhenExists), один сценарий на тест, минимум дублирования (общая подготовка в @BeforeEach или фабриках данных), не тестировать тривиальный код (геттеры/сеттеры) ради процента покрытия.

---

# Краткий конспект для собеседования

## Unit vs Integration tests

- **Unit** — один класс/метод, зависимости замоканы, быстрые, проверяют логику. **Integration** — несколько компонентов или слой, реальная/контейнерная БД или контекст Spring, проверяют связку и инфраструктуру.
- Unit — каждый коммит; integration — в CI и перед релизом. Unit без БД; integration — с БД (часто Testcontainers).

## Mockito

- **Моки** — заглушки зависимостей: **when(...).thenReturn(...)** задаёт поведение, **verify(mock).method(...)** проверяет вызовы.
- **@Mock** — создание мока (с **@ExtendWith(MockitoExtension.class)** в JUnit 5). **@InjectMocks** — экземпляр тестируемого класса с внедрёнными моками. **@MockBean** — мок в тестовом контексте Spring (подмена бина).

## @SpringBootTest

- Загружает **ApplicationContext** для тестов (полный или с ограничениями). **webEnvironment**: MOCK (без сервера), RANDOM_PORT (реальный сервер). Используется для интеграционных тестов с контекстом и при необходимости с TestRestTemplate/WebTestClient.

## MockMvc

- Тестирование **веб-слоя без запуска сервера**: запросы к DispatcherServlet, проверка статуса и тела (в т.ч. JsonPath). Используется с **@WebMvcTest** и **@MockBean** для сервисов. Удобно для проверки маппинга, кодов ответов и формата JSON контроллеров.

## Testcontainers

- Запуск **Docker-контейнеров** (PostgreSQL, Redis и т.д.) во время тестов. Интеграционные тесты идут против реальной СУБД; совместимость с production выше, чем при in-memory БД. В Spring Boot 3.1+ — **@ServiceConnection** для автоматической подстановки URL/учётных данных в DataSource.

---

*Документ предназначен для подготовки к техническим собеседованиям Java Backend. Рекомендуется дополнять практикой: писать unit- и интеграционные тесты в своих проектах и разбирать тесты в открытых репозиториях Spring Boot.*
