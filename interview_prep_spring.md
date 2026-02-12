 ## Spring

1. [Bean lifecycle. What it looks like, how the bean is created, etc.](#вопрос-1-bean-lifecycle-what-it-looks-like-how-the-bean-is-created-etc)
2. [Methods for declaring beans. List and name the advantages and disadvantages](#вопрос-2-methods-for-declaring-beans-list-and-name-the-advantages-and-disadvantages)
3. [Bean scopes. Name and tell about their life cycle](#вопрос-3-bean-scopes-name-and-tell-about-their-life-cycle)
4. [Tools for creating proxies. Differences between Dynamic Proxy and CGLib](#вопрос-4-tools-for-creating-proxies-differences-between-dynamic-proxy-and-cglib)
5. [Spring MVC vs Spring Boot. Differences](#вопрос-5-spring-mvc-vs-spring-boot-differences)
6. [Distinguishes @Component from @Service and @Repository](#вопрос-6-distinguishes-component-from-service-and-repository)
7. [How to perform a native database query when using Spring Data JPA?](#вопрос-7-how-to-perform-a-native-database-query-when-using-spring-data-jpa)
8. [How does @Transactional work in Spring? What happens if one service calls a Transactional method from another Transactional method?](#вопрос-8-how-does-transactional-work-in-spring-what-happens-if-one-service-calls-a-transactional-method-from-another-transactional-method)
9. [Name a few popular spring boot starters](#вопрос-9-name-a-few-popular-spring-boot-starters)
10. [What is DI and IoC?](#вопрос-10-what-is-di-and-ioc)

---

### Вопрос 1. Bean lifecycle. What it looks like, how the bean is created, etc.

#### 1.1. Общая картина жизненного цикла бина

Говорим про **singleton-бин** в обычном Spring Boot приложении.

Упрощённо последовательность такая:

1. Spring читает конфигурацию (`@Configuration`, `@ComponentScan`, `@Bean`, XML) и строит **BeanDefinition** для каждого бина (метаданные: класс, scope, зависимости и т.п.).
2. При запуске контекста (`refresh()`):
   - создаются все **singleton**-бины (по умолчанию "eager");
   - каждый бин проходит через ряд этапов:
     1. **Инстанцирование** (создание объекта, `new`).
     2. **Внедрение зависимостей** (setter/field/constructor injection).
     3. Вызов "Aware"-колбеков (`BeanNameAware`, `BeanFactoryAware`, `ApplicationContextAware` и др., если реализованы).
     4. Проход через `BeanPostProcessor` (до инициализации).
     5. Инициализация:
        - `@PostConstruct`,
        - `InitializingBean.afterPropertiesSet()`,
        - кастомный `init-method`, если задан.
     6. Проход через `BeanPostProcessor` (после инициализации).
3. Бин становится **готов к использованию** и живёт в контексте до его закрытия.
4. При остановке контекста:
   - для singleton-бинов вызываются методы разрушения:
     - `@PreDestroy`,
     - `DisposableBean.destroy()`,
     - `destroy-method`, если задан.

Для **prototype**-бинов Spring НЕ вызывает destroy-колбеки автоматически — ответственность на тебе.

---

#### 1.2. Детальнее по шагам (на примере)

Пусть у нас есть бин:

```java
@Component
public class MyService implements InitializingBean, DisposableBean, BeanNameAware {

    @Override
    public void setBeanName(String name) {
        System.out.println("BeanNameAware: " + name);
    }

    @PostConstruct
    public void postConstruct() {
        System.out.println("@PostConstruct");
    }

    @Override
    public void afterPropertiesSet() {
        System.out.println("InitializingBean.afterPropertiesSet");
    }

    @PreDestroy
    public void preDestroy() {
        System.out.println("@PreDestroy");
    }

    @Override
    public void destroy() {
        System.out.println("DisposableBean.destroy");
    }
}
```

Порядок для этого бина (упрощённо):

1. Spring создаёт экземпляр `MyService` (`new`).
2. Внедряет зависимости (через конструктор/поля/сеттеры).
3. Вызывает `setBeanName(...)` (если реализован `BeanNameAware`).
4. Пропускает бин через `BeanPostProcessor#postProcessBeforeInitialization(...)`.
5. Вызывает:
   - `@PostConstruct`-метод;
   - `afterPropertiesSet()` (если реализован `InitializingBean`);
   - init-method из конфига (если задан).
6. Пропускает бин через `BeanPostProcessor#postProcessAfterInitialization(...)`.
7. Бин служит.
8. При закрытии контекста:
   - вызывает `@PreDestroy`;
   - `destroy()` (если реализован `DisposableBean`);
   - destroy-method из конфига.

---

#### 1.3. Роль BeanPostProcessor и прокси

Многие "магические" фичи Spring (например, `@Transactional`, `@Async`, AOP) работают через **`BeanPostProcessor`**:

- На этапе `postProcessAfterInitialization` Spring может обернуть исходный бин в **прокси**:
  - возвращаемый из контекста бин уже **не оригинальный объект**, а прокси-обёртка;
  - прокси перехватывает вызовы методов и добавляет кросс-секшн логику (транзакции, логирование, ретраи и т.д.).

Важно при дебаге:
- то, что ты видишь как бин, часто — прокси, а не оригинальный класс;
- из этого следуют ограничения, например:
  - `@Transactional` на `private`-методах не работает, и self-invocation не перехватывается (вопрос 8).

---

#### 1.4. Жизненный цикл и scope

Описанное выше — для **singleton**:
- создаётся один раз при старте контекста;
- уничтожается при его закрытии.

Для других scope-ов (request/session/prototype) отличается момент:
- когда именно бин создаётся;
- кто и когда его уничтожает (см. вопрос 3).

---

### Вопрос 2. Methods for declaring beans. List and name the advantages and disadvantages

В современном Spring / Spring Boot есть несколько основных способов объявить бин.

#### 2.1. Аннотации стереотипов: `@Component`, `@Service`, `@Repository`, `@Controller`, `@RestController`

Пример:

```java
@Service
public class UserService {
    ...
}
```

Как работает:
- `@ComponentScan` сканирует пакеты и находит классы с этими аннотациями;
- регистрирует их как бины в контексте.

Стереотипы:
- `@Component` — базовый, "просто бин";
- `@Service` — сервисный слой (бизнес-логика);
- `@Repository` — DAO-слой, дополнительно:
  - включает перевод persistence-исключений в `DataAccessException` (через `@Repository`-postprocessor);
- `@Controller` / `@RestController` — веб-слой (MVC/REST).

**Плюсы**:
- удобно, декларативно, минимум кода;
- хорошо сочетается с автосканированием и Spring Boot;
- стереотипы дают семантику слоям (понятнее при ревью).

**Минусы**:
- бин "жёстко" привязан к Spring (аннотации в доменных классах);
- меньше контроля, сложнее явно управлять порядком инициализации/alias-ами (но можно через доп. аннотации).

---

#### 2.2. Java-конфигурация: методы `@Bean` в `@Configuration` классах

Пример:

```java
@Configuration
public class AppConfig {

    @Bean
    public UserService userService(UserRepository userRepository) {
        return new UserService(userRepository);
    }
}
```

Особенности:
- метод с `@Bean` регистрирует результат как бин в контексте;
- параметры метода — это зависимости (Spring сам передаст нужные бины);
- по умолчанию, если класс помечен `@Configuration`, Spring использует CGLIB-прокси, чтобы **гарантировать singleton** даже при вызове метода `userService()` из того же конфига.

**Плюсы**:
- явный, типобезопасный код Java;
- удобно конфигурировать сторонние библиотеки/бины, не помеченные `@Component`;
- легко управлять порядком/условиями регистрации (`@Conditional`, `@Profile` и др.).

**Минусы**:
- больше бойлерплейта, чем с `@Component`;
- при неправильном использовании (без `@Configuration` или с `proxyBeanMethods=false`) можно случайно создать несколько экземпляров.

---

#### 2.3. XML-конфигурация

Исторический способ:

```xml
<beans>
    <bean id="userService" class="com.example.UserService"/>
</beans>
```

Сейчас:
- в новых проектах практически не используется;
- но полезно знать для legacy/интервью.

**Плюсы**:
- полное отделение конфигурации от кода;
- удобно было в до-Java-config эпоху.

**Минусы**:
- громоздко, неудобно, нет типобезопасности;
- тяжёлое сопровождение, особенно в больших проектах.

---

#### 2.4. Функциональная/программная регистрация (реже)

Например, через `GenericApplicationContext`:

```java
GenericApplicationContext context = new GenericApplicationContext();
context.registerBean(UserService.class);
context.refresh();
```

Используется:
- в особых случаях (DSL, фреймворки поверх Spring);
- для обычного приложения редко востребовано.

---

#### 2.5. Как ответить на собесе

- "Бины можно объявлять как через компонент-скан (`@Component`, `@Service`, `@Repository`, `@Controller`), так и через Java-конфигурацию (`@Bean` в `@Configuration`) и, исторически, через XML. Стереотипы удобны и декларативны для собственных классов приложения, а `@Bean`-методы хороши для сторонних библиотек и тонкой настройки. XML сейчас встречается в legacy-проектах."

---

### Вопрос 3. Bean scopes. Name and tell about their life cycle

#### 3.1. Основные scope-ы в Spring

Базовые (ядро Spring):
- `singleton` — один бин на весь контекст (по умолчанию);
- `prototype` — новый бин при каждом запросе.

В веб-приложениях (Spring Web/MVC):
- `request` — один бин на HTTP-запрос;
- `session` — один бин на HTTP-сессию;
- `application` — один бин на `ServletContext`;
- `websocket` — один бин на WebSocket-сессию.

Указываются через:

```java
@Scope("prototype")
@Scope(value = WebApplicationContext.SCOPE_REQUEST)
```

или через `@RequestScope`, `@SessionScope` (шорткаты).

---

#### 3.2. `singleton`

По умолчанию все бины — **singleton**:

```java
@Component
public class MyService { ... }
```

- создаётся **один раз** при инициализации контекста (eager init);
- хранится в контейнере до закрытия контекста;
- при каждом `getBean(...)` или внедрении отдаётся **один и тот же экземпляр**;
- Spring сам вызывает колбеки инициализации/разрушения.

Применение:
- stateless-сервисы;
- репозитории, конфигурации, фасады и т.п.

---

#### 3.3. `prototype`

```java
@Scope("prototype")
@Component
public class MyPrototypeBean { ... }
```

- при каждом запросе к контейнеру (`getBean`, автосвязывание в другой бин) создаётся **новый экземпляр**;
- Spring:
  - вызывает все инициализационные шаги (инъекции, `@PostConstruct`, `afterPropertiesSet`);
  - **НЕ** вызывает колбеки разрушения (`@PreDestroy`, `destroy()`).

Важно:
- если `prototype`-бин внедрён в `singleton` через обычный `@Autowired`:
  - **один и тот же экземпляр** будет использоваться всё время;
  - "каждый раз новый" не сработает.
- для "каждый раз новый" используют:
  - `ObjectProvider<MyPrototypeBean>`;
  - `@Lookup`-метод;
  - или прокси (`@Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)`).

Применение:
- редко, для стейтфул-сущностей, создаваемых по запросу;
- например, какие-то объекты, живущие в рамках конкретной операции.

---

#### 3.4. `request` / `session` / `application` / `websocket`

Только в веб-контексте:

- `request`:
  - один бин на один HTTP-запрос;
  - создаётся при первом обращении в рамках запроса;
  - уничтожается после завершения запроса.

```java
@RequestScope
@Component
public class RequestScopedBean { ... }
```

- `session`:
  - один бин на HTTP-сессию;
  - создаётся при первом обращении в рамках сессии;
  - живёт, пока жива сессия (или пока его явно не уберут).

```java
@SessionScope
@Component
public class SessionScopedBean { ... }
```

- `application`:
  - по сути аналог singleton, но привязан к `ServletContext`;
  - один на весь веб-приложение в контейнере сервлетов.

- `websocket`:
  - один бин на WebSocket-сессию.

Особенности:
- такие бины обычно создаются через **прокси**:
  - в singleton-контексте инжектится прокси, который внутри делегирует на конкретный экземпляр в зависимости от текущего запроса/сессии.

---

#### 3.5. Как ответить на собесе

- "По умолчанию Spring создаёт бины в scope `singleton` — один экземпляр на контекст. Есть также `prototype` (новый экземпляр при каждом запросе к контейнеру) и веб-скоупы: `request`, `session`, `application`, `websocket`, где бин живёт в рамках HTTP-запроса, сессии и т.п. Для prototype Spring не вызывает колбеки разрушения, а при внедрении prototype в singleton по умолчанию получаем один экземпляр; для динамического поведения используют `ObjectProvider`, `@Lookup` или scoped-прокси."


### Вопрос 4. Tools for creating proxies. Differences between Dynamic Proxy and CGLib

#### 4.1. Зачем вообще нужны прокси в Spring

Spring активно использует **динамические прокси** для:
- AOP (`@Transactional`, `@Async`, логирование, ретраи и т.п.);
- security (`@PreAuthorize` и др.);
- кэширования (`@Cacheable`);
- scoped-бинов (`@RequestScope`, `@SessionScope` и др.).

Идея:
- вместо того чтобы вернуть "сырой" бин, контейнер возвращает **прокси** — объект, который:
  - реализует те же интерфейсы / наследует тот же класс;
  - перехватывает вызовы методов;
  - добавляет кросс-секшн-логику (транзакции, логирование и т.п.);
  - делегирует в реальный целевой бин.

---

#### 4.2. JDK Dynamic Proxy

Классический механизм из JDK (`java.lang.reflect.Proxy`):
- умеет генерировать прокси-классы **только для интерфейсов**.

В Spring:
- используется, если бин **реализует хотя бы один интерфейс** и включён `proxyTargetClass=false` (по умолчанию долгое время для многих конфигураций).

Пример псевдокода:

```java
MyInterface proxy = (MyInterface) Proxy.newProxyInstance(
    classLoader,
    new Class<?>[]{MyInterface.class},
    invocationHandler
);
```

Ограничения:
- нельзя проксировать "чистый" класс без интерфейсов;
- нельзя перехватить вызовы `final`-методов (и вообще финальные методы не переопределяются).

---

#### 4.3. CGLIB (Class-based proxy)

**CGLIB** — библиотека, которая генерирует подклассы (subclass) для проксирования:
- прокси **наследует** целевой класс;
- переопределяет его методы и вставляет перехваты.

В Spring:
- используется, когда:
  - целевой класс **не реализует интерфейсы**;
  - или явно включено `proxyTargetClass=true` / `@EnableAspectJAutoProxy(proxyTargetClass = true)`.

Ограничения:
- нельзя проксировать `final`-классы;
- нельзя перехватить `final`-методы;
- конструктор целевого класса всё равно выполняется.

---

#### 4.4. Как Spring выбирает между JDK Proxy и CGLIB

По умолчанию:
- если есть интерфейс — Spring создаёт **JDK Dynamic Proxy**, проксирует интерфейсы;
- если нет — использует **CGLIB** и проксирует класс.

Можно принудительно включить CGLIB:

```java
@EnableAspectJAutoProxy(proxyTargetClass = true)
```

или в Boot-конфиге:

```properties
spring.aop.proxy-target-class=true
```

---

#### 4.5. Кратко различия

- **JDK Dynamic Proxy**:
  - прокси **только по интерфейсам**;
  - легче/проще, без сторонних зависимостей;
  - не требует bytecode generation по классам.

- **CGLIB**:
  - прокси **по классам** — можно проксировать класс без интерфейсов;
  - ограничение: нельзя final-классы/методы;
  - использует генерацию байткода (раньше отдельная библиотека, сейчас встроена в Spring через ByteBuddy и т.п.).

---

#### 4.6. Как ответить на собесе

- "Spring создаёт прокси для бинов через JDK Dynamic Proxy (если есть интерфейсы) или через CGLIB (если нужно проксировать класс). JDK-прокси работают только с интерфейсами, CGLIB — с наследованием от класса, но не умеет проксировать final-классы/методы. Прокси используются для `@Transactional`, AOP, security и других кросс-секшн задач."

---

### Вопрос 5. Spring MVC vs Spring Boot. Differences

#### 5.1. Что такое Spring MVC

**Spring MVC** — это веб-фреймворк в составе Spring Framework:
- модуль `spring-webmvc`;
- реализует паттерн **Model-View-Controller**;
- даёт:
  - `@Controller`, `@RestController`, `@RequestMapping` и т.п.;
  - `HandlerMapping`, `HandlerAdapter`, `ViewResolver`;
  - поддержку REST, форм, валидации и т.д.

Раньше:
- ты вручную собирал приложение:
  - настраивал `DispatcherServlet`;
  - конфигурировал view-резолверы, message converters, статические ресурсы и т.д.

---

#### 5.2. Что такое Spring Boot

**Spring Boot** — это надстройка над Spring Framework, которая:
- даёт **автоконфигурацию**:
  - поднимает и настраивает Spring MVC, DataSource, JPA, Security и т.п. по зависимостям и пропертям;
- предоставляет **opinionated defaults**:
  - "разумные настройки по умолчанию";
- добавляет **`spring-boot-starter-*`** стартеры:
  - наборы зависимостей для типичных задач (web, data-jpa, security, actuator и т.д.);
- позволяет запускать приложение как **self-contained jar** c embedded-сервером (Tomcat/Jetty/Undertow).

Ключевое:
- Spring Boot **использует Spring MVC** (если ты делаешь веб-приложение), а не заменяет его.

---

#### 5.3. Основные отличия с точки зрения разработчика

- **Настройка**:
  - Spring MVC "голый" → много ручной конфигурации (XML/Java Config).
  - Spring Boot → минимум конфигурации, автоконфиг основывается на зависимостях и пропертях.

- **Запуск**:
  - Spring MVC традиционно разворачивается как WAR в контейнере сервлетов (Tomcat, etc.);
  - Spring Boot — `java -jar app.jar`, встроенный сервер.

- **Зависимости**:
  - Boot управляет версиями через BOM / starters;
  - меньше проблем с версионным адом.

- **Разработка**:
  - с Boot проще стартануть проект от нуля (`start.spring.io`);
  - меньше бойлерплейта.

---

#### 5.4. Как ответить на собесе

- "Spring MVC — это модуль веб-фреймворка в Spring, реализующий MVC (контроллеры/модели/представления). Spring Boot — это надстройка, которая делает конфигурацию Spring-приложений 'автоматической', предоставляет стартовые зависимости и встроенный сервер. Веб-сервис на Boot по сути использует Spring MVC под капотом, но с минимальной ручной настройкой."

---

### Вопрос 6. Distinguishes @Component from @Service and @Repository

#### 6.1. Общая суть стереотипных аннотаций

`@Component`, `@Service`, `@Repository`, `@Controller`, `@RestController` — это **стереотипы**:
- все они (косвенно) помечены как `@Component`;
- все участвуют в компонент-сканировании (`@ComponentScan`);
- все регистрируют бин в контексте.

Разница — в **семантике** и некоторых дополнительных фичах.

---

#### 6.2. @Component

```java
@Component
public class MyHelper { ... }
```

- базовая, "голая" аннотация для любого бина;
- не несёт специфического смысла насчёт того, к какому слою он принадлежит;
- не добавляет спец-поведения (кроме того, что класс становится кандидатом на сканирование).

Используют:
- для утилитных/инфраструктурных бинов, которые не очевидно относятся к service/repository/controller.

---

#### 6.3. @Service

```java
@Service
public class UserService { ... }
```

- семантически — **сервисный слой**, бизнес-логика;
- технически — тот же `@Component` (мета-аннотация содержит `@Component`);
- может использоваться инструментами/фреймворками для:
  - генерации документации;
  - разметки слоёв (например, в проверках архитектуры).

Spring сам по себе не даёт `@Service` особых технических бонусов (в отличие от `@Repository`).

---

#### 6.4. @Repository

```java
@Repository
public class UserRepository { ... }
```

- семантика — **слой доступа к данным** (DAO/Repository);
- кроме того, Spring:
  - оборачивает исключения уровня persistence (JPA/Hibernate, JDBC) в **единый иерархический набор** `DataAccessException`;
  - это делается через PersistenceExceptionTranslationPostProcessor, который ищет `@Repository`.

То есть:
- для `@Repository` есть **реальное дополнительное поведение**:
  - перехват и перевод низкоуровневых исключений в унифицированные runtime-исключения Spring.

---

#### 6.5. Почему важно различать

1. **Читаемость и архитектура**:
   - по аннотации видно, к какому слою относится класс;
   - помогает поддерживать "слоистую" архитектуру (controller → service → repository).

2. **Интеграция с инструментами**:
   - архитектурные линтеры (ArchUnit и др.) могут проверять, чтобы:
     - контроллеры не дергали репозитории напрямую;
     - сервисы не использовали web-слой и т.п.

3. **DataAccessException translation**:
   - `@Repository` реально включает важное поведение по переводу исключений.

---

#### 6.6. Как ответить на собесе

- "`@Component` — базовая аннотация для любого бина. `@Service` и `@Repository` — стереотипы, которые семантически помечают классы как сервисный или DAO-слой; для `@Repository` Spring дополнительно включает перевод низкоуровневых persistence-исключений в `DataAccessException`. Все три участвуют в компонент-сканировании, но `@Service/@Repository` помогают явно выражать слои и использовать архитектурные проверки."


### Вопрос 7. How to perform a native database query when using Spring Data JPA?

#### 7.1. Native query через `@Query` в репозитории

Самый частый способ — объявить метод в интерфейсе репозитория с аннотацией `@Query` и `nativeQuery = true`:

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Query(
        value = "SELECT * FROM users u WHERE u.email = :email",
        nativeQuery = true
    )
    Optional<User> findByEmailNative(@Param("email") String email);
}
```

Особенности:
- `value` — текст SQL;
- `nativeQuery = true` говорит Spring Data, что это **SQL**, а не JPQL;
- биндинг параметров через `:name` и `@Param("name")`;
- возвращаемые типы:
  - сущности (`User`);
  - проекции (интерфейсные/DTO);
  - примитивы/массивы (при аккуратном маппинге).

---

#### 7.2. Native query через `EntityManager`

Можно получить доступ к `EntityManager` и вручную вызывать `createNativeQuery`:

```java
@Repository
public class UserNativeRepository {

    @PersistenceContext
    private EntityManager em;

    public List<User> findActiveUsers() {
        return em.createNativeQuery(
                "SELECT * FROM users WHERE active = true",
                User.class                 // маппинг на сущность
            )
            .getResultList();
    }
}
```

Особенности:
- второй параметр (`User.class`) позволяет сразу маппить результат на сущность;
- без него вернётся `List<Object[]>`.

---

#### 7.3. Native + проекции

Для возвращения не всей сущности, а только части полей, можно использовать **интерфейсные проекции**:

```java
public interface UserSummary {
    String getEmail();
    String getName();
}

public interface UserRepository extends JpaRepository<User, Long> {

    @Query(
        value = "SELECT u.email AS email, u.name AS name FROM users u WHERE u.active = true",
        nativeQuery = true
    )
    List<UserSummary> findActiveUserSummaries();
}
```

Spring Data по alias-ам (`AS email`, `AS name`) сматчит колонки с методами геттеров в интерфейсе.

---

#### 7.4. Как ответить на собесе

- "В Spring Data JPA нативные запросы можно писать через `@Query(nativeQuery = true)` в интерфейсе репозитория или через `EntityManager.createNativeQuery`. В первом случае мы получаем очень удобную интеграцию с методами репозитория и проекциями, во втором — полный контроль за SQL и маппингом."

---

### Вопрос 8. How does @Transactional work in Spring? What happens if one service calls a Transactional method from another Transactional method?

#### 8.1. Как работает `@Transactional` (в общем)

`@Transactional` — это **декларативное управление транзакциями**:
- Spring оборачивает бин в **транзакционный прокси** (через AOP);
- при вызове метода:
  1. прокси проверяет, есть ли уже активная транзакция;
  2. в зависимости от `propagation`:
     - начинает новую,
     - использует существующую,
     - или вообще выполняет без транзакции;
  3. выполняет метод;
  4. при отсутствии неперехваченных исключений:
     - делает `COMMIT`;
     - при `RuntimeException`/`Error` (по умолчанию) — `ROLLBACK`.

По умолчанию:
- `propagation = REQUIRED`;
- `rollbackFor` — только unchecked (`RuntimeException`, `Error`).

---

#### 8.2. На какой уровень навешивать @Transactional

Обычно:
- навешивают на **сервисный слой** (`@Service`), а не на контроллеры/репозитории;
- методы сервисов становятся "границами" транзакций.

Пример:

```java
@Service
public class OrderService {

    @Transactional
    public void createOrder(...) {
        // несколько вызовов репозиториев
    }
}
```

---

#### 8.3. Важный момент: прокси и self-invocation

`@Transactional` работает через **прокси вокруг бина**:
- когда кто-то **извне** (другой бин) вызывает `orderService.createOrder()`, вызов проходит через прокси → AOP срабатывает → транзакция открывается/закрывается.
- если один метод того же бина вызывает другой "транзакционный" метод **через `this`**:

```java
public class OrderService {

    @Transactional
    public void outer() {
        inner(); // ВАЖНО: вызов напрямую, без прокси
    }

    @Transactional
    public void inner() {
        // сюда прокси не вмешался
    }
}
```

- `inner()` в этом случае **не пройдёт через прокси**, то есть поведение `@Transactional` на нём не сработает отдельно — он будет выполняться в контексте транзакции `outer` (если та есть) как обычный метод.

Это называют **self-invocation problem**.

---

#### 8.4. Что если один сервис вызывает другой транзакционный метод?

Кейс:

```java
@Service
public class ServiceA {

    private final ServiceB serviceB;

    public ServiceA(ServiceB serviceB) {
        this.serviceB = serviceB;
    }

    @Transactional
    public void methodA() {
        // ...
        serviceB.methodB();
        // ...
    }
}

@Service
public class ServiceB {

    @Transactional
    public void methodB() {
        // ...
    }
}
```

Если у обоих `@Transactional` с `propagation = REQUIRED` (по умолчанию):
- при вызове `methodA`:
  - прокси `ServiceA` создаёт **одну транзакцию**;
  - при вызове `serviceB.methodB()`:
    - прокси `ServiceB` увидит уже активную транзакцию и **присоединится** к ней;
  - итог — **одна общая транзакция**.

Если поменять propagation, сценарий меняется, например:
- `REQUIRES_NEW` в `methodB`:
  - `methodA` → транзакция T1;
  - `methodB` → новая транзакция T2 (T1 приостанавливается на время T2).

---

#### 8.5. Кратко про самые важные `propagation`

- `REQUIRED` (по умолчанию):
  - использует текущую транзакцию, если есть;
  - иначе создаёт новую.

- `REQUIRES_NEW`:
  - всегда создаёт новую транзакцию;
  - существующая (если была) приостанавливается.

- `NESTED`:
  - создаёт **вложенную** транзакцию (через savepoint внутри текущей);
  - поддерживается не всеми платформами.

---

#### 8.6. Как ответить на собесе

- "`@Transactional` работает через прокси и перехват вызовов методов: перед выполнением метода Spring открывает или использует транзакцию в зависимости от `propagation`, а по завершении делает commit/rollback. Если один бин вызывает другой `@Transactional`-метод через прокси, транзакционное поведение применяется; если же метод вызывает другой транзакционный метод через `this` (self-invocation), то прокси не участвует и вторая аннотация не срабатывает отдельно. По умолчанию пропагация REQUIRED, поэтому несколько уровней вызовов участвуют в одной транзакции."

---

### Вопрос 9. Name a few popular spring boot starters

Spring Boot **starter** — это удобный "пакет" зависимостей для определённой функциональности. Он подтягивает нужные библиотеки и автоконфигурацию.

Несколько популярных:

- `spring-boot-starter-web`:
  - Spring MVC + embedded Tomcat (по умолчанию);
  - REST API, контроллеры, JSON (Jackson), валидация.

- `spring-boot-starter-webflux`:
  - реактивный стек на базе Spring WebFlux;
  - Netty/Servlet-стек, `Mono`/`Flux`.

- `spring-boot-starter-data-jpa`:
  - Spring Data JPA + Hibernate;
  - интеграция с реляционными БД через ORM.

- `spring-boot-starter-security`:
  - Spring Security, фильтры, конфигурация аутентификации/авторизации.

- `spring-boot-starter-actuator`:
  - health-check-и, метрики, info, /actuator endpoints;
  - удобен для прод-мониторинга.

- `spring-boot-starter-test`:
  - JUnit, Spring Test, AssertJ, MockMvc, Mockito и др. для тестирования.

Можно упомянуть и другие:
- `spring-boot-starter-validation` (Bean Validation / Hibernate Validator);
- `spring-boot-starter-mail`, `spring-boot-starter-amqp`, `spring-boot-starter-cache` и т.д.

**Как ответить:**
- "Популярные стартеры: `spring-boot-starter-web` для REST/MVC, `spring-boot-starter-data-jpa` для работы с БД через JPA, `spring-boot-starter-security` для безопасности, `spring-boot-starter-actuator` для health/metrics и `spring-boot-starter-test` для тестов. Стартеры подтягивают набор зависимостей и автоконфигурацию под определённую задачу."

---

### Вопрос 10. What is DI and IoC?

#### 10.1. IoC — Inversion of Control

**Inversion of Control (инверсия управления)** — это общий принцип, когда:
- **контейнер/фреймворк управляет жизненным циклом объектов и их связями**;
- а твой код просто описывает, что нужно сделать, и даёт фреймворку точки расширения.

Вместо:

```java
UserRepository repo = new UserRepositoryImpl();
UserService service = new UserService(repo);
```

Ты пишешь:

```java
@Service
public class UserService {
    private final UserRepository repo;

    public UserService(UserRepository repo) {
        this.repo = repo;
    }
}
```

А Spring сам:
- создаёт `UserRepositoryImpl`;
- создаёт `UserService`;
- связывает их друг с другом.

Главная идея:
- ты отдаёшь контроль над созданием и "склеиванием" объектов контейнеру (Spring IoC Container).

---

#### 10.2. DI — Dependency Injection

**Dependency Injection (внедрение зависимостей)** — это **конкретный способ реализации IoC**, когда:
- зависимости **подставляются в объект извне**, а не создаются им внутри.

Варианты DI в Spring:
- **Constructor injection** (рекомендуется):

```java
@Service
public class OrderService {
    private final UserRepository userRepository;

    public OrderService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

- **Field injection** (`@Autowired` на поле) — часто считают anti-pattern, но много где встречается.
- **Setter injection** — зависимости можно менять после создания (редко нужно).

Spring:
- находит зависимости по типу/квалификатору (`@Qualifier`);
- создаёт/настраивает их;
- инжектит при создании бина.

---

#### 10.3. Почему это важно

Преимущества IoC/DI:
- **тестируемость**:
  - легко подменять зависимости моками/стабами;
- **слабое зацепление (loose coupling)**:
  - классы зависят от интерфейсов, а не от конкретных реализаций;
- **конфигурация в одном месте**:
  - всё управление "кто с кем живёт" сосредоточено в конфигурации/аннотациях;
- **возможность кросс-секшн фич (AOP, транзакции, security)**:
  - контейнер контролирует создание и доступ → может навесить прокси и пр.

---

#### 10.4. Как ответить на собесе

- "IoC — это принцип, при котором фреймворк берёт на себя создание объектов и управление их жизненным циклом, а приложение предоставляет только конфигурацию и точки расширения. DI — конкретная реализация IoC, когда зависимости объектов передаются им извне (через конструктор/сеттер/поле), вместо того чтобы создаваться внутри. Spring — контейнер IoC, который реализует DI и позволяет легко заменять зависимости, тестировать и навешивать кросс-секшн функциональность." 


