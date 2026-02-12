### Содержание

- [Вопрос 1. How to create a thread?](#вопрос-1-how-to-create-a-thread)
- [Вопрос 2. What is the difference between primitive and reference data types?](#вопрос-2-what-is-the-difference-between-primitive-and-reference-data-types)
- [Вопрос 3. What can the final keyword be applied to?](#вопрос-3-what-can-the-final-keyword-be-applied-to)
- [Вопрос 4. What is static and why is it needed?](#вопрос-4-what-is-static-and-why-is-it-needed)
- [Вопрос 5. What is a functional interface? Name the popular ones](#вопрос-5-what-is-a-functional-interface-name-the-popular-ones)
- [Вопрос 6. What is a terminal function?](#вопрос-6-what-is-a-terminal-function)
- [Вопрос 7. Map vs flatMap stream API](#вопрос-7-map-vs-flatmap-stream-api)
- [Вопрос 8. OOP principles](#вопрос-8-oop-principles)
- [Вопрос 9. Contract equals and hashcode](#вопрос-9-contract-equals-and-hashcode)
- [Вопрос 10. How does inheritance work in java?](#вопрос-10-how-does-inheritance-work-in-java)
- [Вопрос 11. What are generic types and why are they needed?](#вопрос-11-what-are-generic-types-and-why-are-they-are-needed)
- [Вопрос 12. Methods of the Object class. Name them and tell why they are needed](#вопрос-12-methods-of-the-object-class-name-them-and-tell-why-they-are-needed)
- [Вопрос 13. The most popular GCs and general algorithm of work](#вопрос-13-the-most-popular-gcs-and-general-algorithm-of-work)
- [Вопрос 14. What is try-with-resources and why is it needed?](#вопрос-14-what-is-try-with-resources-and-why-is-it-needed)
- [Вопрос 15. Int pool and String pool. What is it and why is it necessary?](#вопрос-15-int-pool-and-string-pool-what-is-it-and-why-is-it-necessary)
- [Вопрос 16. Stack and Heap, what is stored and where](#вопрос-16-stack-and-heap-what-is-stored-and-where)
- [Вопрос 17. Popular thread-safe collections. Name and tell how they work](#вопрос-17-popular-thread-safe-collections-name-and-tell-how-they-work)
- [Вопрос 18. HashMap algorithm](#вопрос-18-hashmap-algorithm)
- [Вопрос 19. Differences between ArrayList and LinkedList. What and when works faster.](#вопрос-19-differences-between-arraylist-and-linkedlist-what-and-when-works-faster)
- [Вопрос 20. How do TreeMap and TreeSet work?](#вопрос-20-how-do-treemap-and-treeset-work)
- [Вопрос 21. Hierarchy of collections. Tell us what it looks like and name the implementations](#вопрос-21-hierarchy-of-collections-tell-us-what-it-looks-like-and-name-the-implementations)
- [Вопрос 22. Exception hierarchy. Checked and Unchecked exceptions](#вопрос-22-exception-hierarchy-checked-and-unchecked-exceptions)
- [Вопрос 23. What are wrapper classes and why are they needed?](#вопрос-23-what-are-wrapper-classes-and-why-are-they-needed)
- [Вопрос 24. Immutable objects. Examples in Java. How to create them?](#вопрос-24-immutable-objects-examples-in-java-how-to-create-them)
- [Вопрос 25. What is serialization in Java? What do you need to do to serialize an object? What is serialVersionUID?](#вопрос-25-what-is-serialization-in-java-what-do-you-need-to-do-to-serialize-an-object-what-is-serialversionuid)
- [Вопрос 26. What is the volatile keyword for?](#вопрос-26-what-is-the-volatile-keyword-for)
- [Вопрос 27. Is an array an object or a primitive?](#вопрос-27-is-an-array-an-object-or-a-primitive)
- [Вопрос 28. Thread.sleep vs Thread.yield разница](#вопрос-28-threadsleep-vs-threadyield-разница)
- [Вопрос 29. How do the wait, notify, notifyAll methods work?](#вопрос-29-how-do-the-wait-notify-notifyall-methods-work)
- [Вопрос 30. How does the Thread.join() method work?](#вопрос-30-how-does-the-threadjoin-method-work)
- [Вопрос 31. What is AtomicInteger and how does it work?](#вопрос-31-what-is-atomicinteger-and-how-does-it-work)
- [Вопрос 32. Why is the transient keyword needed?](#вопрос-32-why-is-the-transient-keyword-needed)
- [Вопрос 33. What are deadlock and livelock?](#вопрос-33-what-are-deadlock-and-livelock)
- [Вопрос 34. Methods for synchronizing access to shared resources between threads. Name them and name the advantages and disadvantages](#вопрос-34-methods-for-synchronizing-access-to-shared-resources-between-threads-name-them-and-name-the-advantages-and-disadvantages)
- [Вопрос 35. What is the synchronized keyword used for and what are its features?](#вопрос-35-what-is-the-synchronized-keyword-used-for-and-what-are-its-features)

---

### Вопрос 1. How to create a thread?

#### 1. Что такое поток (Thread) в Java

- **Поток** — это отдельная линия выполнения внутри одного процесса JVM.
- Все потоки одного процесса **делят общую память (heap)**, но выполняются независимо.
- Потоки позволяют:
  - **Параллельно** выполнять несколько задач (на многопроцессорных системах — реально параллельно).
  - Не блокировать основной поток (например, UI или основной поток сервера).
- Минусы:
  - Сложность синхронизации (race condition, deadlock и т.д.).
  - Переключение контекста между потоками тоже потребляет ресурсы.

На собеседовании важно показать, что ты понимаешь не только “как создать поток”, но и **зачем** и какие есть риски.

---

#### 2. Основные способы создать поток в Java

Кратко по вариантам:
- **Способ 1 — наследоваться от `Thread`**: учебный, в проде почти не используют.
- **Способ 2 — реализовать `Runnable` и передать в `new Thread(...)`**: базовый, нормальный способ.
- **Способ 3 — `Callable` + `Future`/`FutureTask`**: когда нужен результат/ошибка из потока.
- **Способ 4 — пул потоков через `ExecutorService`**: стандарт в реальных приложениях.

---

##### Способ 1: Наследование от `Thread`

Идея: создать свой класс, наследоваться от `Thread` и переопределить метод `run()`.

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Hello from MyThread: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        Thread t = new MyThread(); // создание потока
        t.start();                 // запуск потока (НЕ вызывать run() напрямую)
    }
}
```

**Минусы**:
- Класс уже наследуется от `Thread`, ты не можешь наследоваться от другого класса.
- Логика задачи жёстко связана с механизмом потока.

Использовать можно для простых демо, но **в продакшене почти не применяют**.

---

##### Способ 2: Реализация `Runnable` (классический и рекомендованный)

Идея: выносишь логику в отдельный объект (`Runnable`), а `Thread` — просто оболочка, которая её запускает.

```java
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Hello from Runnable: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        Runnable task = new MyTask();
        Thread t = new Thread(task); // создаём поток с задачей
        t.start();                   // запускаем поток
    }
}
```

С Java 8 обычно используют лямбды:

```java
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("Hello from lambda: " + Thread.currentThread().getName());
        });
        t.start();
    }
}
```

**Плюсы**:
- Логика задачи отделена от механизма потока.
- Можно переиспользовать один и тот же `Runnable` в разных потоках.
- Класс всё ещё может наследоваться от другого класса.

На собесе хорошо звучит фраза:  
**"В обычном коде я предпочитаю использовать `Runnable`, а не наследоваться от `Thread`."**

---

##### Способ 3: `Callable` + `Future` (если нужен результат)

`Runnable` не может вернуть значение и бросать checked-исключения.  
Если нужно **получить результат из потока** или обработать checked-ошибки — используют `Callable<V>`.

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class Main {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Callable<Integer> task = () -> {
            // какая-то работа
            Thread.sleep(500);
            return 42;
        };

        FutureTask<Integer> futureTask = new FutureTask<>(task);
        Thread t = new Thread(futureTask); // создаём поток с задачей
        t.start();                         // запускаем поток

        Integer result = futureTask.get(); // блокирующее ожидание результата
        System.out.println("Result = " + result);
    }
}
```

**Ключевая мысль для собеса**:  
**"`Callable` + `Future` позволяют запускать задачу в отдельном потоке и безопасно получить результат/ошибку."**

---

##### Способ 4: Пул потоков (`ExecutorService`) — правильный путь в проде

В реальных системах **не создают потоки руками каждый раз** через `new Thread(...)`.  
Вместо этого используют **пулы потоков** (`thread pool`) через `ExecutorService`:

- Ограничивают количество одновременно работающих потоков.
- Переиспользуют потоки, не создавая их бесконечно.
- Управляют жизненным циклом и очередью задач.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Main {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(4); // пул из 4 потоков

        Runnable task = () -> {
            System.out.println("Task in pool: " + Thread.currentThread().getName());
        };

        executor.submit(task); // отправляем задачу в пул

        executor.shutdown();   // инициируем корректное завершение пула
    }
}
```

**Так можно отвечать на собесе в одну фразу**:

- "В Java поток можно создать разными способами: унаследоваться от `Thread`, передать задачу как `Runnable` в `new Thread(...)`, использовать `Callable` + `Future`, но в реальных проектах обычно работают через пулы потоков (`ExecutorService`), чтобы контролировать количество потоков и экономно расходовать ресурсы."

### Вопрос 2. What is the difference between primitive and reference data types?

#### 1. Какие есть примитивные типы в Java

В Java **8 примитивных типов**:
- **Целые**: `byte`, `short`, `int`, `long`
- **Вещественные**: `float`, `double`
- **Символьный**: `char`
- **Логический**: `boolean`

Характеристики:
- Хранят **простое значение** (число, символ, флаг).
- Имеют **фиксированный размер** (например, `int` — 32 бита).
- Не являются объектами, у них **нет методов**.
- Не могут быть `null` (кроме случая автоупаковки/распаковки в обёртки).

Примеры:

```java
int x = 10;
double price = 19.99;
char letter = 'A';
boolean isActive = true;
```

---

#### 2. Какие есть ссылочные (reference) типы

**Ссылочный тип** — это всё, что **не примитив**:
- Классы: `String`, `Object`, любые твои классы (`User`, `Order` и т.д.).
- Интерфейсы.
- Массивы: `int[]`, `String[]`.
- Enum, записи (record), и т.п.

Характеристики:
- Переменная хранит **не само значение, а ссылку** (адрес) на объект в heap.
- Могут быть `null`.
- У них есть **методы, поля, поведение**.

Примеры:

```java
String name = "John";      // ссылочный тип
Integer age = 25;          // ссылочный тип (обёртка над int)
int[] numbers = {1, 2, 3}; // ссылочный тип (массив)
User user = new User();    // ссылочный тип (пользовательский класс)
```

---

#### 3. Wrapper-классы (классы-обёртки для примитивов)

Для каждого примитива есть **класс-обёртка**:
- `byte` → `Byte`
- `short` → `Short`
- `int` → `Integer`
- `long` → `Long`
- `float` → `Float`
- `double` → `Double`
- `char` → `Character`
- `boolean` → `Boolean`

Зачем нужны:
- Для работы с **коллекциями** (`List`, `Map` и т.д.), которые могут хранить только **объекты**, а не примитивы.
- Для представления **отсутствия значения** через `null` (например, `Integer` вместо `int`, когда "нет возраста").
- Для дополнительных **утилитных методов** (`Integer.parseInt`, `Double.valueOf` и т.д.).

Пример автоупаковки (autoboxing) и автораспаковки (unboxing):

```java
int x = 10;
Integer y = x;   // autoboxing: int -> Integer
int z = y;       // unboxing: Integer -> int
```

Важно: чрезмерный autoboxing/unboxing даёт лишние объекты и может быть дорогим в горячем коде.

---

#### 4. Ключевые отличия primitive vs reference

**1) Что хранит переменная**
- Примитив: **само значение** (10, true, 'A').
- Ссылочный: **ссылку на объект** в heap.

**2) Null**
- Примитив: не может быть `null`.
- Ссылочный: может быть `null` → `NullPointerException`, если дернуть метод.

**3) Размер и производительность**
- Примитивы:
  - Фиксированный размер.
  - Хранятся компактно (например, в массивах).
  - Обычно быстрее, меньше нагрузка на GC.
- Ссылочные:
  - Дополнительный overhead на объект, ссылку, работу GC.

**4) Поведение при присваивании**

```java
int a = 10;
int b = a;   // копируется само значение
b = 20;      // a остаётся 10

User u1 = new User();
User u2 = u1; // копируется ссылка на тот же объект
u2.name = "Bob"; // меняем тот же объект, u1 тоже "видит" Bob
```

**5) Использование в коллекциях**
- Коллекции (`List`, `Set`, `Map`) не могут работать с примитивами напрямую — только с объектами.
- Поэтому в коллекциях используют `Integer`, `Long`, `Boolean` и т.п.

---

#### 5. Как красиво ответить на собесе

- "В Java есть два семейства типов: **примитивные** и **ссылочные**.  
  Примитивы (`int`, `double`, `boolean` и т.д.) хранят **само значение**, имеют фиксированный размер и не могут быть `null`.  
  Ссылочные типы (`String`, любые классы, массивы, wrapper-классы) хранят **ссылку на объект в heap**, могут быть `null` и иметь методы/состояние."
- "Для каждого примитива есть **wrapper-класс** (`Integer`, `Long`, `Boolean` и т.д.), который используется в коллекциях и когда нужно представить отсутствие значения через `null`."

### Вопрос 3. What can the final keyword be applied to?

#### 1. Что такое `final` в целом

`final` — это модификатор, который означает в общем смысле **"нельзя изменить дальше"**:
- для переменной — **нельзя переназначить** ссылку / значение;
- для метода — **нельзя переопределить** в подклассе;
- для класса — **нельзя наследоваться** от этого класса.

Важно:  
**`final` делает неизменяемой ссылку/определение, но не обязательно внутреннее состояние объекта.**

---

#### 2. Где может применяться `final`

`final` в Java можно применять к:

- **локальным переменным** (внутри метода, блока):
  - нельзя повторно присвоить значение после инициализации.
- **параметрам метода / конструктора**:
  - нельзя переприсвоить параметр внутри метода.
- **полям класса**:
  - после установки значения (обычно в конструкторе) поле менять нельзя.
- **методам**:
  - метод нельзя переопределить в наследнике.
- **классам**:
  - от класса нельзя наследоваться.

(К пакету/интерфейсу `final` не применяется.)

---

#### 3. `final` для переменных и полей

**3.1. Локальные переменные**

```java
void example() {
    final int x = 10;
    // x = 20; // ошибка компиляции: cannot assign a value to final variable 'x'
}
```

**3.2. Параметры методов**

```java
void process(final String name) {
    // name = "Other"; // нельзя переназначить
    System.out.println(name);
}
```

**3.3. Поля класса (часто для иммутабельных объектов)**

```java
class User {
    private final String name;
    private final int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // нет сеттеров → объект становится по сути неизменяемым
}
```

**Важный момент для ссылок:**

```java
final List<String> list = new ArrayList<>();
list.add("A");  // ОК — меняем содержимое списка
// list = new ArrayList<>(); // НЕЛЬЗЯ — переназначить ссылку
```

`final` запрещает менять **ссылку**, но не сам объект, на который она указывает (если только объект сам не сделан иммутабельным).

---

#### 4. `final` для методов

Если метод объявлен как `final`, его **нельзя переопределить** в наследнике.

```java
class Parent {
    public final void doWork() {
        System.out.println("Parent work");
    }
}

class Child extends Parent {
    // @Override
    // public void doWork() { } // так делать НЕЛЬЗЯ — компилятор выдаст ошибку
}
```

Зачем так делают:
- чтобы **зафиксировать поведение** метода и не дать наследникам его менять;
- иногда — для безопасности или чтобы не ломали инварианты класса.

---

#### 5. `final` для классов

Класс, объявленный как `final`, **нельзя наследовать**:

```java
public final class String {
    // ...
}

// class MyString extends String { } // так нельзя — компилятор выдаст ошибку
```

Зачем:
- чтобы никто не мог **переопределить поведение** базового класса;
- упростить модель безопасности и инварианты;
- пример из JDK: `String`, `Integer`, `Long`, `LocalDate` и многие другие — `final`.

---

#### 6. Дополнительные нюансы `final`

- **Blank final**: поле `final` без значения при объявлении, но оно **обязательно** должно быть проинициализировано в **каждом конструкторе**.

```java
class Config {
    private final String url;

    public Config(String url) {
        this.url = url; // обязательно присвоить
    }
}
```

- **effectively final**: локальная переменная без ключевого слова `final`, но которая фактически не меняется.  
  Такая переменная может использоваться в лямбдах и анонимных классах как будто она `final`.

```java
void test() {
    int x = 10; // effectively final, если дальше не меняется
    Runnable r = () -> System.out.println(x);
}
```

---

#### 7. Как кратко ответить на собесе

- "Ключевое слово `final` в Java можно применять к **переменным, полям, параметрам, методам и классам**.  
  Для переменных/полей оно запрещает повторное присваивание, для методов — переопределение в наследниках, а для классов — наследование.  
  При этом `final` делает неизменяемой **ссылку или определение**, но не обязательно внутреннее состояние объекта, если сам объект не иммутабелен."

---

### Вопрос 4. What is static and why is it needed?

#### 1. Общая идея `static`

`static` в Java означает **"принадлежит классу, а не объекту"**:
- статическое поле — одно общее значение на весь класс, а не копия на каждый объект;
- статический метод — его можно вызывать без экземпляра (`ClassName.method()`), он не привязан к конкретному объекту;
- статический вложенный класс — не хранит ссылку на внешний объект.

`static` не про "один раз в памяти навсегда", а именно про **классовый уровень**, а не объектный.

---

#### 2. Где можно использовать `static`

`static` можно применить к:
- **полям (переменным класса)**;
- **методам**;
- **вложенным классам** (static nested classes);
- **инициализационным блокам** (static blocks).

Нельзя писать `static` для:
- локальных переменных внутри методов;
- самих классов верхнего уровня (только вложенные могут быть `static`).

---

#### 3. Статические поля (static fields)

Используются, когда значение **общее для всех объектов** класса:

```java
class User {
    private static int count = 0; // общее поле-счётчик для всех пользователей

    public User() {
        count++;
    }

    public static int getCount() {
        return count;
    }
}

public class Main {
    public static void main(String[] args) {
        new User();
        new User();
        System.out.println(User.getCount()); // 2
    }
}
```

Типичные примеры:
- счётчики, константы, кэш на уровне класса.

---

#### 4. Статические методы (static methods)

Статический метод не зависит от конкретного объекта:

```java
class MathUtils {
    public static int sum(int a, int b) {
        return a + b;
    }
}

int r = MathUtils.sum(2, 3); // вызов без объекта
```

Характеристики:
- нельзя обращаться к нестатическим полям/методам напрямую (нет `this`);
- обычно это **утилитные** или **фабричные** методы (`of`, `valueOf` и т.д.).

Примеры из JDK:
- `Math.abs`, `Collections.sort`, `Integer.parseInt` — всё это статические методы.

---

#### 5. Статические блоки и статические константы

**Статический блок** — код, который выполняется один раз при загрузке класса:

```java
class Config {
    public static final String DEFAULT_URL;

    static {
        // сложная инициализация
        DEFAULT_URL = "https://example.com";
        System.out.println("Class loaded");
    }
}
```

Используется:
- для сложной инициализации статических полей;
- для логирования факта загрузки класса и т.п.

Константы обычно объявляют как:

```java
public static final int MAX_SIZE = 100;
```

---

#### 6. Статические вложенные классы

```java
class Outer {
    static class Inner {
        void print() {
            System.out.println("Static nested class");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Outer.Inner inner = new Outer.Inner(); // без экземпляра Outer
        inner.print();
    }
}
```

Отличие от нестатических внутренних классов:
- `static`-класс **не хранит ссылку на экземпляр внешнего класса**;
- его можно использовать как обычный класс, просто с неймингом `Outer.Inner`.

---

#### 7. Зачем вообще нужен `static` (кратко)

- **Общее состояние** для всех объектов (счётчики, конфигурации, кэш).
- **Утилитные методы**, которые не привязаны к объекту (`parseInt`, `sort`, математика).
- **Константы** (`public static final`).
- **Статические вложенные классы** для логической группировки без ссылки на внешний объект.

Идеальная фраза:
- "Ключевое слово `static` поднимает поле/метод/класс на уровень **класса**, а не экземпляра, и используется для общих данных, утилитных методов и вложенных классов без связи с объектом."

---

### Вопрос 5. What is a functional interface? Name the popular ones

#### 1. Определение функционального интерфейса

**Функциональный интерфейс** — это интерфейс, у которого **ровно один абстрактный метод**.

Пример:

```java
@FunctionalInterface
interface MyFunction {
    int apply(int x);
}
```

Ключевые моменты:
- аннотация `@FunctionalInterface` **не обязательна**, но помогает компилятору и человеку;
- могут быть:
  - `default`-методы;
  - `static`-методы;
  - методы из `Object` (`toString`, `equals`, `hashCode`);
  — они **не считаются** абстрактными и не ломают "функциональность".

---

#### 2. Зачем нужны функциональные интерфейсы

Они — основа:
- **лямбда-выражений**;
- **method references** (ссылки на методы);
- функционального стиля программирования в Java 8+.

```java
@FunctionalInterface
interface StringProcessor {
    String process(String s);
}

public class Main {
    public static void main(String[] args) {
        StringProcessor upper = s -> s.toUpperCase(); // лямбда реализует функциональный интерфейс
        System.out.println(upper.process("hello"));    // HELLO
    }
}
```

Любая лямбда **всегда** привязывается к какому-то функциональному интерфейсу.

---

#### 3. Популярные функциональные интерфейсы из `java.util.function`

Самые часто используемые:

- **`Function<T, R>`** — принимает `T`, возвращает `R`:
  - пример: преобразование объекта в другой тип;
  - `String::length` можно представить как `Function<String, Integer>`.

- **`Consumer<T>`** — принимает `T`, **ничего не возвращает**:
  - пример: печать, логирование, отправка в стороннюю систему;
  - используется в `forEach`.

- **`Supplier<T>`** — ничего не принимает, **возвращает `T`**:
  - пример: фабрика/поставщик значения (`() -> new User()`).

- **`Predicate<T>`** — принимает `T`, возвращает `boolean`:
  - пример: фильтрация (`x -> x > 10`).

- **`BiFunction<T, U, R>`** — принимает два аргумента `T` и `U`, возвращает `R`.

- **`UnaryOperator<T>`** — частный случай `Function<T, T>` (вход и выход одного типа).

- **`BinaryOperator<T>`** — частный случай `BiFunction<T, T, T>` (два аргумента одного типа и результат того же типа).

Также часто встречаются:
- `IntFunction`, `LongFunction`, `IntPredicate`, `IntSupplier` и т.д. — примитивные версии, чтобы не плодить `Integer`/`Long`.

---

#### 4. Классические функциональные интерфейсы до Java 8

Из старых, но до сих пор очень популярных:

- `java.lang.Runnable` — метод `void run()`;
- `java.util.concurrent.Callable<V>` — метод `V call() throws Exception`;
- `java.util.Comparator<T>` — метод `int compare(T o1, T o2)`.

Все они также функциональные интерфейсы и отлично работают с лямбдами:

```java
Runnable r = () -> System.out.println("Hello");
Callable<Integer> c = () -> 42;
Comparator<String> cmp = (a, b) -> a.length() - b.length();
```

---

#### 5. Как ответить на собесе

- "Функциональный интерфейс — это интерфейс с **одним абстрактным методом**, который может иметь дополнительные `default` и `static` методы.  
  Он нужен как **тип-мишень** для лямбда-выражений и method references."
- "Из популярных функциональных интерфейсов: `Runnable`, `Callable`, `Comparator`, а также из `java.util.function`: `Function`, `Consumer`, `Supplier`, `Predicate`, `BiFunction`, `UnaryOperator`, `BinaryOperator`."

---

### Вопрос 6. What is a terminal function?

*(На собесах чаще говорят “terminal operation” или “терминальная операция стрима”.)*

#### 1. Что такое терминальная операция в Stream API

В контексте Java Stream API **терминальная операция (terminal function/operation)** — это операция, которая:
- **завершает** поток (после неё стрим больше нельзя использовать);
- **запускает выполнение всей цепочки** промежуточных операций (`map`, `filter` и т.д.);
- **возвращает результат**: коллекцию, число, Optional, boolean и т.п., а не новый Stream.

До терминальной операции все промежуточные (`map`, `filter`, `distinct`, `sorted` и т.д.) **ленивые** — они не выполняются, пока не будет вызван терминальный метод.

---

#### 2. Примеры терминальных операций

Типичные терминальные операции:

- **Сбор в коллекцию**:
  - `collect(...)` — самый мощный терминальный метод:

```java
List<String> names = users.stream()
    .map(User::getName)
    .filter(name -> name.length() > 3)
    .collect(Collectors.toList()); // ТЕРМИНАЛЬНАЯ
```

- **Агрегации/редукции**:
  - `count()`
  - `sum()`, `average()` (через числовые стримы `IntStream`, `LongStream`, `DoubleStream`)
  - `reduce(...)`

```java
long count = list.stream().filter(x -> x > 10).count(); // ТЕРМИНАЛЬНАЯ
```

- **Поиск и проверка условий**:
  - `findFirst()`, `findAny()`
  - `anyMatch(...)`, `allMatch(...)`, `noneMatch(...)`

```java
boolean hasAdult = users.stream()
    .anyMatch(u -> u.getAge() >= 18); // ТЕРМИНАЛЬНАЯ
```

- **Итерация без возврата значения**:
  - `forEach(...)`, `forEachOrdered(...)`

```java
names.stream()
    .map(String::toUpperCase)
    .forEach(System.out::println); // ТЕРМИНАЛЬНАЯ
```

После любой из этих операций стрим **считается использованным** — повторно на нём вызвать что-то нельзя.

---

#### 3. Терминальные vs промежуточные операции

**Промежуточные (intermediate)**:
- возвращают **новый Stream**;
- **ленивые** — не обрабатывают элементы сами по себе;
- примеры: `map`, `flatMap`, `filter`, `distinct`, `sorted`, `limit`, `skip`.

**Терминальные (terminal)**:
- возвращают **не Stream**, а результат (коллекция, число, Optional и т.д.), либо `void` (`forEach`);
- **запускают весь пайплайн**;
- закрывают стрим.

На собесе важно понимать фразу:
- “Stream API ленивый: **ничего не выполнится, пока не вызвать терминальную операцию**”.

---

#### 4. Как кратко ответить на собесе

- "В Java Stream API терминальная операция — это операция, которая **завершает поток и запускает выполнение всей цепочки промежуточных операций**.  
  Она возвращает уже не Stream, а итоговый результат (`collect`, `count`, `findFirst`, `anyMatch`, `forEach` и т.п.) и делает стрим непригодным к повторному использованию."

---

### Вопрос 7. Map vs flatMap stream API

#### 1. Кратко: разница в одном предложении

- **`map`**: берёт элемент и превращает его в **один другой объект** → размер стрима обычно тот же.
- **`flatMap`**: берёт элемент и превращает его в **стрим/коллекцию элементов**, а затем **“расплющивает”** (flatten) всё в один общий поток.

Формально:
- `map: T -> R`
- `flatMap: T -> Stream<R>` с последующим слиянием всех этих `Stream<R>` в один `Stream<R>`.

---

#### 2. Пример с `map`

```java
List<String> words = List.of("java", "stream");

List<Integer> lengths = words.stream()
    .map(String::length)      // "java" -> 4, "stream" -> 6
    .toList();                // [4, 6]
```

Здесь на входе `Stream<String>`, на выходе `Stream<Integer>`.  
На каждый элемент — **один** результат.

---

#### 3. Пример, где `map` даёт вложенные структуры

```java
List<String> words = List.of("java", "stream");

List<List<Character>> chars = words.stream()
    .map(word -> word.chars()
        .mapToObj(c -> (char) c)
        .toList()
    )
    .toList();
```

Тип будет `List<List<Character>>` — список списков.  
Иногда это то, что нужно, но чаще — нет, и тут заходит `flatMap`.

---

#### 4. Пример с `flatMap` — расплющивание

```java
List<String> words = List.of("java", "stream");

List<Character> chars = words.stream()
    .flatMap(word -> word.chars()
        .mapToObj(c -> (char) c) // получаем Stream<Character> для каждого слова
    )
    .toList(); // один плоский список символов
```

Здесь:
- `word -> word.chars().mapToObj(...)` даёт `Stream<Character>` для каждого слова;
- `flatMap` руками **сливает** все эти стримы в один `Stream<Character>`;
- результат: **один плоский список** всех символов.

---

#### 5. Типичный пример с коллекцией коллекций

```java
class User {
    private String name;
    private List<String> roles;

    public List<String> getRoles() { return roles; }
}

List<User> users = ...;

// Получить список всех ролей всех пользователей (плоский)
List<String> allRoles = users.stream()
    .map(User::getRoles)      // Stream<List<String>>
    .flatMap(List::stream)    // Stream<String> — расплющили
    .distinct()               // оставили уникальные
    .toList();
```

Логика:
- `map(User::getRoles)` даёт `Stream<List<String>>` (стрим списков ролей);
- `flatMap(List::stream)` превращает каждый `List<String>` в `Stream<String>` и всё склеивает в один поток строк.

---

#### 6. Где `map`, а где `flatMap` в стримах Optional / реактивщине

Идея та же:

- Для `Optional`:
  - `map` — когда функция возвращает **обычное значение**;
  - `flatMap` — когда функция возвращает **Optional**.

```java
Optional<String> name = getUser().map(User::getName);          // map
Optional<Address> address = getUser().flatMap(User::getAddressOptional); // flatMap
```

- В реактивных библиотеках (`Mono`, `Flux`, `CompletableFuture`) паттерн такой же:  
  `map` → обычное значение, `flatMap` → ещё одна обёртка.

---

#### 7. Как ответить на собесе

- "В Stream API `map` принимает функцию `T -> R` и делает один элемент из одного, в то время как `flatMap` принимает функцию `T -> Stream<R>` и **расплющивает** полученные стримы в один поток.  
  `flatMap` нужен, когда у нас коллекции внутри коллекций (`List<List<T>>`, `Stream<List<T>>`) и мы хотим один плоский `Stream<T>`."

---

### Вопрос 8. OOP principles

#### 1. Основные принципы ООП

Классическая четвёрка принципов ООП (часто спрашивают как "OOP pillars"):
- **Encapsulation** — инкапсуляция
- **Inheritance** — наследование
- **Polymorphism** — полиморфизм
- **Abstraction** — абстракция

На собесе важно не просто перечислить, а **коротко объяснить каждый и привести пример на Java**.

---

#### 2. Encapsulation — инкапсуляция

Идея: **скрывать внутреннее устройство** объекта и предоставлять наружу только нужный интерфейс.

- Скрываем состояние через модификаторы доступа (`private` поля).
- Управляем доступом через методы (`getters/setters`, бизнес-методы).
- Можем гарантировать инварианты (например, возраст не может быть отрицательным).

```java
public class User {
    private String name;  // скрыто
    private int age;      // скрыто

    public User(String name, int age) {
        setAge(age);
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("Age must be >= 0");
        }
        this.age = age;
    }
}
```

**Собес-фраза**:
- "Инкапсуляция — это сокрытие внутреннего состояния и управление доступом к нему через публичный интерфейс класса."

---

#### 3. Inheritance — наследование

Идея: описать **отношение "is-a"** и переиспользовать код базового класса.

```java
class Animal {
    public void speak() {
        System.out.println("Some sound");
    }
}

class Dog extends Animal {
    @Override
    public void speak() {
        System.out.println("Woof");
    }
}
```

В Java у класса может быть:
- **один** родительский класс (одинарное наследование);
- множество реализованных интерфейсов.

**Собес-фраза**:
- "Наследование позволяет описывать иерархии классов и переиспользовать поведение базовых классов, задавая отношение 'X is a Y'."

(Подробно про наследование ещё в вопросе 10 ниже.)

---

#### 4. Polymorphism — полиморфизм

Идея: **один интерфейс — много реализаций**.  
Код работает с базовым типом, а фактически выполняется реализация конкретного подкласса.

```java
Animal a1 = new Dog();
Animal a2 = new Cat();

List<Animal> animals = List.of(a1, a2);
for (Animal a : animals) {
    a.speak(); // для Dog -> "Woof", для Cat -> "Meow"
}
```

- Тип ссылки `Animal`, но реальный объект может быть `Dog`, `Cat` и т.д.
- Вызов виртуального метода (`speak`) разрешается **во время выполнения**.

**Собес-фраза**:
- "Полиморфизм позволяет вызывать один и тот же метод на базовом типе, а реально выполнять разные реализации в зависимости от конкретного объекта."

---

#### 5. Abstraction — абстракция

Идея: **выделить только важные для задачи характеристики**, скрыв детали реализации.

В Java:
- абстрактные классы (`abstract class`);
- интерфейсы (`interface`).

```java
interface PaymentService {
    void pay(double amount);
}

class CardPaymentService implements PaymentService {
    @Override
    public void pay(double amount) {
        // логика списания с карты
    }
}

class CryptoPaymentService implements PaymentService {
    @Override
    public void pay(double amount) {
        // логика крипто-платежа
    }
}
```

Код сверху может зависеть от `PaymentService`, не зная, как именно реализована оплата.

**Собес-фраза**:
- "Абстракция — это умение работать с сущностями через их интерфейсы, не зная деталей реализации, что упрощает замену и расширение поведения."

---

#### 6. Как ответить на собесе

- "В ООП выделяют четыре основных принципа: инкапсуляция, наследование, полиморфизм и абстракция.  
  Инкапсуляция — сокрытие состояния и управление доступом к нему, наследование — переиспользование и иерархии 'is-a', полиморфизм — разные реализации одного интерфейса, абстракция — работа с сущностями через интерфейсы, а не через конкретные реализации."

---

### Вопрос 9. Contract equals and hashcode

#### 1. Для чего нужны `equals` и `hashCode`

Используются для:
- корректной работы **коллекций** на основе хэш-таблиц:
  - `HashMap`, `HashSet`, `LinkedHashMap`, `LinkedHashSet`;
- сравнения объектов **по значению**, а не по ссылке.

По умолчанию:
- `Object.equals` сравнивает **ссылки** (`==`);
- `Object.hashCode` обычно основан на адресе/идентичности объекта.

Если твой класс логически представляет "значение" (например, `User` с id), нужно переопределять оба.

---

#### 2. Контракт `equals`

Метод `equals` должен удовлетворять свойствам:
- **Рефлексивность**: `x.equals(x)` всегда `true`.
- **Симметричность**: `x.equals(y)` == `y.equals(x)`.
- **Транзитивность**: если `x.equals(y)` и `y.equals(z)` → `x.equals(z)`.
- **Непротиворечивость**: повторные вызовы дают тот же результат, пока объекты не меняются.
- **Сравнение с null**: `x.equals(null)` всегда `false`, не должно падать с NPE.

Пример реализации:

```java
public class User {
    private final Long id;
    private final String name;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        User user = (User) o;
        return Objects.equals(id, user.id) &&
               Objects.equals(name, user.name);
    }
}
```

---

#### 3. Контракт `hashCode`

**Ключевое правило**:

- Если `x.equals(y) == true`, то `x.hashCode() == y.hashCode()` **обязан** быть равен.
- Обратное не обязательно: одинаковые `hashCode` не гарантируют `equals == true`.

Дополнительно:
- В течение жизни объекта, пока он используется как ключ в `HashMap`/элемент в `HashSet`, его `hashCode` должен **не меняться** (т.е. поля, участвующие в equals/hashCode, не должны "плавать").

Пример:

```java
@Override
public int hashCode() {
    return Objects.hash(id, name);
}
```

---

#### 4. Что ломается, если контракт нарушен

Типичные баги:
- `HashSet` начинает содержать **дубликаты** (по логике) объектов.
- `HashMap` не может найти значение по "такому же" ключу.

Пример проблемы:

```java
Set<User> set = new HashSet<>();
User u = new User(1L, "Bob");
set.add(u);

// если изменить поле, участвующее в equals/hashCode (например, name),
// а hashCode зависит от name, объект может "потеряться" в HashSet
```

Поэтому:
- либо делай такие объекты **иммутабельными** (поля final),
- либо не включай изменяемые поля в equals/hashCode.

---

#### 5. Практические рекомендации

1. Если переопределяешь `equals`, **обязательно** переопредели и `hashCode`.
2. Используй `Objects.equals` и `Objects.hash` для краткости.
3. Думай, какие поля задают **идентичность** объекта (часто это `id`).
4. Не включай сильно изменяемые поля в equals/hashCode, если объект где-то выступает как ключ.

---

#### 6. Как ответить на собесе

- "`equals` и `hashCode` используются для логического сравнения объектов и корректной работы коллекций на хэш-таблицах (`HashMap`, `HashSet`).  
  Контракт: если `x.equals(y)` истинно, то `x.hashCode()` и `y.hashCode()` обязаны совпадать, иначе коллекции будут вести себя некорректно (потерянные элементы, дубликаты и т.д.)."

---

### Вопрос 10. How does inheritance work in java?

#### 1. Базовые правила наследования в Java

- Класс может **наследоваться только от одного** другого класса (`extends`).
- Класс может **реализовывать несколько интерфейсов** (`implements`).
- Наследуются:
  - все **не private** поля и методы;
  - но конструкторы **не наследуются**.

Пример:

```java
class Animal {
    public void speak() {
        System.out.println("Some sound");
    }
}

class Dog extends Animal {
    @Override
    public void speak() {
        System.out.println("Woof");
    }
}
```

`Dog` — это `Animal` (отношение **is-a**).

---

#### 2. Вызов конструкторов и `super`

- Конструктор **подкласса всегда вызывает конструктор суперкласса**:
  - если явно не указано `super(...)`, компилятор вставит `super();` (вызов дефолтного конструктора).

```java
class Animal {
    public Animal(String name) {
        System.out.println("Animal: " + name);
    }
}

class Dog extends Animal {
    public Dog() {
        super("Dog"); // обязательно вызвать конструктор родителя
    }
}
```

Также можно вызывать методы родителя:

```java
@Override
public void speak() {
    super.speak(); // опционально: вызвать поведение родителя
    System.out.println("Woof");
}
```

---

#### 3. Переопределение методов (`@Override`)

- Подкласс может **переопределить** (override) метод родителя, чтобы изменить поведение.
- Аннотация `@Override`:
  - помогает компилятору отловить ошибки (не тот сигнатура/тип);
  - улучшает читаемость.

Правила:
- нельзя ослаблять модификатор доступа (нельзя `public` → `protected`);
- нельзя бросать **более широкие checked-исключения**, чем в родителе.

---

#### 4. Модификаторы доступа и наследование

- `public` — видно везде, наследуется.
- `protected` — видно в том же пакете + в наследниках (даже в других пакетах).
- package-private (без модификатора) — видно только в том же пакете.
- `private` — видно только внутри самого класса, **не наследуется** как доступный член.

Но даже `private` поля физически существуют в объекте наследника, просто напрямую недоступны — к ним можно обратиться через методы родителя.

---

#### 5. `final` и наследование

- `final class` — **нельзя наследовать**:

```java
public final class String { }
// class MyString extends String { } // ошибка
```

- `final`-метод — **нельзя переопределить** в наследнике.

Используется для:
- запрета расширения API;
- фиксации критичного поведения, которое нельзя менять в подклассах.

---

#### 6. Наследование vs композиция

На собесах любят спросить:
- "Когда вы используете наследование, а когда композицию?"

Кратко:
- Наследование — если отношение **is-a** (Dog is an Animal).
- Композиция — если отношение **has-a** (User has an Address).

Java-разработчики часто предпочитают **композицию вместо глубоких иерархий наследования**, чтобы:
- избежать хрупких базовых классов;
- уменьшить связанность;
- легче тестировать и переиспользовать код.

---

#### 7. Как ответить на собесе

- "В Java класс может наследоваться только от одного родителя (`extends`), при этом он наследует его поля и методы (кроме конструкторов и private-членов) и может переопределять методы для изменения поведения.  
  Конструктор подкласса всегда вызывает конструктор суперкласса через `super(...)`. Наследование задаёт отношение 'is-a', а для большинства случаев изменений поведения и повторного использования кода сейчас предпочитают композицию вместо глубоких иерархий."

---

### Вопрос 11. What are generic types and why are they needed?

#### 1. Что такое дженерики в Java

**Generic types (дженерики)** — это параметризованные типы.  
Идея: мы пишем класс/интерфейс/метод один раз, но он работает **с разными типами**, при этом с **проверкой типов на этапе компиляции**.

Примеры:

```java
List<String> names = new ArrayList<>();
List<Integer> numbers = new ArrayList<>();
```

Один и тот же класс `ArrayList`, но параметр типа (`String`, `Integer`) делает его "специализацией".

Зачем:
- **Типобезопасность**: меньше `ClassCastException` в runtime.
- **Меньше кастов**: не нужно постоянно писать `(String) obj`.
- **Читаемость**: из сигнатур видно, с какими типами работает код.
Что такое инвариантность в дженериках? Это свойство, при котором List<A> не является родителем или наследником List<B>, даже если A наследуется от B.

Зачем это нужно? Для обеспечения Type Safety (безопасности типов). Это гарантирует, что в коллекцию, предназначенную для строк, никто не сможет подложить другой тип данных (например, число) через ссылку на родительский тип. Если бы дженерики не были инвариантными, мы бы постоянно получали ClassCastException во время выполнения программы.

Как с этим жить? (Wildcards)
Иногда нам всё-таки нужно принять «список чего угодно, что является наследником чего-то». Для этого придумали Wildcards (?):

List<? extends Number> (Ковариантность): «Список любого типа, который наследуется от Number». Из него можно читать, но в него нельзя писать (кроме null).

List<? super Integer> (Контравариантность): «Список любого типа, который является родителем для Integer». В него можно писать, но читать из него можно только как Object.
---

#### 2. Объявление и использование дженериков

**2.1. Дженерик-класс**

```java
class Box<T> {        // T — параметр типа
    private T value;

    public Box(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}

Box<String> box = new Box<>("hello");
String v = box.getValue();
```

Частые обозначения:
- `T` — Type
- `E` — Element (в коллекциях)
- `K`, `V` — Key, Value
- `R` — Result

**2.2. Дженерик-метод**

```java
public static <T> List<T> singletonList(T value) {
    List<T> list = new ArrayList<>();
    list.add(value);
    return list;
}
```

---

#### 3. Type erasure (стирание типов)

В Java дженерики реализованы через **стирание типов**:

- Информация о параметрах типа (`<String>`, `<Integer>`) есть **только на этапе компиляции**.
- В runtime JVM видит "сырой" тип:
  - `List<String>` и `List<Integer>` становятся просто `List`.

Последствия:

1. **Нельзя создавать новые экземпляры параметра типа**:

```java
class Box<T> {
    // T value = new T(); // так нельзя — тип стёрт
}
```

2. **Нельзя использовать примитивы как параметры типа**:

```java
List<int> list; // НЕЛЬЗЯ
List<Integer> list; // так правильно
```

3. **Нельзя сделать `new T[]`** — тоже из-за стирания:

```java
// T[] arr = new T[10]; // нельзя
```

4. **Нельзя перегружать методы только по разным generic-параметрам**:

```java
void process(List<String> list) { }
void process(List<Integer> list) { } // конфликт после стирания → обе как process(List)
```

---

#### 4. Invariance, covariance, contravariance (инвариантность и "ковариантность" через wildcard)

Ключевой факт:
- **Дженерики классов в Java инвариантны**:

```java
List<Object> lo;
List<String> ls = new ArrayList<>();
// lo = ls; // нельзя, хотя String — это Object
```

Это сделано для безопасности типов:
- если бы так можно было, можно было бы добавить в `lo` объект, который не `String`, и сломать список строк.

Чтобы гибко работать с наследованием и дженериками, используют **wildcards** (`?`) и правило **PECS**.

---

#### 5. Wildcards и правило PECS

**Wildcard** — это `?` в параметре типа: `List<?>`, `List<? extends Number>`, `List<? super Integer>`.

Правило **PECS** (Joshua Bloch, Effective Java):
- **P**roducer — `extends`
- **C**onsumer — `super`

То есть:
- Если коллекция **производит** значения (мы из неё читаем) → `? extends T`.
- Если коллекция **потребляет** значения (мы в неё пишем) → `? super T`.

**Пример: `? extends` (producer)**:

```java
List<? extends Number> list = List.of(1, 2, 3); // может быть List<Integer>, List<Double>...
Number n = list.get(0); // читать можно как Number
// list.add(10); // НЕЛЬЗЯ добавлять (кроме null)
```

**Пример: `? super` (consumer)**:

```java
List<? super Integer> list = new ArrayList<Number>(); // или List<Object>
list.add(10);        // добавлять Integer ОК
// Integer x = list.get(0); // вернуть можем только как Object
```

---

#### 6. Bounded type parameters (`<T extends ...>`)

Можно ограничивать параметр типа:

```java
class NumberBox<T extends Number> {
    private T value;

    public NumberBox(T value) {
        this.value = value;
    }

    public double doubleValue() {
        return value.doubleValue();
    }
}
```

`T extends Number` означает: T — это `Number` или его потомок.  
Так у нас внутри появляются методы `Number` (`doubleValue`, `intValue` и т.д.).

Также можно ограничивать несколькими типами (interface + class):

```java
<T extends SomeClass & SomeInterface>
```

---

#### 7. Зачем нужны дженерики (собес-формулировка)

- "Дженерики позволяют писать **обобщённый код** с сильной проверкой типов на этапе компиляции: коллекции и методы становятся типобезопасными, не требуют ручных кастов, и ошибки вылезают при компиляции, а не в рантайме.  
  В Java дженерики реализованы через стирание типов, поэтому есть ограничения вроде `new T()`, запрета примитивов и перегрузок только по generic-параметрам. Для работы с наследованием используются wildcard-и и правило PECS (`extends` для producer, `super` для consumer)."

---

### Вопрос 12. Methods of the Object class. Name them and tell why they are needed

#### 1. Основные методы `Object`

Класс `java.lang.Object` — корень иерархии всех классов в Java. У него есть ключевые методы:

- `public final Class<?> getClass()`
- `public int hashCode()`
- `public boolean equals(Object obj)`
- `protected Object clone() throws CloneNotSupportedException`
- `public String toString()`
- `protected void finalize() throws Throwable` *(deprecated в новых версиях)*
- `public final void wait() throws InterruptedException`
- `public final void wait(long timeout) throws InterruptedException`
- `public final void wait(long timeout, int nanos) throws InterruptedException`
- `public final void notify()`
- `public final void notifyAll()`

Ниже — по назначению.

---

#### 2. `getClass()`

```java
public final Class<?> getClass()
```

- Возвращает объект `Class`, описывающий реальный тип объекта в runtime.
- Полезен для логирования, рефлексии, проверок типа:

```java
Object obj = "hello";
System.out.println(obj.getClass().getName()); // java.lang.String
```

---

#### 3. `equals()` и `hashCode()`

Мы уже подробно разбирали контракт в вопросе 9. Здесь — кратко:

- `equals(Object obj)` — логическое сравнение объектов.
- `hashCode()` — хэш-код, используемый в хэш-коллекциях (`HashMap`, `HashSet` и др.).

По умолчанию:
- `equals` сравнивает ссылки (`==`),
- `hashCode` связан с идентичностью объекта.

В value-объектах (`User`, `Money`, `Point`) нужно **переопределять оба**.

---

#### 4. `toString()`

```java
public String toString()
```

Используется для получения строкового представления объекта:

- по умолчанию: `getClass().getName() + "@" + Integer.toHexString(hashCode())`;
- в реальной жизни всегда переопределяют для:
  - логирования;
  - отладки;
  - удобного вывода.

```java
@Override
public String toString() {
    return "User{id=" + id + ", name='" + name + "'}";
}
```

---

#### 5. `clone()`

```java
protected Object clone() throws CloneNotSupportedException
```

- Предназначен для **поверхностного копирования** объекта.
- По умолчанию кидает `CloneNotSupportedException`, если класс не реализует `Cloneable`.

Использование:

```java
class User implements Cloneable {
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone(); // поверхностная копия
    }
}
```

На практике:
- `clone` и `Cloneable` считаются **неудачным дизайном**, часто их избегают;
- предпочитают копирующие конструкторы или статические фабрики (`copyOf`).

---

#### 6. `finalize()` (устаревший)

```java
@Deprecated
protected void finalize() throws Throwable
```

- Вызывался GC **перед уничтожением объекта**, чтобы освободить ресурсы.
- Ненадёжный, непредсказуемый, с проблемами производительности и безопасности.
- В современных Java **deprecated** / **удаляется**, вместо этого:
  - использовать `try-with-resources`;
  - явное закрытие ресурсов (close).

---

#### 7. Методы синхронизации: `wait`, `notify`, `notifyAll`

Эти методы участвуют в низкоуровневой синхронизации монитором (`synchronized`):

- `wait()` — поток **освобождает монитор** и засыпает, пока другой поток не вызовет `notify/notifyAll` на том же объекте.
- `notify()` — будит **один** ожидающий поток.
- `notifyAll()` — будит **все** ожидающие потоки.

Используются только внутри синхронизированных блоков:

```java
synchronized (lock) {
    while (!condition) {
        lock.wait();
    }
}
```

На собесах достаточно понимать:
- это низкоуровневый механизм координации потоков;
- в проде часто предпочитают более высокоуровневые примитивы (`Lock`, `Condition`, `CountDownLatch`, `Semaphore`, `BlockingQueue` и т.п.).

---

#### 8. Как ответить на собесе

- "Класс `Object` задаёт базовое поведение для всех объектов в Java: определяет методы сравнения (`equals`, `hashCode`), строковое представление (`toString`), доступ к информации о классе (`getClass`), базовую механику клонирования (`clone`), устаревший хук под финализацию (`finalize`) и низкоуровневые методы синхронизации (`wait`, `notify`, `notifyAll`).  
  Эти методы либо переопределяются (equals/hashCode/toString/clone), либо используются как фундамент для более высокоуровневых API."

---

### Вопрос 13. The most popular GCs and general algorithm of work

#### 1. Базовые идеи работы GC в JVM

GC (Garbage Collector) отвечает за:
- автоматическое освобождение памяти от **недостижимых** объектов;
- чтобы ты не делал `free/delete` вручную.

Ключевые идеи:
- **Tracing GC**: от корней (root set) помечаем достижимые объекты.
- **Generational hypothesis**: большинство объектов "живут недолго" →  
  делим heap на **молодое** и **старое** поколение.

Типичный алгоритм:
1. **Mark** — пометить все достижимые объекты.
2. **Sweep** — освободить память от непомеченных.
3. **Compact** — по необходимости сжать (уплотнить) память, чтобы избежать фрагментации.

---

#### 2. Генерационная модель (young / old)

Heap обычно делится на:
- **Young generation** (Eden + Survivor):
  - сюда попадают новые объекты;
  - частые, но быстрые **minor GC**.
- **Old generation**:
  - объекты, пережившие несколько сборок в young;
  - реже, но тяжелее **major/full GC**.

Minor GC:
- сканирует young;
- копирует живые объекты (copying collector) в survivor/old;
- быстро очищает всё остальное.

---

#### 3. Основные GC в HotSpot JVM

Исторически и по сей день:

- **Serial GC**:
  - однопоточный;
  - простой, маленький оверхед;
  - подходит для маленьких heap, embedded.

- **Parallel GC** (раньше назывался throughput collector):
  - использует несколько потоков для GC;
  - ориентирован на **максимальную пропускную способность** (throughput), но с более длинными паузами;
  - хороший вариант, если паузы не критичны.

- **CMS (Concurrent Mark Sweep)** *(устаревший, удалён в новых версиях)*:
  - старался минимизировать паузы, больше работы делает параллельно с приложением;
  - частично конкурентный mark-sweep;
  - заменён более современными G1/ZGC/Shenandoah.

- **G1 (Garbage-First) GC**:
  - **дефолтный GC в современных JVM** (начиная с Java 9+ для серверных конфигураций);
  - делит heap на регионы;
  - собирает регионы **по приоритету мусора ("garbage-first")**, стараясь держать паузы в заданных пределах;
  - комбинирует генерационный и регионный подход.

- **ZGC**:
  - **very low pause** GC (паузы обычно < 10 мс, даже на больших heap);
  - работает в основном конкурентно, использует цветные указатели, load barriers;
  - подходит для больших heap (десятки/сотни ГБ) и чувствительных к паузам систем.

- **Shenandoah** (от Red Hat):
  - тоже low-pause GC с конкурентной компакцией;
  - похож по целям на ZGC.

---

#### 4. Как работает G1 (упрощённо)

G1 (Garbage-First):
- Делит heap на множество равных **регионов**.
- Каждый регион может быть:
  - young;
  - old;
  - humongous (для больших объектов).
- GC отслеживает, сколько "мусора" в каждом регионе.

Алгоритм (упрощённо):
1. **Concurrent marking** — конкурентно помечает живые объекты по всему heap.
2. Строит список регионов по "выгодности" (где больше мусора).
3. Во время пауз выбирает самые "грязные" регионы и:
   - копирует из них живые объекты в другие регионы;
   - освобождает регионы целиком.

Цель:
- соблюдать **целевые паузы** (например, <= 200мс), собирая мусор кусками;
- избегать долгих full GC.

---

#### 5. Какой GC по умолчанию

Для современных версий Java (JDK 11/17/21, типичная server-конфигурация):
- по умолчанию используется **G1 GC**.

Можно явно задать GC флагами JVM, например:
- `-XX:+UseG1GC`
- `-XX:+UseParallelGC`
- `-XX:+UseZGC`

---

#### 6. Как ответить на собесе

- "Java использует трассирующие, обычно генерационные GC: объекты сначала попадают в young generation, часто собираются быстрыми minor GC, выжившие переносятся в old generation, которая собирается реже и тяжелее.  
  Базовый алгоритм — mark-sweep-compact."
- "Из популярных сборщиков: **Serial**, **Parallel**, устаревший **CMS**, и современные **G1** (по умолчанию), **ZGC**, **Shenandoah**.  
  G1 делит heap на регионы и собирает их по приоритету "самых мусорных", чтобы держать паузы в заданных пределах."

---
### Вопрос 14. What is try-with-resources and why is it needed?

#### 1. Суть try-with-resources

`try-with-resources` — это конструкция, которая:
- автоматически **закрывает ресурсы** после использования;
- гарантирует вызов `close()` даже при исключениях;
- уменьшает количество `try/finally`-шумa.

Работает с любыми объектами, реализующими `AutoCloseable` (`Closeable` тоже подходит).

---

#### 2. Пример без и с try-with-resources

**Без try-with-resources:**

```java
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("data.txt"));
    String line = br.readLine();
} catch (IOException e) {
    // handle
} finally {
    if (br != null) {
        try {
            br.close();
        } catch (IOException e) {
            // ignore
        }
    }
}
```

**С try-with-resources:**

```java
try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
    String line = br.readLine();
} catch (IOException e) {
    // handle
}
```

Ресурс `br` автоматически закроется в конце блока `try`.

---

#### 3. Как это работает

- В заголовке `try (...)` объявляются ресурсы.
- После выхода из блока:
  - JVM вызовет `close()` на каждом ресурсе (в порядке **обратном объявлению**);
  - если при закрытии бросаются исключения, они добавляются как **suppressed** (доступны через `Throwable.getSuppressed()`).

Начиная с Java 9:
- можно использовать уже существующие переменные (effectively final) в `try`, не объявляя их заново.

---

#### 4. Как ответить на собесе

- "Try-with-resources — это синтаксический сахар для работы с ресурсами, реализующими `AutoCloseable`: он гарантирует вызов `close()` и освобождение ресурса даже при исключениях, заменяя громоздкий try/finally и уменьшая риск утечек."

---

### Вопрос 15. Int pool and String pool. What is it and why is it necessary?

#### 1. String pool

**String pool** — это таблица уникальных строк в JVM (interned строки).

Ключевые моменты:
- Литералы строк (`"abc"`) автоматически попадают в пул.
- JVM хранит **одну копию** каждого строкового литерала.
- Можно явно поместить строку в пул через `String.intern()`.

Пример:

```java
String a = "hello";
String b = "hello";

System.out.println(a == b); // true — обе ссылаются на одну строку в пуле

String c = new String("hello");
System.out.println(a == c);         // false — c вне пула
System.out.println(a == c.intern()); // true — intern() вернёт ссылку из пула
```

**Зачем нужен String pool**:
- экономия памяти — одна копия часто используемых строк;
- быстрые сравнения `==` для литералов (но в обычном коде всё равно использовать `equals`).

---

#### 2. Integer / int pool (Integer cache)

Для обёрточных типов тоже есть кэши, наиболее известен:

- **`Integer` cache**: диапазон по умолчанию `[-128, 127]`.

При автоупаковке (`Integer.valueOf`):

```java
Integer x = 100;
Integer y = 100;
System.out.println(x == y); // true — обе ссылки из кэша

Integer a = 200;
Integer b = 200;
System.out.println(a == b); // false — вне диапазона кэша
```

Важно:
- Кэшируется **`Integer`**, не `int`.
- Диапазон можно изменить флагом `-XX:AutoBoxCacheMax=...` (для верхней границы).

Подобные кэши есть и у других обёрток (`Byte`, `Short`, `Long`, `Character` с малым диапазоном).

**Зачем нужен Integer pool**:
- уменьшение количества создаваемых объектов при частом использовании маленьких чисел;
- оптимизация памяти и скорости (меньше GC).

---

#### 3. Подводные камни при сравнении через `==`

```java
Integer x = 127;
Integer y = 127;
System.out.println(x == y); // true (кэш)

Integer a = 128;
Integer b = 128;
System.out.println(a == b); // false (разные объекты)
```

Вывод:
- никогда не полагайся на кэш при логическом сравнении — **используй `equals`**.

---

#### 4. Как ответить на собесе

- "String pool — это таблица интернированных строк, где JVM хранит одну копию каждого строкового литерала; это экономит память и ускоряет сравнение строк-литералов.  
  У обёрток вроде `Integer` есть аналогичный кэш (int pool) для маленьких значений, например `[-128, 127]`, чтобы не создавать лишние объекты при автоупаковке."

---

### Вопрос 16. Stack and Heap, what is stored and where

#### 1. Что такое Stack и Heap в контексте JVM

- **Stack (стек)** — область памяти для **кадров вызова методов** (stack frames) для каждого потока.
  - У **каждого потока свой стек**.
  - Там живут:
    - локальные переменные примитивных типов;
    - ссылки на объекты;
    - return-адреса, временные данные.
  - Управление строго по принципу LIFO (последним пришёл — первым ушёл).

- **Heap (куча)** — общая область памяти для **всех объектов**:
  - все экземпляры классов, массивы;
  - поля объектов;
  - управляется GC.

Важно:
- Объект **всегда** в heap.
- В стеке может лежать **ссылка** на этот объект.

---

#### 2. Что лежит в стеке

Стековая рамка (stack frame) метода содержит:
- значения **примитивов** локальных переменных (`int`, `double`, `boolean` и т.д.);
- **ссылки** на объекты (`User`, `String` и т.д.);
- параметры метода;
- служебную информацию для JVM.

Пример:

```java
void foo() {
    int x = 10;           // x — примитив в стеке
    User user = new User(); // user — ссылка в стеке, сам объект в heap
}
```

Когда метод `foo` заканчивает выполнение:
- его frame снимается со стека;
- переменные `x` и `user` исчезают;
- объект `new User()` в heap остаётся, пока на него есть хоть одна живая ссылка.

---

#### 3. Что лежит в куче (heap)

В heap размещаются:
- все объекты (`new ...`);
- массивы (`new int[10]`, `new String[5]`);
- строки (`new String("...")`, строковые литералы после загрузки в String pool).

Пример:

```java
User u1 = new User("Bob");
User u2 = u1;
```

- В heap один объект `User("Bob")`.
- В стеке две ссылки (`u1`, `u2`), указывающие на один и тот же объект.

Когда все ссылки на объект исчезнут (переменные вышли из области видимости и т.п.) —  
объект становится **недостижимым** и будет собран GC.

---

#### 4. Как связаны stack/heap и потоки

- У **каждого потока** свой стек:
  - рекурсия/глубокие вызовы → `StackOverflowError`.
- Heap **общий** для всех потоков:
  - поэтому нужны синхронизация/lock-и при доступе нескольких потоков к одним и тем же объектам.

---

#### 5. Типичные вопросы/мифы

- "Примитивы в стеке, объекты в куче?"  
  Ответ: **и да, и нет**:
  - локальные примитивы метода → в стеке;
  - примитивы как поля объекта → в объекте в heap;
  - массив примитивов → массив в heap, стек хранит только ссылку.

- "Строки всегда в pool?"  
  Нет:
  - литералы — да, в String pool;
  - `new String("...")` создаёт новый объект в heap (можно интернировать вручную).

---

#### 6. Как ответить на собесе

- "В JVM стек — это per-thread структура, где хранятся кадры вызовов методов: локальные переменные и ссылки на объекты. Управление там LIFO, и при выходе из метода его frame просто снимается.  
  Heap — общая область памяти для всех объектов и массивов, управляемая GC. Объекты всегда живут в куче, а стек содержит только ссылки на них. Из-за этого для объектов, разделяемых между потоками, нужна синхронизация, а при глубоких вызовах можно получить `StackOverflowError`."

---

### Вопрос 17. Popular thread-safe collections. Name and tell how they work

#### 1. Обёртки `Collections.synchronizedXXX`

- `Collections.synchronizedList(list)`
- `Collections.synchronizedSet(set)`
- `Collections.synchronizedMap(map)`

Как работают:
- возвращают **обёртку**, которая синхронизирует **каждый вызов метода** на общем мониторе.

```java
List<String> list = Collections.synchronizedList(new ArrayList<>());

// при итерировании нужен внешний synchronized
synchronized (list) {
    for (String s : list) {
        // ...
    }
}
```

Плюсы:
- просто, быстро прикрутить к уже существующей коллекции.

Минусы:
- **грубая блокировка** (один общий lock) → слабая масштабируемость под высокой нагрузкой.

---

#### 2. `ConcurrentHashMap`

Основная **thread-safe Map** в Java.

Как работает (упрощённо, Java 8+):
- Хранит данные в **таблице бакетов** (как `HashMap`), но:
  - чтения в большинстве случаев **нелокируемые** (volatile/atomic операции);
  - модификации используют **тонкие локи** (на части структуры), а не один глобальный lock.
- Поддерживает **конкурентное чтение и запись** с минимальными блокировками.

Когда использовать:
- общие кэши;
- счётчики, статистика;
- любые частые операции чтения/записи из многих потоков.

---

#### 3. `CopyOnWriteArrayList` / `CopyOnWriteArraySet`

Как работают:
- при каждой **записи** (add/remove/set) создаётся **новая копия массива**;
- чтения идут **без синхронизации**, по "старому" снимку.

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("a");      // копирует массив + добавляет элемент
String s = list.get(0); // чтения очень дешёвые и без блокировок
```

Плюсы:
- очень быстрые и безопасные чтения без lock;
- идеально для "read-mostly" сценариев.

Минусы:
- дорогие записи (копирование массива) → не подходит при частых модификациях.

---

#### 4. Очереди и блокирующие очереди (`java.util.concurrent`)

Популярные:
- `ConcurrentLinkedQueue` — неблокирующая, на CAS, для **многопоточной очереди** без жёстких требований к блокировкам.
- `LinkedBlockingQueue`, `ArrayBlockingQueue`, `PriorityBlockingQueue`:
  - **блокирующие очереди** для продюсер/консюмер паттернов;
  - `put()`/`take()` блокируются при пустоте/переполнении.

Плюсы:
- "правильный" способ обмена задачами между потоками;
- не надо вручную писать wait/notify.

---

#### 5. Отсортированные конкурентные коллекции

- `ConcurrentSkipListMap<K,V>`
- `ConcurrentSkipListSet<E>`

Как работают:
- реализованы через **skip list** (слоёный связный список);
- поддерживают **отсортированность** и **конкурентный доступ** (`O(log n)` операции).

Когда нужны:
- когда нужен **отсортированный** набор/мапа + доступ из нескольких потоков.

---

#### 6. Как кратко ответить на собесе

- "Для thread-safe коллекций в Java есть два подхода: обёртки `Collections.synchronizedXXX`, которые дают грубый монитор на каждую операцию, и специализированные коллекции из `java.util.concurrent` (`ConcurrentHashMap`, `CopyOnWriteArrayList`, блокирующие очереди, `ConcurrentSkipListMap/Set`), которые дают лучшую масштабируемость за счёт тонких локов, копирования-на-запись или неблокирующих алгоритмов."

---

### Вопрос 18. HashMap algorithm

#### 1. Структура данных

`HashMap<K,V>` хранит данные в **массиве бакетов**:
- каждый бакет — или:
  - `null`;
  - или **цепочка** записей (`Node`), связанный список / дерево.

С Java 8:
- если количество элементов в бакете > порог (по умолчанию 8) и размер таблицы достаточно велик, связанный список **превращается в красно-чёрное дерево** (tree bin) для `O(log n)` в худшем случае.

---

#### 2. Как рассчитывается индекс бакета

1. Берём `hash = key.hashCode()`.
2. Применяем **вторичное хеширование** (чтобы лучше раскидать биты).
3. Индекс: `index = (n - 1) & hash`, где `n` — длина массива.

Это дешёвая мод-операция, если размер массива — степень двойки.

---

#### 3. Добавление элемента (`put`)

Упрощённый алгоритм:

1. Если таблица ещё не инициализирована — выделить первый массив.
2. По хешу находим бакет.
3. Если бакет пуст (`null`) — просто кладём туда новый `Node`.
4. Если не пуст:
   - пробегаем по цепочке:
     - если найден **ключ с таким же `hash` и `equals`** → обновляем `value`;
     - иначе добавляем новый элемент в конец списка / дерева.
5. Если длина списка в бакете стала больше порога → **treeify** (превратить в красно-чёрное дерево) при достаточном размере таблицы.
6. Увеличиваем `size`, проверяем порог ресайза:
   - если `size > capacity * loadFactor` → **resize** (увеличить массив и перераспределить элементы).

---

#### 4. Поиск элемента (`get`)

1. Считаем `hash` и индекс бакета.
2. Идём по цепочке / дереву в этом бакете:
   - сравниваем `hash`;
   - если `hash` совпал — сравниваем `equals` по ключу.
3. Возвращаем найденное значение или `null`.

Амортизированная сложность:
- `O(1)` в среднем;
- `O(log n)` при tree bin;
- в худшем теоретическом случае — `O(n)`, но Java 8+ значительно снижает вероятность этого за счёт деревьев.

---

#### 5. Ресайз и load factor

- **`initialCapacity`** — начальная ёмкость массива.
- **`loadFactor`** (по умолчанию 0.75) — насколько таблица может быть заполнена, прежде чем расшириться.

При ресайзе:
- ёмкость удваивается;
- элементы **перераспределяются** по новой таблице (ре-хеширование с учётом нового размера).

---

#### 6. Как кратко ответить на собесе

- "HashMap — это хэш-таблица, где ключ по `hashCode` попадает в бакет, при коллизиях элементы хранятся в связанном списке, а с Java 8 — при больших коллизиях в красно-чёрном дереве. Средняя сложность операций `get/put` — `O(1)`, а при tree bin — `O(log n)`. При превышении порога `capacity * loadFactor` происходит resize с перераспределением элементов."

---

### Вопрос 19. Differences between ArrayList and LinkedList. What and when works faster.

#### 1. Кратко: во что они устроены

- **`ArrayList`**:
  - внутри **динамический массив** (`Object[]`);
  - индексация по массиву.

- **`LinkedList`**:
  - внутри **двусвязный список** (`prev`/`next`);
  - каждый элемент — отдельный узел c ссылками.

---

#### 2. Сложность операций

**ArrayList**:
- `get(index)` — **O(1)** (прямой доступ по индексу).
- `set(index)` — **O(1)**.
- `add()` в конец (амортизированно) — **O(1)**.
- `add(index)`, `remove(index)` — **O(n)** (надо сдвигать элементы).

**LinkedList**:
- `get(index)` — **O(n)** (надо дойти по ссылкам).
- `add()` в конец — **O(1)** (если есть хвост).
- `add(index)` — **O(n)** (надо дойти до позиции, сами ссылки переставляются за O(1)).
- `remove` по ссылке на узел — **O(1)**, по индексу — **O(n)**.

---

#### 3. Что и когда быстрее (практически)

**ArrayList быстрее, когда**:
- много операций `get(index)` / итераций по списку;
- добавление в конец списка;
- память важна (меньше overhead по ссылкам).

**LinkedList имеет смысл, когда**:
- нужно много **вставок/удалений в середину**, но:
  - у тебя есть **Iterator** / ссылка на узел;
  - и списки не гигантские.

На практике:
- в 99% случаев используют **`ArrayList`**;
- `LinkedList` редко реально выигрывает, особенно с учётом cache locality и общей стоимости аллокаций.

---

#### 4. Как ответить на собесе

- "ArrayList основан на массиве и даёт `O(1)` доступ по индексу и быстрые проходы, но дорогие вставки/удаления в середину (со сдвигом). LinkedList — двусвязный список: вставки/удаления узла — `O(1)`, но доступ по индексу — `O(n)`. В реальном коде почти всегда предпочтителен ArrayList, а LinkedList стоит рассматривать только при специфичных паттернах вставок/удалений."

---

### Вопрос 20. How do TreeMap and TreeSet work?

#### 1. Общая идея

- **`TreeMap<K,V>`** и **`TreeSet<E>`** — это **отсортированные** коллекции.
- Реализованы через **самобалансирующееся бинарное дерево поиска** (в HotSpot — **красно-чёрное дерево**).
- Основные операции (`get`, `put`, `contains`, `remove`) работают за **`O(log n)`**.

---

#### 2. TreeMap

`TreeMap<K,V>`:
- хранит пары `key -> value` в отсортированном по ключу виде;
- порядок определяет:
  - либо `K implements Comparable<K>` (`compareTo`);
  - либо переданный в конструктор `Comparator<K>`.

Особенности:
- поддерживает:
  - `firstKey()`, `lastKey()`;
  - `headMap`, `tailMap`, `subMap` (диапазоны ключей);
  - `higherKey`, `lowerKey` и т.п.
- `null`-ключ:
  - в стандартной реализации **не допускается** (получишь `NullPointerException` при сравнении).

---

#### 3. TreeSet

`TreeSet<E>`:
- по сути — **обёртка над `TreeMap<E, Object>`**:
  - значения не хранятся (используется фиктивный `PRESENT` объект).
- Элемент уникален, порядок определяется:
  - `Comparable` элемента;
  - или `Comparator`, переданный в конструктор.

Key points:
- нет дубликатов;
- автоматическая отсортированность;
- методы `first`, `last`, `ceiling`, `floor`, `subSet` и т.д.

---

#### 4. Когда использовать TreeMap / TreeSet

Использовать, когда:
- важно **сортированное** представление:
  - например, нужно всегда иметь ключи/элементы по возрастанию;
- нужны **операции по диапазонам**:
  - все элементы `< key`;
  - все элементы в `[from, to)`.

Если сортировка не нужна:
- обычно лучше `HashMap` / `HashSet` → `O(1)` в среднем.

---

#### 5. Как ответить на собесе

- "TreeMap и TreeSet реализованы через самобалансирующееся дерево (красно-чёрное), что даёт `O(log n)` на вставку, поиск и удаление при сохранении отсортированного порядка ключей/элементов. TreeSet внутри использует TreeMap, храня только ключи. Их берут, когда нужна естественная сортировка или операции по диапазонам, а для простого хранения без порядка обычно используют HashMap/HashSet."

---

### Вопрос 21. Hierarchy of collections. Tell us what it looks like and name the implementations

#### 1. Верхний уровень: `Collection`, `Map`, `Iterator`

В Java две большие ветки коллекций:

- **`java.util.Collection<E>`** — "коллекции элементов":
  - подтипы:
    - `List<E>`
    - `Set<E>`
    - `Queue<E>` / `Deque<E>`
- **`java.util.Map<K,V>`** — отображения ключ-значение.

Отдельно:
- **`Iterator<E>` / `ListIterator<E>`** — итераторы;
- **`Iterable<E>`** — всё, что можно итерировать в `for-each`.

---

#### 2. Ветка `List` (упорядоченные коллекции с индексом)

**Интерфейс**: `List<E>`  
Особенности:
- упорядоченный набор элементов;
- допускает дубликаты;
- доступ по индексу.

Типичные реализации:
- **`ArrayList`** — динамический массив (по умолчанию в 99% случаев).
- **`LinkedList`** — двусвязный список.
- `Vector` (устаревший, синхронизированный, не использовать в новом коде).

---

#### 3. Ветка `Set` (множества, без дубликатов)

**Интерфейс**: `Set<E>`  
Особенности:
- нет дубликатов;
- порядок не гарантируется (зависит от реализации).

Подтипы и реализации:

- **`HashSet`**:
  - основан на `HashMap<E, Object>`;
  - не гарантирует порядок;
  - `O(1)` в среднем для `add/contains/remove`.

- **`LinkedHashSet`**:
  - запоминает **порядок вставки**;
  - основан на `LinkedHashMap`.

- **`SortedSet<E>` / `NavigableSet<E>`**:
  - отсортированные множества;
  - основная реализация — **`TreeSet<E>`** (красно-чёрное дерево).

---

#### 4. Ветка `Queue` / `Deque` (очереди, стеки)

**Интерфейс**: `Queue<E>`  
Особенности:
- обычно **FIFO** (first-in-first-out);
- базовые операции `offer`, `poll`, `peek`.

Расширение: **`Deque<E>`** (double-ended queue):
- можно добавлять/забирать элементы и с головы, и с хвоста;
- можно использовать как **стек** (LIFO) через `push/pop/peek`.

Реализации:
- `LinkedList` реализует и `List`, и `Deque`.
- `ArrayDeque` — быстрая deqeue на массиве (рекомендуемый стек/очередь).
- `PriorityQueue` — очередь с приоритетом (min-heap).
- В `java.util.concurrent`:
  - `ArrayBlockingQueue`, `LinkedBlockingQueue`, `PriorityBlockingQueue`, `ConcurrentLinkedQueue` и др.

---

#### 5. Ветка `Map` (ключ-значение)

**Интерфейс**: `Map<K,V>`  
Особенности:
- хранит пары `key -> value`;
- ключи уникальны;
- значения могут дублироваться.

Реализации:

- **`HashMap`**:
  - хэш-таблица;
  - `O(1)` в среднем для `get/put`;
  - порядок не гарантируется.

- **`LinkedHashMap`**:
  - запоминает порядок вставки / либо порядок доступа (LRU-стиль).

- **`TreeMap`**:
  - реализует `SortedMap` / `NavigableMap`;
  - отсортирован по ключу (красно-чёрное дерево);
  - `O(log n)` операции.

- `EnumMap`, `WeakHashMap`, `IdentityHashMap` — спец. реализации для отдельных кейсов.

Thread-safe варианты:
- `ConcurrentHashMap` (основной);
- `Collections.synchronizedMap(...)` — обёртка с монитором.

---

#### 6. Как ответить на собесе

- "Иерархия коллекций в Java делится на две основные ветки: `Collection` (с подтипами `List`, `Set`, `Queue/Deque`) и `Map`.  
  Типичные реализации: для `List` — `ArrayList`, `LinkedList`; для `Set` — `HashSet`, `LinkedHashSet`, `TreeSet`; для `Queue/Deque` — `ArrayDeque`, `LinkedList`, `PriorityQueue`; для `Map` — `HashMap`, `LinkedHashMap`, `TreeMap`, плюс thread-safe варианты в `java.util.concurrent`."

---

### Вопрос 22. Exception hierarchy. Checked and Unchecked exceptions

#### 1. Основная иерархия исключений

Корень:
- `java.lang.Throwable`

Две большие ветки:
- `Error` — ошибки JVM, обычно **не обрабатываются**:
  - `OutOfMemoryError`, `StackOverflowError`, `InternalError` и т.д.
- `Exception` — "исключения" уровня приложения:
  - `RuntimeException` и его наследники (unchecked);
  - все остальные (checked).

Схематично:

```text
Throwable
 ├─ Error
 │   └─ OutOfMemoryError, StackOverflowError, ...
 └─ Exception
     ├─ RuntimeException
     │   ├─ NullPointerException
     │   ├─ IllegalArgumentException
     │   ├─ IndexOutOfBoundsException
     │   ├─ ArithmeticException
     │   └─ ...
     └─ (checked exceptions)
         ├─ IOException
         ├─ SQLException
         ├─ ClassNotFoundException
         └─ ...
```

---

#### 2. Checked exceptions

**Checked** — подтипы `Exception`, которые **не являются** `RuntimeException`.

Примеры:
- `IOException`
- `SQLException`
- `ClassNotFoundException`
- `InterruptedException`

Правила:
- компилятор **заставляет**:
  - либо **объявить** их в сигнатуре (`throws IOException`);
  - либо **обработать** в `try-catch`.

Плюсы:
- заставляют явно думать о "нормальных" ошибочных ситуациях (I/O, DB, сеть).

Минусы:
- зашумляют сигнатуры;
- часто приводит к `throws Exception` или глупым `catch (Exception e) {}`.

---

#### 3. Unchecked exceptions (RuntimeException)

**Unchecked** — это:
- `RuntimeException` и его наследники;
- любые `Error`.

Примеры:
- `NullPointerException`
- `IllegalArgumentException`
- `IndexOutOfBoundsException`
- `ArithmeticException`
- `IllegalStateException`

Правила:
- компилятор не требует `throws` и `try-catch`.

Использование:
- для **программных ошибок** (bug-ов):
  - неверные аргументы;
  - нарушение инвариантов;
  - ошибки в логике.

---

#### 4. Когда использовать checked vs unchecked

Типичная рекомендация (из Effective Java):
- **Checked**:
  - когда *клиент может что-то разумно сделать* для восстановления:
    - проблемы IO, недоступность ресурса, ограничения бизнеса.
- **Unchecked**:
  - когда это **программная ошибка**, от которой код не может "нормально" восстановиться:
    - NPE, неверный аргумент, неправильное состояние.

---

#### 5. Как ответить на собесе

- "В Java иерархия исключений начинается с `Throwable`, от которого наследуются `Error` и `Exception`. Checked-исключения — это подтипы `Exception` (кроме `RuntimeException`), которые компилятор заставляет объявлять/обрабатывать, например `IOException` или `SQLException`. Unchecked — это `RuntimeException` и его наследники, а также `Error`; они не требуют явного `throws` и используются для программных ошибок и серьёзных проблем JVM."

---

### Вопрос 23. What are wrapper classes and why are they needed?

#### 1. Что такое wrapper-классы

Wrapper-классы — это **обёртки над примитивами**:

- `boolean` → `Boolean`
- `byte` → `Byte`
- `short` → `Short`
- `int` → `Integer`
- `long` → `Long`
- `char` → `Character`
- `float` → `Float`
- `double` → `Double`

Каждый из них — обычный `final`-класс в `java.lang`, хранящий значение примитивного типа.

---

#### 2. Зачем нужны wrapper-классы

Основные причины:

1. **Коллекции**:
   - Коллекции (`List`, `Set`, `Map`) работают только с объектами → примитивы нельзя класть напрямую.
   - Поэтому для `List<int>` используем `List<Integer>`.

2. **Nullable-значения**:
   - Примитив не может быть `null`, а обёртка может.
   - Пример: поле `Integer age`, где `null` означает "возраст не указан".

3. **Методы и утилиты**:
   - парсинг из строк (`Integer.parseInt`, `Double.parseDouble`);
   - константы (`Integer.MAX_VALUE`, `Double.MIN_VALUE` и т.д.).

4. **Reflection / generic API**:
   - многие общие API ожидают `Object`.

---

#### 3. Autoboxing / unboxing

Java автоматически конвертирует между примитивом и обёрткой:

```java
int x = 10;
Integer y = x;   // autoboxing: int -> Integer
int z = y;       // unboxing: Integer -> int
```

Опасности:
- **null + unboxing** → `NullPointerException`:

```java
Integer a = null;
int b = a; // NPE
```

- **автоупаковка в циклах / горячем коде**:
  - создаёт кучу объектов и давит на GC;
  - стоит избегать лишних боксовок.

---

#### 4. Как ответить на собесе

- "Wrapper-классы — это объектные обёртки над примитивами (`Integer`, `Long`, `Boolean` и т.д.), которые нужны, чтобы работать с примитивами там, где требуются объекты: в коллекциях, generic-API, при необходимости хранить `null` и использовать утилитные методы вроде `parseInt`. Java поддерживает autoboxing/unboxing, но им нельзя злоупотреблять из-за лишних аллокаций и риска NPE при распаковке `null`."

---

### Вопрос 24. Immutable objects. Examples in Java. How to create them?

#### 1. Что такое immutable-объект

**Неизменяемый объект** — это объект, состояние которого **нельзя изменить** после создания.

Свойства:
- все поля, которые определяют состояние, устанавливаются **один раз**;
- нет сеттеров/методов, меняющих состояние;
- сам объект потокобезопасен (если его состояние полностью immutable).

---

#### 2. Примеры неизменяемых классов в Java

Классика:
- `String`
- все wrapper-классы (`Integer`, `Long`, `Boolean`, `BigDecimal`, `BigInteger`)
- новые даты/время в `java.time`:
  - `LocalDate`, `LocalTime`, `LocalDateTime`, `Instant`, `Duration`, `Period`, и т.д.

Пример: `String`:
- поля `final` + класс `final`;
- методы `concat`, `substring` и т.п. возвращают **новые** объекты, не меняя исходный.

---

#### 3. Как создать immutable-класс (шаблон)

Правила:

1. Сделать класс **`final`** (чтобы не могли переопределить поведение через наследование).
2. Сделать все поля, определяющие состояние:
   - `private` и `final`.
3. Инициализировать поля **в конструкторе**.
4. Не давать сеттеров.
5. Для ссылочных полей:
   - если они сами изменяемые (например, `List`), делать **defensive copy** при присваивании и при возврате.

Пример:

```java
public final class UserProfile {
    private final String name;
    private final int age;
    private final List<String> roles;

    public UserProfile(String name, int age, List<String> roles) {
        this.name = name;
        this.age = age;
        // защитная копия
        this.roles = List.copyOf(roles);
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public List<String> getRoles() {
        return roles; // List.copyOf даёт неизменяемый список
    }
}
```

Начиная с Java 16:
- можно использовать **`record`**:

```java
public record UserProfileRecord(String name, int age, List<String> roles) { }
```

`record` по умолчанию:
- `final`;
- все компоненты final;
- генерирует конструктор, `equals`, `hashCode`, `toString`.
Но для изменяемых полей (типа `List`) всё равно нужны defensive copy, если нужен реально immutable-слайс.

---

#### 4. Плюсы immutable-объектов

- Естественная **потокобезопасность** (если нет внутренней мутации).
- Упрощение reasoning-а и тестирования.
- Можно безопасно шарить между потоками, кэшировать и переиспользовать.
- Подходят для ключей `Map`/элементов `Set` (если состояние не меняется → `equals/hashCode` стабильны).

---

#### 5. Как ответить на собесе

- "Неизменяемый объект — это объект, состояние которого нельзя изменить после создания. В Java примеры: `String`, wrapper-классы, `LocalDate/LocalDateTime` и многие классы в `java.time`.  
  Чтобы создать immutable-класс, делают класс `final`, поля `private final`, инициализируют их в конструкторе, не дают сеттеров и для изменяемых полей используют защитные копии при присваивании и возврате."

---

### Вопрос 25. What is serialization in Java? What do you need to do to serialize an object? What is serialVersionUID?

#### 1. Что такое сериализация

**Сериализация** — это процесс преобразования объекта в поток байт:
- для записи на диск;
- для отправки по сети;
- для кэширования и т.п.

Обратный процесс — **десериализация**: из байт восстанавливается объект.

В Java стандартный механизм — через `java.io.Serializable`.

---

#### 2. Что нужно сделать, чтобы сериализовать объект

1. **Реализовать интерфейс `Serializable`**:

```java
import java.io.Serializable;

public class User implements Serializable {
    private String name;
    private int age;
}
```

2. Использовать `ObjectOutputStream` / `ObjectInputStream`:

```java
// сериализация
try (ObjectOutputStream oos =
         new ObjectOutputStream(new FileOutputStream("user.bin"))) {
    User user = new User("Bob", 25);
    oos.writeObject(user);
}

// десериализация
try (ObjectInputStream ois =
         new ObjectInputStream(new FileInputStream("user.bin"))) {
    User user = (User) ois.readObject();
}
```

3. Все поля тоже должны быть сериализуемыми:
- или сами реализуют `Serializable`;
- или помечены как `transient` (не сериализуются).

---

#### 3. Что такое `serialVersionUID`

`serialVersionUID` — это **версионный идентификатор класса** для механизма сериализации.

Пример:

```java
private static final long serialVersionUID = 1L;
```

Зачем нужен:
- при десериализации JVM сравнивает `serialVersionUID` в классе и в байтах объекта;
- если они **не совпадают**, бросается `InvalidClassException`:
  - считается, что класс изменился **не совместимо** с сохранённым состоянием.

Если `serialVersionUID` не указан:
- JVM **генерирует его автоматически** на основе структуры класса (имя, поля, методы и т.п.);
- любое изменение структуры (добавление поля, изменение сигнатуры) может поменять авто-uid →
  старые сохранённые объекты перестанут десериализовываться.

Поэтому:
- в production-классах, которые сериализуются, **рекомендуется явно задавать** `serialVersionUID`.

---

#### 4. Тонкости и нюансы

- Поля, помеченные `transient`, **не попадают** в сериализацию:

```java
class User implements Serializable {
    private String name;
    private transient String password; // не сериализуется
}
```

- Можно кастомизировать сериализацию через методы:
  - `private void writeObject(ObjectOutputStream out)`  
  - `private void readObject(ObjectInputStream in)`

- Стандартная Java-сериализация считается **тяжёлой и хрупкой** для распределённых систем;
  - в проде часто используют альтернативы: JSON, protobuf, Avro, Kryo и т.п.

---

#### 5. Как ответить на собесе

- "Сериализация в Java — это преобразование объекта в поток байт (и обратно) через `Serializable` и `ObjectOutputStream/ObjectInputStream`.  
  Чтобы класс был сериализуемым, он должен реализовывать `Serializable`, а для контроля совместимости версий используется `serialVersionUID`: если он совпадает, объект можно корректно десериализовать, если нет — будет `InvalidClassException`."

---

### Вопрос 26. What is the volatile keyword for?

#### 1. Что делает `volatile`

`volatile` — это модификатор для **полей**, который:
- гарантирует **видимость** изменений между потоками;
- накладывает **ограничения на переупорядочивание** операций (memory barriers).

Если поле `x` объявлено как:

```java
volatile int x;
```

то:
- запись в `x` из одного потока **немедленно** становится видна другим потокам;
- чтение `x` всегда идёт **из памяти**, а не из устаревшего кеша/регистра.

---

#### 2. Чего `volatile` НЕ делает

Важно:
- `volatile` **НЕ делает операции атомарными**, если это многошаговые операции.

Например:

```java
volatile int count;

count++; // НЕ атомарно! это read-modify-write
```

`count++` всё равно может породить гонку:
- поток A читает значение;
- поток B читает то же значение;
- оба увеличивают и записывают → одна инкрементация теряется.

Для атомарных инкрементов нужны:
- `AtomicInteger` / `LongAdder`;
- или синхронизация (`synchronized`, `Lock`).

---

#### 3. Типичные кейсы использования `volatile`

1. **Флаги завершения/состояния**:

```java
volatile boolean running = true;

void worker() {
    while (running) {
        // do work
    }
}

void stop() {
    running = false;
}
```

2. **Double-checked locking (DCL)** для ленивой инициализации:

```java
class Singleton {
    private static volatile Singleton instance;

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

`volatile` здесь нужен, чтобы:
- не было "полу-сконструированных" объектов из-за переупорядочивания;
- запись в `instance` была корректно видна всем потокам.

---

#### 4. Когда `volatile` достаточно, а когда нет

`volatile` хватает, когда:
- нужно только **видимость** простого значения (флаг, ссылка);
- нет инвариантов между несколькими полями;
- нет сложных read-modify-write операций.

Нужно `synchronized` / `Lock` / атомарные классы, когда:
- есть **несколько полей**, связанные инварианты;
- операции **композитные** (`if (x) { y++ }` и т.п.);
- нужна атомарность и/или ожидание (`wait/notify`, блокировки).

---

#### 5. Как ответить на собесе

- "`volatile` используется для обеспечения видимости изменений поля между потоками и предотвращения опасного переупорядочивания, но не делает составные операции атомарными. Его обычно используют для простых флагов и паттернов вроде double-checked locking; для инкрементов и сложной логики нужны `AtomicXXX` или синхронизация."

---

### Вопрос 27. Is an array an object or a primitive?

#### 1. Статус массива в Java

**Массив в Java — это всегда объект.**

Даже если это массив примитивов:

```java
int[] arr = new int[10];
```

- `arr` — это **объект**, хранящий 10 примитивов `int`.
- `arr instanceof Object` → `true`.
- У массива есть поле `length` и методы `getClass()`, `hashCode()`, `toString()` от `Object`.

---

#### 2. Типы массивов

Примеры:

- массив примитивов:

```java
int[] ints = new int[5];           // объект типа int[]
double[] doubles = new double[10]; // объект типа double[]
```

- массив ссылок:

```java
String[] names = new String[5];    // объект типа String[]
User[] users = new User[10];       // объект типа User[]
```

Все они — объекты в heap, на которые указывают ссылки в стеке.

---

#### 3. Особенности массивов

- Массивы **ковариантны**:

```java
Number[] a = new Integer[10]; // компилируется
```

Но можно получить `ArrayStoreException`:

```java
Number[] a = new Integer[10];
a[0] = 1.5; // runtime ArrayStoreException
```

- Тип массива доступен через `getClass()`:

```java
int[] arr = new int[10];
System.out.println(arr.getClass().getName()); // [I
```

---

#### 4. Как ответить на собесе

- "В Java массивы — это объекты, даже если они хранят примитивы. У массива есть тип (`int[]`, `String[]`), он живёт в куче, имеет свойство `length` и наследует методы `Object`. Переменная массива — это ссылка, а не примитив."

---

### Вопрос 28. Thread.sleep vs Thread.yield разница

#### 1. `Thread.sleep`

```java
Thread.sleep(millis);
```

Что делает:
- переводит текущий поток в состояние **TIMED_WAITING** на как минимум `millis` миллисекунд;
- поток **не выполняет код** это время;
- после таймаута планировщик может снова дать ему процессор (не строго ровно по миллисекундам).

Особенности:
- **не освобождает захваченные мониторы**:

```java
synchronized (lock) {
    Thread.sleep(1000); // lock всё ещё удерживается
}
```

- бросает `InterruptedException`, если поток прерывают во время сна.

---

#### 2. `Thread.yield`

```java
Thread.yield();
```

Что делает:
- поток **намекает планировщику**, что он готов уступить процессор другим потокам того же приоритета;
- это **hint**, а не гарантия:
  - может сработать, может быть проигнорировано;
  - поток может тут же снова быть выбран к исполнению.

Особенности:
- не бросает исключений;
- не задаёт время ожидания;
- часто **никак не помогает** и почти не используется в проде.

---

#### 3. Разница по смыслу

- `sleep`:
  - явно "усыпляет" поток на определённое время;
  - гарантированно делает паузу хотя бы на указанное время;
  - удобно для таймаутов, тестов, простых ретраев.

- `yield`:
  - просто говорит планировщику: "можно дать поработать другим";
  - нет гарантии, что реальный переключатель произойдёт;
  - поведение сильно зависит от JVM/OS.

Оба:
- **не освобождают блокировки** (`synchronized` мониторы, `Lock`), пока поток "спит" или "yield-ит".

---

#### 4. Практическое использование

- `Thread.sleep`:
  - допустимо использовать для:
    - простых поллингов с задержкой;
    - эмуляции задержек в тестах;
    - бэкоффов (с осторожностью).

- `Thread.yield`:
  - обычно **не рекомендуется**:
    - нет чёткой семантики;
    - легко сломать производительность или сделать код нестабильным.

Лучше использовать:
- высокоуровневые примитивы: `Lock`, `Condition.await()`, `BlockingQueue`, `CountDownLatch`, `Semaphore`, `Park/Unpark`.

---

#### 5. Как ответить на собесе

- "`Thread.sleep` приостанавливает поток на заданное время (TIMED_WAITING), не освобождая блокировки, и после таймаута поток может быть снова запланирован. `Thread.yield` лишь даёт планировщику подсказку уступить процессор другим потокам, без гарантий; на практике imprecise и редко используется в продакшене."

---

### Вопрос 29. How do the wait, notify, notifyAll methods work?

#### 1. Где эти методы объявлены

- Методы `wait()`, `notify()`, `notifyAll()` объявлены в **`java.lang.Object`**, а не в `Thread`.
- Это низкоуровневый механизм **межпоточной координации** на уровне **монитора объекта**.

Используются **только** внутри синхронизированных блоков/методов:

```java
synchronized (lock) {
    lock.wait();
}
```

Иначе будет `IllegalMonitorStateException`.

---

#### 2. Что делает `wait()`

```java
void wait() throws InterruptedException
```

Вызов `wait()` на объекте `lock`:
- поток **должен владеть монитором** этого объекта (`synchronized(lock)` уже активен);
- поток:
  1. **освобождает монитор** (отпускает `lock`);
  2. переходит в состояние **WAITING**;
  3. ждёт, пока другой поток вызовет `notify` / `notifyAll` на **том же самом объекте**.
- когда его "разбудят":
  - поток снова пытается захватить монитор `lock`;
  - когда захватывает — продолжает выполнение после `wait()`.

Есть также `wait(long timeout)` / `wait(long timeout, int nanos)` — ждут не дольше указанного времени.

Важно:
- `wait()` может завершиться:
  - по `notify`/`notifyAll`;
  - по прерыванию (`InterruptedException`);
  - по спонтанному "просыпанию" (**spurious wakeup**).
- Поэтому принято ждать в **цикле**:

```java
synchronized (lock) {
    while (!condition) {
        lock.wait();
    }
    // здесь condition == true
}
```

---

#### 3. Что делает `notify()`

```java
void notify()
```

- Будит **один** (произвольный) поток, который сейчас ждёт на этом же объекте (`lock.wait()`).
- Разбудить можно только внутри `synchronized(lock)`, иначе `IllegalMonitorStateException`.
- Разбудившийся поток:
  - переходит из WAITING в BLOCKED (ждёт монитор);
  - продолжает выполнение только после того, как снова захватит монитор `lock`.

---

#### 4. Что делает `notifyAll()`

```java
void notifyAll()
```

- Будит **все** потоки, которые ждут на этом объекте.
- Дальше они по одному будут пытаться захватить монитор и продолжить выполнение.

Использование:
- часто безопаснее, чем `notify()`, чтобы не "потерять" уведомление, если несколько условий завязаны на один мониторы.

---

#### 5. Типичный паттерн producer/consumer с wait/notify

```java
class BlockingQueueSimple<T> {
    private final Queue<T> queue = new LinkedList<>();
    private final int capacity;

    public BlockingQueueSimple(int capacity) {
        this.capacity = capacity;
    }

    public void put(T item) throws InterruptedException {
        synchronized (queue) {
            while (queue.size() == capacity) {
                queue.wait(); // ждём, пока освободится место
            }
            queue.add(item);
            queue.notifyAll(); // разбудить потребителей
        }
    }

    public T take() throws InterruptedException {
        synchronized (queue) {
            while (queue.isEmpty()) {
                queue.wait(); // ждём, пока что-то положат
            }
            T item = queue.remove();
            queue.notifyAll(); // разбудить производителей
            return item;
        }
    }
}
```

---

#### 6. Как ответить на собесе

- "Методы `wait/notify/notifyAll` определены в `Object` и работают на уровне монитора: `wait` освобождает монитор и переводит поток в ожидание на объекте, `notify`/`notifyAll` будят один или все ожидающие потоки. Вызывать их можно только внутри `synchronized` по тому же объекту, и всегда стоит проверять условие в цикле из-за возможных ложных пробуждений."

---

### Вопрос 30. How does the Thread.join() method work?

#### 1. Что делает `join()`

```java
public final void join() throws InterruptedException
```

- Метод `join()` вызывается **на потоке**, чтобы **подождать его завершения**.

Пример:

```java
Thread t = new Thread(() -> {
    // какая-то работа
});
t.start();
t.join(); // текущий поток ждёт, пока t завершится
```

- Текущий поток (тот, кто вызвал `join`) блокируется до тех пор, пока:
  - поток `t` не закончит выполнение (`TERMINATED`);
  - или не произойдёт прерывание (`InterruptedException`).

---

#### 2. Перегрузки с таймаутами

```java
join(long millis)
join(long millis, int nanos)
```

- Ждут не дольше указанного времени.
- Если поток-цель не завершился за это время — `join` возвращает управление, поток продолжает работу.

---

#### 3. Как это реализовано под капотом (упрощённо)

Грубо говоря:
- поток `joiner` вызывает `join()` на объекте `Thread t`;
- внутри `join()` используется `wait()` на мониторе потока `t`;
- когда поток `t` завершает `run()`, JVM вызывает `notifyAll()` на этом объекте.

То есть `join()` — это обёртка над `wait()` по объекту потока.

---

#### 4. Типичные кейсы

- Подождать, пока завершатся **все worker-потоки**:

```java
List<Thread> threads = ...
for (Thread t : threads) {
    t.start();
}
for (Thread t : threads) {
    t.join();
}
```

- Синхронное ожидание результата фоновой задачи (если не используется `Future`/`ExecutorService`).

---

#### 5. Как ответить на собесе

- "`Thread.join()` блокирует вызывающий поток до завершения указанного потока (или до истечения таймаута), реализуя ожидание на объекте потока через `wait/notify` под капотом. Это простой способ дождаться окончания работы других потоков."

---

### Вопрос 31. What is AtomicInteger and how does it work?

#### 1. Что такое `AtomicInteger`

`AtomicInteger` из пакета `java.util.concurrent.atomic`:
- класс-обёртка над `int` с **атомарными операциями**:
  - инкремент/декремент;
  - сравнение-и-установка (CAS);
  - `addAndGet`, `getAndSet`, и т.д.

Главное:
- операции безопасны при доступе из **нескольких потоков без `synchronized`**, за счёт атомарных инструкций/механизмов CAS.

---

#### 2. Пример использования

```java
AtomicInteger counter = new AtomicInteger(0);

void increment() {
    counter.incrementAndGet(); // атомарный инкремент
}
```

Внутри:
- используется низкоуровневый CAS (Compare-And-Set):
  - "если текущее значение = ожидаемому, запиши новое".

Пример CAS:

```java
boolean updated = counter.compareAndSet(5, 10);
// если counter == 5, станет 10 и вернёт true, иначе false
```

---

#### 3. Почему `AtomicInteger` лучше, чем `volatile int` для счётчиков

`volatile int count`:
- **видимость** изменений есть;
- но операции `count++` **НЕ атомарны** (read–modify–write).

`AtomicInteger`:
- `incrementAndGet()` реализован через CAS:
  - читает текущее значение,
  - пытается записать новое,
  - при конфликте (кто-то изменил значение между чтением и записью) повторяет попытку.

Итог:
- гарантируется, что ни одно приращение не потеряется, даже при высокой конкуренции.

---

#### 4. Где уместен `AtomicInteger`, а где нет

Уместен:
- простые счётчики/статистика;
- генераторы ID в рамках одного процесса;
- флаги/ссылки, где нужны редкие обновления и частые чтения.

Не уместен:
- сложные инварианты между **несколькими полями**;
- критические секции с несколькими действиями — там нужен `synchronized` / `Lock`.

---

#### 5. Как ответить на собесе

- "`AtomicInteger` — это потокобезопасная обёртка над int, предоставляющая атомарные операции инкремента, декремента и сравнения-и-установки на основе CAS. В отличие от `volatile int`, он гарантирует, что операции вроде `incrementAndGet()` не потеряют инкременты при доступе из нескольких потоков без использования `synchronized`."

---

### Вопрос 32. Why is the transient keyword needed?

#### 1. Что делает `transient`

`transient` — модификатор поля, который говорит механизму **стандартной Java-сериализации**:
- **не сериализовать** это поле.

Пример:

```java
class User implements Serializable {
    private String name;
    private transient String password; // не попадёт в поток байт
}
```

При сериализации:
- значение `password` **не будет записано**;
- при десериализации это поле получит **значение по умолчанию**:
  - для ссылок — `null`;
  - для примитивов — `0`, `false` и т.д.

---

#### 2. Когда используется `transient`

1. **Чувствительные данные**:
   - пароли, токены, секреты, которые нельзя сохранять/логировать.

2. **Несериализуемые поля**:
   - объекты, которые не реализуют `Serializable`.
   - Например: `Thread`, `Socket`, `InputStream` и т.д.

3. **Производные / кэшируемые значения**:
   - поля, которые можно вычислить заново на основе других сериализуемых полей;
   - чтобы не дублировать данные и не ломать контракт.

---

#### 3. `transient` и кастомная сериализация

Даже если поле `transient`, его можно:
- всё равно сериализовать вручную в `writeObject`;
- и восстановить в `readObject`.

Пример:

```java
private transient int cachedHash;

private void writeObject(ObjectOutputStream out) throws IOException {
    out.defaultWriteObject(); // сериализуем обычные поля
    // cachedHash не пишем — он будет пересчитан
}

private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
    in.defaultReadObject();
    // пересчитать cachedHash при необходимости
}
```

---

#### 4. Как ответить на собесе

- "`transient` помечает поле как несериализуемое для стандартной Java-сериализации: его значение не попадает в поток байт и при десериализации сбрасывается в значение по умолчанию. Это используют для чувствительных данных (пароли), несериализуемых зависимостей и временных/кэшируемых полей, которые можно восстановить."

---

### Вопрос 29. How do the wait, notify, notifyAll methods work?

#### 1. Где эти методы объявлены

- Методы `wait()`, `notify()`, `notifyAll()` объявлены в **`java.lang.Object`**, а не в `Thread`.
- Это низкоуровневый механизм **межпоточной координации** на уровне **монитора объекта**.

Используются **только** внутри синхронизированных блоков/методов:

```java
synchronized (lock) {
    lock.wait();
}
```

Иначе будет `IllegalMonitorStateException`.

---

#### 2. Что делает `wait()`

```java
void wait() throws InterruptedException
```

Вызов `wait()` на объекте `lock`:
- поток **должен владеть монитором** этого объекта (`synchronized(lock)` уже активен);
- поток:
  1. **освобождает монитор** (отпускает `lock`);
  2. переходит в состояние **WAITING**;
  3. ждёт, пока другой поток вызовет `notify` / `notifyAll` на **том же самом объекте**.
- когда его "разбудят":
  - поток снова пытается захватить монитор `lock`;
  - когда захватывает — продолжает выполнение после `wait()`.

Есть также `wait(long timeout)` / `wait(long timeout, int nanos)` — ждут не дольше указанного времени.

Важно:
- `wait()` может завершиться:
  - по `notify`/`notifyAll`;
  - по прерыванию (`InterruptedException`);
  - по спонтанному "просыпанию" (**spurious wakeup**).
- Поэтому принято ждать в **цикле**:

```java
synchronized (lock) {
    while (!condition) {
        lock.wait();
    }
    // здесь condition == true
}
```

---

#### 3. Что делает `notify()`

```java
void notify()
```

- Будит **один** (произвольный) поток, который сейчас ждёт на этом же объекте (`lock.wait()`).
- Разбудить можно только внутри `synchronized(lock)`, иначе `IllegalMonitorStateException`.
- Разбудившийся поток:
  - переходит из WAITING в BLOCKED (ждёт монитор);
  - продолжает выполнение только после того, как снова захватит монитор `lock`.

---

#### 4. Что делает `notifyAll()`

```java
void notifyAll()
```

- Будит **все** потоки, которые ждут на этом объекте.
- Дальше они по одному будут пытаться захватить монитор и продолжить выполнение.

Использование:
- часто безопаснее, чем `notify()`, чтобы не "потерять" уведомление, если несколько условий завязаны на один мониторы.

---

#### 5. Типичный паттерн producer/consumer с wait/notify

```java
class BlockingQueueSimple<T> {
    private final Queue<T> queue = new LinkedList<>();
    private final int capacity;

    public BlockingQueueSimple(int capacity) {
        this.capacity = capacity;
    }

    public void put(T item) throws InterruptedException {
        synchronized (queue) {
            while (queue.size() == capacity) {
                queue.wait(); // ждём, пока освободится место
            }
            queue.add(item);
            queue.notifyAll(); // разбудить потребителей
        }
    }

    public T take() throws InterruptedException {
        synchronized (queue) {
            while (queue.isEmpty()) {
                queue.wait(); // ждём, пока что-то положат
            }
            T item = queue.remove();
            queue.notifyAll(); // разбудить производителей
            return item;
        }
    }
}
```

---

#### 6. Как ответить на собесе

- "Методы `wait/notify/notifyAll` определены в `Object` и работают на уровне монитора: `wait` освобождает монитор и переводит поток в ожидание на объекте, `notify`/`notifyAll` будят один или все ожидающие потоки. Вызывать их можно только внутри `synchronized` по тому же объекту, и всегда стоит проверять условие в цикле из-за возможных ложных пробуждений."

---

### Вопрос 30. How does the Thread.join() method work?

#### 1. Что делает `join()`

```java
public final void join() throws InterruptedException
```

- Метод `join()` вызывается **на потоке**, чтобы **подождать его завершения**.

Пример:

```java
Thread t = new Thread(() -> {
    // какая-то работа
});
t.start();
t.join(); // текущий поток ждёт, пока t завершится
```

- Текущий поток (тот, кто вызвал `join`) блокируется до тех пор, пока:
  - поток `t` не закончит выполнение (`TERMINATED`);
  - или не произойдёт прерывание (`InterruptedException`).

---

#### 2. Перегрузки с таймаутами

```java
join(long millis)
join(long millis, int nanos)
```

- Ждут не дольше указанного времени.
- Если поток-цель не завершился за это время — `join` возвращает управление, поток продолжает работу.

---

#### 3. Как это реализовано под капотом (упрощённо)

Грубо говоря:
- поток `joiner` вызывает `join()` на объекте `Thread t`;
- внутри `join()` используется `wait()` на мониторе потока `t`;
- когда поток `t` завершает `run()`, JVM вызывает `notifyAll()` на этом объекте.

То есть `join()` — это обёртка над `wait()` по объекту потока.

---

#### 4. Типичные кейсы

- Подождать, пока завершатся **все worker-потоки**:

```java
List<Thread> threads = ...
for (Thread t : threads) {
    t.start();
}
for (Thread t : threads) {
    t.join();
}
```

- Синхронное ожидание результата фоновой задачи (если не используется `Future`/`ExecutorService`).

---

#### 5. Как ответить на собесе

- "`Thread.join()` блокирует вызывающий поток до завершения указанного потока (или до истечения таймаута), реализуя ожидание на объекте потока через `wait/notify` под капотом. Это простой способ дождаться окончания работы других потоков."

---

### Вопрос 31. What is AtomicInteger and how does it work?

#### 1. Что такое `AtomicInteger`

`AtomicInteger` из пакета `java.util.concurrent.atomic`:
- класс-обёртка над `int` с **атомарными операциями**:
  - инкремент/декремент;
  - сравнение-и-установка (CAS);
  - `addAndGet`, `getAndSet`, и т.д.

Главное:
- операции безопасны при доступе из **нескольких потоков без `synchronized`**, за счёт атомарных инструкций/механизмов CAS.

---

#### 2. Пример использования

```java
AtomicInteger counter = new AtomicInteger(0);

void increment() {
    counter.incrementAndGet(); // атомарный инкремент
}
```

Внутри:
- используется низкоуровневый CAS (Compare-And-Set):
  - "если текущее значение = ожидаемому, запиши новое".

Пример CAS:

```java
boolean updated = counter.compareAndSet(5, 10);
// если counter == 5, станет 10 и вернёт true, иначе false
```

---

#### 3. Почему `AtomicInteger` лучше, чем `volatile int` для счётчиков

`volatile int count`:
- **видимость** изменений есть;
- но операции `count++` **НЕ атомарны** (read–modify–write).

`AtomicInteger`:
- `incrementAndGet()` реализован через CAS:
  - читает текущее значение,
  - пытается записать новое,
  - при конфликте (кто-то изменил значение между чтением и записью) повторяет попытку.

Итог:
- гарантируется, что ни одно приращение не потеряется, даже при высокой конкуренции.

---

#### 4. Где уместен `AtomicInteger`, а где нет

Уместен:
- простые счётчики/статистика;
- генераторы ID в рамках одного процесса;
- флаги/ссылки, где нужны редкие обновления и частые чтения.

Не уместен:
- сложные инварианты между **несколькими полями**;
- критические секции с несколькими действиями — там нужен `synchronized` / `Lock`.

---

#### 5. Как ответить на собесе

- "`AtomicInteger` — это потокобезопасная обёртка над int, предоставляющая атомарные операции инкремента, декремента и сравнения-и-установки на основе CAS. В отличие от `volatile int`, он гарантирует, что операции вроде `incrementAndGet()` не потеряют инкременты при доступе из нескольких потоков без использования `synchronized`."

---

### Вопрос 32. Why is the transient keyword needed?

#### 1. Что делает `transient`

`transient` — модификатор поля, который говорит механизму **стандартной Java-сериализации**:
- **не сериализовать** это поле.

Пример:

```java
class User implements Serializable {
    private String name;
    private transient String password; // не попадёт в поток байт
}
```

При сериализации:
- значение `password` **не будет записано**;
- при десериализации это поле получит **значение по умолчанию**:
  - для ссылок — `null`;
  - для примитивов — `0`, `false` и т.д.

---

#### 2. Когда используется `transient`

1. **Чувствительные данные**:
   - пароли, токены, секреты, которые нельзя сохранять/логировать.

2. **Несериализуемые поля**:
   - объекты, которые не реализуют `Serializable`.
   - Например: `Thread`, `Socket`, `InputStream` и т.д.

3. **Производные / кэшируемые значения**:
   - поля, которые можно вычислить заново на основе других сериализуемых полей;
   - чтобы не дублировать данные и не ломать контракт.

---

#### 3. `transient` и кастомная сериализация

Даже если поле `transient`, его можно:
- всё равно сериализовать вручную в `writeObject`;
- и восстановить в `readObject`.

Пример:

```java
private transient int cachedHash;

private void writeObject(ObjectOutputStream out) throws IOException {
    out.defaultWriteObject(); // сериализуем обычные поля
    // cachedHash не пишем — он будет пересчитан
}

private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
    in.defaultReadObject();
    // пересчитать cachedHash при необходимости
}
```

---

#### 4. Как ответить на собесе

- "`transient` помечает поле как несериализуемое для стандартной Java-сериализации: его значение не попадает в поток байт и при десериализации сбрасывается в значение по умолчанию. Это используют для чувствительных данных (пароли), несериализуемых зависимостей и временных/кэшируемых полей, которые можно восстановить."

### Вопрос 33. What are deadlock and livelock?

#### 1. Deadlock (взаимная блокировка)

**Deadlock** — ситуация, когда несколько потоков **навсегда заблокированы**, каждый ждёт ресурс, который удерживает другой поток, и прогресса нет.

Классический пример с двумя локами:

```java
final Object lockA = new Object();
final Object lockB = new Object();

Thread t1 = new Thread(() -> {
    synchronized (lockA) {
        synchronized (lockB) {
            // ...
        }
    }
});

Thread t2 = new Thread(() -> {
    synchronized (lockB) {
        synchronized (lockA) {
            // ...
        }
    }
});
```

Возможный сценарий:
- `t1` захватил `lockA`;
- `t2` захватил `lockB`;
- `t1` ждёт `lockB`, `t2` ждёт `lockA` → оба висят навсегда.

Ключевые признаки:
- потоки **заблокированы** (BLOCKED/WAITING);
- нет прогресса без внешнего вмешательства.

---

#### 2. Livelock (ожившая блокировка)

**Livelock** — потоки **не заблокированы**, они **постоянно что-то делают**, но **прогресса тоже нет**.

Интуиция:
- потоки всё время "уступают" друг другу, меняют состояние, откатываются, но полезной работы не происходит.

Иллюстрация (упрощённая идея):

```java
// Оба потока пытаются вежливо освободить ресурс, когда видят конфликт,
// и оба его освобождают / повторно захватывают бесконечно.
```

Отличие от deadlock:
- при deadlock — все стоят и молчат;
- при livelock — все "бегают на месте".

---

#### 3. Как избегать deadlock

Основные приёмы:

1. **Фиксированный порядок захвата локов**:
   - если нужно захватывать несколько локов, **везде** захватывать их в одном порядке (`lock1` → `lock2` → `lock3`).

2. **Тайм-ауты на локах**:
   - использовать `tryLock(long timeout, TimeUnit)` из `java.util.concurrent.locks.Lock` вместо бесконечного `lock()`.

3. **Минимизировать область блокировки**:
   - держать локи как можно меньше по времени;
   - не вызывать "чужой" код под локом.

4. Избегать **взаимных вызовов** сервисов/слоёв с блокировками без чёткого протокола.

---

#### 4. Как ответить на собесе

- "Deadlock — это когда потоки навсегда заблокированы, каждый ждёт ресурс, который удерживает другой, и система не может продвинуться. Livelock — когда потоки формально не заблокированы, но из-за постоянных откатов и уступок тоже не делают прогресса. Deadlock лечат фиксированным порядком захвата локов, тайм-аутами и минимизацией критических секций."

---

### Вопрос 34. Methods for synchronizing access to shared resources between threads. Name them and name the advantages and disadvantages

#### 1. `synchronized`

**Что это:**
- ключевое слово, использующее **мониторы объектов**:
  - `synchronized`-методы;
  - `synchronized`-блоки.

**Плюсы:**
- простая, встроенная в язык семантика;
- автоматически работает с `wait/notify` (мониторный паттерн);
- гарантии **видимости** и **атомарности** в пределах блока;
- сложно "перепутать" unlock (освобождение происходит автоматически).

**Минусы:**
- только **эксклюзивные** блокировки;
- нет тайм-аутов/tryLock;
- может быть грубо и медленно под высокой конкуренцией, если использовать один глобальный монитор.

---

#### 2. `ReentrantLock` и другие `java.util.concurrent.locks`

**`ReentrantLock`**:
- более гибкий аналог `synchronized`:
  - `lock() / unlock()`;
  - `tryLock()` / `tryLock(timeout, unit)`;
  - возможность **fair**-режима;
  - `Condition` вместо `wait/notify`.

**Плюсы:**
- тайм-ауты при захвате;
- неблокирующие попытки (`tryLock`);
- несколько `Condition` на один лок;
- иногда лучше масштабируется.

**Минусы:**
- нужно **всегда** помнить об `unlock()` в `finally`;
- сложнее читать/сопровождать, чем `synchronized`.

Другие:
- `ReadWriteLock` (например, `ReentrantReadWriteLock`):
  - разделение на **read-lock** (несколько читателей) и **write-lock** (один писатель);
- `StampedLock`:
  - более современный, с оптимистичными чтениями.

---

#### 3. Высокоуровневые примитивы синхронизации

Из `java.util.concurrent`:

- **`CountDownLatch`**:
  - один или несколько потоков ждут, пока счётчик не станет 0.

- **`CyclicBarrier`**:
  - группа потоков ждёт друг друга, чтобы "вместе" перейти на следующую фазу.

- **`Semaphore`**:
  - ограничение доступа к ресурсу фиксированным количеством "разрешений".

- **`Exchanger`**:
  - два потока обмениваются объектами.

**Плюсы:**
- уменьшают необходимость вручную писать `wait/notify`;
- более декларативные/читабельные сценарии синхронизации.

**Минусы:**
- нужно хорошо понимать семантику, чтобы не повесить систему;
- это инструменты "по задаче", а не универсальная замена локам.

---

#### 4. Атомарные типы и неблокирующие структуры

- **Атомарные классы**:
  - `AtomicInteger`, `AtomicLong`, `AtomicReference`, `AtomicBoolean`, `LongAdder` и т.д.
- **Неблокирующие очереди/коллекции**:
  - `ConcurrentLinkedQueue`, `ConcurrentHashMap`, и т.п.

**Плюсы:**
- масштабируемость под высокой конкуренцией (меньше блокировок);
- отсутствие классических deadlock-ов.

**Минусы:**
- подходят только для локальной логики (счётчики, флаги, простые структуры);
- сложнее рассуждать о системных инвариантах.

---

#### 5. `volatile`

Не полноценная "синхронизация", но важный элемент:
- гарантирует **видимость** изменений и ограничивает переупорядочивание;
- не даёт атомарности сложных операций.

Используется:
- для флагов / состояния;
- в комбинации с другими примитивами.

---

#### 6. Как ответить на собесе

- "В Java есть несколько уровней синхронизации: низкоуровневый `synchronized` и `wait/notify`, более гибкие локи (`ReentrantLock`, `ReadWriteLock`), высокоуровневые примитивы (`CountDownLatch`, `Semaphore`, `CyclicBarrier`), атомарные типы и concurrent-коллекции, а также `volatile` для видимости. `synchronized` проще и встроен в язык, локи дают тайм-ауты и гибкость, атомарные типы и concurrent-коллекции лучше масштабируются, но применимы не ко всем задачам."

---

### Вопрос 35. What is the synchronized keyword used for and what are its features?

#### 1. Для чего нужен `synchronized`

`synchronized` используется для:
- **взаимного исключения** (mutual exclusion) — только один поток за раз может выполнять код внутри блока/метода;
- обеспечения **видимости** изменений между потоками:
  - вход в `synchronized`-блок → "happens-before" по отношению к выходу другого потока из блока на том же мониторе.

Проще: защищает критические секции при доступе к общему состоянию.

---

#### 2. Формы `synchronized`

1. **Синхронизированный блок**:

```java
private final Object lock = new Object();

void increment() {
    synchronized (lock) {
        count++;
    }
}
```

2. **Синхронизированный нестатический метод**:

```java
public synchronized void increment() {
    count++;
}
```

Эквивалентно:

```java
public void increment() {
    synchronized (this) {
        count++;
    }
}
```

3. **Синхронизированный статический метод**:

```java
public static synchronized void log(String msg) {
    // ...
}
```

Эквивалентно:

```java
public static void log(String msg) {
    synchronized (MyClass.class) {
        // ...
    }
}
```

---

#### 3. На чём реально синхронизируется

- Для `synchronized (obj)` — монитором является **конкретный объект `obj`**.
- Для `synchronized` нестатического метода — монитор `this`.
- Для `static synchronized` — монитор `Class`-объекта (`MyClass.class`).

Важно:
- разные объекты → **разные мониторы**;
- чтобы защитить общий ресурс, нужно синхронизироваться на **одном и том же объекте** во всех местах.

---

#### 4. Гарантии памяти (happens-before)

Важные особенности:

- Вход в `synchronized`:
  - устанавливает **barrier чтения** — поток "видит" все изменения, которые были **записаны** другими потоками до их выхода из синхронизированного блока на том же мониторе.

- Выход из `synchronized`:
  - устанавливает **barrier записи** — все изменения в блоке становятся видимы другим потокам после того, как они войдут в блок на том же мониторе.

Именно поэтому `synchronized` решает не только проблему гонок по состоянию, но и проблему **кеша/переупорядочивания**.

---

#### 5. Особенности и ограничения

- **Реентрантность**:
  - один и тот же поток может войти в `synchronized` по одному и тому же монитору несколько раз (рекурсивные вызовы) — монитор считает "счётчик" захватов.

- **Блокировка**:
  - если монитор уже удерживается другим потоком, поток-блокирующийся по этому монитору ждёт в состоянии BLOCKED.

- **Распространённые ошибки**:
  - синхронизация на разных объектах (например, на `this` в одном месте и на отдельном `lock` в другом);
  - долгие операции под локом (I/O, сеть, тяжёлая БД);
  - вызов чужого кода под локом (может привести к deadlock-ам).

---

#### 6. Когда `synchronized` подходит, а когда лучше другое

Подходит:
- простые критические секции;
- защита небольших кусков состояния;
- когда не нужны тайм-ауты и справедливость.

Лучше `Lock`/`ReadWriteLock`:
- когда нужны `tryLock`, тайм-ауты, прерываемые ожидания;
- когда требуется разделение чтения и записи (read/write locks).

---

#### 7. Как ответить на собесе

- "`synchronized` используется для защиты критических секций и обеспечивает и взаимное исключение, и гарантию видимости изменений через монитор объекта. Он может применяться к методам и блокам, синхронизируясь на `this`, `Class` или явном объекте, при этом является реентрантным. Это базовый встроенный механизм синхронизации; для более гибких сценариев часто используют `ReentrantLock` и другие примитивы из `java.util.concurrent`."

