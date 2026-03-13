# Spring Core — подробное руководство для подготовки к собеседованию

Глубокое техническое описание Spring Core и IoC-контейнера для подготовки к собеседованиям Java Backend.

---

## Table of Contents (оглавление)

- [Введение в Spring Framework](#введение-в-spring-framework)
- [Архитектура Spring Framework](#архитектура-spring-framework)
- [Inversion of Control](#inversion-of-control-ioc)
- [Dependency Injection](#dependency-injection-di)
- [IoC Container](#ioc-container)
- [BeanFactory vs ApplicationContext](#beanfactory-vs-applicationcontext)
- [Spring Beans](#spring-beans)
- [Bean Definition](#bean-definition)
- [Жизненный цикл Spring Bean](#жизненный-цикл-spring-bean)
- [Bean Scopes](#bean-scopes)
- [Конфигурация Spring](#конфигурация-spring)
- [Component Scanning](#component-scanning)
- [Dependency Injection аннотации](#dependency-injection-аннотации)
- [BeanPostProcessor](#beanpostprocessor)
- [FactoryBean](#factorybean)
- [Aware Interfaces](#aware-interfaces)
- [ApplicationContext](#applicationcontext)
- [Жизненный цикл ApplicationContext](#жизненный-цикл-applicationcontext)
- [Spring Events](#spring-events)
- [Resource Loading](#resource-loading)
- [Environment и Profiles](#environment-и-profiles)
- [Spring Proxies](#spring-proxies)
- [Основы Spring AOP](#основы-spring-aop)
- [Как Spring создаёт и управляет Beans](#как-spring-создает-и-управляет-beans)
- [Краткий конспект для собеседования](#краткий-конспект-для-собеседования)

---

# Введение в Spring Framework

## Что такое Spring Framework

**Spring Framework** — платформа для построения Java-приложений, предоставляющая инфраструктуру для разработки: управление объектами (IoC), внедрение зависимостей (DI), конфигурация, работа с данными, веб-слой, безопасность и др. Spring не навязывает конкретную технологию, а даёт единый способ конфигурации и интеграции компонентов.

## Какие задачи он решает

- **Слабая связность и тестируемость** — зависимости внедряются извне, можно подменять реализации (моки в тестах).
- **Единая конфигурация** — описание компонентов и их связей в одном месте (конфиг, аннотации), а не разбросанное создание объектов в коде.
- **Устранение дублирования** — общая инфраструктура (транзакции, безопасность, AOP) применяется декларативно, без повторения кода.
- **Упрощение интеграции** — подключение БД, очередей, веб-серверов через стандартные абстракции Spring.

## Почему Spring стал стандартом в Java backend

- **Широкая экосистема** — Spring MVC, Data, Security, Boot, Cloud покрывают типичные задачи enterprise-приложений.
- **Конвенции и готовые решения** — минимум кода для типовых сценариев; удобная интеграция с другими библиотеками.
- **Активное развитие и сообщество** — долгая история, совместимость, документация и множество примеров.
- **Spring Boot** — быстрый старт приложения без ручной настройки контейнера и зависимостей.

## Основные модули Spring

| Модуль | Назначение |
|--------|------------|
| **spring-core** | IoC, DI, утилиты (Resource, Environment). |
| **spring-beans** | BeanFactory, BeanDefinition, жизненный цикл bean. |
| **spring-context** | ApplicationContext, события, конфигурация, профили. |
| **spring-aop** | Прокси, аспекты, pointcut, advice. |
| **spring-expression (SpEL)** | Язык выражений для конфигурации и аннотаций. |

Дополнительно: spring-jdbc, spring-tx, spring-web, spring-webmvc, spring-test и др. — строятся поверх core/context.

---

# Архитектура Spring Framework

## Основные модули и роль Spring Core

| Модуль | Описание |
|--------|----------|
| **Spring Core** | Ядро: IoC-контейнер, DI, загрузка ресурсов. Базовый слой для всех остальных модулей. |
| **Spring Context** | ApplicationContext, события, Environment, профили, аннотационная конфигурация. Расширяет Core. |
| **Spring AOP** | Аспектно-ориентированное программирование: прокси, объявление сквозной логики (логирование, транзакции). |
| **Spring MVC** | Веб-слой: контроллеры, маппинг запросов, представления. Использует Context для получения bean. |
| **Spring Data** | Упрощение доступа к данным (репозитории, JPA, MongoDB и т.д.). Интегрируется с контекстом. |
| **Spring Security** | Аутентификация и авторизация. Строится на фильтрах и bean в контексте. |
| **Spring Boot** | Автоконфигурация, встроенный сервер, стартеры. Запускает и настраивает ApplicationContext. |

**Роль Spring Core:** предоставляет IoC-контейнер (BeanFactory) и механизмы DI. Остальные модули используют контейнер для регистрации и получения своих компонентов (контроллеры, сервисы, репозитории и т.д.).

---

# Inversion of Control (IoC)

## Концепция IoC

**Inversion of Control (инверсия управления)** — принцип, при котором **управление созданием и жизненным циклом объектов передаётся внешнему контейнеру**, а не самому коду приложения. Класс не создаёт свои зависимости через `new`, а получает их извне (через конструктор, сеттер или поле). Таким образом, «контроль» над тем, какие реализации подставляются и когда объекты создаются, **инвертирован**: не приложение решает, а контейнер.

## Какие проблемы решает

- **Жёсткая связность** — без IoC класс сам создаёт зависимости (`new Service()`), сложно подменить реализацию и тестировать.
- **Дублирование кода** — создание графа объектов и их настройка размазаны по приложению.
- **Единая точка конфигурации** — контейнер знает, какой класс за какой интерфейс отвечает и как связаны компоненты.

## Почему IoC важен для Spring

Spring по сути является **IoC-контейнером**: он создаёт объекты (beans), разрешает зависимости между ними и управляет их жизненным циклом. Вся модель Spring (beans, конфигурация, DI, профили) построена на идее IoC. Без IoC не было бы единого места, где описываются компоненты и их связи, и не было бы возможности применять AOP, транзакции и другие сквозные механизмы через прокси.

---

# Dependency Injection (DI)

## Что такое Dependency Injection

**Dependency Injection (внедрение зависимостей)** — способ реализации IoC: зависимости **передаются** объекту извне (контейнером), а не создаются внутри него. Объект объявляет, что ему нужны зависимости (конструктор, сеттер, поле), а контейнер их подставляет при создании bean.

## Типы внедрения

### Constructor injection (внедрение через конструктор)

Зависимости передаются через конструктор. Рекомендуемый способ в современном Spring.

```java
@Service
public class OrderService {
    private final OrderRepository orderRepository;
    private final PaymentService paymentService;

    public OrderService(OrderRepository orderRepository, PaymentService paymentService) {
        this.orderRepository = orderRepository;
        this.paymentService = paymentService;
    }
}
```

**Плюсы:** зависимости явные и неизменяемые (`final`), объект нельзя создать в неполном состоянии, удобно для тестов и для обязательных зависимостей.

### Setter injection (внедрение через сеттер)

Зависимости задаются через setter-методы после создания объекта.

```java
@Service
public class OrderService {
    private OrderRepository orderRepository;

    @Autowired
    public void setOrderRepository(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }
}
```

**Плюсы:** опциональные зависимости, возможность менять зависимость позже. **Минусы:** объект может быть использован до вызова сеттера (неполное состояние).

### Field injection (внедрение в поле)

Зависимость помечается аннотацией на поле; контейнер внедряет её через рефлексию.

```java
@Service
public class OrderService {
    @Autowired
    private OrderRepository orderRepository;
}
```

**Минусы:** нельзя сделать поле `final`, сложнее тестировать (нужен контекст или рефлексия), зависимости не видны в конструкторе. В новых проектах предпочитают constructor injection.

## Преимущества constructor injection

- **Неизменяемость** — поля можно объявить `final`, зависимости не меняются после создания.
- **Явный контракт** — все обязательные зависимости видны в конструкторе; нельзя создать bean без них.
- **Удобство тестов** — в тестах зависимости передаются в конструктор без Spring.
- **Совместимость с циклическими зависимостями** — в ряде случаев Spring может разрешить цикл именно при constructor injection (хотя циклы по возможности лучше избегать).

---

# IoC Container

## Что такое IoC-контейнер

**IoC-контейнер** (в Spring — реализация `BeanFactory` и её расширение `ApplicationContext`) — объект, который:

1. **Хранит метаданные** о компонентах (BeanDefinition): класс, scope, зависимости, порядок инициализации.
2. **Создаёт экземпляры** (beans) по этим метаданным.
3. **Разрешает зависимости** — подставляет в bean другие beans (по типу, имени, qualifier).
4. **Управляет жизненным циклом** — вызов методов инициализации и уничтожения, применение BeanPostProcessor, создание прокси.

Код приложения запрашивает bean по типу или имени (`context.getBean(OrderService.class)` или через `@Autowired`), а не создаёт их сам — этим и занимается контейнер.

## Как Spring управляет объектами

- Контейнер создаёт bean **по требованию** (lazy) или при **старте контекста** (eager), в зависимости от scope и конфигурации.
- Зависимости разрешаются по **графу**: если bean A зависит от B, контейнер сначала создаёт (или находит) B, затем внедряет его в A.
- Для каждого bean применяются **BeanPostProcessor** (например, для `@Autowired`, создания прокси под AOP).
- Контейнер вызывает **callback'и жизненного цикла** (`@PostConstruct`, `InitializingBean`, `@PreDestroy`, `DisposableBean`) и при закрытии контекста уничтожает beans (в обратном порядке создания).

---

# BeanFactory vs ApplicationContext

## BeanFactory

**BeanFactory** — базовый контракт IoC-контейнера. Отвечает за:

- Регистрацию и хранение BeanDefinition.
- Создание bean по имени или типу.
- Разрешение зависимостей (в т.ч. циклических в допустимых случаях).
- Управление scope (singleton/prototype и др.).

Реализация по умолчанию — `DefaultListableBeanFactory`. Не содержит загрузки конфигурации из XML/аннотаций, событий, профилей, интернационализации — только «фабрика» bean.

## ApplicationContext

**ApplicationContext** — расширение BeanFactory. Наследует все возможности BeanFactory и добавляет:

- **Загрузку конфигурации** — из XML, Java-конфигов, аннотаций (@Configuration, @ComponentScan).
- **События** — ApplicationEvent, ApplicationListener; публикация и подписка на события.
- **Resource access** — унифицированный доступ к ресурсам (classpath, URL, файлы) через интерфейс Resource.
- **Интернационализация (i18n)** — MessageSource.
- **Интеграция с AOP** — автоматическая регистрация инфраструктурных bean и BeanPostProcessor для прокси.

Типичные реализации: `AnnotationConfigApplicationContext`, `ClassPathXmlApplicationContext`, в Spring Boot — контекст, создаваемый из `@SpringBootApplication`.

## Таблица сравнения

| Критерий | BeanFactory | ApplicationContext |
|----------|-------------|---------------------|
| Создание и получение bean | Да | Да (наследует) |
| Загрузка конфигурации (XML, @Configuration) | Нет | Да |
| События (ApplicationEvent) | Нет | Да |
| Resource loading (Resource) | Ограниченно | Полноценно |
| Интернационализация (MessageSource) | Нет | Да |
| Автоматическая регистрация BeanPostProcessor и др. | Нет | Да |
| Lazy инициализация bean по умолчанию | Да | Обычно eager при старте (можно настроить) |
| Типичное использование | Низкоуровневые сценарии | Стандарт в приложениях и Spring Boot |

В приложениях почти всегда используют **ApplicationContext**; BeanFactory остаётся базовым контрактом внутри контекста.

---

# Spring Beans

## Что такое bean

**Bean** — объект, который **создаётся и управляется IoC-контейнером** Spring. Контейнер создаёт экземпляр по BeanDefinition, подставляет зависимости, вызывает callback'и инициализации и при закрытии контекста — уничтожения. В коде bean получают через `getBean()` или внедряют через `@Autowired` / конструктор.

## Что такое bean definition

**BeanDefinition** — метаданные о bean: класс, имя, scope, конструктор/фабричный метод, свойства для setter injection, зависимости (по имени или типу), lazy init, порядок инициализации и т.д. Контейнер по BeanDefinition знает, **как** создать bean и как связать его с другими beans. BeanDefinition регистрируются при загрузке конфигурации (XML, @Bean, component scan).

## Как Spring управляет beans

- **Регистрация** — из конфигурации и component scan создаются BeanDefinition и регистрируются в контейнере (по имени и алиасам).
- **Создание** — при первом обращении (или при старте для eager singleton) контейнер создаёт экземпляр (через конструктор или FactoryBean), внедряет зависимости, применяет BeanPostProcessor, вызывает инициализацию.
- **Кэширование** — singleton beans хранятся в контейнере и возвращаются при повторных запросах; prototype при каждом запросе создаётся заново.
- **Уничтожение** — при закрытии контекста вызываются `@PreDestroy` / `DisposableBean` для singleton beans в обратном порядке.

---

# Bean Definition

## Что такое BeanDefinition

**BeanDefinition** — интерфейс в Spring, описывающий **метаданные** bean: как его создать и как с ним обращаться. Регистрируется в контейнере до создания самого объекта. Реализации: `RootBeanDefinition`, `ChildBeanDefinition`, `GenericBeanDefinition`.

## Какие метаданные содержит BeanDefinition

| Метаданные | Описание |
|------------|----------|
| Имя bean / алиасы | Идентификация в контейнере. |
| Имя класса | Класс экземпляра (или родитель для child definition). |
| Scope | singleton, prototype, request, session и т.д. |
| Constructor arguments | Аргументы конструктора (для разрешения зависимостей). |
| Property values | Значения для setter injection. |
| Lazy init | Создавать при первом обращении или при старте контекста. |
| Init / destroy method names | Имена методов инициализации и уничтожения. |
| Primary / autowire candidate | Участвует ли в autowire по типу, является ли primary. |
| Factory method / factory bean | Создание через статический или инстансный фабричный метод. |

## Как Spring использует BeanDefinition для создания beans

1. При запросе bean контейнер ищет BeanDefinition по имени или типу.
2. Разрешает зависимости (конструктор или свойства) — рекурсивно создаёт или находит другие beans.
3. Создаёт экземпляр: через рефлексию по конструктору, по фабричному методу или через FactoryBean.
4. Применяет BeanPostProcessor (например, для @Autowired и прокси).
5. Вызывает инициализацию (InitializingBean, init-method, @PostConstruct).
6. Регистрирует singleton в кэше и возвращает bean (или возвращает новый экземпляр для prototype).

---

# Жизненный цикл Spring Bean

## Полный lifecycle (по шагам)

1. **Создание BeanDefinition** — конфигурация (XML, @Configuration, component scan) порождает BeanDefinition и регистрирует их в контейнере.
2. **Instantiation** — контейнер создаёт экземпляр объекта (вызов конструктора или фабричного метода / FactoryBean).
3. **Dependency injection** — контейнер заполняет поля/сеттеры/конструктор зависимостями (другими beans).
4. **BeanPostProcessor.postProcessBeforeInitialization** — вызов всех зарегистрированных BeanPostProcessor до инициализации (здесь, например, применяется внедрение @Autowired для полей/сеттеров, если не использовался конструктор).
5. **Initialization** — вызов `@PostConstruct`, затем `InitializingBean.afterPropertiesSet()`, затем указанный init-method.
6. **BeanPostProcessor.postProcessAfterInitialization** — вызов после инициализации; здесь часто создаётся **прокси** (AOP, транзакции). Возвращённый объект может быть прокси, а не исходный bean — его и хранит контейнер.
7. **Bean ready** — bean доступен для использования; для singleton кэшируется в контейнере.
8. **Destruction** — при закрытии ApplicationContext для singleton вызываются `@PreDestroy`, затем `DisposableBean.destroy()`, затем указанный destroy-method.

Схема:

```
BeanDefinition → Instantiation → DI → postProcessBeforeInitialization
    → @PostConstruct → afterPropertiesSet → init-method
    → postProcessAfterInitialization (здесь прокси)
    → Bean ready
    → (при shutdown) @PreDestroy → destroy() → destroy-method
```

## @PostConstruct и @PreDestroy

- **@PostConstruct** (javax.annotation) — метод вызывается **после** внедрения зависимостей и **до** afterPropertiesSet/init-method. Удобно для логики инициализации в самом bean.
- **@PreDestroy** — метод вызывается при уничтожении bean (закрытие контекста). Освобождение ресурсов, отмена подписок.

```java
@Component
public class MyService {
    @PostConstruct
    public void init() {
        // после DI, перед использованием
    }

    @PreDestroy
    public void cleanup() {
        // при закрытии контекста
    }
}
```

## InitializingBean и DisposableBean

- **InitializingBean** — интерфейс с методом `afterPropertiesSet()`. Вызывается контейнером после DI, после @PostConstruct, перед init-method. Раньше часто использовался для инициализации; сейчас часто достаточно @PostConstruct или init-method.
- **DisposableBean** — метод `destroy()`. Вызывается при уничтожении bean (после @PreDestroy, перед destroy-method). Аналог — аннотация @PreDestroy или destroy-method в конфигурации.

---

# Bean Scopes

## Стандартные scope

| Scope | Описание |
|-------|----------|
| **singleton** | Один экземпляр на контейнер (по умолчанию). Создаётся при старте (или при первом обращении при lazy). |
| **prototype** | Новый экземпляр при каждом запросе (getBean / инжект). Контейнер не управляет жизненным циклом после создания — destroy не вызывается. |
| **request** | В веб-контексте — один экземпляр на HTTP-запрос. |
| **session** | В веб-контексте — один экземпляр на HTTP-сессию. |
| **application** | В веб-контексте — один экземпляр на ServletContext. |
| **websocket** | Один экземпляр на WebSocket-сессию. |

Задать scope: `@Scope("prototype")` или `@Scope(WebApplicationContext.SCOPE_REQUEST)` и т.д.

## Singleton scope vs паттерн Singleton

- **Паттерн Singleton (GoF)** — один экземпляр на **всё приложение (ClassLoader)**; создаётся самим классом (статическая инстанция или enum), контроль у класса.
- **Spring singleton scope** — один экземпляр **на IoC-контейнер (ApplicationContext)**. Может быть несколько контекстов — тогда будет несколько экземпляров «singleton» bean. Создание и хранение контролирует контейнер, а не класс. По сути это **контейнерный синглтон**, а не глобальный синглтон процесса.

---

# Конфигурация Spring

## Способы конфигурации

### XML configuration

Описание beans и зависимостей в XML. Раньше основной способ; сейчас чаще используют Java/аннотации.

```xml
<bean id="orderService" class="com.example.OrderService">
    <constructor-arg ref="orderRepository"/>
</bean>
<bean id="orderRepository" class="com.example.OrderRepositoryImpl"/>
```

### Java configuration

Класс с аннотацией `@Configuration`; методы с `@Bean` возвращают bean. Полный контроль над созданием и зависимостями.

```java
@Configuration
public class AppConfig {
    @Bean
    public OrderRepository orderRepository() {
        return new OrderRepositoryImpl();
    }

    @Bean
    public OrderService orderService(OrderRepository orderRepository) {
        return new OrderService(orderRepository);
    }
}
```

### Annotation configuration

Компоненты помечаются аннотациями (`@Component`, `@Service`, `@Repository`, `@Controller`); контейнер находит их через **component scanning**. Зависимости задаются через `@Autowired` и т.д. Часто комбинируется с несколькими `@Configuration` и `@Bean` для инфраструктурных bean.

## Аннотации конфигурации

| Аннотация | Назначение |
|-----------|------------|
| **@Configuration** | Класс объявляет конфигурацию; методы @Bean регистрируются в контексте. |
| **@Bean** | Метод создаёт и возвращает bean; имя по умолчанию — имя метода. |
| **@ComponentScan** | Указывает пакеты для сканирования @Component и производных (@Service, @Repository и т.д.). В @SpringBootApplication уже встроен scan от класса приложения. |
| **@Import** | Подключает другую конфигурацию (@Configuration) или классы, которые контекст обрабатывает как конфигурацию/компоненты. |

Пример:

```java
@Configuration
@ComponentScan(basePackages = "com.example")
@Import(DataSourceConfig.class)
public class AppConfig { }
```

---

# Component Scanning

## Как Spring находит beans

**Component scanning** — процесс обхода указанных пакетов (и подпакетов), поиск классов с аннотациями `@Component` и производными и регистрация их как BeanDefinition. Контекст использует метаданные класса (рефлексия) и создаёт bean при старте или при первом обращении. Запускается при создании ApplicationContext (например, при указании `@ComponentScan` или при использовании `@SpringBootApplication`, который включает сканирование).

## Аннотации для регистрации компонентов

| Аннотация | Назначение | Специфика |
|-----------|------------|-----------|
| **@Component** | Универсальный стереотип «компонент». | Базовый уровень; сканируется. |
| **@Service** | Семантика «сервисный слой». | По поведению то же, что @Component; различие в именовании и смысле для разработчика. |
| **@Repository** | Слой доступа к данным. | Помимо регистрации bean, может переводить исключения БД в DataAccessException (при интеграции с persistence). |
| **@Controller** | Веб-контроллер (MVC). | Обрабатывается диспетчером для маппинга запросов. |
| **@Configuration** | Класс с @Bean. | Сканируется и обрабатывается как конфигурация; @Bean методы регистрируются. |

Все они по умолчанию попадают в сканирование при указании соответствующего базового пакета в @ComponentScan (или при скане от @SpringBootApplication).

---

# Dependency Injection аннотации

## @Autowired

Поле, конструктор или метод (в т.ч. setter) помечаются как зависимость для внедрения. Контейнер подставляет подходящий bean по **типу**. Если кандидатов несколько — требуется уточнение через @Qualifier или выбор primary bean.

- **Constructor** — предпочтительно; все аргументы конструктора считаются зависимостями.
- **Setter / метод** — один аргумент; вызывается после создания bean.
- **Поле** — внедрение через рефлексию; не рекомендуется в новых проектах в пользу конструктора.

По умолчанию `required = true` — при отсутствии подходящего bean контекст не поднимается. Для опциональной зависимости: `@Autowired(required = false)`.

## @Qualifier

Уточняет, **какой** bean внедрять при нескольких кандидатах одного типа. Значение — имя bean или qualifier, заданный при объявлении bean.

```java
@Autowired
@Qualifier("jdbcOrderRepository")
private OrderRepository orderRepository;
```

## @Primary

Указывает **предпочтительный** bean при внедрении по типу, когда кандидатов несколько и @Qualifier не задан. На конфигурации: `@Primary` на методе @Bean или на классе компонента.

```java
@Bean
@Primary
public OrderRepository orderRepository() {
    return new JdbcOrderRepository();
}
```

## @Resource (JSR-250)

Внедрение по **имени** (по умолчанию — имя поля или метода). Не Spring-специфичная аннотация. Часто используется как альтернатива @Autowired + @Qualifier при привязке по имени bean.

```java
@Resource(name = "jdbcOrderRepository")
private OrderRepository orderRepository;
```

---

# BeanPostProcessor

## Что такое BeanPostProcessor

**BeanPostProcessor** — интерфейс контейнера, позволяющий **модифицировать или заменить** bean до и после его инициализации. Контейнер вызывает реализацию для каждого создаваемого bean. Используется для внедрения зависимостей (@Autowired реализовано через BeanPostProcessor), создания прокси (AOP, транзакции), валидации и т.д.

## Зачем нужен

- Добавить кастомную логику **до** или **после** инициализации каждого bean.
- Изменить экземпляр или вернуть **другой объект** (например, прокси) — контейнер будет работать с возвращённым объектом.
- Реализовать свои механизмы инжекта, валидации, проксирования без изменения ядра контейнера.

## Методы

- **postProcessBeforeInitialization(Object bean, String beanName)** — вызывается **после** DI и **до** @PostConstruct, InitializingBean, init-method. Может вернуть тот же или обёрнутый bean.
- **postProcessAfterInitialization(Object bean, String beanName)** — вызывается **после** всей инициализации. Здесь обычно создаётся **прокси** для AOP (возвращается прокси вместо исходного bean). Контейнер сохраняет и отдаёт то, что вернул метод.

Важно: если BeanPostProcessor возвращает другой объект, последующие BeanPostProcessor и контекст работают уже с этим объектом; для AOP именно так подменяется bean на прокси.

---

# FactoryBean

## Что такое FactoryBean

**FactoryBean** — интерфейс для bean, которые сами являются **фабрикой** другого объекта. Контейнер создаёт экземпляр класса, реализующего FactoryBean, а затем вызывает `getObject()` и **в качестве bean по имени фабрики** возвращает результат getObject(), а не сам экземпляр FactoryBean. Если нужен сам экземпляр FactoryBean, к имени добавляют префикс `&` (например, `getBean("&myFactoryBean")`).

## Зачем используется

- Создание объекта сложнее, чем вызов конструктора: нужны параметры из конфигурации, выбор реализации, проксирование.
- Интеграция со сторонними библиотеками, которые сами создают объекты (например, клиенты, пулы).
- Один FactoryBean может создавать разные типы объектов в зависимости от конфигурации.

## Отличие от обычного Bean

- **Обычный @Bean / @Component** — контейнер хранит и возвращает сам созданный объект.
- **FactoryBean** — контейнер хранит и возвращает по умолчанию результат **getObject()**; класс FactoryBean скрыт за «продуктом». Сам фабричный объект доступен через `&beanName`.

Пример:

```java
public class MyFactoryBean implements FactoryBean<MyService> {
    @Override
    public MyService getObject() {
        return new MyService(); // или сложная логика
    }
    @Override
    public Class<?> getObjectType() {
        return MyService.class;
    }
}
```

---

# Aware Interfaces

Интерфейсы **Aware** позволяют bean получить от контейнера ссылку на инфраструктурные объекты. Контейнер вызывает соответствующий setter после создания bean и до инициализации (в рамках обработки BeanPostProcessor и внутренней настройки).

## BeanNameAware

Метод `setBeanName(String name)` — передаётся **имя bean** в контейнере. Полезно для логов или выбора поведения по имени.

## BeanFactoryAware

Метод `setBeanFactory(BeanFactory beanFactory)` — передаётся ссылка на **BeanFactory**. Позволяет программно получать другие beans, проверять наличие bean и т.д. Редко нужен в обычном коде; чаще в инфраструктуре и кастомных расширениях.

## ApplicationContextAware

Метод `setApplicationContext(ApplicationContext applicationContext)` — передаётся **ApplicationContext**. Доступ к контексту: публикация событий, получение bean по имени/типу, доступ к Environment, ResourceLoader. Удобно, когда внедрить контекст через конструктор не хочется (например, в legacy-коде). В новых приложениях часто предпочитают внедрять только нужные зависимости, а не весь контекст.

Пример:

```java
@Component
public class MyComponent implements ApplicationContextAware {
    private ApplicationContext context;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) {
        this.context = applicationContext;
    }
}
```

---

# ApplicationContext

## Что такое ApplicationContext

**ApplicationContext** — расширение BeanFactory, основной способ работы с IoC-контейнером в приложении. Помимо создания и выдачи bean, предоставляет загрузку конфигурации (XML, @Configuration, component scan), события, ресурсы, профили, интернационализацию и автоматическую регистрацию инфраструктурных bean (в т.ч. BeanPostProcessor для AOP).

## Возможности

- **getBean** — получение bean по имени или типу.
- **Публикация событий** — `publishEvent(ApplicationEvent)`.
- **Доступ к Environment** — профили, свойства.
- **Доступ к ресурсам** — `getResource(String)`.
- **Регистрация shutdown hook** — корректное закрытие контекста при остановке JVM (по умолчанию во многих реализациях).

Типичные реализации: `AnnotationConfigApplicationContext`, `ClassPathXmlApplicationContext`; в Spring Boot контекст создаётся при запуске приложения.

---

# Жизненный цикл ApplicationContext

## Этапы (упрощённо)

1. **Загрузка конфигурации** — чтение XML или сканирование классов с @Configuration и @ComponentScan.
2. **Создание BeanDefinition** — из XML, @Bean-методов и отсканированных @Component-классов; создаются объекты BeanDefinition.
3. **Регистрация BeanDefinition** — BeanDefinition регистрируются в внутреннем BeanFactory (по имени и алиасам).
4. **Создание BeanFactory** — используется реализация DefaultListableBeanFactory (или аналог); в неё записаны все BeanDefinition.
5. **Регистрация BeanPostProcessor** — контекст регистрирует свои и загруженные BeanPostProcessor (например, для @Autowired, для AOP), чтобы они применялись при создании bean.
6. **Создание singleton beans** — по графу зависимостей создаются все не-lazy singleton beans: инстанциация, DI, BeanPostProcessor, инициализация.
7. **Context ready** — контекст готов к использованию; вызываются callback'и типа ApplicationReadyEvent (в Spring Boot). Далее приложение обслуживает запросы; при остановке контекст закрывается и вызываются destroy-методы для singleton beans.

Важно: порядок создания bean определяется зависимостями и при необходимости зависимостями от BeanFactory/ApplicationContext (через Aware или инжект контекста). BeanPostProcessor создаются раньше остальных bean, чтобы применять их к последующим.

---

# Spring Events

## Event system

Spring предоставляет **событийную модель** в рамках ApplicationContext. Можно публиковать события и подписываться на них; это даёт слабую связность между компонентами.

## ApplicationEvent

Раньше все события наследовали **ApplicationEvent**. В современных версиях можно публиковать и **произвольные объекты**; контекст оборачивает их в PayloadApplicationEvent. События публикуются через `ApplicationContext.publishEvent(Object event)`.

## ApplicationListener

**ApplicationListener<ApplicationEvent>** — подписчик на события. Контекст вызывает метод `onApplicationEvent` для зарегистрированных слушателей при публикации события. Альтернатива — аннотация **@EventListener** на методе с параметром типа события; метод вызывается при публикации соответствующего события.

Пример:

```java
// Публикация
context.publishEvent(new OrderCreatedEvent(order));

// Слушатель
@Component
public class OrderEventListener {
    @EventListener
    public void handleOrderCreated(OrderCreatedEvent event) {
        // ...
    }
}
```

События обрабатываются **синхронно** в том же потоке, что и publishEvent. Для асинхронной обработки метод помечают `@Async` (при включённом асинхронном выполнении).

---

# Resource Loading

## Resource abstraction

Spring абстрагирует доступ к ресурсам (файлы, classpath, URL) через интерфейс **Resource**. Контекст реализует **ResourceLoader** — по строке (путь/URL) возвращается Resource. Это унифицирует работу с classpath, файловой системой и HTTP.

## Типы Resource

| Тип | Описание |
|-----|----------|
| **ClassPathResource** | Ресурс из classpath (JAR или каталог классов). |
| **FileSystemResource** | Ресурс из файловой системы. |
| **UrlResource** | Ресурс по URL (http, file и т.д.). |
| **InputStreamResource** | Обёртка над InputStream (без повторного чтения). |

Получение через контекст: `context.getResource("classpath:config/app.properties")` или `context.getResource("file:/etc/app/config.xml")`. Префиксы: `classpath:`, `file:`, `https:` и т.д.

---

# Environment и Profiles

## Environment

**Environment** — абстракция над **профилями** и **источниками свойств** (property sources). Доступ: `context.getEnvironment()`. Позволяет проверять активные профили (`environment.getActiveProfiles()`) и получать свойства (`environment.getProperty("key")`). Свойства подставляются в `@Value("${key}")` и в конфигурацию.

## PropertySources

**PropertySources** — упорядоченный набор источников свойств (например, системные переменные, переменные окружения, application.properties). При запросе свойства поиск идёт по порядку; первый найденный ключ возвращается. Можно добавлять свои источники (например, удалённый конфиг).

## Profiles

**Профили** — именованные наборы конфигурации (например, `dev`, `prod`). Bean и @Configuration можно условно регистрировать по профилю: `@Profile("prod")`, `@Profile("!test")`. При старте контекста задаётся набор активных профилей (переменная, свойство, код); только подходящие под активные профили bean создаются. Удобно для переключения БД, логов, фич по окружению.

---

# Spring Proxies

## Зачем Spring использует прокси

Spring применяет **прокси** там, где нужно добавить поведение **вокруг** вызова метода bean без изменения самого класса:

- **Транзакции** (@Transactional) — начало/коммит/откат транзакции до и после метода.
- **AOP** — логирование, безопасность, кастомные аспекты.
- **Lazy инициализация** — отложенное создание bean при первом обращении.
- **Дополнительные интерфейсы** — контекст может отдавать прокси, реализующий дополнительные интерфейсы (например, для экспорта через RMI).

Вызовы идут в прокси; прокси выполняет сквозную логику и затем вызывает **целевой bean** (target). Поэтому вызовы методов **внутри того же bean** (this.method()) не проходят через прокси и не получают, например, транзакцию — только внешние вызовы.

## Типы прокси

| Тип | Описание |
|-----|----------|
| **JDK Dynamic Proxy** | Реализует **интерфейсы** целевого bean. Создаётся через java.lang.reflect.Proxy. Требует, чтобы у bean был хотя бы один интерфейс. |
| **CGLIB Proxy** | Наследует **класс** целевого bean (подкласс). Подходит для классов без интерфейсов. Final-методы переопределить нельзя — вызовы к ним не проксируются. |

Spring по умолчанию выбирает: если у bean есть интерфейсы — JDK proxy; иначе CGLIB (при наличии CGLIB в classpath). Можно принудительно включить CGLIB для всех (например, в @EnableTransactionManagement).

---

# Основы Spring AOP

## Aspect

**Aspect** — модуль, инкапсулирующий сквозную логику (логирование, транзакции). В Spring аспект представлен классом с методами-advice и привязкой к pointcut. Реализуется как bean с аннотацией @Aspect (при включённом AOP).

## Advice

**Advice** — код, выполняемый в определённой точке (до/после/вокруг вызова метода). Типы: **@Before**, **@After**, **@AfterReturning**, **@AfterThrowing**, **@Around** (наиболее гибкий — полный контроль над вызовом и возвратом).

## JoinPoint

**JoinPoint** — точка выполнения, в которой срабатывает advice (например, вызов метода). Содержит информацию о методе, аргументах, целевом объекте. В advice доступен через параметр типа JoinPoint или ProceedingJoinPoint (в @Around).

## Pointcut

**Pointcut** — выражение, определяющее **где** срабатывает advice (какие методы/классы). Задаётся через выражение (например, execution, within, @annotation). Совет привязывается к pointcut: `@Before("execution(* com.example.service.*.*(..))")`.

Пример:

```java
@Aspect
@Component
public class LoggingAspect {
    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore(JoinPoint jp) {
        // логирование перед вызовом
    }
}
```

---

# Как Spring создаёт и управляет Beans

## Процесс создания bean (сводка)

1. **Запрос bean** — по имени или типу контейнер находит BeanDefinition.
2. **Разрешение зависимостей (dependency resolution)** — для конструктора или полей/сеттеров контейнер рекурсивно получает или создаёт зависимости (другие beans). При циклической зависимости возможны особые пути (например, раннее создание прокси для одного из bean).
3. **Instantiation** — создаётся экземпляр (конструктор или FactoryBean.getObject()).
4. **Dependency injection** — заполняются поля/сеттеры/конструктор (если не был использован при создании).
5. **BeanPostProcessor** — вызываются postProcessBeforeInitialization и postProcessAfterInitialization; при AOP здесь создаётся прокси, и контейнер сохраняет именно его как bean.
6. **Инициализация** — @PostConstruct, InitializingBean, init-method.
7. **Bean готов** — для singleton кладётся в кэш; при следующих запросах возвращается тот же экземпляр (или прокси).

## Проксирование

Если для bean настроен pointcut (транзакции, AOP), один из BeanPostProcessor (например, AbstractAutoProxyCreator) в postProcessAfterInitialization создаёт прокси и возвращает его контейнеру. Контейнер хранит и отдаёт прокси; при вызове методов прокси выполняет advice и делегирует вызов целевому bean. Поэтому «внутренние» вызовы (this.method()) не проходят через прокси.

---

# Краткий конспект для собеседования

## IoC

- **Inversion of Control** — управление созданием и жизненным циклом объектов передаётся контейнеру. Класс не создаёт зависимости через `new`, а получает их извне. Spring — IoC-контейнер.

## DI

- **Dependency Injection** — способ реализации IoC: зависимости передаются объекту (конструктор, сеттер, поле). Типы: constructor (рекомендуется), setter, field. Constructor даёт неизменяемость и явный контракт.

## Bean lifecycle

- BeanDefinition → Instantiation → DI → postProcessBeforeInitialization → @PostConstruct → afterPropertiesSet → init-method → postProcessAfterInitialization (здесь часто прокси) → bean ready. При shutdown: @PreDestroy → destroy() → destroy-method.

## ApplicationContext

- Расширение BeanFactory: загрузка конфигурации (XML, @Configuration, scan), события, ресурсы, профили, интернационализация. Основной способ работы с контейнером в приложении.

## Proxy

- Spring использует прокси для транзакций, AOP, lazy. JDK Dynamic Proxy — по интерфейсам; CGLIB — наследование класса. Вызовы внутри того же bean (this) не идут через прокси.

## Bean scopes

- singleton (один на контейнер), prototype (новый при каждом запросе), request/session/application (в веб-контексте). Spring singleton — «на контейнер», а не глобальный синглтон процесса.

## BeanFactory vs ApplicationContext

- BeanFactory — базовый контракт: создание и выдача bean. ApplicationContext — плюс конфигурация, события, ресурсы, профили. В приложениях используют ApplicationContext.

---

*Документ предназначен для углублённой подготовки к техническим собеседованиям Java Backend. Рекомендуется дополнять практикой и материалами по Spring Boot и Spring MVC.*
