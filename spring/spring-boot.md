# Spring Boot — подробное руководство для подготовки к собеседованию

Техническое описание Spring Boot: как он устроен внутри и какие концепции нужны на собеседованиях Java Backend.

---

## Table of Contents (оглавление)

- [Введение в Spring Boot](#введение-в-spring-boot)
- [Зачем нужен Spring Boot](#зачем-нужен-spring-boot)
- [Spring Boot vs Spring Framework](#spring-boot-vs-spring-framework)
- [Архитектура Spring Boot](#архитектура-spring-boot)
- [Spring Boot Starters](#spring-boot-starters)
- [@SpringBootApplication](#springbootapplication)
- [Auto Configuration](#auto-configuration)
- [Как работает Auto Configuration](#как-работает-auto-configuration)
- [Conditional Annotations](#conditional-annotations)
- [Embedded Servers](#embedded-servers)
- [SpringApplication](#springapplication)
- [Configuration и External Configuration](#configuration-и-external-configuration)
- [application.properties и application.yml](#applicationproperties-и-applicationyml)
- [Profiles](#profiles)
- [@ConfigurationProperties](#configurationproperties)
- [Spring Boot Actuator](#spring-boot-actuator)
- [Logging в Spring Boot](#logging-в-spring-boot)
- [Spring Boot DevTools](#spring-boot-devtools)
- [Жизненный цикл Spring Boot приложения](#жизненный-цикл-spring-boot-приложения)
- [Spring Boot Internals](#spring-boot-internals)
- [Краткий конспект для собеседования](#краткий-конспект-для-собеседования)

---

# Введение в Spring Boot

## Что такое Spring Boot

**Spring Boot** — надстройка над Spring Framework, которая упрощает создание и запуск приложений. Spring Boot предоставляет:

- **Автоконфигурацию** — настройку бинов и инфраструктуры по умолчанию на основе classpath и свойств.
- **Стартеры (starters)** — готовые наборы зависимостей для типовых сценариев (веб, JPA, безопасность и т.д.).
- **Встроенный сервер** — приложение можно запустить как исполняемый JAR без внешнего Tomcat/Jetty.
- **Production-ready функции** — Actuator для мониторинга, метрик и проверки здоровья приложения.

Spring Boot не заменяет Spring, а использует его: поднимает `ApplicationContext`, регистрирует бины и добавляет конвенции и автоматическую настройку.

## Какую проблему он решает

Раньше для приложения на Spring нужно было вручную настраивать контекст (XML или Java-конфиг), подключать зависимости с совместимыми версиями, настраивать диспетчер сервлетов и контейнер. Spring Boot решает это за счёт конвенций: достаточно добавить стартер и (при необходимости) свойства — приложение поднимается с разумными настройками по умолчанию.

## Почему он стал стандартом для Java backend

- **Быстрый старт** — минимум кода и конфигурации для запуска веб-приложения или сервиса.
- **Единый способ сборки** — один исполняемый JAR (fat JAR) со встроенным сервером; удобно для контейнеров и облака.
- **Совместимые версии** — BOM (Bill of Materials) задаёт согласованные версии зависимостей, меньше конфликтов.
- **Широкая экосистема** — стартеры для веб, данных, безопасности, облака; Actuator, DevTools, тестовые утилиты.

---

# Зачем нужен Spring Boot

## Проблемы обычного Spring

- **Сложная конфигурация** — нужно явно объявлять множество бинов (DataSource, EntityManagerFactory, TransactionManager, диспетчер сервлетов и т.д.) и связывать их между собой.
- **XML-конфигурация** — раньше доминировала; многословная и не типобезопасная; сейчас часто заменяется Java-конфигом, но его тоже нужно писать вручную.
- **Ручная настройка инфраструктуры** — выбор и настройка сервера (Tomcat/Jetty), деплой WAR, настройка пулов соединений, логирования — всё ложится на разработчика.

## Как Spring Boot решает эти проблемы

- **Auto Configuration** — бины создаются автоматически при наличии в classpath нужных классов и при отсутствии своих определений. Разработчик переопределяет только то, что нужно изменить.
- **Starters** — одна зависимость (например, `spring-boot-starter-web`) тянет за собой согласованный набор библиотек и включает соответствующую автоконфигурацию.
- **Встроенный сервер** — приложение запускается из `main`; сервер (Tomcat/Jetty/Undertow) встроен в JAR и стартует вместе с приложением. Не нужен отдельный WAR и внешний контейнер для разработки и простого деплоя.
- **Внешняя конфигурация** — настройки выносятся в `application.properties` / `application.yml` и переменные окружения; один и тот же артефакт может работать в разных окружениях.

---

# Spring Boot vs Spring Framework

| Критерий | Spring Framework | Spring Boot |
|----------|------------------|-------------|
| **Конфигурация** | Явная: XML или Java-конфиг, объявление бинов вручную | Конвенции + Auto Configuration; минимум явной конфигурации |
| **Скорость разработки** | Больше кода на настройку контекста и инфраструктуры | Быстрый старт за счёт стартеров и автоконфигурации |
| **Инфраструктура** | Разработчик настраивает DataSource, сервер, пулы и т.д. | Настройки по умолчанию из свойств; при необходимости переопределение |
| **Встроенные серверы** | Нет; деплой в внешний Tomcat/Jetty (WAR) | Да; Tomcat/Jetty/Undertow встроены, запуск из JAR |
| **Зависимости** | Версии задаются вручную; возможны конфликты | BOM задаёт совместимые версии; стартеры группируют зависимости |
| **Production-ready** | Нужно самостоятельно добавлять health, метрики | Actuator предоставляет эндпоинты из коробки |

Spring Boot **строится на** Spring Framework и использует тот же IoC-контейнер (ApplicationContext); он добавляет слой конвенций и автоконфигурации поверх.

---

# Архитектура Spring Boot

## Основные компоненты

| Компонент | Назначение |
|-----------|------------|
| **Spring Boot Starters** | Наборы зависимостей (POM) для веб, JPA, безопасности и т.д.; подтягивают библиотеки и транзитивно включают автоконфигурацию. |
| **Auto Configuration** | Классы с `@Configuration` и условными бинами (`@ConditionalOnClass`, `@ConditionalOnMissingBean` и др.); подключаются через `spring.factories` / `AutoConfiguration.imports` и настраивают приложение по умолчанию. |
| **Embedded Servers** | Tomcat, Jetty, Undertow встроены как зависимости; при запуске создаётся и стартует сервер внутри процесса JVM. |
| **Production-ready features** | Actuator — эндпоинты `/health`, `/metrics`, `/info`, `/env` и др. для мониторинга и диагностики; настраиваются через свойства. |

Схема упрощённо:

```
Приложение (main → SpringApplication.run)
    → Создание ApplicationContext
    → Загрузка конфигурации (properties, YAML, профили)
    → Регистрация Auto Configuration (из spring.factories / imports)
    → Component scan, регистрация @Bean из приложения
    → Создание бинов (в т.ч. контроллеры, сервисы, автоконфиг)
    → Запуск embedded server (Tomcat/Jetty/Undertow)
    → Приложение готово к приёму запросов
```

---

# Spring Boot Starters

## Что такое starter dependencies

**Starters** — артефакты Maven/Gradle, которые объявляют **набор транзитивных зависимостей** для типовой задачи. Одна зависимость подтягивает совместимые библиотеки и часто подключает соответствующую **автоконфигурацию**. Имена обычно `spring-boot-starter-*`.

## Зачем они нужны

- Не нужно вручную подбирать и выравнивать версии десятков библиотек (Spring MVC, Jackson, Tomcat, и т.д.).
- Один стартер даёт готовый «сценарий»: веб, JPA, безопасность — с уже включённой автоконфигурацией.
- Упрощается обновление: меняется версия Spring Boot, стартеры подтягивают совместимые версии зависимостей.

## Примеры стартеров

| Starter | Назначение |
|---------|------------|
| **spring-boot-starter-web** | Веб-приложение: Spring MVC, встроенный Tomcat, Jackson для JSON. |
| **spring-boot-starter-data-jpa** | JPA/Hibernate, транзакции, базовая настройка репозиториев. |
| **spring-boot-starter-security** | Spring Security: аутентификация, авторизация, CSRF и т.д. |
| **spring-boot-starter-test** | JUnit 5, Mockito, AssertJ, MockMvc, тестовый контекст Spring (@SpringBootTest). |
| **spring-boot-starter-data-redis** | Клиент Redis, настройка ConnectionFactory. |
| **spring-boot-starter-validation** | Bean Validation (JSR-380), Hibernate Validator. |

Пример в `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Версия не указывается — она задаётся родительским POM или BOM Spring Boot.

---

# @SpringBootApplication

## Что это за аннотация

**@SpringBootApplication** — составная аннотация, которая объединяет три вещи: конфигурацию приложения, включение автоконфигурации и сканирование компонентов. Вешается на класс с методом `main`, от которого обычно запускают приложение.

## Что она включает

### @SpringBootConfiguration

По сути это **@Configuration**: класс считается источником бинов и конфигурации Spring. Spring Boot использует этот класс и как точку входа конфигурации (например, для дополнительных источников конфигурации).

### @EnableAutoConfiguration

Включает механизм **автоконфигурации**. Spring Boot загружает классы из `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (и устаревший `META-INF/spring.factories` с ключом `EnableAutoConfiguration`) и применяет их как конфигурации с учётом условий (@ConditionalOnClass, @ConditionalOnMissingBean и т.д.). Именно здесь регистрируются бины для Tomcat, DataSource, Jackson, диспетчера сервлетов и многое другое.

### @ComponentScan

Сканирование компонентов с **базовым пакетом по умолчанию** — пакет класса, помеченного @SpringBootApplication, и все его подпакеты. Все @Component, @Service, @Repository, @Controller и т.д. в этом дереве пакетов регистрируются в контексте.

## Пример

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

Этого достаточно, чтобы поднять контекст, применить автоконфигурацию и отсканировать компоненты в пакете `Application` и ниже. Базовый пакет для scan можно переопределить: `@SpringBootApplication(scanBasePackages = "com.example")`.

---

# Auto Configuration

## Что такое auto configuration

**Auto Configuration** — механизм Spring Boot, который **автоматически** регистрирует бины и настраивает инфраструктуру на основе:

- содержимого **classpath** (какие классы присутствуют);
- наличия или отсутствия **определённых бинов** (например, своего DataSource);
- **свойств** конфигурации (application.properties, переменные окружения).

Цель — приложение работает с минимумом кода; разработчик добавляет свойства или свои бины только там, где нужно отойти от конвенций.

## Как Spring Boot автоматически настраивает приложение

- В JAR’ах стартеров и spring-boot-autoconfigure лежат классы с `@Configuration` и условными бинами (`@ConditionalOnClass`, `@ConditionalOnMissingBean` и т.д.).
- Эти классы перечислены в `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (или в `spring.factories`).
- При запуске Spring Boot загружает эти классы и обрабатывает их как конфигурации. Условия проверяются: если класс в classpath, бина ещё нет, свойство включено — бин создаётся.
- В результате создаются, например, `TomcatServletWebServerFactory`, `DispatcherServlet`, бины для Jackson, для DataSource (если есть драйвер и данные в конфиге) и т.д.

## Какие условия используются

Условия задаются аннотациями: наличие класса в classpath (@ConditionalOnClass), отсутствие бина (@ConditionalOnMissingBean), значение свойства (@ConditionalOnProperty), наличие другого бина (@ConditionalOnBean) и др. Подробнее — в разделе [Conditional Annotations](#conditional-annotations).

---

# Как работает Auto Configuration

## Механизм подключения автоконфигураций

Spring Boot ищет классы автоконфигурации в двух местах:

1. **META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports** (предпочтительный формат в Spring Boot 2.7+) — текстовый файл, по одной полной имени класса на строку.
2. **META-INF/spring.factories** (устаревший, но поддерживается) — ключ `org.springframework.boot.autoconfigure.EnableAutoConfiguration`, значение — список полных имён классов через запятую.

Пример содержимого `AutoConfiguration.imports`:

```
org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration
```

Пример `spring.factories`:

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration
```

## Как Spring Boot находит и применяет эти классы

- При обработке `@EnableAutoConfiguration` контекст читает перечисленные выше файлы из всех JAR в classpath (в первую очередь из `spring-boot-autoconfigure` и стартеров).
- Загружаются перечисленные классы; они должны быть отмечены `@Configuration` (или эквивалентом).
- Каждый такой класс обрабатывается как конфигурация: методы с `@Bean` и условия (@Conditional*) проверяются; если условия выполнены, бины регистрируются.
- Порядок применения можно менять через `@AutoConfigureBefore`, `@AutoConfigureAfter`, `@AutoConfigureOrder`.

В итоге без единой строки кода в приложении в контексте появляются диспетчер сервлетов, встроенный Tomcat, маппинг JSON и т.д. — при условии, что в classpath есть соответствующие классы и не объявлены свои бины, переопределяющие конвенции.

---

# Conditional Annotations

## Назначение

Условные аннотации позволяют регистрировать бины или целые конфигурации **только при выполнении условий**. В автоконфигурации они отвечают за то, чтобы, например, DataSource создавался только при наличии драйвера JDBC и отсутствии своего DataSource.

## Основные аннотации

| Аннотация | Условие |
|-----------|---------|
| **@Conditional** | Общее условие — передаётся класс, реализующий `Condition` (метод `matches`). |
| **@ConditionalOnClass** | Указанный класс (или классы) присутствует в classpath. |
| **@ConditionalOnMissingBean** | В контексте ещё нет бина указанного типа (или имени). Позволяет не переопределять бины пользователя. |
| **@ConditionalOnProperty** | Свойство из Environment имеет заданное значение (например, `feature.enabled=true`) или присутствует/отсутствует. |
| **@ConditionalOnBean** | В контексте уже есть бин указанного типа (или имени). Регистрация бина только при наличии зависимости. |
| **@ConditionalOnMissingClass** | Указанный класс отсутствует в classpath. |
| **@ConditionalOnWebApplication** / **@ConditionalOnNotWebApplication** | Контекст веб-приложения или не веб. |

## Использование в auto configuration

Типичный фрагмент автоконфигурации:

```java
@Configuration
@ConditionalOnClass(DataSource.class)
@ConditionalOnMissingBean(DataSource.class)
public class DataSourceAutoConfiguration {
    @Bean
    @ConditionalOnProperty(name = "spring.datasource.url")
    public DataSource dataSource(Environment env) {
        // создание DataSource из свойств
    }
}
```

- Бин создаётся только если в classpath есть `DataSource`, в контексте ещё нет своего DataSource и задано свойство `spring.datasource.url`.
- Так автоконфигурация не мешает пользователю объявить свой `@Bean DataSource` и не включается без нужных зависимостей.

---

# Embedded Servers

## Встроенные серверы

Spring Boot может встроить один из серверов:

| Сервер | Зависимость | Описание |
|--------|-------------|----------|
| **Tomcat** | По умолчанию в `spring-boot-starter-web` | Сервлет-контейнер Apache Tomcat. |
| **Jetty** | `spring-boot-starter-web` с исключением Tomcat + добавлением `spring-boot-starter-jetty` | Легковесный сервер Eclipse Jetty. |
| **Undertow** | Аналогично: исключить Tomcat, добавить `spring-boot-starter-undertow` | Сервер от Red Hat (Undertow). |

## Как Spring Boot запускает сервер

- Автоконфигурация (например, `ServletWebServerFactoryAutoConfiguration`) создаёт бин типа `ServletWebServerFactory` (для Tomcat — `TomcatServletWebServerFactory`).
- При старте контекста бин типа `ServletWebServerApplicationContext` (или аналог) получает эту фабрику и вызывает `getWebServer()`. Фабрика создаёт экземпляр сервера (Tomcat/Jetty/Undertow), настраивает порт, контекст-путь, регистрирует диспетчер сервлетов (DispatcherServlet) и запускает сервер (обычно в отдельном потоке).
- Порты и контекст задаются свойствами: `server.port`, `server.servlet.context-path`. По умолчанию приложение слушает порт 8080.

## Как работает встроенный контейнер

- Контейнер живёт в том же JVM, что и приложение; он не отдельный процесс.
- DispatcherServlet регистрируется по пути `/` (или по `server.servlet.context-path`) и обрабатывает HTTP-запросы, передавая их в контроллеры Spring MVC.
- Для production тот же JAR можно запускать как обычное приложение; при деплое в контейнер (Docker/Kubernetes) часто отключают встроенный сервер и деплоят WAR во внешний Tomcat, но типичный сценарий Spring Boot — один исполняемый JAR со встроенным сервером.

---

# SpringApplication

## Класс SpringApplication

**SpringApplication** — класс, который запускает Spring Boot-приложение. Обычный способ: `SpringApplication.run(Application.class, args)`.

Он:

- Создаёт экземпляр `ApplicationContext` (по умолчанию для веб — `AnnotationConfigServletWebServerApplicationContext`).
- Настраивает контекст: регистрирует конфигурационный класс (помеченный @SpringBootApplication), подключает автоконфигурацию, настраивает Environment (properties, профили).
- Обновляет контекст (refresh): загрузка определений бинов, создание бинов, запуск встроенного сервера.
- Регистрирует shutdown hook для корректного закрытия контекста при остановке JVM.

## Процесс запуска (упрощённо)

1. **Создание ApplicationContext** — выбор типа контекста (сервлетный веб или обычный) и создание экземпляра.
2. **Загрузка конфигурации** — регистрация класса с @SpringBootApplication, загрузка автоконфигураций из AutoConfiguration.imports / spring.factories, настройка Environment (properties, YAML, профили).
3. **Обновление контекста (refresh)** — сканирование компонентов, регистрация BeanDefinition, создание бинов (в т.ч. автоконфигурация, контроллеры, сервисы), вызов BeanPostProcessor, инициализация бинов.
4. **Запуск embedded server** — после создания бинов контекст создаёт и запускает WebServer (Tomcat/Jetty/Undertow); приложение готово к приёму запросов.

Дополнительно: до и после каждого этапа публикуются события (ApplicationStartingEvent, ApplicationReadyEvent и т.д.), на которые можно подписаться (ApplicationListener, @EventListener).

---

# Configuration и External Configuration

## Как Spring Boot загружает конфигурацию

Конфигурация загружается в **Environment** и доступна через `@Value`, `@ConfigurationProperties` и прямой доступ к Environment. Источники (PropertySource) добавляются в определённом порядке; первый найденный ключ выигрывает.

## Приоритет источников (от высшего к низшему, упрощённо)

1. Аргументы командной строки.
2. Свойства из Java-системы (System.getProperties()).
3. Переменные окружения (OS).
4. `application.properties` / `application.yml` вне JAR (рядом с исполняемым JAR).
5. `application.properties` / `application.yml` внутри JAR (в classpath).
6. Дефолтные свойства через `SpringApplication.setDefaultProperties`.

Профили уточняют имена файлов: например, `application-prod.yml` загружается при активном профиле `prod`. Более специфичные источники (например, профильные файлы) имеют приоритет над общими.

Полный порядок описан в документации Spring Boot (Externalized Configuration); для собеседования достаточно понимать: командная строка и переменные окружения переопределяют файлы, файлы вне JAR — файлы внутри JAR.

---

# application.properties и application.yml

## Как работают файлы конфигурации

- **application.properties** — формат Java Properties (key=value, по одному свойству на строку).
- **application.yml** (или **application.yaml**) — YAML: иерархия через отступы, списки и вложенные объекты.

Оба ищутся в classpath (часто `src/main/resources`) и опционально в текущей директории рядом с исполняемым JAR. Загружаются при старте контекста и попадают в Environment. Подстановки: в `application.properties` можно использовать `${other.key}` и значения по умолчанию `${key:default}`.

## Различия properties и YAML

| Критерий | application.properties | application.yml |
|----------|------------------------|-----------------|
| Формат | Плоский ключ=значение; вложенность через точку в ключе | Иерархия через отступы, списки, повторное использование структур |
| Читаемость сложной конфигурации | Громоздко при многих вложенных ключах | Удобнее для вложенных и списковых структур |
| Поддержка | Встроена в JDK/Spring | Требует парсера YAML (SnakeYAML в Spring Boot) |

Пример YAML:

```yaml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    username: sa
```

Эквивалент в properties:

```properties
server.port=8080
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
```

---

# Profiles

## Что такое Spring Profiles

**Профили** — именованные наборы конфигурации (например, `dev`, `test`, `prod`). Позволяют включать разные бины и свойства в зависимости от окружения. В Spring Boot профили задаются свойством `spring.profiles.active` (или переменной окружения `SPRING_PROFILES_ACTIVE`, аргументом `--spring.profiles.active=prod`).

## Зачем используются

- Разные настройки БД, логов, уровней логирования для разработки и production.
- Условная регистрация бинов: `@Profile("prod")` — бин создаётся только при активном профиле `prod`.
- Разные файлы конфигурации: `application-dev.yml`, `application-prod.yml` загружаются при активном соответствующем профиле.

## Примеры профилей

| Профиль | Типичное использование |
|---------|-------------------------|
| **dev** | Локальная БД, подробные логи, DevTools, H2 Console. |
| **test** | Тестовый контекст, in-memory БД, моки внешних сервисов. |
| **prod** | Production БД, минимум логов, отключённые отладочные эндпоинты Actuator. |

В коде:

```java
@Configuration
@Profile("prod")
public class ProdConfig {
    @Bean
    public DataSource dataSource() {
        // production DataSource
    }
}
```

В YAML можно задавать профиль-специфичные свойства в том же файле:

```yaml
---
spring:
  config:
    activate:
      on-profile: prod
server:
  port: 8443
```

---

# @ConfigurationProperties

## Что делает аннотация

**@ConfigurationProperties** связывает **свойства** из Environment с полями Java-класса. Spring Boot подставляет значения по именам свойств (с учётом префикса): например, префикс `app` и поле `name` соответствуют свойству `app.name`. Поддерживаются вложенные объекты и списки. Класс обычно помечают `@ConfigurationProperties(prefix = "app")` и регистрируют как бин (через `@EnableConfigurationProperties` или как @Bean).

## Как связывает конфигурацию с Java-классами

- По префиксу и имени поля (в kebab-case или camelCase) ищется свойство: `app.thread-pool.size` → поле `threadPool.size` или вложенный объект.
- Типы приводятся автоматически (числа, boolean, списки, вложенные объекты). Можно добавить валидацию (JSR-303) через аннотации на полях.

Пример:

```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String name = "MyApp";
    private int threadPoolSize = 10;
    private List<String> allowedHosts;
    // getters/setters
}
```

В `application.yml`:

```yaml
app:
  name: MyService
  thread-pool-size: 20
  allowed-hosts:
    - host1.example.com
    - host2.example.com
```

Регистрация в контексте:

```java
@SpringBootApplication
@EnableConfigurationProperties(AppProperties.class)
public class Application { }
```

Или бин в конфигурации: `@Bean AppProperties appProperties() { return new AppProperties(); }` с аннотацией `@ConfigurationProperties` на классе.

---

# Spring Boot Actuator

## Что такое Actuator

**Spring Boot Actuator** — подсистема **production-ready** функций: эндпоинты для мониторинга, здоровья приложения, метрик, переменных окружения, логов и т.д. Подключается зависимостью `spring-boot-starter-actuator`; эндпоинты включаются и настраиваются через свойства.

## Зачем используется

- Проверка **здоровья** приложения (health) для балансировщиков и оркестраторов.
- Сбор **метрик** (метрики JVM, HTTP, кастомные) для Prometheus, Grafana и др.
- **Диагностика** в production: свойства, переменные окружения, условия автоконфигурации (при включённом эндпоинте).
- Управление приложением (graceful shutdown, перезагрузка конфигурации — в зависимости от настроек и версии).

## Примеры эндпоинтов

| Эндпоинт | Назначение |
|----------|------------|
| **/actuator/health** | Состояние приложения (UP/DOWN); при включении details — проверки БД, дисков и т.д. |
| **/actuator/metrics** | Список метрик; /actuator/metrics/{name} — значение метрики. |
| **/actuator/info** | Информация о приложении (из свойств info.* или InfoContributor). |
| **/actuator/env** | Переменные окружения и свойства (с маскировкой секретов). |
| **/actuator/beans** | Список бинов в контексте (удобно для отладки). |

Включение и экспозиция по HTTP:

```properties
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=when-authorized
```

По умолчанию многие эндпоинты отключены от публичного доступа; в production их обычно защищают (безопасность, сетевое разделение) и ограничивают набор (exposure.include).

---

# Logging в Spring Boot

## Система логирования

Spring Boot по умолчанию подключает **Logback** как реализацию SLF4J. Зависимости от других логгеров (например, JCL, java.util.logging) перенаправляются в SLF4J через мосты (jcl-over-slf4j и т.д.), чтобы всё шло в один бэкенд (Logback).

## Конфигурация логирования

- Уровни и аппендеры задаются в **application.properties/YAML** или в **logback-spring.xml** (в classpath). Имя `logback-spring.xml` даёт возможность использовать расширения Spring Boot (профили, свойство `logging.file.name` и т.д.).
- Свойства: `logging.level.root=INFO`, `logging.level.org.springframework.web=DEBUG`, `logging.file.name=app.log` (или устаревшее `logging.file`). Для пакетов: `logging.level.com.example=DEBUG`.
- В production часто вывод в JSON (например, через logstash-logback-encoder) и централизованный сбор логов.

---

# Spring Boot DevTools

## Что даёт DevTools

**spring-boot-devtools** — набор утилит для удобства разработки. Основное:

- **Автоматический перезапуск** — при изменении classpath (перекомпиляция классов) приложение перезапускается. Классы загружаются двумя classloader’ами: «базовые» (библиотеки) и «перезагружаемые» (код приложения); меняется только перезагружаемая часть, что быстрее полного рестарта.
- Кэш отключен или ослаблен по умолчанию (например, Thymeleaf, шаблоны) в dev.
- Удобства: live reload (при поддержке браузером), настройки по умолчанию для локальной разработки.

Зависимость подключается с `optional` или в scope `runtime`, чтобы не тянуть её в production-сборку.

## Developer productivity

- Меньше времени на ручной перезапуск после изменений кода.
- Быстрый цикл обратной связи при разработке веб-приложений и API.

---

# Жизненный цикл Spring Boot приложения

## Последовательность этапов

1. **Запуск SpringApplication** — вызов `SpringApplication.run(...)`; определение типа приложения (сервлетное/реактивное/обычное), создание контекста.
2. **Создание ApplicationContext** — инстанциирование контекста (например, `AnnotationConfigServletWebServerApplicationContext`), настройка Environment (properties, профили), регистрация конфигурационного класса с @SpringBootApplication.
3. **Загрузка конфигурации** — чтение application.properties/yml, переменных окружения, подключение автоконфигураций из AutoConfiguration.imports / spring.factories.
4. **Обновление контекста (refresh)** — component scan, регистрация BeanDefinition из автоконфигураций и приложения, создание бинов (включая ServletWebServerFactory, DispatcherServlet, контроллеры, сервисы), инициализация бинов.
5. **Запуск embedded server** — контекст создаёт WebServer из фабрики, регистрирует DispatcherServlet, запускает сервер (порт из конфигурации); публикуется событие ApplicationReadyEvent.
6. **Приложение готово** — обрабатывает HTTP-запросы; при остановке JVM вызывается shutdown hook, контекст закрывается, уничтожаются бины, сервер останавливается.

---

# Spring Boot Internals

## Как работает auto configuration

- Классы автоконфигурации перечислены в JAR’ах (AutoConfiguration.imports или spring.factories).
- При обработке @EnableAutoConfiguration контекст загружает эти классы и применяет их как @Configuration с учётом @Conditional*.
- Условия проверяются по classpath, по наличию бинов, по свойствам; при выполнении условий регистрируются бины (DataSource, сервер, диспетчер и т.д.). Порядок можно менять через @AutoConfigureBefore/After/Order.

## Как создаются бины

Тот же механизм Spring Core: после регистрации всех BeanDefinition контекст строит граф зависимостей и создаёт бины (конструктор/фабрика), внедряет зависимости, применяет BeanPostProcessor (в т.ч. для AOP и транзакций), вызывает @PostConstruct и InitializingBean. Автоконфигурация лишь добавляет дополнительные BeanDefinition до этого этапа.

## Как Spring Boot интегрируется со Spring Core

Spring Boot использует стандартный **ApplicationContext** (расширение BeanFactory). Он не подменяет контейнер, а:

- Создаёт контекст подходящего типа (сервлетный веб, реактивный или обычный).
- Регистрирует конфигурационный класс с @SpringBootApplication и загружает автоконфигурации.
- Настраивает Environment (PropertySources из файлов и окружения).
- После refresh контекста получает бин WebServer и запускает его.

Вся работа с бинами, DI, жизненным циклом — по правилам Spring Core; Spring Boot добавляет конвенции, автоконфигурацию и запуск встроенного сервера.

---

# Краткий конспект для собеседования

## Что такое Spring Boot

Надстройка над Spring Framework: автоконфигурация, стартеры, встроенный сервер, production-ready функции (Actuator). Упрощает создание и запуск приложений за счёт конвенций и минимума явной конфигурации.

## Что делает @SpringBootApplication

Объединяет три вещи: **@SpringBootConfiguration** (класс как @Configuration), **@EnableAutoConfiguration** (подключение автоконфигураций из imports/spring.factories), **@ComponentScan** (сканирование компонентов от пакета класса с этой аннотацией).

## Как работает auto configuration

Классы с @Configuration и @Conditional* перечислены в META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports (или в spring.factories). При старте контекста они загружаются; условия (@ConditionalOnClass, @ConditionalOnMissingBean и т.д.) проверяются; при выполнении регистрируются бины. Так появляются сервер, DataSource, DispatcherServlet и др. без кода в приложении.

## Что такое starters

Наборы транзитивных зависимостей для типовых сценариев (web, data-jpa, security, test). Один артефакт подтягивает совместимые библиотеки и подключает соответствующую автоконфигурацию. Упрощают зависимости и версии.

## Что такое Actuator

Подсистема эндпоинтов для мониторинга и диагностики: /health, /metrics, /info, /env и др. Подключается spring-boot-starter-actuator; включается и ограничивается через свойства (exposure.include, безопасность). Используется для проверки здоровья и сбора метрик в production.

## Что такое profiles

Именованные наборы конфигурации (dev, test, prod). Задаются через spring.profiles.active. Позволяют включать разные бины (@Profile) и разные свойства (application-{profile}.yml); один артефакт — разные окружения.

---

*Документ предназначен для подготовки к техническим собеседованиям Java Backend. Рекомендуется дополнять материалами по Spring Core и практикой с реальными проектами.*
