---
title: "Практическая работа №7: Основные типы данных и  безопасность типов в Dart"
discipline: "Разработка мобильных приложений"
tags:
  [
    dart,
    flutter,
    data-types,
    sound-null-safety,
    collections,
    records,
    pattern-matching,
  ]
---

# Практическая работа: Основные типы данных в языке Dart (Flutter Foundation)

> [!NOTE]
> **Цель работы:** Освоить систему типов языка Dart в контексте кроссплатформенной разработки на Flutter. Изучить числа (`int`, `double`, `num`), строки (`String`), логический тип (`bool`), базовые коллекции (`List`, `Set`, `Map`), механизм  безопасности null (Null Safety), а также современные возможности Dart 3+ (Records, Pattern Matching, Collection if/for).

---

## 📋 Содержание

1. [Архитектура системы типов Dart и Null Safety](#1-архитектура-системы-типов-dart-и-null-safety)
2. [Тема 1. Числовые типы: int, double, num и математические операции](#тема-1-числовые-типы-int-double-num-и-математические-операции)
3. [Тема 2. Строки (String), интерполяция и кодовые точки (Runes)](#тема-2-строки-string-интерполяция-и-кодовые-точки-runes)
4. [Тема 3. Логический тип (bool) и строгость условий](#тема-3-логический-тип-bool-и-строгость-условий)
5. [Тема 4. Списки (List): списковые включения и spread-операторы](#тема-4-списки-list-списковые-включения-и-spread-операторы)
6. [Тема 5. Множества (Set) и Словари (Map): парсинг JSON-структур](#тема-5-множества-set-и-словари-map-парсинг-json-структур)
7. [Тема 6. Null, Records (кортежи) и Pattern Matching](#тема-6-null-safety-records-кортежи-и-pattern-matching)
8. [50 Смешанных заданий по обработке данных (Flutter Logic Focus)](#50-смешанных-заданий-по-обработке-данных)

---

## 1. Архитектура системы типов Dart и  Null Safety

Dart — строго типизированный язык со статической проверкой типов. Благодаря механизму **Null Safety** (безопасности типов) переменные по умолчанию не могут принимать значение `null`, если они явно не объявлены как допускающие null (Nullable).

### Иерархия объявления переменных

```
                  ┌────────────────────────┐
                  │      Object? (Top)     │
                  └───────────┬────────────┘
                              │
               ┌──────────────┴──────────────┐
               ▼                             ▼
        ┌─────────────┐               ┌─────────────┐
        │   Object    │               │    Null     │
        └──────┬──────┘               └─────────────┘
   ┌───────────┼───────────┬───────────┐
   ▼           ▼           ▼           ▼
┌─────┐    ┌────────┐  ┌───────┐  ┌────────┐
│ num │    │ String │  │ bool  │  │ List<T>│ ...
└──┬──┘    └────────┘  └───────┘  └────────┘
 ┌─┴──────┐
 ▼        ▼
int     double
```

### Модификаторы объявления

| Ключевое слово | Назначение                      | Время инициализации               | Изменяемость значения                         |
| :------------- | :------------------------------ | :-------------------------------- | :-------------------------------------------- |
| `var`          | Автовывод типа компилятором     | В момент выполнения               | Можно изменять значение того же типа          |
| `final`        | Неизменяемая константа          | В рантайме (при первом обращении) | Нельзя переприсвоить                          |
| `const`        | Константа времени компиляции    | При компиляции программы          | Нельзя переприсвоить; глубокая неизменяемость |
| `late`         | Отложенная инициализация        | До первого чтения переменной      | Можно (если не указан `final`)                |
| `dynamic`      | Отключение статической проверки | В рантайме                        | Допускает присвоение любых типов              |

---

## Тема 1. Числовые типы: int, double, num и математические операции

В Dart числа представлены двумя основными подтипами абстрактного класса `num`:

- `int` — целые 64-битные числа на мобильных платформах (нативного компилятора AOT).
- `double` — 64-битные числа с плавающей точкой (стандарт IEEE 754).
- `num` — общий предок; используется, когда переменная может быть как целым, так и дробным числом.

### Пример кода

```dart
void main() {
  int screenWidthPx = 1080;
  double devicePixelRatio = 2.625;

  // Безопасный парсинг строк
  String rawInput = "450.50";
  double? parsedPrice = double.tryParse(rawInput);

  // Округление и форматирование
  double itemPrice = 129.897;
  print("Округление до целого: ${itemPrice.round()}"); // 130
  print("Округление вниз: ${itemPrice.floor()}");       // 129
  print("Округление вверх: ${itemPrice.ceil()}");        // 130
  print("До 2 знаков: ${itemPrice.toStringAsFixed(2)}"); // "129.90"

  // Целочисленное деление (~/) и остаток (%)
  int totalMinutes = 135;
  int hours = totalMinutes ~/ 60; // 2 часа
  int minutes = totalMinutes % 60; // 15 минут
  print("Время: $hours ч. $minutes мин.");
}
```

### Задания для закрепления

1. **Конвертер валют:** Напишите функцию, принимающую сумму в рублях (`int`) и курс доллара (`double`), возвращающую строку с результатом, отформатированным строго до 2 знаков после запятой.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0b38041c-944a-4c3a-a236-e2a13acb226b" />

2. **Безопасный калькулятор возраста:** Примите ввод пользователя в виде `String`. Используя `int.tryParse()`, верните возраст или `-1`, если пользователь ввел некорректный текст.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2d9f5a1b-d710-40fa-bcdb-abd965b82ec0" />

3. **Расчет плотности пикселей (DP):** Реализуйте формулу `dp = px / density` с использованием оператора целочисленного или дробного деления.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/885fb4a3-61fa-48b4-9e18-39d2b9875045" />

4. **Побитовые флаги сенсоров:** Используя побитовые операторы `&`, `|` и `<<`, закодируйте состояние трех мобильных датчиков в одну переменную `int`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/40697b4e-23aa-4a01-a0af-bee1c2419840" />

5. **Проверка четности без остатка от деления:** Проверьте, является ли целое число четным, используя побитовое умножение `(number & 1) == 0`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bb91e2a5-ae82-49eb-bd6c-45b2332260ee" />


---

## Тема 2. Строки (String), интерполяция и кодовые точки (Runes)

Строка `String` в Dart представляет собой неизменяемую (immutable) последовательность символов в кодировке UTF-16.

### Особенности работы со строками

- Интерполяция: `$variable` для простых переменных и `${expression}` для выражений.
- Многострочные литералы: тройные кавычки `'''многострочный текст'''`.
- Raw-строки: `r'C:\flutter\bin'` (символ обратного слэша не экранируется).
- Кодовые точки (Runes): используются для корректной обработки 32-битных эмодзи и спецсимволов.

### Пример кода

```dart
void main() {
  String appTitle = "Marketplace";
  double discount = 0.15;

  // Интерполяция выражений
  String promoBanner = "В приложении $appTitle скидка составляет ${(discount * 100).toInt()}%!";
  print(promoBanner);

  // Методы трансформации
  String rawPhone = "  +7 (999) 000-11-22 \n";
  String cleanPhone = rawPhone.trim().replaceAll(RegExp(r'[^0-9+]'), '');
  print("Чистый номер: $cleanPhone"); // "+79990001122"

  // Дополнение нулями (Padding)
  int receiptNumber = 42;
  String formattedReceipt = receiptNumber.toString().padLeft(6, '0');
  print("Чек: #$formattedReceipt"); // "#000042"

  // Работа с Runes (эмодзи)
  String statusMessage = "Статус заказа: Доставлен 🚀";
  print("Длина в UTF-16: ${statusMessage.length}");
  print("Количество символов (Runes): ${statusMessage.runes.length}");
}
```

### Задания для закрепления

1. **Маскирование email:** Напишите функцию, преобразующую `ivanov.flutter@gmail.com` в `i************r@gmail.com`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3b022881-ec4a-4922-b208-795f0c1ea66a" />

2. **Валидатор префикса ссылки:** Проверьте, начинается ли переданная строка с безопасного протокола `https://` с помощью метода `.startsWith()`<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fd1eb035-0e41-4d19-9ff7-b62a0cf9edb0" />


3. **Генератор инициалов (Monogram):** Примите строку из двух слов («Александр Пушкин») и верните заглавные буквы «АП»..<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f3de84cf-9b8c-46a4-9378-9f5ed357346f" />

4. **Разбор CSV-строки корзины:** Разбейте строку вида `"Яблоко,3,150.5"` методом `.split(',')` и извлеките наименование, количество и стоимость.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a1216025-360e-4baa-8d7f-137df2d1dff9" />

5. **Подсчет реальной длины строки с эмодзи:** Напишите утилиту, которая считает точное количество символов в тексте с комбинированными эмодзи (через `.runes`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/16d245a6-5415-4589-b2f2-794e76005871" />


---

## Тема 3. Логический тип (bool) и строгость условий

В отличие от JavaScript, Python или C++, в Dart **нет концепции "truthy/falsy" значений**.

```dart
// В JavaScript: if ("hello") { ... } сработает!
// В Dart: ошибка компиляции! Тип String не может быть приведен к bool.
```

### Пример кода

```dart
void main() {
  bool isAuthorized = true;
  bool hasActiveSubscription = false;
  int userBalance = 0;

  // Строгая проверка логического выражения
  bool canWatchContent = isAuthorized && hasActiveSubscription;

  // Обязательное явное сравнение чисел с нулем
  bool hasMoney = userBalance > 0;

  // Тернарный оператор для выбора стиля UI во Flutter
  String buttonText = isAuthorized ? "В личный кабинет" : "Войти в систему";
  print(buttonText);
}
```

### Задания для закрепления

1. **Проверка права доступа к экрану:** Сформируйте булево выражение: пользователь авторизован, подтвердил email и не находится в списке заблокированных<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f3e405d5-36bd-4243-84d0-a9d4f405d7b3" />


2. **Определитель пустоты строки:** Напишите функцию `bool isBlank(String? input)`, возвращающую `true`, если строка равна `null`, пуста или состоит только из пробелов.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/53200cee-1538-4588-b708-16316c1d838e" />

3. **Тоггл состояния (Инвертор):** Напишите функцию, переключающую флаг темной темы `isDarkMode` на противоположное значение.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8061f815-9c7b-4545-bfab-1d92d3371baf" />

4. **Валидатор возраста для аренды авто:** Проверьте совокупность условий: возраст \(\ge 21\) года И стаж вождения \(\ge 2\) лет.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/62c7a1ce-abad-4d27-9953-c9a1d922f573" />

5. **Строгая фильтрация коллекций:** Напишите проверку, что баланс пользователя строго положителен, избегая неявных сравнений. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6568cb6f-6d28-4398-88c2-accaf0094106" />


---

## Тема 4. Списки (List): списковые включения и spread-операторы

`List<E>` — упорядоченная коллекция элементов с доступом по целочисленному индексу. В экосистеме Flutter списки служат основой для построения виджетов `Column`, `Row` и `ListView`.

### Специальные возможности списков в Dart

- **Spread-оператор (`...` и `...?`):** Вставка элементов одной коллекции внутрь другой.
- **Collection `if`:** Условное включение виджета или элемента в список.
- **Collection `for`:** Генерация элементов списка на основе итерации.

### Пример кода

```dart
void main() {
  bool isAdmin = true;
  List<String>? optionalBadges = ["PRO", "TOP"];

  // Использование Collection if, Collection for и Spread-оператора
  List<String> menuItems = [
    "Главная",
    "Профиль",
    if (isAdmin) "Панель администратора",
    ...?optionalBadges,
    for (int i = 1; i <= 3; i++) "Бонус #$i",
  ];

  print(menuItems);

  // Функциональные методы обработки
  List<int> rawScores = [45, 80, 12, 95, 60];

  // Цепочка фильтрации и трансформации (Iterable)
  List<String> passedResults = rawScores
      .where((score) => score >= 60)
      .map((score) => "Сдал: $score баллов")
      .toList();

  print(passedResults);

  // Агрегация через fold
  int totalScore = rawScores.fold(0, (prev, element) => prev + element);
  print("Сумма баллов: $totalScore");
}
```

### Задания для закрепления

1. **Удаление дубликатов с сохранением порядка:** Дан список `List<int>` с повторами. Верните список уникальных элементов через `.toSet().toList()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/30271c37-619c-4e7b-af37-fe1b80b56250" />

2. **Пагинация среза (Slice):** Реализуйте метод `getPage(List<T> list, int page, int pageSize)`, возвращающий соответствующий подсписок с помощью `.sublist()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1deb3ecf-5a0e-4c56-90d3-2ff7923c3f87" />

3. **Сортировка товаров по цене:** Дан список цен `List<double>`. Отсортируйте его по убыванию с помощью метода `.sort()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/544d6d0e-6c68-4223-b396-142a661bc4a2" />

4. **Поиск первого подходящего элемента:** Найдите в списке строк первое слово длиннее 8 символов с помощью `.firstWhere()`, обработав случай отсутствия через `orElse`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0d293a43-e0c7-4637-80aa-a3dff68e26a6" />

5. **Генератор виджетов через List.generate:** Создайте список строк вида `["Слот 1", "Слот 2", ... "Слот 10"]` с помощью конструктора `List.generate()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/35e190cf-af91-4e4d-a188-c4797f4bab73" />


---

## Тема 5. Множества (Set) и Словари (Map): парсинг JSON-структур

- `Set<E>` — неупорядоченная коллекция уникальных элементов (быстрый поиск за \(O(1)\)).
- `Map<K, V>` — структура хранения пар «ключ-значение». Стандартный тип для десериализации JSON в Flutter — `Map<String, dynamic>`.

### Пример кода

```dart
void main() {
  // 1. Множества (Set) и операции над ними
  Set<String> localTags = {"mobile", "dart", "flutter"};
  Set<String> remoteTags = {"flutter", "android", "ios"};

  Set<String> allTags = localTags.union(remoteTags); // Объединение
  Set<String> commonTags = localTags.intersection(remoteTags); // Пересечение {"flutter"}
  print("Общие теги: $commonTags");

  // 2. Словари (Map): имитация ответа REST API
  Map<String, dynamic> userJson = {
    "id": 101,
    "name": "Алексей",
    "isPremium": true,
    "settings": {
      "theme": "dark",
      "notifications": true,
    },
    "roles": ["developer", "tester"]
  };

  // Безопасное чтение значений
  String userName = userJson["name"] as String? ?? "Неизвестный";
  int userId = userJson["id"] as int;
  bool isDark = userJson["settings"]?["theme"] == "dark";

  print("Пользователь: $userName (ID: $userId), Темная тема: $isDark");

  // Мутация карты
  userJson.putIfAbsent("lastLogin", () => DateTime.now().toIso8601String());
  print("Обновленная карта: $userJson");
}
```

### Задания для закрепления

1. **Подсчет частоты слов:** Дана строка текста. С помощью `Map<String, int>` подсчитайте, сколько раз каждое слово встречается в тексте.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/22766a8f-4c7a-4eeb-9229-259c440b1360" />

2. **Симметрическая разность тегов:** Найдите теги, которые присутствуют только в одном из двух множеств `Set<String>`, но не в обоих одновременно.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b3399588-2521-42bf-bcdb-74ca116d4e8d" />

3. **Безопасное извлечение настроек:** Напишите функцию, извлекающую значение `String` из вложенной `Map<String, dynamic>` с гарантированным возвратом дефолтного значения при отсутствии ключа.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b9404d8a-abb4-415e-a62f-6795eb807e21" />

4. **Трансформация карты (Map Entries):** Преобразуйте `Map<String, double>` с ценами в рублях в карту с ценами в долларах с помощью `.map()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c0e77d04-c6b7-4034-9823-78266c96b024" />

5. **Группировка пользователей по городу:** Дан список карт пользователей. Сгруппируйте их в структуру `Map<String, List<Map<String, dynamic>>>`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0889726a-af4e-48c7-be7c-98ee3ac8e9f8" />


---

## Тема 6. Null Safety, Records (кортежи) и Pattern Matching

### Операторы звуковой безопасности Null (Null Safety)

- `T?` — переменная может содержать значение типа `T` или `null`.
- `?.` (Null-aware access) — вызов метода/свойства только если объект не `null`.
- `??` (If-null operator) — подстановка значения по умолчанию.
- `??=` (Null-aware assignment) — присваивание только если текущее значение равно `null`.
- `!` (Null assertion) — утверждение разработчика, что значение точно не `null` (опасно: при ошибке выбрасывает исключение).

### Кортежи (Records) и Pattern Matching (Dart 3+)

Records позволяют возвращать несколько разнородных значений из функции без создания отдельного класса-модели.

### Пример кода

```dart
// Функция возвращает кортеж из двух значений: статус успеха и сообщение
(bool isSuccess, String message) authenticate(String login, String password) {
  if (login == "admin" && password == "secret") {
    return (true, "Авторизация успешна!");
  }
  return (false, "Неверный логин или пароль");
}

void main() {
  // 1. Деструктуризация Record
  var (success, msg) = authenticate("admin", "secret");
  print("Результат: $success, Сообщение: $msg");

  // 2. Именованный Record
  ({int code, String error}) errorResponse = (code: 404, error: "Страница не найдена");
  print("Код ошибки: ${errorResponse.code}");

  // 3. Pattern Matching со switch-выражением
  int httpStatus = 200;
  String statusDescription = switch (httpStatus) {
    200 => "Успешно",
    >= 400 && < 500 => "Ошибка клиента",
    >= 500 && < 600 => "Ошибка сервера",
    _ => "Неизвестный статус",
  };
  print("Статус: $statusDescription");
}
```

### Задания для закрепления

1. **Координаты экрана через Record:** Напишите функцию, возвращающую кортеж `(double x, double y)` центра экрана смартфона.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/32a74ccf-c509-4203-8b28-e0d89964a6ad" />

2. **Безопасная распаковка профиля:** Дана переменная `String? bio`. Если она `null`, запишите в переменную `displayBio` текст «Информация отсутствует».<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/266348e9-95b2-4feb-8de6-71d3f56851cc" />

3. **Pattern Matching над JSON:** Используя конструкцию `switch (json)`, проверьте формат входящего пакета: валидный объект с `id` или объект с полем ошибки.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4c581c38-727d-4cc6-948f-91296aeda9f6" />

4. **Служебная переменная с `late`:** Объявите переменную `late final double expensiveCalculation`, которая вычисляется только при первом реальном обращении.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/39ab41c6-b085-4add-addd-d028fae95983" />

5. **Безопасный поиск в списке:** Извлеките первый элемент списка `List<int>? numbers` через операторы `?.[0]` и `?? -1`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/090c732b-8973-4f7a-a355-c855c8cf47a5" />


---

## 50 Смешанных заданий по обработке данных

Каждое задание моделирует практическую задачу мобильного приложения на Flutter (UI-стейт, расчеты, форматирование, фильтрация каталога, сериализация).

### Блок 1: Числа, финансы и расчеты корзины (1–10)

1. **Калькулятор скидки с промокодом:** Рассчитайте итоговую стоимость заказа при условии: скидка 15%, но не более 500 рублей.
  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6e381a19-98d1-4c6d-b8ea-3ea562e075ba" />

2. **Форматирование цены по стандарту валют:** Преобразуйте число `15400900.5` в строку `"15 400 900.50 ₽"`.
  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cac17da2-db5b-4009-8f98-2c800a6704a9" />

3. **Расчет кешбэка баллами:** Начислите 1 балл за каждые полные 100 рублей чека, используя оператор целочисленного деления `~/`.
   <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d270e2ae-a263-474c-83f2-2dc1c73e0a29" />

4. **Ограничитель диапазона (Clamp):** Напишите функцию, ограничивающую уровень громкости динамика в диапазоне от `0.0` до `1.0`.
   <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b52fb93e-8cff-4f61-97e0-c3f5186bf803" />

5. **Конвертер байтов в мегабайты:** Преобразуйте размер файла из байтов в МБ с округлением до одного знака после запятой.
    <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/af75cc42-df3b-4fa6-ab80-bcf172deb397" />

6. **Вычисление процентного прогресса:** По текущему шагу `current` и общему числу шагов `total` верните `double` от `0.0` до `1.0`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/eb6d4bed-79e9-4151-9f51-8fdd7355aa47" />

    
7. **Округление до ближайшего десятка:** Округлите переданное число вверх до ближайшего числа, кратного 10 (например, `43` -> `50`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9778b220-68f1-4a70-9401-02fa45d5d3b6" />

8. **Калькулятор стоимости доставки:** Базовая ставка 200 руб. + 30 руб. за каждый километр свыше 5 км.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b6eaffd3-8cd0-42ef-9943-9d0413340957" />

9. **Проверка делимости без остатка:** Определите, делится ли размер скачиваемого пакета ровно на блоки по 1024 байта.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c17fd41e-58cf-4714-bffe-d431a929f593" />

10. **Парсер диапазона цен:** Извлеките минимальную и максимальную цену из строки `"1500 - 4500 руб"`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ee78b81e-4637-44de-9b07-1ac165f8267f" />


### Блок 2: Строки, валидация полей и парсинг текста (11–20)

11. **Валидатор пароля на регулярных выражениях:** Проверьте пароль на наличие минимум 8 символов, одной цифры и одной заглавной буквы.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a58516e7-f700-4fe1-990b-5332af37870d" />

12. **Очистка поискового ввода:** Удалите из поискового запроса двойные пробелы, спецсимволы `#`, `$`, `%` и приведите к нижнему регистру.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c531f080-84e5-4f78-aeba-ab2c4053d2f1" />

13. **Генератор сокращенного имени автора:** Преобразуйте `"Иванов Иван Иванович"` в `"Иванов И. И."`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4aa03f0f-02cd-4520-b944-b408c6049738" />

14. **Парсинг HEX-цвета для Flutter:** Преобразуйте строку вида `"#FF5722"` в целочисленное значение цвета `int` (`0xFFFF5722`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1949eae5-8b99-4f1b-8dc0-436d36a7f32f" />

15. **Форматирование таймера звонка:** Переведите количество секунд `int seconds = 125` в строку вида `"02:05"`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/75465823-b5c4-4432-acf5-43fc832591cf" />

16. **Скрытие номера телефона:** Преобразуйте `"+79991234567"` в `"+7 (999) ***-**-67"`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f62bda8b-5840-4a91-94c4-ee3c2afd7fca" />

17. **Определитель языка текста (Кириллица/Латиница):** Определите, содержит ли строка символы русского алфавита с помощью проверки диапазонов кодовых точек.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/78b2d703-c23d-435f-9d89-479fa92e75d6" />

18. **Генератор слага (Slugify):** Преобразуйте заголовок статьи `"Быстрый старт во Flutter!"` в `"bystryi-start-vo-flutter"`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d712f65f-99b8-47b3-a542-0e610986d111" />

19. **Подсчет слов в заметке:** Подсчитайте количество слов в тексте заметки, разделенных пробелами и переносами строк.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8004d0f3-923f-4f90-a1f5-c5a962d3febe" />

20. **Выделение тегов из текста:** Извлеките все хештеги из сообщения блога (слова, начинающиеся с `#`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a1e76428-ee99-43d5-89c4-06662309a1dc" />


### Блок 3: Списки, пагинация и фильтрация каталога (21–30)

21. **Фильтр каталога по диапазону цен:** Из списка карточек товаров отберите только те, у которых цена находится в заданном диапазоне.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e4f8b40f-a659-46fc-b9d4-182c727034bd" />

22. **Удаление завершенных задач из To-Do списка:** Отфильтруйте список задач, оставив только элементы со статусом `isCompleted == false`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e027ac31-57f1-4cff-9b3b-5c646646d2fe" />

23. **Инверсия порядка чатов:** Разверните список сообщений без создания промежуточных копий в памяти.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ff5dccc1-fcda-4549-9151-95af4d73324f" />

24. **Склейка двух списков баннеров:** Объедините системные промо-акции и персональные предложения с помощью spread-оператора `...`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dc8874d4-f93d-4ddd-bf1a-1d616a40dd70" />

25. **Поиск максимального элемента без библиотек:** Найдите товар с наивысшим рейтингом в списке с помощью метода `.reduce()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ee66e75a-2aa6-4b19-a993-613def4c1169" />

26. **Чередование элементов списка:** Вставьте рекламный баннер после каждого 5-го элемента ленты новостей.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f640e643-27f9-41a1-b99e-bfae6e551e01" />

27. **Проверка идентичности двух списков:** Напишите функцию, проверяющую, содержат ли два списка одинаковые элементы на тех же позициях.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/495b0722-ff90-4f1a-a7d7-c0d37f7b7b89" />

28. **Извлечение уникальных категорий:** Из списка всех товаров магазина сформируйте `List<String>` уникальных названий категорий.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2ad0d0e1-3cbf-4d5a-a9f2-00a82e25d83d" />

29. **Сортировка списка по нескольким полям:** Отсортируйте список товаров сначала по доступности на складе, а затем по возрастанию цены.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/328b64fa-6fa8-45c9-a474-5bb5aa7ec94d" />

30. **Постраничная группировка (Chunking):** Разбейте длинный список из 100 элементов на пачки (страницы) по 10 элементов.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4aa21d19-300c-47e7-ad07-16500d432e1e" />


### Блок 4: Map, JSON-сериализация и кэширование (31–40)

31. **Десериализация профиля:** Напишите парсер словаря `Map<String, dynamic>` в безопасные локальные переменные с дефолтными значениями.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/26cf06d8-644b-4e2b-affd-78cad69a59ec" />

32. **Конвертация списка в ассоциативную карту:** Преобразуйте `List<User>` в `Map<int, User>`, где ключом выступает уникальный `id`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/be610f6f-39ae-420d-aa4f-2607610b8db2" />

33. **Слияние локального и серверного профиля:** Объедините настройки приложения из кэша и свежие настройки с бэкенда с приоритетом сервера.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/93468ec4-8394-49f0-9dfe-ccf2c0412ab6" />

34. **Удаление ключей с null-значениями:** Очистите словарь от всех пар, где значение равно `null`, перед отправкой POST-запроса.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/307e57d7-d2e6-443d-b306-0b84022832b1" />

35. **Кэш экранов (LRU-концепт):** Реализуйте простейшую карту последних 5 открытых страниц, удаляя самый старый элемент при переполнении.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9254c5ae-4246-4c0c-878a-ed0a45bf9192" />

36. **Инверсия карты (Значения становятся ключами):** Поменяйте местами ключи и значения в словаре `Map<String, String>` (гарантируя уникальность).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/53718345-277a-472e-93fa-794caa992286" />

37. **Подсчет суммы товаров по категориям:** На основе списка покупок верните `Map<String, double>`, где ключ — категория, а значение — общая сумма затрат.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e275ab7b-52e4-4c60-bf30-226176f63684" />

38. **Валидация обязательных JSON-полей:** Проверьте, содержит ли карта ответа сервера ключи `"status"`, `"data"` и `"timestamp"`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/03df5766-6f7f-49bd-8904-e2a9577bd60c" />

39. **Построение дерева категорий:** Преобразуйте плоский список категорий с `parentId` в иерархическую карту подкатегорий.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/18fbb6b0-f190-41f1-affd-483d6baf15b3" />

40. **Сериализация параметров фильтра в URL:** Соберите из словаря `{"brand": "Apple", "minPrice": 50000}` строку параметров `brand=Apple&minPrice=50000`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a575f680-d155-4e8f-a544-203060f9a466" />


### Блок 5: Sound Null Safety, Records и архитектурные пайплайны (41–50)

41. **Парсер геолокации через Record:** Напишите функцию, принимающую строку `"54.01, 38.29"` и возвращающую кортеж `(double lat, double lon)?`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/308c5c7e-1c6a-4cce-aab4-84737c6c3e82" />

42. **Резолвер состояний загрузки (Pattern Matching):** С помощью `switch` верните текст сообщения для состояний `(isLoading: true, hasError: false)`, `(isLoading: false, hasError: true)` и `(isLoading: false, hasError: false)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/026bfc73-65c9-4bb5-a5fb-109997f6e5a3" />

43. **Безопасная цепочка вызовов (Deep Null Traversal):** Считайте улицу из вложенного объекта компании: `user?.company?.address?.street ?? "Адрес не указан"`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9430ba18-fdd1-4759-9d3c-e0b4ee6a6f8d" />

44. **Сопоставление типа ответа API:** Напишите функцию обработки ответа через сопоставление типов: `case int code`, `case String error`, `case Map data`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/596afc19-d774-4522-9539-06512955c347" />

45. **Деструктуризация координат точки в цикле:** Пройдитесь по списку кортежей `List<(int x, int y)>` с прямой деструктуризацией `for (var (x, y) in points)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ef6cec02-f917-49e2-a8b5-0b6a466bb4a2" />

46. **Обмен значений двух переменных без третьей:** Поменяйте местами значения переменных `a` и `b` с помощью синтаксиса кортежей: `(a, b) = (b, a)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fa2f678f-4db7-4395-9ce4-44dc9de7acb6" />

47. **Валидатор комплексной формы регистрации:** Функция возвращает Record `({bool isValid, List<String> errors})` с перечнем всех найденных ошибок формы.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/892ae073-411e-4c64-9def-1aa724b98c36" />

48. **Обработка диапазона громкости через Switch Guard:** Используйте паттерн с условием `when`: `case int v when v > 80 => "Слишком громко!"`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5e3edfec-ac9a-46c3-b964-6c8c28428a8d" />

49. **Пайплайн очистки телефонной книги:** Дан список `List<String?>` с поврежденными записями. Очистите его от `null`, пустых строк и верните `Set<String>` уникальных нормализованных номеров.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a24f88cf-1290-41c5-bda8-c3ffde15ed14" />

50. **Итоговый аналитический модуль корзины:**
    - Примите корзину покупок в виде `List<Map<String, dynamic>>`.
    - Рассчитайте общую стоимость позиций с учетом индивидуальных скидок.
    - Выделите категории товаров и проверьте бесплатность доставки (от 2000 руб.).
    - Верните результат в виде именованного кортежа `({double total, double discountAmount, bool isFreeDelivery})`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0d6f6994-acff-4c40-9af4-bf71fb498715" />


---
