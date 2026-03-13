# Apache Maven — подготовка к собеседованию (Java Backend)

Полное руководство по Maven для подготовки к техническим собеседованиям на позицию Java backend-разработчика.

---

## 1. Введение в инструменты сборки

### Что такое инструмент сборки (Build Tool)

- **Инструмент сборки** — программа, которая автоматизирует компиляцию, тестирование, упаковку и развёртывание приложения из исходного кода.
- Управляет зависимостями, порядком шагов и окружением сборки.

### Зачем нужна автоматизация сборки

| Без автоматизации | С автоматизацией (Maven/Gradle) |
|-------------------|--------------------------------|
| Ручной `javac`, копирование JAR | Одна команда: `mvn package` |
| Скачивание библиотек вручную | Зависимости объявляются в конфиге и подтягиваются автоматически |
| Разные структуры проектов в команде | Единая структура (convention over configuration) |
| Сложно воспроизвести сборку | Воспроизводимая сборка на любой машине |

### Типичный процесс сборки

```
Исходный код → Компиляция → Тесты → Упаковка → Развёртывание
     │              │           │         │            │
     │              │           │         │            └── deploy (артефакт в репозиторий)
     │              │           │         └── package (JAR/WAR/EAR)
     │              │           └── test (unit/integration)
     │              └── compile
     └── validate (проверка POM и структуры)
```

- **compile** — компиляция исходного кода в байткод.
- **test** — запуск тестов (часто JUnit/TestNG).
- **package** — создание артефакта (JAR, WAR и т.д.).
- **deploy** — публикация артефакта в удалённый репозиторий.

---

## 2. Что такое Apache Maven

### Определение

**Apache Maven** — инструмент управления проектами и сборки, ориентированный на Java. Использует модель проекта на основе **POM (Project Object Model)** и предопределённые фазы жизненного цикла.

### Ключевые цели Maven

1. **Упрощение процесса сборки** — единый способ сборки проектов.
2. **Единая система управления зависимостями** — координаты, репозитории, транзитивные зависимости.
3. **Соглашения вместо конфигурации** — стандартная структура каталогов и фаз.
4. **Расширяемость** — плагины для любых задач (компиляция, тесты, отчёты, деплой).

### Convention over Configuration

Maven предполагает стандартную структуру проекта. Если её придерживаться, минимум настроек в `pom.xml`:

```
my-app/
├── pom.xml
└── src/
    ├── main/
    │   ├── java/        ← исходный код
    │   └── resources/   ← ресурсы (properties, xml и т.д.)
    └── test/
        ├── java/        ← тесты
        └── resources/   ← ресурсы для тестов
```

Отклонения от этой структуры требуют явной настройки плагинов.

---

## 3. Основные концепции Maven

### Project Object Model (POM)

- **POM** — XML-файл `pom.xml`, описывающий проект: метаданные, зависимости, плагины, цели сборки.
- Maven при сборке загружает POM и по нему определяет, что компилировать, какие зависимости подтянуть и как упаковать артефакт.

### Координаты (Coordinates)

Однозначно идентифицируют артефакт в репозитории:

| Координата | Назначение | Пример |
|------------|------------|--------|
| **groupId** | Организация/проект (обратная доменная запись) | `com.company.project` |
| **artifactId** | Имя артефакта (модуля) | `user-service` |
| **version** | Версия артефакта | `1.0.0`, `2.1-SNAPSHOT` |
| **packaging** | Тип упаковки (необязательно, по умолчанию `jar`) | `jar`, `war`, `pom` |

Полное имя артефакта: `groupId:artifactId:packaging:version`  
Пример: `org.springframework.boot:spring-boot-starter-web:jar:3.2.0`

### Артефакты

- **Артефакт** — результат сборки (JAR, WAR, EAR) или зависимость (библиотека), идентифицируемая координатами.
- Хранятся в репозиториях в виде файлов с именами вида `artifactId-version.packaging`.

### Репозитории

- **Локальный (local)** — каталог на диске (часто `~/.m2/repository`). Кэш всех загруженных артефактов.
- **Удалённый (remote)** — сервер (Nexus, Artifactory, Maven Central). Maven скачивает артефакты по запросу.
- **Maven Central** — центральный публичный репозиторий (https://repo.maven.apache.org/maven2/). По умолчанию используется для разрешения зависимостей.

---

## 4. Структура Maven-проекта

### Стандартная раскладка каталогов

```
project/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/           # Исходный код приложения
│   │   │   └── com/example/App.java
│   │   └── resources/      # Ресурсы, попадающие в classpath
│   │       ├── application.properties
│   │       └── META-INF/
│   └── test/
│       ├── java/           # Исходный код тестов
│       │   └── com/example/AppTest.java
│       └── resources/      # Ресурсы для тестов
└── target/                 # Результаты сборки (генерируется)
    ├── classes/
    ├── test-classes/
    └── project-1.0.jar
```

### src/main/java

- Исходный код приложения.
- По умолчанию компилируется в `target/classes` и попадает в итоговый артефакт.

### src/main/resources

- Файлы, которые копируются в `target/classes` и оказываются в classpath (properties, XML, статика и т.д.).
- Не компилируются как Java, но включаются в JAR/WAR.

### src/test/java

- Исходный код тестов (JUnit, TestNG и др.).
- Компилируется и выполняется на фазе `test`, не попадает в основной артефакт.

### src/test/resources

- Ресурсы, доступные только в classpath во время выполнения тестов (например, тестовые конфиги, данные).

---

## 5. Файл pom.xml

### Структура pom.xml

Корневой элемент — `<project>` с namespace Maven. Основные блоки:

- **Метаданные проекта** — `groupId`, `artifactId`, `version`, `packaging`, `name`, `description`.
- **Зависимости** — `<dependencies>`.
- **Управление зависимостями** — `<dependencyManagement>` (часто в parent POM).
- **Плагины** — `<build><plugins>`.
- **Репозитории** — `<repositories>`, `<pluginRepositories>`.
- **Профили** — `<profiles>`.

### Минимальный пример pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>My Application</name>
    <description>Example Maven project</description>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

### Назначение основных элементов

| Элемент | Описание |
|---------|----------|
| `modelVersion` | Версия модели POM (для Maven 3 всегда `4.0.0`) |
| `groupId` | Идентификатор организации/проекта |
| `artifactId` | Имя артефакта |
| `version` | Версия проекта; `SNAPSHOT` — нестабильная разработка |
| `packaging` | Тип артефакта: `jar`, `war`, `pom`, `ear` |
| `properties` | Свойства для подстановки в POM и плагинах |
| `dependencies` | Список зависимостей проекта |
| `build` | Настройки сборки, плагины |
| `parent` | Ссылка на родительский POM (наследование) |
| `modules` | Список модулей (мультимодульный проект) |

---

## 6. Управление зависимостями

### Зависимости (dependencies)

Зависимости объявляются в `<dependencies>`. Maven подтягивает их в локальный репозиторий и добавляет в classpath при сборке и запуске.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>3.2.0</version>
    <scope>compile</scope>
</dependency>
```

### Транзитивные зависимости

- Если библиотека A зависит от B, а проект зависит от A, то **B подтягивается автоматически** (транзитивно).
- Дерево зависимостей можно посмотреть: `mvn dependency:tree`.
- Конфликты версий: при нескольких версиях одной зависимости Maven выбирает одну по правилам (nearest definition, при равной глубине — first declaration).

### Области видимости зависимостей (Dependency Scopes)

| Scope | Компиляция | Тесты | Запуск (runtime) | Пример использования |
|-------|------------|-------|-------------------|----------------------|
| **compile** | да | да | да | Основные библиотеки приложения (по умолчанию) |
| **provided** | да | да | нет | API, которые поставляет контейнер (Servlet API, Lombok) |
| **runtime** | нет | да | да | Драйверы БД, реализации (например, JDBC-драйвер) |
| **test** | нет | да | нет | JUnit, Mockito, тестовые утилиты |
| **system** | да | да | да | Локальный JAR по пути (не рекомендуется) |
| **import** | — | — | — | Только в `<dependencyManagement>` для BOM (POM) |

Пример `provided` (Servlet API в веб-приложении):

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```

### Дерево зависимостей

```bash
mvn dependency:tree
```

Вывод показывает прямые и транзитивные зависимости и помогает находить дубликаты и конфликты версий.

```
[INFO] com.example:my-app:jar:1.0-SNAPSHOT
[INFO] +- org.springframework.boot:spring-boot-starter-web:jar:3.2.0
[INFO] |  +- org.springframework.boot:spring-boot-starter:jar:3.2.0
[INFO] |  |  +- ...
[INFO] |  \- org.springframework:spring-webmvc:jar:6.1.1
[INFO] \- junit:junit:jar:4.13.2:test
```

---

## 7. Жизненный цикл сборки Maven

Maven имеет три встроенных жизненных цикла. Наиболее важен **default** (сборка и распространение артефакта).

### Clean lifecycle

- Цель: удалить артефакты предыдущей сборки.
- Фаза: **clean** — удаляет каталог `target/`.

### Default lifecycle

Основные фазы (выполняются по порядку до запрошенной):

```
validate → compile → test → package → verify → install → deploy
```

| Фаза | Действие |
|------|----------|
| **validate** | Проверка корректности проекта и наличия необходимой информации |
| **compile** | Компиляция исходного кода в `target/classes` |
| **test** | Запуск юнит-тестов (например, Surefire) |
| **package** | Упаковка скомпилированного кода в JAR/WAR и т.д. |
| **verify** | Проверки артефакта (например, интеграционные тесты) |
| **install** | Установка артефакта в локальный репозиторий |
| **deploy** | Копирование артефакта в удалённый репозиторий |

Вызов фазы запускает все предыдущие: `mvn install` выполняет validate → compile → test → package → verify → install.

### Site lifecycle

- Генерация отчётов и документации проекта (site).
- Фаза **site** создаёт сайт в `target/site`.

### Схема выполнения фаз

```
mvn package
    │
    ├── validate
    ├── initialize
    ├── generate-sources
    ├── process-sources
    ├── generate-resources
    ├── process-resources
    ├── compile
    ├── process-classes
    ├── generate-test-sources
    ├── process-test-sources
    ├── generate-test-resources
    ├── process-test-resources
    ├── test-compile
    ├── process-test-classes
    ├── test
    ├── prepare-package
    ├── package
    └── (verify, install, deploy не выполняются)
```

---

## 8. Плагины Maven

### Что такое плагины

- **Плагин** — расширение Maven, привязанное к фазам жизненного цикла.
- Содержит **goals** (цели). Вызов цели может быть привязан к фазе (например, `compiler:compile` к фазе `compile`).

### Зачем они нужны

- Компиляция, тесты, упаковка, деплой, отчёты — всё делают плагины.
- Без плагинов фазы жизненного цикла не выполняли бы реальных действий.

### Часто используемые плагины

| Плагин | Назначение | Типичная фаза |
|--------|------------|----------------|
| **maven-compiler-plugin** | Компиляция Java | compile |
| **maven-surefire-plugin** | Запуск юнит-тестов | test |
| **maven-jar-plugin** | Сборка JAR | package |
| **maven-war-plugin** | Сборка WAR | package |
| **spring-boot-maven-plugin** | Сборка executable JAR и запуск Spring Boot | package, run |

Пример настройки компилятора под Java 17:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.11.0</version>
            <configuration>
                <source>17</source>
                <target>17</target>
                <encoding>UTF-8</encoding>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### Spring Boot Maven Plugin

Используется в Spring Boot проектах для:

- Сборки «fat JAR» (со всеми зависимостями);
- Запуска приложения: `mvn spring-boot:run`.

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```

---

## 9. Репозитории Maven

### Локальный репозиторий

- Путь по умолчанию: `~/.m2/repository` (или значение из `settings.xml`: `<localRepository>`).
- Все скачанные артефакты и установленные локально (`mvn install`) хранятся здесь.
- При разрешении зависимостей Maven сначала ищет артефакт локально, затем в удалённых репозиториях.

### Удалённый репозиторий

- Определяется в POM в `<repositories>` или глобально в `settings.xml`.
- По умолчанию используется **Maven Central** (id: `central`).
- В корпоративной среде часто добавляют Nexus или Artifactory как зеркало или основной remote.

Пример добавления репозитория в `pom.xml`:

```xml
<repositories>
    <repository>
        <id>company-releases</id>
        <url>https://nexus.company.com/repository/maven-releases/</url>
        <releases><enabled>true</enabled></releases>
        <snapshots><enabled>false</enabled></snapshots>
    </repository>
</repositories>
```

### Maven Central

- Публичный репозиторий: https://repo.maven.apache.org/maven2/
- Содержит огромное количество открытых библиотек.
- Публикация в Central требует соблюдения правил (подпись, метаданные, проверки).

### Разрешение зависимостей

```
Запрос зависимости (groupId:artifactId:version)
    │
    ├── 1. Поиск в локальном репозитории (~/.m2/repository)
    │       └── Найдено → использование
    │
    └── 2. Не найдено → запрос к удалённым репозиториям
            (в порядке: репозитории из POM, затем Central)
            └── Скачивание в локальный репозиторий и использование
```

---

## 10. Мультимодульные проекты

Проект разбит на несколько модулей; у каждого свой `pom.xml`, общая сборка управляется из корня.

### Parent POM

- Отдельный проект с `<packaging>pom</packaging>`.
- Содержит общие зависимости в `<dependencyManagement>`, общие плагины в `<build>`, свойства в `<properties>`.
- Модули подключают его через `<parent>`.

```xml
<!-- parent/pom.xml -->
<groupId>com.example</groupId>
<artifactId>parent</artifactId>
<version>1.0</version>
<packaging>pom</packaging>

<modules>
    <module>module-a</module>
    <module>module-b</module>
</modules>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>3.2.0</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### Aggregator POM

- POM, который перечисляет модули в `<modules>` и собирает их одной командой из корня (`mvn clean install`).
- Может совпадать с parent POM (один файл выполняет обе роли).

### Модули

Каждый модуль — отдельная папка со своим `pom.xml`:

```xml
<!-- module-a/pom.xml -->
<parent>
    <groupId>com.example</groupId>
    <artifactId>parent</artifactId>
    <version>1.0</version>
    <relativePath>../parent/pom.xml</relativePath>
</parent>
<artifactId>module-a</artifactId>
```

Структура:

```
root/
├── pom.xml          (aggregator + опционально parent)
├── module-a/
│   └── pom.xml
└── module-b/
    └── pom.xml
```

---

## 11. Профили Maven (Profiles)

### Что такое профили

- **Профиль** — набор настроек (свойства, зависимости, плагины, репозитории), активируемый по условию или вручную.
- Позволяет иметь разные конфигурации для dev, test, prod без дублирования проектов.

### Активация

- По умолчанию: `mvn -P prod clean package`
- По свойству: `<activation><property><name>env</name><value>prod</value></property></activation>`
- По ОС, по наличию файла, по JDK и т.д.

Пример профилей для разных окружений:

```xml
<profiles>
    <profile>
        <id>dev</id>
        <properties>
            <spring.profiles.active>dev</spring.profiles.active>
        </properties>
    </profile>
    <profile>
        <id>prod</id>
        <activation>
            <activeByDefault>false</activeByDefault>
        </activation>
        <properties>
            <spring.profiles.active>prod</spring.profiles.active>
        </properties>
    </profile>
</profiles>
```

Сборка: `mvn clean package -P prod`.

---

## 12. Основные команды Maven

| Команда | Описание |
|---------|----------|
| `mvn clean` | Удаляет `target/` (фаза clean) |
| `mvn compile` | Компилирует `src/main/java` в `target/classes` |
| `mvn test` | Компилирует и запускает тесты (Surefire) |
| `mvn package` | Собирает JAR/WAR в `target/` (без установки в репозиторий) |
| `mvn install` | Собирает артефакт и устанавливает в локальный репозиторий |
| `mvn deploy` | Публикует артефакт в удалённый репозиторий (часто требует настройки в `settings.xml`) |
| `mvn dependency:tree` | Выводит дерево зависимостей |
| `mvn dependency:resolve` | Скачивает все зависимости |
| `mvn clean install` | Очистка + полная сборка и установка в локальный репозиторий |
| `mvn spring-boot:run` | Запуск Spring Boot приложения (если подключён spring-boot-maven-plugin) |

Комбинирование фаз:

```bash
mvn clean package -DskipTests        # Сборка без тестов
mvn clean install -P prod            # Сборка с профилем prod
mvn dependency:tree -Dverbose        # Дерево зависимостей с конфликтами
```

---

## 13. Maven в проектах Spring Boot

### spring-boot-starter-parent

Родительский POM Spring Boot задаёт версии зависимостей и плагинов:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
    <relativePath/>
</parent>

<artifactId>my-spring-app</artifactId>
<version>1.0</version>
```

Даёт:

- Управление версиями библиотек Spring и совместимых зависимостей.
- Настроенный maven-compiler-plugin (Java-версия).
- Настроенный spring-boot-maven-plugin.

### spring-boot-maven-plugin

- Упаковывает приложение в executable JAR (fat JAR) со встроенными зависимостями.
- Цель `spring-boot:run` запускает приложение без предварительной установки в репозиторий.

В Spring Boot 3 при использовании `spring-boot-starter-parent` плагин подключается по умолчанию; при необходимости его можно явно описать в `<build><plugins>`.

---

## 14. Преимущества и недостатки Maven

### Преимущества

- Чёткая структура проекта и жизненный цикл.
- Мощное управление зависимостями (транзитивность, scope, BOM).
- Огромная экосистема плагинов.
- Один формат конфигурации (XML), привычный для многих команд.
- Хорошая поддержка мультимодульных и корпоративных проектов.
- Maven Central и интеграция с Nexus/Artifactory.

### Недостатки

- XML может быть многословным; сложные POM трудно читать.
- Медленнее Gradle при больших проектах (нет инкрементальной сборки «из коробки» в той же мере).
- Кастомные сценарии часто требуют написания или настройки плагинов.
- Жёсткая привязка к фазам жизненного цикла не всегда удобна.

---

## 15. Maven и Gradle — краткое сравнение

| Критерий | Maven | Gradle |
|----------|--------|--------|
| Конфигурация | XML (pom.xml) | DSL (Groovy/Kotlin) — build.gradle |
| Подход | Конвенция, фазы, плагины | Задачи (tasks), гибкая настройка |
| Скорость | Обычно медленнее на больших проектах | Кэш и инкрементальная сборка, часто быстрее |
| Читаемость | Много тегов в XML | Более компактный и выразительный код |
| Управление зависимостями | Сильное, репозитории и BOM | Аналогично, плюс гибкие конфигурации |
| Популярность в Java | Очень широко | Растёт, стандарт в Android и многих новых проектах |

Для собеседования достаточно понимать: Maven — конвенция и фазы, XML; Gradle — задачи и DSL, часто быстрее и гибче.

---

## 16. Типичные вопросы на собеседовании

### Что такое Maven?

Инструмент сборки и управления проектами для Java. Обеспечивает единый жизненный цикл сборки, управление зависимостями через POM и репозитории, расширяемость через плагины. Основан на соглашениях (convention over configuration).

### Что такое pom.xml?

Файл Project Object Model — XML-описание проекта: координаты (groupId, artifactId, version), зависимости, плагины, свойства, модули, репозитории. Maven использует его для сборки и разрешения зависимостей.

### Что такое scope (область видимости) зависимости?

Scope определяет, на каких этапах зависимость доступна: **compile** (всюду), **provided** (компиляция и тесты, не в runtime), **runtime** (тесты и запуск), **test** (только тесты). Влияет на classpath при компиляции, тестах и запуске, а также на включение в артефакт.

### Что такое жизненные циклы и фазы Maven?

Три цикла: **clean** (очистка), **default** (сборка и распространение), **site** (документация). В default основные фазы: validate → compile → test → package → verify → install → deploy. Запуск фазы выполняет её и все предыдущие.

### Что такое плагины в Maven?

Плагины — расширения, которые выполняют реальные действия (компиляция, тесты, упаковка). Содержат цели (goals), привязанные к фазам жизненного цикла. Примеры: maven-compiler-plugin, maven-surefire-plugin, spring-boot-maven-plugin.

### Что такое репозиторий в Maven?

Хранилище артефактов. **Локальный** (`~/.m2/repository`) — кэш на машине разработчика. **Удалённый** — сервер (Nexus, Artifactory, Maven Central). Maven сначала ищет артефакт локально, при отсутствии — скачивает из удалённого и сохраняет локально.

### В чём разница между install и deploy?

**install** — копирует собранный артефакт в локальный репозиторий (`~/.m2/repository`). **deploy** — публикует артефакт в удалённый репозиторий (нужна настройка в POM и часто в `settings.xml`).

### Что такое транзитивная зависимость?

Зависимость зависимости. Если проект зависит от библиотеки A, а A зависит от B, то B подтягивается автоматически. Дерево таких зависимостей показывается командой `mvn dependency:tree`.

### Зачем нужен dependencyManagement?

Централизованное объявление версий (и иногда scope) зависимостей без автоматического добавления их в проект. Модули или дочерние POM ссылаются на артефакт без указания версии. Часто используется в parent POM и с BOM (например, Spring Boot).

### Что такое BOM (Bill of Materials)?

POM с `<packaging>pom</packaging>`, содержащий только `<dependencyManagement>` с согласованным набором версий. Подключается через `<scope>import</scope>` в `<dependencyManagement>`. Упрощает согласование версий в большом проекте или экосистеме (например, Spring Boot BOM).

---

*Документ предназначен для подготовки к техническим собеседованиям на позицию Java backend-разработчика. Рекомендуется дополнять его примерами из своих проектов и актуальными версиями Maven и библиотек.*
