<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/95c2684e-53b6-48a9-a527-f4227afba53c" /><img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8bf41d9c-b35a-4f0e-8254-d189ef364a3c" />---
title: "Практическая работаv №8: Объектно-ориентированное программирование в Dart"
discipline: "Разработка мобильных приложений"
tags:
  [
    dart,
    oop,
    flutter,
    constructors,
    inheritance,
    mixins,
    abstract-classes,
    sealed-classes,
  ]
---

# Практическая работа: Объектно-ориентированное программирование в Dart (Flutter Architecture)

> [!NOTE]
> **Цель работы:** Глубоко освоить объектно-ориентированную модель языка Dart. Изучить проектирование классов, генеративные, именованные, фабричные и константные конструкторы, списки инициализации, механизм неявных интерфейсов, одиночное наследование, концепцию миксинов (`mixin`), а также современные модификаторы классов (`sealed`, `base`, `interface`) для построения масштабируемой архитектуры приложений на Flutter.

---

## 📋 Содержание

1. [Архитектура ООП в Dart: всё есть объект](#1-архитектура-ооп-в-dart-всё-есть-объект)
2. [Тема 1. Классы, инкапсуляция, методы и геттеры/сеттеры](#тема-1-классы-инкапсуляция-методы-и-геттерысеттеры)
3. [Тема 2. Все виды конструкторов: Named, Initializer List, Factory, Const](#тема-2-все-виды-конструкторов-named-initializer-list-factory-const)
4. [Тема 3. Наследование (extends), полиморфизм и super](#тема-3-наследование-extends-полиморфизм-и-super)
5. [Тема 4. Абстрактные классы, неявные интерфейсы (implements) и Sealed-классы](#тема-4-абстрактные-классы-неявные-интерфейсы-implements-и-sealed-классы)
6. [Тема 5. Миксины (Mixins): композиция поведения через with и on](#тема-5-миксины-mixins-композиция-поведения-через-with-и-on)
7. [50 Смешанных заданий по ООП (Mobile Architecture Focus)](#50-смешанных-заданий-по-ооп)

---

## 1. Архитектура ООП в Dart: всё есть объект

В Dart любая сущность является объектом — экземпляром класса, наследующегося от корневого типа `Object?` (или `Object` для non-nullable значений). Даже числа, функции и `null` являются полноправными объектами.

```
                            ┌────────────────┐
                            │    Object?     │
                            └───────┬────────┘
                                    │
                                    ▼
                            ┌────────────────┐
                            │     Object     │
                            └───────┬────────┘
            ┌───────────────────────┼───────────────────────┐
            ▼                       ▼                       ▼
    ┌──────────────┐        ┌──────────────┐        ┌──────────────┐
    │ Widget (UI)  │        │  Repository  │        │ num, String  │
    └──────────────┘        └──────────────┘        └──────────────┘
```

### Ключевые особенности ООП в Dart

- **Отсутствие ключевых слов public/private/protected:** Видимость ограничивается библиотекой (файлом). Префикс подчеркивания `_` делает класс, поле или метод приватным для текущего файла.
- **Единое наследование классов:** Класс может наследоваться только от одного суперкласса (`extends`).
- **Множественные неявные интерфейсы:** Любой класс неявно определяет интерфейс, содержащий все его публичные поля и методы (`implements`).
- **Миксины (`with`):** Механизм повторного использования кода в нескольких несвязанных иерархиях без проблем множественного наследования (Diamond Problem).

---

## Тема 1. Классы, инкапсуляция, методы и геттеры/сеттеры

### Структура класса

Класс объединяет переменные состояния (поля) и функции, оперирующие этими данными (методы).

### Пример кода

```dart
class BankAccount {
  final String id;
  String owner;

  // Приватное поле (доступно только внутри данного .dart файла)
  double _balance = 0.0;

  BankAccount({required this.id, required this.owner});

  // Геттер для безопасного чтения баланса
  double get balance => _balance;

  // Сеттер с бизнес-валидацией
  set balance(double value) {
    if (value < 0) {
      throw ArgumentError("Баланс счета не может быть отрицательным");
    }
    _balance = value;
  }

  // Бизнес-метод с возвратом логического статуса
  bool withdraw(double amount) {
    if (amount > 0 && _balance >= amount) {
      _balance -= amount;
      return true;
    }
    return false;
  }

  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
    }
  }

  // Переопределение метода строкового представления
  @override
  String toString() => "Счет #$id ($owner): ${_balance.toStringAsFixed(2)} ₽";
}

void main() {
  var account = BankAccount(id: "ACC-40817", owner: "Иван Иванов");
  account.deposit(5000);
  account.withdraw(1200);
  print(account); // Вызывает toString()
}
```

### Задания для закрепления

1. **Инкапсулированный термостат:** Создайте класс `Thermostat` с приватным полем `_temperatureCelsius`. Реализуйте геттер температуры в градусах Фаренгейта и сеттер с ограничением диапазона от -50 до +100 °C.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/92250363-235a-43ac-ab04-ffe29fe2656e" />

2. **Счетчик шагов с защитой от скручивания:** Разработайте класс `Pedometer`, где текущие шаги можно только увеличивать через метод `addSteps(int count)`, а уменьшать нельзя.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e14cfdf4-ccfe-42fb-8ecd-86f034009b93" />

3. **Модель книги с расчетом времени чтения:** Класс `Book` содержит поля названия и числа страниц. Добавьте вычисляемый геттер `estimatedReadingHours`, считающий время из расчета 1 страница в 2 минуты.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e5a00b0b-8cc1-4b07-af14-8afcfd4bc96d" />

4. **Валидатор пароля в сеттере:** Класс `UserCredentials` с приватным паролем `_passwordHash`. Сеттер принимает сырую строку, проверяет ее длину (\(\ge 8\) символов) и сохраняет псевдо-хэш.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d06db0ab-8e01-4377-92ff-009440c3bbee" />

5. **Статический счетчик экземпляров:** Добавьте в класс `DeviceSession` статическое поле `static int activeSessionsCount`, которое увеличивается при создании каждого нового объекта.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fd2c1538-f3b4-4d89-b49b-923bed1d1443" />


---

## Тема 2. Все виды конструкторов: Named, Initializer List, Factory, Const

Dart предоставляет одну из самых выразительных систем конструкторов среди современных языков программирования.

### Сравнительная таблица конструкторов

| Тип конструктора         | Синтаксис                              | Назначение                                   | Flutter-контекст                          |
| :----------------------- | :------------------------------------- | :------------------------------------------- | :---------------------------------------- |
| **Генеративный**         | `User(this.name, this.age);`           | Быстрое связывание параметров с полями       | Инициализация State-объектов              |
| **Именованный**          | `User.anonymous() : ...;`              | Несколько способов создания объекта          | `EdgeInsets.all()`, `Color.fromARGB()`    |
| **Список инициализации** | `User(...) : _id = uuid(), super(...)` | Вычисление полей до тела конструктора        | Валидация `assert`, инициализация `final` |
| **Константный**          | `const User(this.name);`               | Создание объектов во время компиляции        | Неизменяемые виджеты (`const Text()`)     |
| **Фабричный**            | `factory User.fromJson(...)`           | Возврат кэшированного экземпляра или подтипа | Парсинг REST API, синглтоны               |

### Пример кода

```dart
class UserModel {
  final int id;
  final String name;
  final String role;
  final DateTime createdAt;

  // 1. Основной генеративный конструктор с именованными аргументами
  const UserModel({
    required this.id,
    required this.name,
    this.role = "User",
    required this.createdAt,
  });

  // 2. Именованный конструктор: Гостевой пользователь
  UserModel.guest()
      : id = 0,
        name = "Гость",
        role = "Guest",
        createdAt = DateTime.now(); // Список инициализации

  // 3. Фабричный конструктор: Парсинг JSON ответа сервера
  factory UserModel.fromJson(Map<String, dynamic> json) {
    if (!json.containsKey("id") || !json.containsKey("name")) {
      throw FormatException("Некорректный JSON пользователя");
    }
    return UserModel(
      id: json["id"] as int,
      name: json["name"] as String,
      role: json["role"] as String? ?? "User",
      createdAt: DateTime.parse(json["createdAt"] as String),
    );
  }

  // 4. Фабричный конструктор с кэшированием (Паттерн Singleton/Multiton)
  static final Map<int, UserModel> _cache = {};
  factory UserModel.cached(int id, String name) {
    return _cache.putIfAbsent(
      id,
      () => UserModel(id: id, name: name, createdAt: DateTime.now()),
    );
  }
}
```

### Задания для закрепления

1. **Именованные конструкторы цвета:** Создайте класс `AppColor` с конструкторами `AppColor.fromHex(String hex)` и `AppColor.fromRGBO(int r, int g, int b, double o)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/87a37db8-bafe-4523-a588-7d12376ef5e0" />

2. **Константная точка на плоскости:** Создайте класс `const Point2D(this.x, this.y)`. Создайте два идентичных константных объекта и убедитесь, что `identical(p1, p2) == true`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/16704dc8-f9e8-4528-92c7-47eccd4ee6a1" />

3. **Список инициализации с assert:** Создайте класс `Rectangle` с полями ширины и высоты. В списке инициализации добавьте проверку `assert(width > 0 && height > 0)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/21b3385a-e32d-4aca-8161-f11198127112" />

4. **Фабричный синглтон сетевого клиента:** Реализуйте класс `ApiClient` с фабричным конструктором `factory ApiClient()`, гарантированно возвращающим один и тот же приватный экземпляр.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c5c70b98-6c6c-4a2b-916c-cab9c69f1e47" />

5. **Редирект конструктора (Redirecting constructor):** Напишите класс `Circle(this.radius)` и именованный конструктор `Circle.unit() : this(1.0);`, перенаправляющий вызов на основной.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/06f32951-b448-4d82-b3ba-b660a2eb7a23" />


---

## Тема 3. Наследование (extends), полиморфизм и super

Наследование позволяет создавать иерархии типов, переиспользуя логику базовых классов.

```
                     ┌────────────────────────┐
                     │   UiWidget (Базовый)   │
                     ├────────────────────────┤
                     │ + id: String           │
                     │ + render(): void       │
                     └───────────┬────────────┘
                                 │ extends
         ┌───────────────────────┴───────────────────────┐
         ▼                                               ▼
┌──────────────────┐                           ┌──────────────────┐
│   TextWidget     │                           │   ButtonWidget   │
├──────────────────┤                           ├──────────────────┤
│ + text: String   │                           │ + onClick: Void  │
│ + render(): void │                           │ + render(): void │
└──────────────────┘                           └──────────────────┘
```

### Пример кода

```dart
// Базовый класс компонента интерфейса
abstract class BaseScreen {
  final String routeName;
  final bool requiresAuth;

  BaseScreen({required this.routeName, this.requiresAuth = false});

  void onInit() {
    print("[$routeName] Инициализация базовых ресурсов экрана");
  }

  void render();
}

// Дочерний класс экрана настроек
class SettingsScreen extends BaseScreen {
  final bool isDarkModeEnabled;

  SettingsScreen({required this.isDarkModeEnabled})
      : super(routeName: "/settings", requiresAuth: true); // Вызов конструктора предка

  @override
  void onInit() {
    super.onInit(); // Вызов базовой логики
    print("[SettingsScreen] Загрузка локальных предпочтений темы");
  }

  @override
  void render() {
    print("Отрисовка экрана настроек (Темная тема: $isDarkModeEnabled)");
  }
}
```

### Задания для закрепления

1. **Иерархия банковских карт:** Создайте базовый класс `Card` и наследников `DebitCard` (без овердрафта) и `CreditCard` (с лимитом овердрафта).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0541b7ba-204e-477c-8fe3-49e7868a6ade" />

2. **Полиморфный список виджетов:** Создайте список `List<BaseScreen>` и в цикле вызовите метод `render()` для каждого элемента независимо от конкретного подтипа.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/db184867-db25-4450-98b1-cbf08ee23f64" />

3. **Использование super-параметров (Dart 2.17+):** Перепишите конструктор наследника с использованием компактного синтаксиса `SubClass({required super.id, super.title});`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b5ba43de-1f4b-4df0-b0ad-1cfdd5986c64" />

4. **Ковариантный параметр метода (`covariant`):** Покажите пример сужения типа аргумента в переопределенном методе подкласса с помощью ключевого слова `covariant`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/335c9105-a45f-4a07-bdc0-5ed506e97763" />

5. **Предотвращение переопределения через `@nonVirtual`:** Создайте метод критической проверки лицензии в базовом классе и запретите его изменение в наследниках.

---

## Тема 4. Абстрактные классы, неявные интерфейсы (implements) и Sealed-классы

### Разница между `extends` и `implements`

| Критерий         | `extends` (Наследование)   | `implements` (Реализация интерфейса)                |
| :--------------- | :------------------------- | :-------------------------------------------------- |
| **Количество**   | Строго один класс          | Множество классов через запятую                     |
| **Тела методов** | Наследуются по умолчанию   | **Не наследуются!** Обязательно переопределить всё  |
| **Поля**         | Наследуются поля и геттеры | Поля воспринимаются как контракты геттеров/сеттеров |

### Sealed-классы (Изолированные иерархии в Dart 3+)

Модификатор `sealed` гарантирует, что все возможные наследники класса объявлены **в том же самом файле**. Это позволяет компилятору проверять исчерпываемость выражений `switch` (Exhaustiveness checking) без необходимости ветки `default`.

### Пример кода (Архитектура состояния экрана / MVI / BLoC)

```dart
// Изолированное состояние экрана каталога товаров
sealed class CatalogState {}

class CatalogLoadingState extends CatalogState {}

class CatalogDataState extends CatalogState {
  final List<String> products;
  CatalogDataState(this.products);
}

class CatalogErrorState extends CatalogState {
  final String errorMessage;
  CatalogErrorState(this.errorMessage);
}

// Рендерер UI с исчерпывающим сопоставлением с шаблонами
String renderStateUi(CatalogState state) {
  // Компилятор гарантирует, что рассмотрены ВСЕ возможные состояния!
  return switch (state) {
    CatalogLoadingState() => "Показ спиннера загрузки...",
    CatalogDataState(:final products) => "Каталог: найдено ${products.length} товаров",
    CatalogErrorState(:final errorMessage) => "Ошибка: $errorMessage (Показать кнопку повтора)",
  };
}
```

### Задания для закрепления

1. **Контракт локального кэша:** Объявите интерфейс `StorageService` с методами `save(String key, String val)`, `read(String key)`, `delete(String key)`. Реализуйте класс `MemoryStorageService` через `implements`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/14ec13ad-a17f-4744-bbd6-dbdb65b6a17c" />

2. **Множественная реализация интерфейсов:** Создайте класс `SmartTv`, реализующий сразу два несвязанных интерфейса: `Displayable` и `NetworkConnectable`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ce29c6f5-f475-4843-93af-1f6c9561cf1d" />

3. **Sealed-иерархия авторизации:** Создайте `sealed class AuthState` с наследниками `Unauthenticated`, `Authenticating`, `Authenticated(User user)` и `AuthFailed(String reason)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a33e39e1-f86a-4c7e-acf4-8d375126275e" />

4. **Модификатор `base class`:** Создайте класс `base class Engine`, гарантирующий, что сторонние библиотеки смогут только наследовать его (`extends`), но не смогут реализовать его как чистый интерфейс (`implements`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d1f0b975-eeb9-48e5-91c3-0145c8e2b701" />

5. **Модификатор `interface class`:** Создайте чистый интерфейсный контракт, исключающий прямое наследование реализации вне родной библиотеки.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/08994ecf-b648-4eff-9224-78e04baade55" />



---

## Тема 5. Миксины (Mixins): композиция поведения через with и on

Миксин (`mixin`) — это блок кода, содержащий поля и методы, который можно подмешать в класс без использования классического наследования.

### Синтаксис и правила

- Объявляется ключевым словом `mixin Name { ... }`.
- Подмешивается к классу оператором `with Name`.
- Миксины не могут объявлять генеративные конструкторы (их нельзя создать через `new` или `Name()`).
- Ограничение применимости: `mixin Name on TargetClass` означает, что подмешать данный миксин можно **только** в класс, являющийся наследником `TargetClass`.

### Пример кода

```dart
// 1. Простой миксин логирования жизненного цикла
mixin LoggingMixin {
  void logAction(String action) {
    print("[LOG - ${DateTime.now().toIso8601String()}] Действие: $action");
  }
}

// 2. Миксин валидации с ограничением на класс формы
abstract class FormElement {
  String get tag;
}

mixin ValidationMixin on FormElement {
  bool validateNotNull(dynamic value) {
    if (value == null) {
      print("Элемент $tag не прошел проверку: поле обязательно!");
      return false;
    }
    return true;
  }
}

// 3. Класс поля ввода, объединяющий базовый класс и оба миксина
class EmailInput extends FormElement with LoggingMixin, ValidationMixin {
  @override
  final String tag = "EmailField";

  void onUserSubmitted(String? email) {
    logAction("Пользователь нажал Submit");
    if (validateNotNull(email)) {
      logAction("Email валиден: $email");
    }
  }
}
```

### Задания для закрепления

1. **Миксин сериализации в JSON:** Создайте миксин `JsonPrintable`, добавляющий любому классу метод `printAsJson()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/75203591-4244-4c8c-b641-c2a3b55302a9" />

2. **Ограничение миксина на ChangeNotifier:** Создайте миксин `AutoDisposer on ChangeNotifier`, добавляющий флаг `bool isDisposed` и проверяющий его в методах.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4e732410-9dd2-44a8-b388-d7b8931486b9" />

3. **Аналитический трекер кликов:** Напишите миксин `AnalyticsTracker` с методом `trackEvent(String eventName)` и примените его к классу кнопки `CustomButton`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ebd1af66-f6c9-45c8-98cc-03be8b814f6a" />

4. **Порядок линеаризации миксинов:** Создайте класс с двумя миксинами, реализующими одинаковый метод `execute()`. Покажите на практике, метод какого миксина выполнится первым (принцип правого приоритета).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/16606a16-c677-4898-a5b6-c42911eed947" />

5. **Миксин кэширования вычислений:** Создайте миксин `MemoizationMixin`, сохраняющий результаты тяжелых математических функций в словарь по строковому ключу аргументов.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a45def1a-dc64-4846-a046-ec8f0da70fb4" />


---

## 50 Смешанных заданий по ООП

Задания моделируют архитектурные сценарии коммерческих Flutter-приложений (State Management, Networking, UI Layouts, Analytics, Security).

### Блок 1: Модели данных, DTO и конструкторы

1. **Неизменяемая карточка товара (Immutable Product):** Создайте константную модель товара с обязательными полями, расчетом цены со скидкой и валидацией неотрицательной стоимости в списке инициализации.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/48ce66e0-5415-4293-bf0f-7028df081218" />

2. **Парсер профиля пользователя с безопасными дефолтами:** Реализуйте конструктор `User.fromJson(Map<String, dynamic> json)` с обработкой отсутствующих полей и защитой от `TypeError`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4b293f5d-04c6-42db-8344-f73e64200263" />

3. **Multiton локалей приложения:** Спроектируйте класс `AppLocale` с фабричным конструктором, возвращающим единственный экземпляр для каждого языка: `AppLocale("ru")`, `AppLocale("en")`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/12aa5746-3c49-4bc8-8a32-76186a4ea817" />

4. **Модель геоточки с парсингом строки:** Класс `GeoPoint` с именованным конструктором `GeoPoint.fromString("55.751244, 37.618423")`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4ad14558-8823-49fb-be00-88bfc7684721" />

5. **Конструктор визитки из сущности БД:** Напишите фабричный конструктор `ContactCard.fromDatabaseRecord(Map<String, Object?> row)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b0f54619-f07b-4964-8aeb-58fe1a09354b" />




6. **Клонирование с изменением полей (copyWith):** Добавьте метод `UserModel copyWith({String? name, String? avatarUrl})` для неизменяемой модели.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/db0d8e59-dd9d-4f0f-b95a-f3087d03282d" />

7. **Конструктор диапазона дат:** Класс `DateRange(this.start, this.end)` со списком инициализации, выбрасывающим `AssertionError`, если дата окончания раньше даты начала.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b7c33a7c-f0f2-4df2-93ee-70c0c0550f84" />

8. **DTO банковской транзакции:** Класс `TransactionDto` с именованными конструкторами для пополнения (`TransactionDto.deposit`), списания (`TransactionDto.withdrawal`) и перевода (`TransactionDto.transfer`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ee269548-f80d-4192-8fb8-2fd7004e6ce7" />

9. **Парсер конфигурации окружения (Environment):** Фабричный конструктор `EnvConfig.fromEnvironment()`, читающий флаги через `const String.fromEnvironment()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ede85e11-180e-43c0-9f13-df1fa62b3803" />

10. **Кастомный оператор эквивалентности:** Переопределите оператор `==` и метод `hashCode` для класса `CartItem` на основе совпадения полей `productId` и `selectedSize`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f0e1da8c-c9fb-42b8-bdfd-d0509d1ee7e1" />




### Блок 2: Наследование и иерархия UI-компонентов

11. **Иерархия системных уведомлений:** Базовый класс `InAppNotification` (таймаут, заголовок) и наследники `SuccessNotification`, `WarningNotification`, `CriticalAlertNotification` с разным цветом и иконкой.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/af47ee4e-54bb-4fc9-8888-ade803b52522" />



12. **Компоненты диалоговых окон:** Базовый класс `BaseDialog` с шаблоном отрисовки шапки и подвала, и наследник `ConfirmActionDialog` с кнопками «Подтвердить» и «Отмена».<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7ee0a35b-21f1-4aaa-8951-c9605302f688" />

13. **Иерархия медиаплееров:** Базовый класс `MediaPlayer` (методы `play()`, `pause()`, `seek()`) и специализированные наследники `AudioPlayer` и `HlsVideoPlayer`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2b65987a-7d9b-4579-a215-250192c96220" />

14. **Модели сенсоров смартфона:** Базовый класс `HardwareSensor` и подклассы `AccelerometerSensor` (оси X, Y, Z) и `BarometerSensor` (давление в гПа).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ad631609-643d-48d3-afc4-6b27d753291e" />

15. **Система тарифов подписки:** Базовый класс `SubscriptionPlan` и наследники `MonthlyPlan`, `AnnualDiscountPlan` (скидка 20%) и `FamilyPlan` (множитель цены за участников).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/60268b69-5f6a-4a7b-8582-adfe75593358" />


16. **Слои графического редактора:** Базовый класс `CanvasLayer` (прозрачность, видимость, z-index) и подклассы `VectorLayer`, `RasterBitmapLayer`, `TextLayer`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9bb0f178-1dc2-468f-91e7-cddef2fb224d" />

17. **Иерархия платежных шлюзов:** Базовый класс `PaymentGateway` с защищенным методом логирования и наследники `SbpGateway` (Система быстрых платежей) и `BankCardGateway`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0d64b2fa-963e-4330-be8d-a57d7cad61b4" />

18. **Экраны онбординга:** Базовый экран обучающего тура с кнопкой пропуска и конкретные шаги с уникальными иллюстрациями.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/17f244a7-2524-4789-8408-f4ee24008aa0" />

19. **Кастомная кнопка с состояниями:** Базовый класс `WidgetButton` и подкласс `AsyncLoadingButton`, отображающий индикатор загрузки при активном асинхронном действии.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/eae94480-4f27-49e3-8bee-357408535d2b" />

20. **Иерархия логгеров приложения:** Базовый класс `AppLogger` и подклассы `ConsoleLogger` (форматированный вывод в терминал) и `FileCrashLogger` (запись на диск).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/28deb8f6-d82d-4b21-85d6-a670d3f78a6d" />


### Блок 3: Абстрактные контракты, интерфейсы и репозитории

21. **Репозиторий каталога товаров (Repository Pattern):** Объявите интерфейс `ProductRepository` с методами `Future<List<Product>> getAll()`, `Future<Product> getById(int id)` и создайте две реализации: `NetworkProductRepository` и `MockProductRepository`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8bea3225-8098-4ea9-b71c-2b6dfe6f73d6" />

22. **Интерфейс биометрической аутентификации:** Спроектируйте интерфейс `BiometricAuthService` с методами `canAuthenticate()` и `authenticate()`. Напишите реализацию-заглушку для тестов.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/31f26eea-3d05-4174-934f-a1485f8b5fe1" />

23. **Шифрование пользовательских данных:** Интерфейс `CryptoService` с методами `encrypt(String plainText)` и `decrypt(String cipherText)`. Реализуйте класс `AesCryptoService`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1e1164ba-ed4f-4850-bdd7-2ddd784d7079" />

24. **Контракт аналитического провайдера:** Интерфейс `AnalyticsProvider` с методом `logEvent(String name, Map<String, Object> params)`. Реализуйте множественную отправку через класс `CompositeAnalyticsProvider`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1ffb2184-26ad-4088-ab07-82eb44a3efd0" />


25. **Интерфейс геолокатора:** Спроектируйте контракт получения координат устройства с поддержкой стрима обновлений позиции `Stream<GeoPoint> get positionStream`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e5e82986-ea79-4408-a9cd-1e17faf8a0d4" />



26. **Служба локальных уведомлений:** Интерфейс `NotificationSender` с методами отправки мгновенных и отложенных системных пушей.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/89e0a029-5c82-4e08-a3b7-52bd45e3efbf" />

27. **Контракт синхронизатора баз данных:** Абстрактный класс `DataSyncEngine` с абстрактными методами `fetchRemoteDelta()` и `applyLocalChanges()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b4c63d9e-bc97-4a8b-91f0-2d6b99c8bfa1" />

28. **Провайдер сетевого статуса:** Интерфейс `ConnectivityService` с геттером `bool isOnline` и стримом событий потери интернета.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bc6c3199-d6e2-4609-9172-3c5d72b927d0" />

29. **Хранилище ключ-значение (KeyValueStorage):** Спроектируйте интерфейс с обобщенными методами `T? read<T>(String key)` и `Future<void> write<T>(String key, T value)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c882076d-2b0e-4504-9a4f-8c30dc3c0fcc" />

30. **Интерфейс экспортера отчетов:** Контракт `ReportExporter` с реализациями `PdfReportExporter` и `ExcelReportExporter`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/92f8a229-2451-475f-b868-650953d8ebf7" />


### Блок 4: Миксины и повторное использование функционала

31. **Миксин дебаунса событий (Anti-Spam Click):** Миксин `DebounceMixin`, предотвращающий повторный вызов одного и того же действия чаще чем раз в 500 мс.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/aaad9ba0-832e-49c2-b496-cc6722d60b0c" />

32. **Миксин восстановления состояния экрана (RestorationMixin):** Миксин, автоматически сохраняющий текстовые поля ввода при сворачивании приложения.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bf419577-70f9-4790-9bac-8d743efb0591" />

33. **Миксин проверки сетевого соединения:** Миксин `NetworkAwareMixin`, внедряющий в презентер экрана проверку доступности интернета перед выполнением любого запроса.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/33a0d1f7-8fa3-43e5-a253-d4dc5b2b662e" />



34. **Миксин форматирования цен и валют:** Миксин `CurrencyFormatterMixin`, предоставляющий готовый метод преобразования чисел в красивый денежный формат.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8b2beba9-f735-4fdf-a81e-13d7290af31c" />

35. **Миксин таймера автообновления:** Миксин `PeriodicRefreshMixin`, запускающий периодический опрос сервера каждые 30 секунд при активном экране.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c9301b81-28a9-4107-a67b-4869be95e70e" />


36. **Миксин валидации паспортных данных:** Миксин `PassportValidationMixin` с методами строгой проверки серии, номера и кода подразделения.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ef129932-3a2f-4cb9-ab09-ec5d9061da11" />

37. **Миксин анимации затухания (FadeAnimationController):** Подмешиваемый контроллер управления прозрачностью элемента от 0.0 до 1.0.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c168180c-66de-4884-8a1b-5c2f31d265eb" />

38. **Миксин переключения темы:** Миксин, предоставляющий экрану быстрый доступ к текущей цветовой палитре (темная/светлая).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d0c9d01d-b763-4407-8e70-fa8e2cec5a00" />

39. **Миксин глубоких ссылок (DeepLinkHandlerMixin):** Миксин, парсящий входящие URI-схемы приложения и открывающий целевые экраны.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/890e827e-8155-418f-a1f1-44d3ba2fe4ef" />

40. **Миксин аудита действий администратора:** Подмешиваемый модуль, отправляющий критические действия модератора на выделенный сервер безопасности.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a8461c2c-9b61-4c27-a050-b4f166b5b587" />


### Блок 5: Современные модификаторы классов и архитектурные стейты

41. **Sealed-состояние корзины интернет-магазина:** Создайте `sealed class CartState` с состояниями: `CartEmpty`, `CartLoading`, `CartLoaded(List<Item> items, double total)`, `CartCheckoutError(String error)`. Напишите функцию обработки состояний через исчерпывающий `switch`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ecc59376-cee5-4881-86b0-20a91ffa3a01" />

42. **Sealed-иерархия сетевого ответа (Result Pattern):** Реализуйте `sealed class Result<T>` с подтипами `Success<T>(final T data)` и `Failure<T>(final Exception exception)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6ddf8f73-ee0f-453e-925d-5c71ce0dab6a" />

43. **Архитектура UI-событий экрана логина:** Создайте `sealed class LoginEvent` с событиями `LoginEmailChanged(String email)`, `LoginPasswordChanged(String pass)`, `LoginSubmitted`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/603bf663-27f4-4f42-9ceb-5934c27876ba" />

44. **Модификатор `final class` для безопасности криптоключа:** Объявите класс `final class EncryptionKey`, запретив любое его расширение или реализацию вне текущей библиотеки.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/13a86f9a-f545-48fa-88d1-7bba4932c2ee" />

45. **Модификатор `interface class` для публичного API SDK:** Разработайте интерфейсный класс платежного терминала, запретив прямое наследование логики клиентами библиотеки.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/45b0fcd1-aaa6-4373-b6ea-473adbc985b5" />

46. **Исчерпывающий роутинг экрана через Sealed Route:** Спроектируйте `sealed class AppRoute` для типобезопасной навигации во Flutter.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d9231dd2-476b-436c-9a36-38f16dec7e7e" />

47. **Паттерн «Команда» (Command Pattern):** Создайте абстрактный класс `Command` с методом `execute()` и `undo()` для реализации функции отмены действий в редакторе текста.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5a8e3dcf-57aa-44c3-8cda-d1ce41446fe8" />

48. **Паттерн «Строитель» (Builder):** Реализуйте `NotificationBuilder` с цепочечными вызовами `.setTitle().setBody().setIcon().build()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2518aca3-5920-460c-8be6-ad00761e99f2" />

49. **Паттерн «Наблюдатель» (Observer / EventBus):** Создайте легковесную шину событий с классами событий и методами подписки/отписки.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c232053e-d725-4541-995d-f8ed0773c5dd" />

50. **Комплексный архитектурный модуль заказа такси (Production Case):**
    - Спроектируйте `sealed class TaxiOrderState` (поиск машины, машина назначена, поездка, завершено, отменено).
    - Создайте класс заказа `TaxiOrder` с именованными фабричными конструкторами и списком инициализации.
    - Реализуйте миксины `LocationTrackingMixin` и `FareCalculationMixin`.
    - Напишите контроллер управления поездкой с исчерпывающим сопоставлением с шаблонами.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4b5263d3-39fa-4c82-9dee-33fada9a4b26" />


---
