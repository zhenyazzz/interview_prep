# Spring Web (Spring MVC) — руководство для подготовки к собеседованию

Техническое описание Spring MVC: архитектура, жизненный цикл HTTP-запроса и основные компоненты для подготовки к собеседованиям Java Backend.

---

## Table of Contents (оглавление)

- [Введение в Spring Web](#введение-в-spring-web)
- [Что такое Spring MVC](#что-такое-spring-mvc)
- [Архитектура Spring MVC](#архитектура-spring-mvc)
- [DispatcherServlet](#dispatcherservlet)
- [Жизненный цикл HTTP запроса](#жизненный-цикл-http-запроса)
- [HandlerMapping](#handlermapping)
- [HandlerAdapter](#handleradapter)
- [Controllers](#controllers)
- [@RequestMapping](#requestmapping)
- [REST Controllers](#rest-controllers)
- [Request Parameters](#request-parameters)
- [Request Body](#request-body)
- [Response Body](#response-body)
- [Data Binding](#data-binding)
- [Message Converters](#message-converters)
- [Validation](#validation)
- [Exception Handling](#exception-handling)
- [Interceptors](#interceptors)
- [Filters](#filters)
- [View Resolution](#view-resolution)
- [Краткий конспект для собеседования](#краткий-конспект-для-собеседования)

---

# Введение в Spring Web

## Что такое Spring Web

**Spring Web** — модуль Spring Framework для построения веб-приложений. Его ядро — **Spring MVC** (Model-View-Controller): фреймворк для обработки HTTP-запросов, маппинга на методы контроллеров, привязки параметров и тела запроса, сериализации ответа (HTML, JSON, XML) и разрешения представлений (View).

## Какие задачи он решает

- **Маппинг URL → метод контроллера** — по пути, HTTP-методу, заголовкам и параметрам.
- **Привязка данных** — параметры запроса, путь, заголовки и тело (JSON/XML) к аргументам метода.
- **Валидация** — интеграция с Bean Validation (@Valid).
- **Обработка ошибок** — централизованная обработка исключений (@ControllerAdvice, @ExceptionHandler).
- **Расширяемость** — интерцепторы, фильтры, кастомные конвертеры и резолверы аргументов.

## Где используется Spring MVC

- Классические веб-приложения с серверным рендерингом (JSP, Thymeleaf, FreeMarker) — Model + ViewResolver.
- **REST API** — @RestController, JSON/XML через HttpMessageConverter; типичный выбор для Java backend.
- Гибрид: часть эндпоинтов возвращает HTML, часть — JSON. Spring Boot подключает Spring MVC через `spring-boot-starter-web`.

---

# Что такое Spring MVC

## Паттерн MVC

**MVC (Model-View-Controller)** — разделение ответственности:

| Часть | Назначение |
|-------|-------------|
| **Model** | Данные для отображения и бизнес-состояние. В Spring MVC — объекты в Model/ModelMap или атрибуты запроса. |
| **View** | Представление: как отобразить данные (HTML-шаблон, JSON). ViewResolver выбирает представление по имени. |
| **Controller** | Обрабатывает запрос: вызывает сервисы, кладёт данные в Model, возвращает имя View или объект (для REST — данные напрямую). |

## Как Spring реализует MVC

- **Controller** — класс с @Controller или @RestController; методы помечены @RequestMapping (или @GetMapping и т.д.). Один метод = один (или несколько) URL + HTTP-метод.
- **Model** — передаётся в метод как аргумент (Model, ModelMap, Map); метод заполняет его данными. Для REST часто модель не нужна — возвращается объект.
- **View** — для классического MVC метод возвращает String (имя view); ViewResolver по имени находит шаблон (JSP, Thymeleaf). Для REST метод возвращает объект — он сериализуется в JSON/XML (View в смысле «представление ответа»).

Spring MVC не навязывает жёсткую MVC-схему: один и тот же механизм (DispatcherServlet, HandlerMapping, HandlerAdapter) обслуживает и HTML-страницы, и REST API.

---

# Архитектура Spring MVC

## Основные компоненты

| Компонент | Назначение |
|-----------|------------|
| **DispatcherServlet** | Front Controller: принимает все запросы, делегирует поиск обработчика и вызов, отправляет ответ клиенту. |
| **HandlerMapping** | По запросу (URL, метод, заголовки) определяет, какой **handler** (контроллер + метод) должен обработать запрос. |
| **HandlerAdapter** | Вызывает найденный handler: подставляет аргументы (из запроса, тела, сессии и т.д.) и вызывает метод контроллера. |
| **Controller (Handler)** | Класс с методами, обрабатывающими запросы; возвращает ModelAndView, имя view, или объект (для REST). |
| **ViewResolver** | По имени view (String) находит реализацию View (шаблон, JSP) и рендерит результат. Для REST часто не используется — ответ пишется через HttpMessageConverter. |

## Архитектурная схема

```
                    HTTP Request
                         │
                         ▼
              ┌──────────────────────┐
              │  DispatcherServlet   │  ◄── Front Controller
              └──────────┬───────────┘
                         │
         ┌───────────────┼───────────────┐
         ▼               ▼               ▼
  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
  │HandlerMapping│ │HandlerAdapter│ │ViewResolver │
  │ (кто обработает?)│ │ (вызов метода) │ │ (какой view) │
  └──────┬──────┘ └──────┬──────┘ └──────┬──────┘
         │               │               │
         ▼               ▼               ▼
  Controller + method   Controller    View (HTML/JSON)
         │               возвращает      │
         └───────────────┬────────────────┘
                         │
                         ▼
                    HTTP Response
```

---

# DispatcherServlet

## Что такое DispatcherServlet

**DispatcherServlet** — единственная точка входа для HTTP-запросов в Spring MVC. Это сервлет (наследник HttpServlet), зарегистрированный по пути `/` (или по context-path). Все запросы к приложению проходят через него.

## Почему он называется Front Controller

Паттерн **Front Controller**: один сервлет принимает все запросы и делегирует их внутренним компонентам (HandlerMapping, HandlerAdapter, ViewResolver). Клиент общается только с одним «фасадом»; маршрутизация и выбор представления скрыты внутри.

## Роль в обработке запросов

1. Принимает запрос (doGet, doPost, doPut и т.д. → общий processRequest).
2. Обращается к **HandlerMapping** — получает handler (контроллер + метод) для данного запроса.
3. Обращается к **HandlerAdapter** — адаптер вызывает handler, передаёт ему аргументы (из запроса) и получает результат (ModelAndView или объект).
4. Если результат — ModelAndView или имя view, обращается к **ViewResolver** и рендерит view; если объект (REST) — сериализует его через **HttpMessageConverter** и пишет в тело ответа.
5. Возвращает ответ клиенту (заголовки и тело уже записаны в HttpServletResponse).

DispatcherServlet не содержит бизнес-логики; он только координирует вызовы компонентов Spring MVC.

---

# Жизненный цикл HTTP запроса

## Последовательность шагов

1. **Клиент отправляет HTTP-запрос** — например, `GET /api/users/1` или `POST /api/orders` с телом JSON.
2. **Запрос попадает в DispatcherServlet** — контейнер сервлетов (Tomcat/Jetty) направляет запрос в сервлет по маппингу (обычно `/`). Вызывается метод сервлета (service → doGet/doPost/…).
3. **DispatcherServlet обращается к HandlerMapping** — передаёт текущий HttpServletRequest. HandlerMapping ищет зарегистрированные маппинги (@RequestMapping и т.д.) и возвращает **HandlerExecutionChain** (handler + список интерцепторов) или null.
4. **Определяется нужный Controller** — handler — это обычно экземпляр контроллера и метод (HandlerMethod). Цепочка интерцепторов выполняется (preHandle).
5. **HandlerAdapter вызывает Controller** — адаптер (RequestMappingHandlerAdapter) разрешает аргументы метода: @RequestParam, @PathVariable, @RequestBody, Model и т.д. Вызывает метод контроллера и получает возвращаемое значение.
6. **Controller обрабатывает запрос** — выполняет бизнес-логику (часто через сервисы), возвращает объект, имя view или ModelAndView.
7. **Формируется ответ** — если возвращён объект: выбирается HttpMessageConverter (по типу и Content-Type), объект сериализуется в JSON/XML и записывается в тело ответа. Если возвращено имя view: ViewResolver находит View, модель передаётся во view, рендерится HTML.
8. **DispatcherServlet возвращает HTTP response** — интерцепторы postHandle и afterCompletion; ответ уже отправлен клиенту (commit). Управление возвращается контейнеру сервлетов.

## Схема обработки запроса

```
  [Клиент]  ─── HTTP Request ───►  [DispatcherServlet]
                                        │
                     ┌──────────────────┼──────────────────┐
                     ▼                  ▼                  ▼
              HandlerMapping     HandlerAdapter      ViewResolver
              (handler + chain)  (resolve args,      (view name → View)
                     │           invoke method)            │
                     │                  │                  │
                     └────────► Controller ◄──────────────┘
                                (return value)
                                        │
                          ┌─────────────┴─────────────┐
                          ▼                             ▼
                   Object (REST)                 View name (MVC)
                   → MessageConverter             → View.render()
                   → write body                   → write HTML
                          │                             │
                          └─────────────┬───────────────┘
                                         ▼
                              [HTTP Response to Client]
```

---

# HandlerMapping

## Что делает HandlerMapping

**HandlerMapping** — интерфейс: по запросу (HttpServletRequest) возвращает **handler** (объект, который может обработать запрос) и опционально список **HandlerInterceptor**. DispatcherServlet запрашивает у зарегистрированных HandlerMapping handler для запроса; первый не-null результат используется. Handler обычно — это метод контроллера (HandlerMethod), обёрнутый в цепочку с интерцепторами (HandlerExecutionChain).

## Как происходит поиск controller

- Сопоставление по **URL path** (например, `/api/users`), **HTTP method** (GET, POST), **параметрам** (params), **заголовкам** (headers), **consumes** (Content-Type запроса), **produces** (Accept).
- Реализация по умолчанию для аннотированных контроллеров — **RequestMappingHandlerMapping**: при поднятии контекста сканирует бины с @Controller, собирает с них @RequestMapping (и производные) и строит маппинг «запрос → HandlerMethod». При запросе по этим правилам находится подходящий метод (или 404 при отсутствии маппинга).

## RequestMappingHandlerMapping

**RequestMappingHandlerMapping** — создаёт маппинг для методов, помеченных @RequestMapping, @GetMapping, @PostMapping и т.д. Хранит зарегистрированные пути и условия; при запросе перебирает их (с учётом порядка и специфичности) и возвращает HandlerMethod + HandlerExecutionChain с интерцепторами. Регистрируется автоматически при использовании @EnableWebMvc или spring-boot-starter-web.

---

# HandlerAdapter

## Зачем нужен HandlerAdapter

**Handler** после HandlerMapping — абстрактный объект (например, HandlerMethod). Контейнер сервлетов и DispatcherServlet не знают, как вызвать метод с аргументами из запроса. **HandlerAdapter** инкапсулирует это: он умеет для данного типа handler разрешить аргументы (через ArgumentResolver), вызвать метод и обработать возвращаемое значение (через ReturnValueHandler). DispatcherServlet спрашивает: «поддерживаете ли вы этот handler?» — и делегирует вызов подходящему адаптеру.

## Как Spring вызывает методы контроллера

**RequestMappingHandlerAdapter** (основная реализация для @RequestMapping):

1. Для каждого аргумента метода подбирает **HandlerMethodArgumentResolver**: для @RequestParam — один резолвер, для @RequestBody — другой, для @PathVariable, Model, Principal и т.д. — свои. Резолвер читает запрос (параметры, путь, заголовки, тело) и возвращает объект для подстановки в аргумент.
2. Вызывает метод контроллера с полученными аргументами.
3. Возвращаемое значение обрабатывает **HandlerMethodReturnValueHandler**: например, если возвращён объект и метод помечен @ResponseBody (или класс — @RestController), выбирается конвертер (например, для JSON) и результат записывается в тело ответа. Если возвращено имя view — формируется ModelAndView для ViewResolver.

Таким образом, адаптер связывает «сырой» HTTP-запрос с Java-методом и ответом.

---

# Controllers

## @Controller

**@Controller** — стереотип компонента для веб-слоя. Класс регистрируется как бин; его методы обрабатывают запросы по маппингу (@RequestMapping и др.). По умолчанию возвращаемое значение метода считается **именем view** (String → ViewResolver). Чтобы вернуть тело ответа напрямую (например, JSON), метод помечают **@ResponseBody**.

## @RestController

**@RestController** = **@Controller** + **@ResponseBody** на уровне класса. Все методы считаются возвращающими **тело ответа** (объект сериализуется в JSON/XML через HttpMessageConverter), а не имя view. Стандартный выбор для REST API.

## Разница между ними

| Аспект | @Controller | @RestController |
|--------|-------------|------------------|
| Возврат по умолчанию | Имя view (String → HTML через ViewResolver) | Тело ответа (объект → JSON/XML через MessageConverter) |
| Типичное использование | Серверный рендеринг (Thymeleaf, JSP) | REST API |
| @ResponseBody | Нужен на каждом методе для «сырого» ответа | Уже включён на уровне класса |

Пример REST:

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    @GetMapping("/{id}")
    public User get(@PathVariable Long id) {
        return userService.findById(id);  // → JSON
    }
}
```

---

# @RequestMapping

## Как работает маппинг HTTP-запросов

**@RequestMapping** задаёт, при каких запросах вызывается метод (или все методы класса). Можно указать путь (path/value), HTTP-метод (method), параметры (params), заголовки (headers), consumes (Content-Type запроса), produces (Accept и тип ответа). Маппинг может быть на класс (общий префикс пути) и на метод (дополнение пути и уточнение метода). Более специфичный маппинг имеет приоритет.

## Параметры аннотации

| Параметр | Назначение |
|----------|------------|
| **value / path** | URL path (например, `/users`, `/users/{id}`). Несколько путей — массив. |
| **method** | HTTP-метод: RequestMethod.GET, POST, PUT, DELETE и т.д. Или массив. |
| **params** | Условие по параметрам запроса (например, `"name"`, `"!sort"`, `"version=1"`). |
| **headers** | Условие по заголовкам (например, `"X-API-Version=2"`, `"!X-Debug"`). |
| **consumes** | Ожидаемый Content-Type запроса (например, `"application/json"`). |
| **produces** | Тип ответа (Accept / Content-Type ответа), например `"application/json"`. |

Сокращения: **@GetMapping**, **@PostMapping**, **@PutMapping**, **@DeleteMapping** — то же самое с фиксированным method.

Пример:

```java
@GetMapping(value = "/{id}", produces = MediaType.APPLICATION_JSON_VALUE)
public User get(@PathVariable Long id) { ... }

@PostMapping(consumes = MediaType.APPLICATION_JSON_VALUE, produces = MediaType.APPLICATION_JSON_VALUE)
public User create(@RequestBody @Valid User user) { ... }
```

---

# REST Controllers

## REST-архитектура (кратко)

**REST** — стиль построения API поверх HTTP: ресурсы идентифицируются URL, операции — HTTP-методами (GET — чтение, POST — создание, PUT/PATCH — обновление, DELETE — удаление). Состояние не хранится на сервере между запросами; типичные форматы обмена — JSON или XML.

## Как Spring реализует REST API

- **@RestController** + **@RequestMapping** (и @GetMapping и т.д.) — маппинг URL и метода на методы Java.
- **@PathVariable**, **@RequestParam** — извлечение данных из пути и query-параметров.
- **@RequestBody** — десериализация тела запроса (JSON → объект) через HttpMessageConverter.
- Возвращаемый объект сериализуется в ответ (JSON/XML) через HttpMessageConverter; статус и заголовки задаются через ResponseEntity или аннотации (@ResponseStatus и др.).
- Обработка ошибок — @ControllerAdvice + @ExceptionHandler для единого формата ошибок (например, JSON).

Типичный стек: Spring MVC + Jackson (для JSON) + опционально валидация (@Valid) и документация (OpenAPI/Swagger).

---

# Request Parameters

## @RequestParam

**@RequestParam** — привязка **query-параметра** (или формы) к аргументу метода. Имя параметра запроса по умолчанию совпадает с именем аргумента; можно задать явно: `@RequestParam("page") int page`. Поддерживаются default: `@RequestParam(defaultValue = "0") int page`, optional: `@RequestParam(required = false) String sort`.

```java
@GetMapping("/search")
public List<User> search(@RequestParam String q, @RequestParam(defaultValue = "10") int size) {
    return userService.search(q, size);
}
```

## @PathVariable

**@PathVariable** — привязка **части URL path** к аргументу. В path задаётся шаблон: `/users/{id}`; значение подставляется в аргумент с именем `id` (или указанным в аннотации).

```java
@GetMapping("/users/{id}")
public User get(@PathVariable Long id) {
    return userService.findById(id);
}
```

Можно задать регулярное выражение: `@PathVariable @Pattern(regexp = "[0-9]+") String id` или в path: `{id:[0-9]+}`.

---

# Request Body

## @RequestBody

**@RequestBody** — тело HTTP-запроса (например, JSON) десериализуется и подставляется в аргумент метода. Тип аргумента определяет, какой **HttpMessageConverter** будет использован: для JSON — обычно MappingJackson2HttpMessageConverter (Jackson), который читает InputStream и создаёт объект. Content-Type запроса должен соответствовать (application/json).

## Как происходит десериализация JSON

1. HandlerAdapter для аргумента с @RequestBody выбирает подходящий HttpMessageConverter (по типу аргумента и Content-Type запроса).
2. Конвертер читает тело запроса (HttpInputMessage), парсит JSON в объект (ObjectMapper.readValue) и возвращает его.
3. Если включена валидация (@Valid), после десериализации выполняется Bean Validation; при ошибках выбрасывается исключение (обрабатывается в @ExceptionHandler или конвертируется в 400).

Пример:

```java
@PostMapping
public User create(@RequestBody @Valid User user) {
    return userService.save(user);
}
```

---

# Response Body

## @ResponseBody

**@ResponseBody** на методе (или на классе в @RestController) означает: возвращаемое значение метода **не** интерпретируется как имя view, а записывается в **тело HTTP-ответа**. Spring выбирает HttpMessageConverter по типу возвращаемого значения и заголовку Accept (или produces). Для объекта и application/json используется Jackson — сериализация в JSON. Можно вернуть ResponseEntity для задания статуса и заголовков.

```java
@GetMapping("/{id}")
@ResponseBody  // избыточно при @RestController
public User get(@PathVariable Long id) {
    return userService.findById(id);
}
```

---

# Data Binding

## Как Spring связывает HTTP с объектами Java

**Data binding** — процесс заполнения Java-объектов данными из запроса.

- **Query-параметры и форма** — по имени параметра связываются с полями или с аргументами @RequestParam. Для **объекта** (POJO) без @RequestParam Spring связывает **имена параметров** с именами полей/сеттеров (например, `user.name`, `user.age` → поля `name`, `age` объекта User). Используется для форм и простых query-структур.
- **Path** — @PathVariable подставляет сегмент пути в аргумент.
- **Body** — @RequestBody: тело запроса десериализуется конвертером (JSON → объект).
- **Заголовки** — @RequestHeader привязывает заголовок к аргументу.

Типы приводятся автоматически (String → int, Long, enum и т.д.). Для кастомной логики используются **Converter** или **Formatter** (регистрируются в WebDataBinder или через конфигурацию MVC).

---

# Message Converters

## Что такое HttpMessageConverter

**HttpMessageConverter** — интерфейс Spring: может ли конвертер работать с данным MIME-типом и типом Java; методы **read** (тело запроса → объект) и **write** (объект → тело ответа). Используется для @RequestBody и @ResponseBody (и для HTTP-методов в RestTemplate/WebClient). Несколько конвертеров регистрируются в HandlerAdapter; при обработке запроса/ответа выбирается подходящий по типу и Content-Type/Accept.

## Как Spring конвертирует JSON

- **MappingJackson2HttpMessageConverter** (при наличии Jackson в classpath) обрабатывает `application/json`.
- **read**: читает InputStream из тела запроса, вызывает ObjectMapper.readValue(type), возвращает объект.
- **write**: принимает объект, вызывает ObjectMapper.writeValue(outputStream, value), пишет в тело ответа и выставляет Content-Type: application/json.

Spring Boot при наличии `spring-boot-starter-web` подключает Jackson и регистрирует этот конвертер; для дат, полей и т.д. настраивается ObjectMapper (через бины или свойства).

---

# Validation

## Bean Validation (JSR 380)

**Bean Validation** — стандарт аннотаций для проверки полей и объектов: @NotNull, @Size, @Min, @Max, @Email, @Pattern и т.д. Реализация — Hibernate Validator. Spring интегрирует валидацию с MVC: при аргументе метода с аннотацией **@Valid** или **@Validated** после привязки данных (в т.ч. после десериализации @RequestBody) вызывается валидатор; при нарушении ограничений выбрасывается MethodArgumentNotValidException (для @RequestBody) или другие исключения биндинга.

## @Valid

**@Valid** — триггер валидации для аргумента метода. Обычно ставится перед @RequestBody или перед объектом с полями, которые нужно проверить (включая вложенные объекты). При ошибках валидации генерируется 400 (Bad Request), если не перехватить исключение в @ExceptionHandler.

## @Validated

**@Validated** — аннотация Spring; используется для включения валидации на уровне **метода** (валидация аргументов с группами или кастомными аннотациями) и на уровне **класса** (валидация при вызове метода). На аргументах методов контроллера чаще используют **@Valid** для Bean Validation; @Validated применяют для групповой валидации или на сервисных бинах.

Пример:

```java
@PostMapping
public User create(@RequestBody @Valid User user) {
    return userService.save(user);
}

public class User {
    @NotBlank
    private String name;
    @Email
    private String email;
    @Min(0) @Max(120)
    private Integer age;
}
```

---

# Exception Handling

## @ControllerAdvice

**@ControllerAdvice** — класс с методами обработки исключений и общими настройками для **всех** (или выбранных) контроллеров. Обычно в нём объявляют методы с **@ExceptionHandler**: при выбросе указанного исключения из любого контроллера вызывается этот метод, и его возвращаемое значение (или ResponseEntity) формирует ответ клиенту. Так достигается единый формат ошибок (например, JSON с полем message и кодом).

## @ExceptionHandler

**@ExceptionHandler** на методе указывает тип исключения (или несколько), которые метод обрабатывает. Метод может принимать исключение, запрос, ответ и т.д.; возвращать объект (сериализуется в JSON), ResponseEntity или имя view. Обработчик может логировать ошибку и возвращать нужный HTTP-статус и тело.

Пример:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ErrorResponse handleValidation(MethodArgumentNotValidException ex) {
        // из ex.getBindingResult() собрать ошибки полей
        return new ErrorResponse("Validation failed", errors);
    }

    @ExceptionHandler(EntityNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ErrorResponse handleNotFound(EntityNotFoundException ex) {
        return new ErrorResponse(ex.getMessage());
    }
}
```

---

# Interceptors

## HandlerInterceptor

**HandlerInterceptor** — интерфейс для перехвата запросов **внутри** DispatcherServlet: после выбора handler, но до и после его вызова. Позволяет выполнить общую логику (проверка прав, логирование, изменение модели) для группы путей без дублирования кода в контроллерах.

## Методы

| Метод | Когда вызывается |
|-------|-------------------|
| **preHandle** | До вызова handler. Возврат false прерывает цепочку (ответ обычно формирует сам интерцептор). |
| **postHandle** | После вызова handler, до рендеринга view. Можно изменить ModelAndView. Не вызывается при @ResponseBody (ответ уже записан). |
| **afterCompletion** | После завершения запроса (после рендеринга view или после записи тела ответа). Вызывается всегда; удобно для очистки ресурсов и логирования. |

Регистрация: реализацию добавляют в конфигурацию MVC (WebMvcConfigurer.addInterceptors), привязывая к путей (path patterns).

Пример:

```java
public class LoggingInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, ...) {
        // логировать запрос
        return true;
    }
    @Override
    public void afterCompletion(HttpServletRequest request, ...) {
        // логировать завершение
    }
}
```

---

# Filters

## Servlet Filters

**Filter** — стандартный сервлетный API (javax.servlet.Filter). Выполняется **до** того, как запрос дойдёт до сервлета (и до DispatcherServlet). Цепочка фильтров: FilterChain; вызов chain.doFilter() передаёт управление следующему фильтру или сервлету. Используется для кодировки, CORS, аутентификации (например, Spring Security как цепочка фильтров), логирования запроса/ответа на низком уровне.

## Отличие Filters от Interceptors

| Критерий | Filter | HandlerInterceptor |
|----------|--------|---------------------|
| Уровень | Сервлетный API; до сервлета. | Spring MVC; внутри DispatcherServlet. |
| Зависимость от Spring | Нет; работает в любом сервлетном приложении. | Да; знает о handler, ModelAndView. |
| Контекст | Только HttpServletRequest/Response. | Есть доступ к handler, ModelAndView, исключениям. |
| Типичное использование | Кодировка, CORS, Security, сжатие, логирование потока. | Логирование запроса в контексте MVC, добавление атрибутов в модель, проверки доступа к handler. |

Фильтры выполняются первыми; затем запрос попадает в DispatcherServlet → HandlerMapping → Interceptors (preHandle) → Handler → Interceptors (postHandle, afterCompletion) → ответ.

---

# View Resolution

## Что делает ViewResolver

**ViewResolver** — по **имени view** (String, возвращённое методом контроллера) возвращает реализацию **View**. View отвечает за рендеринг: записывает результат (HTML, JSON и т.д.) в HttpServletResponse. Для классического MVC метод контроллера возвращает, например, `"users/list"`; ViewResolver (например, ThymeleafViewResolver) находит шаблон по префиксу/суффиксу и рендерит его с переданной моделью.

## Как Spring возвращает HTML или JSON

- **HTML**: метод возвращает имя view (String); ViewResolver (InternalResourceViewResolver, ThymeleafViewResolver и т.д.) находит шаблон; View рендерит HTML в ответ. Модель передаётся во view как атрибуты.
- **JSON**: метод возвращает объект и помечен @ResponseBody (или класс — @RestController). ViewResolver не используется; HandlerAdapter записывает ответ через **HttpMessageConverter** (Jackson). Тот же механизм для XML и других форматов.

Итого: один путь — через ViewResolver и View (HTML, шаблоны); второй — через MessageConverter (REST, JSON/XML).

---

# Краткий конспект для собеседования

## DispatcherServlet

Единственная точка входа (Front Controller): принимает все запросы, обращается к HandlerMapping за handler, к HandlerAdapter за вызовом метода контроллера, к ViewResolver или MessageConverter для формирования ответа. Сервлет, маппинг обычно `/`.

## Жизненный цикл HTTP-запроса

Запрос → DispatcherServlet → HandlerMapping (handler + chain) → HandlerAdapter (разрешение аргументов, вызов метода) → Controller возвращает значение → если объект: MessageConverter пишет в ответ; если имя view: ViewResolver + View рендерят HTML → ответ клиенту.

## HandlerMapping

Определяет, какой handler (контроллер + метод) обработает запрос по URL, методу, параметрам, заголовкам. RequestMappingHandlerMapping строит маппинг по @RequestMapping на методах контроллеров.

## HandlerAdapter

Вызывает handler: подставляет аргументы (через ArgumentResolver для @RequestParam, @RequestBody и т.д.), вызывает метод, обрабатывает возвращаемое значение (ReturnValueHandler, в т.ч. запись в ответ через MessageConverter). RequestMappingHandlerAdapter — для @RequestMapping-методов.

## REST Controllers

@RestController = @Controller + @ResponseBody. Маппинг URL через @RequestMapping/@GetMapping и т.д.; @PathVariable, @RequestParam — параметры; @RequestBody — тело (JSON); возвращаемый объект сериализуется в JSON/XML через HttpMessageConverter.

## Message Converters

HttpMessageConverter читает тело запроса (read) и пишет тело ответа (write). Для JSON — MappingJackson2HttpMessageConverter (Jackson). Используются для @RequestBody и @ResponseBody при выборе по типу и Content-Type/Accept.

---

*Документ предназначен для подготовки к техническим собеседованиям Java Backend. Рекомендуется дополнять материалами по Spring Core, Spring Boot и практикой с REST API.*
