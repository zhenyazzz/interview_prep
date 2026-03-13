# Gradle — подготовка к собеседованию (Java Backend)

Руководство по Gradle для подготовки к техническим собеседованиям на позицию Java backend-разработчика.

---

## 1. Введение в Gradle

### Что такое Gradle

- **Gradle** — система автоматизации сборки с открытым исходным кодом, ориентированная на производительность и расширяемость.
- Использует **DSL** (Groovy или Kotlin) для описания сборки вместо XML.
- Поддерживает **инкрементальную сборку**, кэширование и параллельное выполнение задач.
- Официальный инструмент сборки для **Android**; широко используется в Java, Kotlin, Groovy и других экосистемах.

### Зачем был создан Gradle

- **Ограничения Ant** — императивный стиль, нет стандартной структуры проекта и управления зависимостями.
- **Ограничения Maven** — жёсткий жизненный цикл, многословный XML, сложно описывать нестандартные сценарии.
- **Цель Gradle** — объединить гибкость Ant, идеи конвенций Maven и современный DSL с акцентом на скорость и расширяемость.

### Gradle по сравнению с традиционными инструментами

| Аспект | Ant | Maven | Gradle |
|--------|-----|--------|--------|
| Язык конфигурации | XML (императивный) | XML (декларативный) | Groovy/Kotlin DSL |
| Модель | Задачи (targets) | Фазы жизненного цикла | Задачи (tasks) + плагины |
| Зависимости | Вручную | Встроено (POM) | Встроено (configurations) |
| Расширяемость | Макросы, скрипты | Плагины | Плагины, скрипты |
| Инкрементальная сборка | Ручная настройка | Ограниченно | Из коробки, кэш |
| Производительность | Зависит от сценария | Средняя | Высокая (daemon, cache) |

---

## 2. Что такое инструмент сборки

### Автоматизация сборки

- **Инструмент сборки** автоматизирует превращение исходного кода в готовый артефакт (JAR, WAR, исполняемый файл).
- Управляет зависимостями, порядком шагов и окружением, чтобы сборка была воспроизводимой на любой машине.

### Типичный процесс сборки

```
Исходный код → Компиляция → Тесты → Упаковка → Развёртывание
     │              │           │         │            │
     │              │           │         │            └── deploy / publish
     │              │           │         └── jar / war / bootJar
     │              │           └── test
     │              └── compileJava
     └── validate / check structure
```

- **compile** — компиляция в байткод.
- **test** — запуск юнит- и интеграционных тестов.
- **package** — создание JAR/WAR и т.д.
- **deploy/publish** — публикация в репозиторий или сервер.

---

## 3. Ключевые концепции Gradle

### Project

- **Project** — основной объект сборки. Один проект = один каталог с `build.gradle` (или `build.gradle.kts`).
- В мультимодульной сборке каждый подпроект — отдельный объект `Project`.
- Настройки проекта задаются в build-скрипте (зависимости, плагины, задачи, свойства).

### Tasks

- **Task (задача)** — атомарная единица работы: компиляция, копирование файлов, запуск тестов и т.д.
- Задачи связаны зависимостями: порядок выполнения определяется графом зависимостей.
- Примеры: `compileJava`, `test`, `jar`, `clean`.

### Plugins

- **Плагин** — расширение, которое добавляет задачи, конфигурации зависимостей и конвенции к проекту.
- Примеры: `java`, `application`, `org.springframework.boot` — добавляют стандартные задачи и настройки.

### Dependencies

- **Зависимости** — внешние библиотеки (JAR), объявленные в конфигурациях (`implementation`, `testImplementation` и т.д.).
- Gradle скачивает их из репозиториев и подключает к classpath при компиляции и запуске.

### Build scripts

- **Build script** — файл `build.gradle` (Groovy) или `build.gradle.kts` (Kotlin), описывающий проект.
- Содержит плагины, репозитории, зависимости, задачи и прочие настройки.

---

## 4. Build-скрипты Gradle

### build.gradle

- Основной скрипт сборки в **Groovy DSL**.
- Имя файла: `build.gradle`.
- Синтаксис Groovy: точки с запятой не обязательны, скобки методов можно опускать.

### build.gradle.kts

- Скрипт сборки на **Kotlin DSL**.
- Имя файла: `build.gradle.kts`.
- Строгая типизация, автодополнение в IDE, меньше «магии» синтаксиса.

### Groovy DSL vs Kotlin DSL

| Критерий | Groovy DSL | Kotlin DSL |
|----------|------------|------------|
| Файл | build.gradle | build.gradle.kts |
| Типизация | Динамическая | Статическая |
| Поддержка IDE | Хорошая | Отличная (рефакторинг, навигация) |
| Читаемость | Привычная для Java-разработчиков | Ближе к Kotlin/Java |
| Документация/примеры | Больше в сети | Растёт |

### Пример: Groovy DSL

```groovy
plugins {
    id 'java'
    id 'application'
}

group = 'com.example'
version = '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:3.2.0'
    testImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'
}

application {
    mainClass = 'com.example.Application'
}

java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}
```

### Пример: Kotlin DSL

```kotlin
plugins {
    java
    application
}

group = "com.example"
version = "1.0-SNAPSHOT"

repositories {
    mavenCentral()
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web:3.2.0")
    testImplementation("org.junit.jupiter:junit-jupiter:5.10.0")
}

application {
    mainClass.set("com.example.Application")
}

java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}
```

### Основные отличия в синтаксисе

- **Вызовы методов**: Groovy — `repositories { mavenCentral() }`, Kotlin — `repositories { mavenCentral() }` (часто то же самое; в Kotlin строки в кавычках).
- **Строки**: Groovy — `'...'` или `"..."`, Kotlin — только `"..."`.
- **Присваивание свойств**: Groovy — `mainClass = '...'`, Kotlin — `mainClass.set("...")` или `mainClass = "..."` где доступно.
- **Типы**: в Kotlin DSL явно видны типы конфигураций и задач при работе в IDE.

---

## 5. Структура проекта Gradle

Стандартная раскладка после применения плагина `java` совпадает с идеей Maven: исходники и тесты разделены.

### Пример структуры каталогов

```
my-app/
├── build.gradle
├── build.gradle.kts       # либо один из двух
├── settings.gradle
├── gradlew
├── gradlew.bat
├── gradle/
│   └── wrapper/
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── src/
│   ├── main/
│   │   ├── java/          # Исходный код
│   │   │   └── com/example/Application.java
│   │   └── resources/     # Ресурсы (classpath)
│   │       └── application.properties
│   └── test/
│       ├── java/          # Тесты
│       │   └── com/example/ApplicationTest.java
│       └── resources/     # Ресурсы для тестов
└── build/                 # Результаты сборки (генерируется)
    ├── classes/
    ├── libs/
    └── tmp/
```

- **src/main/java** — компилируется в `build/classes/java/main`.
- **src/main/resources** — копируется в `build/resources/main`, попадает в JAR.
- **src/test/java** и **src/test/resources** — используются только при выполнении тестов.

---

## 6. Задачи Gradle (Tasks)

### Что такое задачи

- **Task** — единица работы с именем, действиями (actions) и зависимостями от других задач.
- Выполняются в порядке, определяемом графом: если задача B зависит от A, сначала выполнится A.

### Как работают задачи

1. **Конфигурация (configuration phase)** — Gradle читает скрипты и строит граф задач, выполняет блоки `doFirst`/`doLast` конфигурации (если есть).
2. **Выполнение (execution phase)** — запускаются только запрошенные задачи и их зависимости, каждая задача выполняется один раз.

### Встроенные задачи (плагин Java)

| Задача | Описание |
|--------|----------|
| `compileJava` | Компиляция `src/main/java` |
| `processResources` | Копирование `src/main/resources` в build |
| `classes` | Агрегирует компиляцию и ресурсы (часто зависимость для `jar`) |
| `compileTestJava` | Компиляция тестов |
| `processTestResources` | Ресурсы для тестов |
| `test` | Запуск тестов (JUnit/TestNG) |
| `jar` | Сборка JAR в `build/libs/` |
| `clean` | Удаление каталога `build/` |

### Пользовательские задачи

Задача без типа (только действия):

```groovy
// Groovy
tasks.register('hello') {
    doLast {
        println 'Hello from Gradle!'
    }
}
```

```kotlin
// Kotlin DSL
tasks.register("hello") {
    doLast {
        println("Hello from Gradle!")
    }
}
```

Задача с типом (например, копирование):

```groovy
// Groovy
tasks.register('copyConfig', Copy) {
    from 'src/main/resources/config'
    into 'build/config'
}
```

```kotlin
// Kotlin DSL
tasks.register<Copy>("copyConfig") {
    from("src/main/resources/config")
    into("build/config")
}
```

Зависимость между задачами:

```groovy
tasks.register('buildReport') {
    dependsOn tasks.named('test')
    doLast {
        println "Tests completed. Report would go here."
    }
}
```

```kotlin
tasks.register("buildReport") {
    dependsOn(tasks.named("test"))
    doLast {
        println("Tests completed. Report would go here.")
    }
}
```

---

## 7. Жизненный цикл сборки Gradle

### Как Gradle выполняет сборку

1. **Инициализация** — чтение `settings.gradle`, определение корня и подпроектов.
2. **Конфигурация** — выполнение всех `build.gradle`, построение графа задач, применение плагинов.
3. **Выполнение** — запуск запрошенных задач и их зависимостей в правильном порядке.

```
$ gradle build

Initialization → Settings evaluated, projects loaded
     ↓
Configuration → build.gradle executed, task graph built
     ↓
Execution → :compileJava → :processResources → :classes → :jar → :test → ...
```

### Граф задач (Task Graph)

- Задачи образуют **ориентированный ациклический граф (DAG)**.
- Зависимости задаются через `dependsOn`, `mustRunAfter`, `shouldRunAfter`.
- Gradle вычисляет порядок выполнения и выполняет каждую задачу один раз.

Пример цепочки для `build` (упрощённо):

```
build
  ├── depends on → jar
  │     ├── depends on → classes
  │     │     ├── compileJava
  │     │     └── processResources
  │     └── ...
  └── depends on → test
        ├── depends on → testClasses
        │     ├── compileTestJava
        │     └── processTestResources
        └── ...
```

### Инкрементальная сборка

- Задачи с **входами (inputs)** и **выходами (outputs)** могут быть помечены как **up-to-date**.
- Если входы и выходы не изменились, задача пропускается (OUTPUT: "UP-TO-DATE").
- Это сильно ускоряет повторные сборки. Плагин `java` задаёт входы/выходы для `compileJava`, `jar`, `test` и др.

---

## 8. Управление зависимостями

### Объявление зависимостей

Зависимости объявляются внутри блока `dependencies { }` с указанием **конфигурации** (scope):

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:3.2.0'
    testImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'
}
```

```kotlin
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web:3.2.0")
    testImplementation("org.junit.jupiter:junit-jupiter:5.10.0")
}
```

Формат: `groupId:artifactId:version` (опционально `groupId:artifactId:version:classifier@extension`).

### Конфигурации зависимостей

Конфигурация определяет, когда зависимость нужна (compile/test/runtime) и как она распространяется на потребителей проекта.

### Важные конфигурации (Java/JVM)

| Конфигурация | Компиляция | Тесты | Runtime | Публикация | Аналог Maven |
|--------------|------------|-------|---------|------------|--------------|
| **implementation** | да | да | да | нет (скрыта) | compile (без транзитива) |
| **api** | да | да | да | да | compile (транзитивно) |
| **compileOnly** | да | да | нет | нет | provided |
| **runtimeOnly** | нет | да | да | нет | runtime |
| **testImplementation** | нет | да (тесты) | нет | нет | test |
| **testCompileOnly** | только тесты | да | нет | нет | — |
| **testRuntimeOnly** | нет | да (тесты) | нет | нет | — |

- **implementation** — зависимость используется при компиляции и в runtime, но **не** пробрасывается потребителям. Рекомендуется по умолчанию вместо устаревшего `compile`.
- **api** — то же, но зависимость **пробрасывается** потребителям. Используйте только для API-библиотек.
- **compileOnly** — только для компиляции (например, Lombok, Servlet API в веб-приложении).
- **runtimeOnly** — только для выполнения (например, JDBC-драйвер).
- **testImplementation** — только для тестового кода.

### Примеры

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:3.2.0'
    api 'com.fasterxml.jackson.core:jackson-databind:2.15.3'           // если ваш проект экспортирует Jackson
    compileOnly 'org.projectlombok:lombok:1.18.30'
    runtimeOnly 'com.h2database:h2:2.2.224'
    testImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher:1.3.2'
}
```

Исключение транзитивной зависимости:

```groovy
implementation('com.example:lib:1.0') {
    exclude group: 'org.slf4j', module: 'slf4j-api'
}
```

---

## 9. Репозитории

Gradle скачивает артефакты из объявленных репозиториев. Блок `repositories { }` задаёт их порядок обхода.

### Maven Central

Публичный репозиторий: https://repo.maven.apache.org/maven2/

```groovy
repositories {
    mavenCentral()
}
```

```kotlin
repositories {
    mavenCentral()
}
```

### Локальный репозиторий Maven

Каталог `~/.m2/repository` (или другой локальный Maven-репозиторий):

```groovy
repositories {
    mavenLocal()
    mavenCentral()
}
```

Обычно `mavenLocal()` ставят первым для быстрого доступа к локально собранным артефактам.

### Собственные репозитории

```groovy
repositories {
    maven {
        url = uri("https://nexus.company.com/repository/maven-public/")
        content {
            includeGroup "com.company"
        }
    }
    mavenCentral()
}
```

```kotlin
repositories {
    maven {
        url = uri("https://nexus.company.com/repository/maven-public/")
        content {
            includeGroup("com.company")
        }
    }
    mavenCentral()
}
```

### Пример полной конфигурации

```groovy
repositories {
    mavenLocal()
    maven {
        url 'https://repo.spring.io/milestone'
        mavenContent { releasesOnly(); includeGroup "org.springframework" }
    }
    mavenCentral()
}
```

---

## 10. Плагины

### Что такое плагины

- **Плагин** — расширение, которое добавляет в проект задачи, конфигурации зависимостей и конвенции (например, структуру каталогов).
- Плагины переиспользуются между проектами и упрощают build-скрипты.

### Зачем они нужны

- Стандартизация сборки (Java, War, Spring Boot).
- Меньше кода в `build.gradle`: плагин задаёт типовые задачи и настройки.
- Версионирование: один плагин задаёт версии инструментов (компилятор, тесты и т.д.).

### Часто используемые плагины

| Плагин | ID | Назначение |
|--------|-----|------------|
| Java | `java` | Компиляция, тесты, JAR |
| Application | `application` | Запуск приложения из main, дистрибутив |
| War | `war` | Сборка WAR |
| Spring Boot | `org.springframework.boot` | Executable JAR, spring-boot:run |
| Maven Publish | `maven-publish` | Публикация в Maven-репозиторий |

### Подключение плагинов

**Kotlin DSL / Plugin DSL (рекомендуется):**

```groovy
plugins {
    id 'java'
    id 'application'
    id 'org.springframework.boot' version '3.2.0'
}
```

```kotlin
plugins {
    java
    application
    id("org.springframework.boot") version "3.2.0"
}
```

**Legacy (buildscript):**

```groovy
buildscript {
    repositories { mavenCentral() }
    dependencies {
        classpath 'org.springframework.boot:spring-boot-gradle-plugin:3.2.0'
    }
}
apply plugin: 'org.springframework.boot'
```

### Плагин Java

- Добавляет конфигурации: `implementation`, `compileOnly`, `runtimeOnly`, `testImplementation` и др.
- Задачи: `compileJava`, `processResources`, `classes`, `jar`, `compileTestJava`, `test`, `clean` и т.д.
- Конвенция: `src/main/java`, `src/main/resources`, `src/test/java`, `src/test/resources`.

### Плагин Application

- Задача `run` — запуск приложения (main class задаётся в `application.mainClass`).
- Задачи `installDist` / `distZip` / `distTar` — создание дистрибутива с скриптами запуска.

```groovy
application {
    mainClass = 'com.example.Application'
}
```

### Плагин Spring Boot

- Собирает «fat JAR» (executable JAR со всеми зависимостями).
- Задачи: `bootJar`, `bootRun`.
- Обычно применяется вместе с `java` (или `java-library`).

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0'
}

// bootJar подменяет jar по умолчанию; при необходимости jar оставляют отдельно
```

---

## 11. Gradle Wrapper

### Что такое Gradle Wrapper

- **Gradle Wrapper** — скрипты `gradlew` (Unix) и `gradlew.bat` (Windows) плюс каталог `gradle/wrapper` с JAR и `gradle-wrapper.properties`.
- При первом запуске скачивается указанная в свойствах версия Gradle и сохраняется в кэш; сборка выполняется этой версией.

### Зачем он нужен

- Одинаковая версия Gradle у всех разработчиков и в CI без установки Gradle в систему.
- Воспроизводимость сборки: в репозитории зафиксирована версия Gradle.

### gradlew / gradlew.bat

- **gradlew** (Linux/macOS) и **gradlew.bat** (Windows) — скрипты, которые вызывают Gradle из wrapper.
- Использование: `./gradlew build`, `gradlew.bat build` — вместо глобальной команды `gradle build`.

Создание/обновление wrapper:

```bash
gradle wrapper --gradle-version 8.5
```

В проекте после этого появятся/обновятся:

- `gradlew`, `gradlew.bat`
- `gradle/wrapper/gradle-wrapper.jar`
- `gradle/wrapper/gradle-wrapper.properties` (в т.ч. `distributionUrl` с версией Gradle)

---

## 12. Мультимодульные проекты

### Роль settings.gradle

- **settings.gradle** (или **settings.gradle.kts**) — конфигурирует **корень** сборки и список подпроектов (модулей).
- Выполняется до всех `build.gradle`. В нём подключаются модули: `include 'module-a', 'module-b'`.

### Модули

- Каждый модуль — отдельный каталог со своим `build.gradle`.
- Имя модуля обычно совпадает с именем каталога (или задаётся в `include`).

### Родительский (корневой) проект

- Корневой проект может не содержать кода, а только общие настройки и подпроекты.
- Общие зависимости и плагины можно вынести в **root build.gradle** или в **shared script** (например, `subprojects { }`).

### Пример конфигурации

**settings.gradle:**

```groovy
rootProject.name = 'my-multi-module'
include 'core'
include 'web'
include 'app'
```

**Корневой build.gradle:**

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0' apply false
}

subprojects {
    apply plugin: 'java'
    group = 'com.example'
    version = '1.0-SNAPSHOT'

    repositories {
        mavenCentral()
    }

    java {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }

    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter:3.2.0'
        testImplementation 'org.junit.jupiter:junit-jupiter:5.10.0'
    }
}
```

**Модуль app (Spring Boot):**

**app/build.gradle:**

```groovy
plugins {
    id 'org.springframework.boot'
}

dependencies {
    implementation project(':core')
    implementation project(':web')
}
```

Структура каталогов:

```
my-multi-module/
├── settings.gradle
├── build.gradle
├── core/
│   └── build.gradle
├── web/
│   └── build.gradle
└── app/
    └── build.gradle
```

Зависимость между модулями: `implementation project(':core')` — модуль `app` зависит от `core`.

---

## 13. Производительность Gradle

### Инкрементальная сборка

- Задачи объявляют **inputs** и **outputs**. При неизменных входах и выходах задача помечается как **UP-TO-DATE** и не выполняется.
- Плагины Java, Groovy, Kotlin и др. задают входы/выходы для компиляции и упаковки, что ускоряет повторные сборки.

### Build cache

- **Локальный кэш** — результаты задач (например, скомпилированные классы) сохраняются по хэшу входов и переиспользуются при повторных сборках того же или другого проекта.
- **Удалённый кэш** (опционально) — общий кэш для команды/CI.
- Включение: в `gradle.properties` — `org.gradle.caching=true`.

### Параллельное выполнение

- Gradle может выполнять **независимые** задачи параллельно (несколько проектов в мультимодульной сборке).
- Включение: `org.gradle.parallel=true` в `gradle.properties`.

### Daemon

- **Gradle Daemon** — долгоживущий процесс JVM, который выполняет сборки. Следующий запуск `gradlew` переиспользует тот же процесс.
- Снижает время старта JVM и ускоряет конфигурацию за счёт кэширования классов и скриптов.
- По умолчанию включён. Отключение: `org.gradle.daemon=false`.

Рекомендуемые настройки в **gradle.properties**:

```properties
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

---

## 14. Gradle и Maven

| Критерий | Maven | Gradle |
|----------|--------|--------|
| **Конфигурация** | XML (pom.xml) | Groovy/Kotlin DSL (build.gradle) |
| **Модель** | Фазы жизненного цикла (жёсткие) | Задачи (гибкий граф) |
| **Гибкость** | Сложные сценарии через плагины/профили | Проще писать кастомные задачи и скрипты |
| **Скорость** | Обычно медленнее на больших проектах | Daemon, кэш, инкрементальность — быстрее |
| **Кривая обучения** | Проще для тех, кто знает только XML | Нужно знать основы Groovy/Kotlin и модели задач |
| **Зависимости** | dependencyManagement, scope | Конфигурации (implementation, api и т.д.) |
| **Экосистема** | Очень большая (Maven Central, плагины) | Большая, растёт (те же репозитории) |

Кратко: Maven — конвенция и предсказуемый жизненный цикл; Gradle — гибкость, скорость и современный DSL.

---

## 15. Gradle в проектах Spring Boot

### Плагин Spring Boot для Gradle

- **org.springframework.boot** — добавляет задачи `bootJar` (сборка executable JAR) и `bootRun` (запуск приложения).
- При применении плагина задача `jar` по умолчанию не создаёт обычный JAR; за это отвечает `bootJar`. При необходимости можно оставить и `jar` для библиотеки.

### Типичные зависимости

- `spring-boot-starter-web` — веб-приложение.
- `spring-boot-starter-test` — тесты (JUnit 5, Mockito, AssertJ и др.).
- При использовании Spring Boot BOM версии часто задаёт плагин или родительский POM; в чистом Gradle можно использовать **dependency management plugin** или указывать версии вручную.

### Пример build.gradle (Groovy)

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.0'
}

group = 'com.example'
version = '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}

tasks.named('test') {
    useJUnitPlatform()
}
```

### Пример build.gradle.kts (Kotlin DSL)

```kotlin
plugins {
    java
    id("org.springframework.boot") version "3.2.0"
}

group = "com.example"
version = "1.0-SNAPSHOT"

repositories {
    mavenCentral()
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    testImplementation("org.springframework.boot:spring-boot-starter-test")
}

java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}

tasks.named<Test>("test") {
    useJUnitPlatform()
}
```

Запуск приложения: `./gradlew bootRun`. Сборка JAR: `./gradlew bootJar` (результат в `build/libs/`).

---

## 16. Частые команды Gradle

| Команда | Описание |
|---------|----------|
| `gradle build` | Полная сборка: компиляция, тесты, упаковка (jar/bootJar и т.д.) |
| `gradle clean` | Удаление каталога `build/` |
| `gradle test` | Запуск тестов |
| `gradle dependencies` | Дерево зависимостей по конфигурациям (по умолчанию — корневой проект) |
| `gradle tasks` | Список доступных задач (с описаниями при `--all`) |
| `gradle tasks --all` | Все задачи, включая служебные |
| `gradle clean build` | Очистка и полная сборка |
| `gradle bootRun` | Запуск Spring Boot приложения (при подключённом Spring Boot plugin) |
| `gradle bootJar` | Сборка executable JAR (Spring Boot) |
| `gradle publishToMavenLocal` | Публикация в локальный Maven-репозиторий (при наличии maven-publish) |

Примеры с флагами:

```bash
./gradlew build -x test          # Сборка без тестов
./gradlew dependencies --configuration runtimeClasspath  # Зависимости для runtime
./gradlew clean build --info    # Подробный лог
./gradlew :module-a:build       # Сборка только модуля module-a
```

### gradle и gradlew

| | gradle | gradlew |
|---|--------|--------|
| **Что это** | Глобальная команда (установленный Gradle в системе) | Скрипт из репозитория проекта (Wrapper) |
| **Версия** | Та, что установлена в системе | Та, что указана в gradle-wrapper.properties |
| **Использование** | `gradle build` | `./gradlew build` или `gradlew.bat build` |
| **Рекомендация** | Для разовых задач, создания wrapper | Для повседневной сборки и CI — всегда использовать wrapper |

В проекте предпочтительно использовать **gradlew**, чтобы все использовали одну и ту же версию Gradle.

---

## 17. Плюсы и минусы Gradle

### Преимущества

- **Производительность** — daemon, инкрементальная сборка, кэш, параллельное выполнение.
- **Гибкость** — произвольные задачи, скрипты на Groovy/Kotlin, тонкая настройка.
- **Краткий DSL** — меньше строк, чем в pom.xml, при сравнимой функциональности.
- **Kotlin DSL** — типизация, хорошая поддержка в IDE.
- **Один инструмент** — Java, Kotlin, Android, C++, JS и др. через плагины.
- **Wrapper** — воспроизводимая сборка без установки Gradle.

### Недостатки

- **Обучение** — нужно понимать Groovy или Kotlin и модель задач.
- **Документация и примеры** — по объёму пока меньше, чем у Maven.
- **Миграция с Maven** — перенос сложных POM и профилей может потребовать времени.
- **Отладка** — сложные скрипты и плагины отлаживать труднее, чем простой XML.

---

## 18. Типичные вопросы на собеседовании

### Что такое Gradle?

Gradle — система автоматизации сборки с открытым исходным кодом. Использует DSL (Groovy или Kotlin) вместо XML, модель на основе задач (tasks) и плагинов. Поддерживает инкрементальную сборку, кэширование и daemon. Широко используется в Java, Kotlin и Android.

### Что такое задачи (tasks) в Gradle?

Task — атомарная единица работы (компиляция, тесты, упаковка и т.д.). У задачи есть имя, действия (doFirst/doLast) и зависимости от других задач. Gradle строит граф задач и выполняет их в нужном порядке; задачи с объявленными входами/выходами могут быть пропущены как UP-TO-DATE при инкрементальной сборке.

### Что такое Gradle Wrapper?

Gradle Wrapper — скрипты `gradlew`/`gradlew.bat` и каталог `gradle/wrapper` с JAR и свойствами. При первом запуске скачивается указанная версия Gradle. Гарантирует одну и ту же версию Gradle у всех разработчиков и в CI без установки Gradle в систему.

### В чём разница между Gradle и Maven?

Gradle использует Groovy/Kotlin DSL и модель задач; Maven — XML и фазы жизненного цикла. Gradle обычно быстрее за счёт daemon, кэша и инкрементальности. В Gradle проще писать кастомные сценарии; в Maven сильнее опора на конвенции и готовые плагины. Оба поддерживают репозитории Maven и управление зависимостями.

### Что такое плагины в Gradle?

Плагины — расширения, которые добавляют в проект задачи, конфигурации зависимостей и конвенции (например, структуру каталогов). Примеры: `java`, `application`, `org.springframework.boot`. Подключаются через `plugins { id '...' }` или `apply plugin: '...'`.

### Что такое конфигурации зависимостей?

Конфигурация определяет, когда зависимость используется (compile, test, runtime) и передаётся ли она потребителям проекта. Основные: **implementation** (компиляция и runtime, не транзитивно), **api** (то же + транзитивно), **compileOnly**, **runtimeOnly**, **testImplementation**. От выбора конфигурации зависит classpath при сборке и тестах и содержимое публикуемого артефакта.

### Чем implementation отличается от api?

**implementation** — зависимость используется в проекте, но не попадает в classpath потребителей (транзитивно скрыта). **api** — зависимость транзитивно видна потребителям. Для библиотек предпочтительно использовать `implementation`, чтобы не «протекать» лишние зависимости; `api` — только для тех библиотек, которые являются частью вашего публичного API.

### Что такое инкрементальная сборка в Gradle?

Задачи объявляют входы (inputs) и выходы (outputs). Если они не изменились с прошлого запуска, задача помечается как UP-TO-DATE и не выполняется. Это ускоряет повторные сборки без полной перекомпиляции и перезапуска тестов.

### Зачем нужен settings.gradle?

Файл `settings.gradle` определяет корень сборки и список подпроектов (`include 'module-a', 'module-b'`). Выполняется до всех `build.gradle`. В мультимодульном проекте без него Gradle не узнает о модулях.

---

*Документ предназначен для подготовки к техническим собеседованиям на позицию Java backend-разработчика. Рекомендуется дополнять его примерами из своих проектов и актуальными версиями Gradle и плагинов.*
