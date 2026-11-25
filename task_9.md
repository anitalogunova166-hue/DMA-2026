---
title: "Практическая работа #9: Асинхронное программирование в Dart"
discipline: "Разработка мобильных приложений"
tags:
  [
    dart,
    async,
    future,
    stream,
    stream-controller,
    isolates,
    event-loop,
    flutter,
  ]
---

# Практическая работа: Асинхронное программирование в Dart (Event Loop, Future, Stream, Isolates)

> [!NOTE]
> **Цель работы:** Глубоко освоить неблокирующую модель выполнения программ в Dart. Изучить архитектуру цикла событий (Event Loop), очереди микротасок и событий, работу с отложенными результатами (`Future`, `async/await`), реактивную обработку непрерывных потоков данных (`Stream`, `StreamController`, `async*`), а также параллельные вычисления в изолированных потоках памяти (`Isolates`).

---

## 📋 Содержание

1. [Модель параллелизма Dart: однопоточность и Event Loop](#1-модель-параллелизма-dart-однопоточность-и-event-loop)
2. [Тема 1. Отложенные вычисления: Future API и синтаксис async/await](#тема-1-отложенные-вычисления-future-api-и-синтаксис-asyncawait)
3. [Тема 2. Непрерывные потоки: Stream API и генераторы async\*](#тема-2-непрерывные-потоки-stream-api-и-генераторы-async)
4. [Тема 3. Реактивное управление: StreamController и StreamTransformer](#тема-3-реактивное-управление-streamcontroller-и-streamtransformer)
5. [Тема 4. Истинная многопоточность: Изоляты (Isolates) и Isolate.run](#тема-4-истинная-многопоточность-изоляты-isolates-и-isolaterun)
6. [50 Смешанных заданий по асинхронной разработке (Mobile Async Focus)](#50-смешанных-заданий-по-асинхронной-разработке)

---

## 1. Модель параллелизма Dart: однопоточность и Event Loop

По умолчанию код Dart выполняется в **одном потоке** (Main Isolate). Это исключает гонки данных (Race Conditions) и необходимость в блокирующих мьютексах, но требует неблокирующего подхода при операциях ввода-вывода (сеть, файлы, таймеры).

### Архитектура цикла событий (Event Loop)

```
                            СТАРТ ПРОГРАММЫ (main())
                                       │
                                       ▼
                     ┌───────────────────────────────────┐
                     │ Очередь микрозадач (Microtask)    │ ◄── Высший приоритет
                     │ (Внутренние системные колбэки)    │
                     └─────────────────┬─────────────────┘
                                       │ Пуста?
                                       ▼ Да
                     ┌───────────────────────────────────┐
                     │ Очередь событий (Event Queue)     │ ◄── Стандартный приоритет
                     │ (I/O, Таймеры, Тапы по экрану)    │
                     └─────────────────┬─────────────────┘
                                       │
                                       ▼
                             Исполнение задачи
                                       │
                                       └──────► Цикл повторяется
```

### Приоритет очередей

1. **Синхронный код:** Выполняется немедленно от первой до последней строчки.
2. **Microtask Queue:** Высокоприоритетные короткие системные задачи (`scheduleMicrotask()`). Очередь событий не начнет обрабатываться, пока очередь микрозадач не опустеет.
3. **Event Queue:** События пользовательского ввода (тапы, жесты), срабатывание таймеров (`Timer`), ответы сетевых сокетов и завершение чтения файлов.

---

## Тема 1. Отложенные вычисления: Future API и синтаксис async/await

`Future<T>` представляет собой контейнер для значения типа `T`, которое станет доступно через некоторое время, либо завершится ошибкой.

### Состояния Future

- **Uncompleted (В процессе):** Асинхронная операция еще выполняется.
- **Completed with Data (Успех):** Операция завершилась, вернув результат.
- **Completed with Error (Ошибка):** Во время выполнения выброшено исключение.

### Пример кода (Future, цепочки и параллельное ожидание)

```dart
// Имитация сетевого запроса к каталогу
Future<List<String>> fetchProductCatalog() async {
  await Future.delayed(const Duration(seconds: 2)); // Неблокирующая задержка

  bool hasNetworkError = false;
  if (hasNetworkError) {
    throw Exception("Ошибка 503: Сервер каталога временно недоступен");
  }

  return ["Смартфон Galaxy S26", "Планшет Tab Ultra", "Наушники Buds Pro"];
}

// Имитация загрузки профиля пользователя
Future<String> fetchUserProfile() async {
  await Future.delayed(const Duration(seconds: 1));
  return "Алексей Смирнов";
}

void main() async {
  print("1. Инициализация экрана...");

  try {
    // 1. Последовательное ожидание через async/await
    String user = await fetchUserProfile();
    print("2. Пользователь загружен: $user");

    // 2. Параллельный запуск нескольких Future через Future.wait()
    print("3. Старт параллельной загрузки дополнительных данных...");
    var results = await Future.wait([
      fetchProductCatalog(),
      Future.delayed(const Duration(milliseconds: 500), () => "Курсы валют OK"),
    ]);

    List<String> products = results[0] as List<String>;
    print("4. Товары получены: $products");

  } catch (error) {
    print("Перехвачена ошибка: $error");
  } finally {
    print("5. Завершение работы, скрытие ProgressBar");
  }

  // 3. Таймаут выполнения операции
  try {
    await fetchProductCatalog().timeout(
      const Duration(milliseconds: 500),
      onTimeout: () => throw Exception("Таймаут соединения! Сервер не ответил вовремя."),
    );
  } catch (e) {
    print("Контроль таймаута: $e");
  }
}
```

### Задания для закрепления

1. **Симулятор авторизации:** Напишите функцию `Future<bool> login(String user, String pass)`, которая через 1.5 секунды возвращает `true`, если пароль равен `"1234"`, и выбрасывает `AuthException` в противном случае.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e095a06a-e4e1-4b28-832e-c0e569e72193" />

2. **Параллельное чтение файлов конфигурации:** Запустите параллельно три вызова `Future` с разным временем задержки с помощью `Future.wait()` и объедините их результаты в одну карту.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b43f95f2-40fc-45f9-9a39-65c4abe984b6" />

3. **Гонка асинхронных задач (Future.any):** Запустите запрос к двум зеркалам одного сервера (задержка 800 мс и 1200 мс) и получите ответ от самого быстрого с помощью `Future.any()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/71fb6d0a-50a2-4d87-8967-c194f2ab4d25" />

4. **Повторные попытки с задержкой (Retry Pattern):** Напишите функцию `retry(Future Function() task, int maxAttempts)`, которая при падении задачи повторяет вызов до 3 раз с паузой в 1 секунду.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6cfec1e6-6b41-4429-a21d-3c7e2f568032" />

5. **Цепочка промисов через then/catchError:** Реализуйте аналогичную логику авторизации без использования ключевых слов `async/await`, используя только методы `.then()`, `.catchError()` и `.whenComplete()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d3be9ae2-a2ff-4b15-b75c-aabe2dc90d16" />


---

## Тема 2. Непрерывные потоки: Stream API и генераторы async\*

Если `Future` возвращает **однократное** значение, то `Stream<T>` поставляет **последовательность** асинхронных событий во времени (клики, показания датчиков GPS, сообщения сокета).

### Два типа потоков

1. **Single-Subscription Stream (Одиночный поток):** Может иметь только одного слушателя за все время жизни (например, чтение файла по кускам). Повторный вызов `.listen()` приведет к исключению `StateError`.
2. **Broadcast Stream (Широковещательный поток):** Позволяет подключать множество независимых подписчиков одновременно (события UI, шина аналитики). Создается через `stream.asBroadcastStream()`.

### Пример кода (Генератор async\* и обработка потока)

```dart
// Асинхронный генератор: возвращает непрерывный поток данных
Stream<int> countdownTimer(int fromSeconds) async* {
  for (int i = fromSeconds; i >= 0; i--) {
    await Future.delayed(const Duration(seconds: 1));
    yield i; // Отправка значения в поток
  }
}

void main() async {
  print("Старт таймера:");

  Stream<int> timerStream = countdownTimer(5);

  // Функциональные операторы над потоком (подобно коллекциям)
  StreamSubscription<String> subscription = timerStream
      .where((seconds) => seconds % 2 == 0) // Фильтруем только четные секунды
      .map((seconds) => "Осталось четных секунд: $seconds")
      .listen(
        (data) => print("[DATA] $data"),
        onError: (err) => print("[ERROR] $err"),
        onDone: () => print("[DONE] Отсчет завершен!"),
        cancelOnError: false,
      );

  // Отмена подписки через 3.5 секунды для предотвращения утечки ресурсов
  await Future.delayed(const Duration(milliseconds: 3500));
  print("Принудительная отмена подписки слушателем!");
  await subscription.cancel();
}
```

### Задания для закрепления

1. **Поток тиков секундомера:** Создайте функцию `Stream<int> stopWatch() async*`, непрерывно генерирующую инкрементный счетчик каждую секунду.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7966a596-c71f-4599-bea8-3b6cdf8c2bd6" />

2. **Поток GPS-координат (Имитация):** Сгенерируйте поток кортежей `(double lat, double lon)`, слегка изменяющих координаты каждые 500 мс.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c37d1664-e36a-421c-bb31-458e0cc8ad04" />

3. **Широковещательный стрим котировок:** Создайте однократный поток цен, преобразуйте его в Broadcast через `.asBroadcastStream()` и подключите двух разных слушателей: UI-виджет и логгер аналитики.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cb053b4a-a67a-44fe-98e3-9eca1aeed6a8" />

4. **Агрегация потока (fold/reduce):** Посчитайте сумму чисел, поступающих из потока из 10 элементов, с помощью метода `stream.fold()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ee6db492-70c9-40fe-a4bb-a6ee0ce603b1" />

5. **Делегирование генератора через yield\*:** Напишите функцию, которая объединяет два потока последовательно с помощью синтаксиса `yield* subStream`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5efc2cff-782b-4adf-bd7e-6f0943a5550c" />


---

## Тема 3. Реактивное управление: StreamController и StreamTransformer

`StreamController<T>` — это программный пульт управления потоком, разделенный на две части:

- **`Sink` (Входной порт):** Место, куда события отправляются (`sink.add()`, `sink.addError()`).
- **`Stream` (Выходной порт):** Поток, на который подписываются клиенты для чтения данных.

```
                           STREAM CONTROLLER
        ┌─────────────────────────────────────────────────────┐
        │                                                     │
Events ─┼──► [ Sink (add / addError) ]                        │
        │                  │                                  │
        │                  ▼                                  │
        │         Внутренний буфер                            │
        │                  │                                  │
        │                  ▼                                  │
        │         [ Stream (listen) ] ──► Subscribers (UI)    │
        │                                                     │
        └─────────────────────────────────────────────────────┘
```

### Пример кода (Простейший реактивный BLoC счетчика без сторонних библиотек)

```dart
import 'dart:async';

class CounterBloc {
  int _counter = 0;

  // Контроллер с поддержкой нескольких слушателей (Broadcast)
  final _stateController = StreamController<int>.broadcast();

  // Публичный поток только для чтения UI-компонентами
  Stream<int> get counterStream => _stateController.stream;

  void increment() {
    _counter++;
    _stateController.sink.add(_counter); // Отправка нового стейта
  }

  void reset() {
    _counter = 0;
    _stateController.sink.add(_counter);
  }

  // Обязательное закрытие контроллера при уничтожении экрана
  void dispose() {
    _stateController.close();
  }
}

void main() async {
  final bloc = CounterBloc();

  final sub1 = bloc.counterStream.listen((val) => print("UI Экран получил: $val"));
  final sub2 = bloc.counterStream.listen((val) => print("Аналитика зафиксировала: $val"));

  bloc.increment(); // 1
  bloc.increment(); // 2
  bloc.reset();     // 0

  await Future.delayed(const Duration(milliseconds: 100));
  await sub1.cancel();
  await sub2.cancel();
  bloc.dispose();
}
```

### Задания для закрепления

1. **Кастомный StreamTransformer:** Реализуйте трансформер строк через `StreamTransformer.fromHandlers()`, переводящий все входящие сообщения в верхний регистр.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a532252d-4292-44b4-8805-408f2f9a076b" />

2. **Контроллер поискового ввода:** Реализуйте класс `SearchControllerWrapper`, принимающий посимвольный ввод пользователя в Sink и отдающий очищенную строку в Stream.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/600cde7e-3f9b-417d-b651-3ecf668bec36" />

3. **Обработка ошибок в контроллере:** Отправьте ошибку в контроллер с помощью `sink.addError(Exception("Сбой датчика"))` и перехватите её в секции `onError` слушателя.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3fe8f297-7d9c-4489-b938-b71830e34b5b" />

4. **Контроль состояния закрытия:** Напишите метод, проверяющий свойство `controller.isClosed` перед попыткой добавить новое событие, предотвращая падение с ошибкой.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e0049096-b970-41ff-970d-3d69f700d2c0" />


5. **Счетчик активных подписчиков:** Сконфигурируйте `StreamController(onListen: ..., onCancel: ...)` для отслеживания момента появления и ухода подписчиков.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/30ac6df0-e875-41e9-a472-522fe63c0df7" />


---

## Тема 4. Истинная многопоточность: Изоляты (Isolates) и Isolate.run

Если задача требует непрерывных тяжелых математических расчетов (например, парсинг 50-мегабайтного JSON, фильтрация графических пикселей, шифрование больших баз данных), она заблокирует Event Loop главного потока, вызвав зависание мобильного экрана (Jank).

Для тяжелых фоновых вычислений Dart использует **Изоляты** (Isolates).

### Отличие изолятов от традиционных потоков Java/C++

- Изоляты **НЕ имеют общей разделяемой памяти** (Shared Memory отсутствует).
- Нет блокировок, взаимных дедлоков и мьютексов.
- Общение между изолятами происходит исключительно через передачу сообщений по системным портам (`SendPort` и `ReceivePort`), либо через метод `Isolate.run()` (начиная с Dart 2.19+).

```
   MAIN ISOLATE (UI & Event Loop)             BACKGROUND WORKER ISOLATE
┌─────────────────────────────────┐       ┌─────────────────────────────────┐
│ Своя изолированная память       │       │ Своя изолированная память       │
│                                 │       │                                 │
│ [SendPort] ─── Message (Copy) ──┼──────►│ [ReceivePort]                   │
│                                 │       │                                 │
│                                 │       │ Тяжелый расчет:                 │
│                                 │       │ парсинг JSON / сжатие фото      │
│                                 │       │                                 │
│ [ReceivePort] ◄── Result ───────┼───────┤ [SendPort]                      │
└─────────────────────────────────┘       └─────────────────────────────────┘
```

### Пример кода: Isolate.run() против низкоуровневых портов

```dart
import 'dart:isolate';

// Тяжелая функция верхнего уровня (Top-level или static)
int heavyCalculation(int iterations) {
  int sum = 0;
  for (int i = 0; i < iterations; i++) {
    sum += (i % 3);
  }
  return sum;
}

void main() async {
  print("1. Главный поток свободен, UI отрисовывается со скоростью 120 FPS");

  // Способ 1: Современный компактный Isolate.run() (Dart 2.19 / 3+)
  print("2. Запуск тяжелого расчета в параллельном изоляте...");
  int result = await Isolate.run(() => heavyCalculation(500000000));
  print("3. Результат из Isolate.run: $result");

  // Способ 2: Низкоуровневые порты для длительного двустороннего обмена
  final receivePort = ReceivePort();
  await Isolate.spawn(_workerEntryPoint, receivePort.sendPort);

  // Ожидаем ответ от запущенного воркера
  final workerMessage = await receivePort.first;
  print("4. Сообщение от постоянного воркера: $workerMessage");
}

void _workerEntryPoint(SendPort sendPort) {
  // Выполнение фоновой задачи
  int calculatedValue = heavyCalculation(100000);
  sendPort.send("Готово! Вычислено: $calculatedValue");
}
```

### Задания для закрепления

1. **Параллельный парсер JSON:** Напишите функцию, принимающую строку из 10 000 фиктивных записей и десериализующую ее в объекты через `Isolate.run()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/617d2f30-f521-48f4-b2b6-09c2b87b480c" />

2. **Поиск простых чисел в фоне:** Запустите вычисление всех простых чисел до 1 000 000 в фоновом изоляте, параллельно выводя в консоль главного потока точки каждую секунду.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6643b1a5-3293-42d8-a03c-e27b601b45bf" />

3. **Двусторонний обмен через порты:** Создайте изолят-калькулятор, который слушает команды по `SendPort`, выполняет операции сложения и отправляет ответ обратно.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1aa18275-fb3d-4465-9414-13b25be80c65" />

4. **Замер падения FPS:** Напишите скрипт, запускающий цикл на 2 млрд итераций сначала синхронно в `main()`, а затем в `Isolate.run()`, сравнив отзывчивость таймеров.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9f21ae60-c1fa-445b-963f-c7897feaf785" />

5. **Принудительное уничтожение изолята:** Запустите бесконечный фоновый изолят через `Isolate.spawn` и принудительно завершите его работу методом `isolate.kill(priority: Isolate.immediate)`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3f3c30b9-877a-4713-b17f-ef441e935db6" />


---

## 50 Смешанных заданий по асинхронной разработке

Задания охватывают реальные сценарии клиентской мобильной разработки (REST API, WebSocket, фоновые очереди, кэширование, датчики, архитектура MVI/BLoC).

### Блок 1: Сетевые запросы, таймеры и Future

1. **Имитатор HTTP GET запроса:** Напишите функцию `fetchData(String url)`, возвращающую JSON через 1200 мс с обработкой статусов 200, 404 и 500.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/683e39fd-205f-487f-80d6-3676a8af164e" />

2. **Экспоненциальная задержка (Exponential Backoff):** Реализуйте повтор сетевого запроса с прогрессирующей паузой: 1с, 2с, 4с, 8с при возникновении `SocketException`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/13259176-3717-4357-8389-51001b23b947" />

3. **Параллельная загрузка медиафайлов:** Скачайте метаданные для 5 картинок одновременно через `Future.wait()`, ограничив параллелизм до 2 одновременных вызовов.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8ce8a528-7cd2-4346-9f29-7e591666051e" />

4. **Валидатор доступности сервера (Ping):** Отправьте пинг-запросы к трем зеркалам и верните URL первого ответившего сервера (`Future.any`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/52bc609f-6a09-4f28-a1af-4f53ba50d609" />


5. **Прерываемый запрос по таймауту:** Оберните вызов длительного сохранения профиля в таймаут 3 секунды с аварийным прерыванием операции.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9a5110ab-e9ec-4877-966e-53292834c5cc" />




6. **Очередь последовательного выполнения (Async Queue):** Спроектируйте класс, выполняющий поступающие асинхронные задачи строго по очереди друг за другом (FIFO).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c5a36c53-9ede-46e9-a05e-761359d7ea67" />


7. **Асинхронный замер времени выполнения:** Напишите утилиту `benchmark(Future Function() task)`, замеряющую точное время выполнения асинхронного блока в миллисекундах (`Stopwatch`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/68b587a3-fb5e-4a6a-a00a-5c78c3857bac" />

8. **Ленивый асинхронный синглтон:** Реализуйте инициализацию конфигурации приложения `AppConfig.getInstance()`, выполняющую чтение данных из асинхронного источника ровно один раз.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/945483e6-caf6-45af-b349-269adccb7db9" />

9. **Параллельное слияние локального и сетевого кэша:** Запустите чтение локальной базы SQLite и сетевой вызов одновременно, вернув локальные данные, если сеть не успела ответить за 300 мс.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5facad8f-9f98-4fc3-9fda-6252507335d2" />

10. **Асинхронный дебаунсер (Debounce):** Напишите функцию, откладывающую выполнение сетевого поиска на 400 мс и сбрасывающую таймер при каждом новом нажатии клавиши.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7ac57436-af24-46ee-b8e0-d152d4e02b46" />


### Блок 2: Потоковые данные, датчики и Stream API

11. **Слушатель котировок криптовалют:** Смоделируйте WebSocket-поток, передающий случайную цену биткоина раз в 200 мс, и отфильтруйте всплески более 5%.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a702adc5-0d40-4269-8407-41c70499dcf1" />

12. **Стриминг прогресса скачивания файла:** Напишите асинхронный генератор `async*`, передающий процент загрузки от 0% до 100% с шагом в 10% каждые 300 мс.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8d2f0a19-bda7-4fa6-ab04-7169ff39bd0b" />

13. **Объединение двух потоков (Merge):** Создайте функцию, объединяющую события из потока геолокации и потока компаса в единый поток навигации.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5fc4955d-8060-49e0-9d8e-c417bfd495ee" />


14. **Дросселирование потока (Throttle):** Реализуйте оператор, пропускающий не более одного клика по кнопке в секунду из непрерывного потока нажатий.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c46b5a9f-8ad9-4a4a-b959-5f47c5a0974b" />


15. **Поток шагов акселерометра:** Сгенерируйте поток показаний ускорения по трем осям, подсчитывающий число шагов при превышении порогового значения.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d832c5df-764c-4ee9-9e02-54948731a10c" />

16. **Автоматическое переподключение потока (Reconnection):** Напишите слушатель стрима чата, который при возникновении ошибки автоматически пересоздает подписку через 2 секунды.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5a362aa7-b6a4-473c-ba4a-9f2a06cae829" />

17. **Буферизация потока пачками (Buffer / Chunk):** Накапливайте события кликов в список и отдавайте их пачками по 5 штук или раз в 3 секунды.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0c6d3f33-17b5-4cca-bdc4-9f9dc198bba0" />

18. **Поток обратного отсчета для OTP-кода:** Создайте стрим таймера на 60 секунд для экрана ввода SMS с уведомлением о завершении срока жизни кода.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/498fea4e-4e24-43b3-9276-63a892b78e5e" />

19. **Потоковый калькулятор скользящего среднего:** Спроектируйте стрим, принимающий числа и отдающий скользящее среднее значение последних 5 измерений.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cf828ddd-d2bc-4e12-9bc3-c7e51596cea4" />



20. **Преобразование Stream в Future:** Извлеките последнее значение из конечного потока с помощью метода `stream.last`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/36233af2-3ac0-41af-9757-404157a9c3ac" />


### Блок 3: Реактивные шины событий и StreamController

21. **Шина глобальных событий (EventBus):** Создайте синглтон `EventBus` на базе широковещательного `StreamController` для обмена событиями между несвязанными экранами.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6a25fbbf-ce26-46f9-9a04-45b9ddcd64a7" />

22. **Реактивная корзина интернет-магазина:** Реализуйте класс `CartStore`, транслирующий в стрим текущее количество товаров и итоговую сумму при вызовах `add()` и `remove()`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fd1677d9-909d-4abe-9d23-935ef2a46ad1" />

23. **Счетчик непрочитанных сообщений:** Спроектируйте контроллер бейджа уведомлений, увеличивающий счетчик при новых событиях и сбрасывающий его в ноль при прочтении.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4479053a-1a8a-4a85-aeec-56b519caf2ad" />

24. **Управление состоянием аудиоплеера:** Реализуйте `AudioPlayerBloc` со стримом состояний: `PlayerStopped`, `PlayerPlaying(track)`, `PlayerPaused`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d43c9816-167a-4f46-af72-6f1c728ead0b" />

25. **Контроллер бесконечной ленты (Pagination Stream):** Спроектируйте стрим, подгружающий следующую страницу новостей при передаче события `LoadNextPage`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/130a6ad8-5990-466f-996c-d4edcc214b3b" />

26. **Реактивный переключатель темной темы:** Создайте менеджер темы приложения, отдающий `ThemeMode.dark` или `ThemeMode.light` через `BehaviorSubject`-подобный контроллер.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fdfd73dc-f649-4737-b6b9-80a5cd5a86e7" />

27. **Слушатель сетевого статуса (Connectivity Stream):** Контроллер, транслирующий изменения подключения (`WiFi`, `Cellular`, `None`) во все подписанные модули.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b1f10659-00b5-4955-9b7b-c37a688fde47" />

28. **Защита контроллера от утечек памяти (Auto-Dispose):** Напишите класс с механизмом проверки наличия подписчиков и автоматическим вызовом `close()` при обнулении слушателей.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/190f80d0-a803-46b0-b1a8-8c4110f5eb25" />

29. **Валидация формы регистрации на стримах:** Создайте два потока для полей Email и Пароль, объедините их через `Rx.combineLatest`-подобную логику и отдавайте флаг активности кнопки `isSubmitEnabled`.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b2999560-97d7-4740-9fc7-df2129e00d35" />

30. **Потоковый логгер крашей:** Направляйте все неотловленные ошибки приложения через `StreamController` в фоновый модуль отправки аналитики.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/17594eae-efd4-4ea7-ae8f-3ac6f23783e7" />


### Блок 4: Изоляты и ресурсоемкие параллельные задачи

31. **Фоновая генерация миниатюр (Thumbnail Generator):** Обработайте массив байтов изображения в `Isolate.run()`, изменив его размер и вернув сжатый массив.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e054c31a-fb13-4fdc-89f4-8b7b54755bdb" />


32. **Шифрование базы заметок:** Выполните шифрование большого текстового файла по алгоритму XOR / AES в параллельном изоляте без просадки FPS.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/38fd752d-9044-48f9-95e5-208912d5216c" />


33. **Полнотекстовый поиск по 100 000 документов:** Передайте поисковый запрос и список документов в фоновый изолят, вернув 10 наиболее релевантных совпадений.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/484612a9-36d6-44ec-9c70-64c195975052" />

34. **Парсинг тяжелого GeoJSON карты города:** Распарсите координаты полигонов районов города в фоновом потоке памяти и верните готовые структуры данных.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0be24210-3c12-446d-a184-49314576625d" />

35. **Сжатие логов в ZIP-архив:** Смоделируйте упаковку текстовых журналов в сжатый архив через изолят перед отправкой в техподдержку.
36. **Двусторонний пул воркеров (Worker Pool):** Создайте менеджер из двух постоянно работающих изолятов, распределяющий между ними математические задачи.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7296bbd2-92a1-4230-9477-7370ce6fd161" />

37. **Генерация PDF-отчета:** Сформируйте текстовую структуру финансового отчета за год в параллельном изоляте.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4aed3537-4563-48ce-ba10-9252a05e883c" />


38. **Сортировка массива из 1 000 000 чисел:** Сравните время сортировки массива в главном потоке и в отдельном изоляте.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1b42661f-51f7-4eb9-9575-91e27a7c44ca" />



39. **Конвертер аудио-семплов:** Смоделируйте преобразование массива сырых аудио-семплов в изоляте.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e7b469d2-dabe-42e4-b1d0-b2f7cc1d6e6c" />


40. **Безопасная передача больших объектов (TransferableTypedData):** Передайте большой бинарный буфер между изолятами без затратного копирования памяти.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8eb60d6e-1e70-41b6-b3ea-018b4d5c0ee4" />


### Блок 5: Сложные асинхронные архитектуры и отказоустойчивость

41. **Отмена сетевой операции через CancelToken:** Реализуйте класс токена отмены, выбрасывающий `CancellationException` при прерывании пользователем ухода со страницы.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8f0b27e4-9d0b-4a78-bdbe-5d57ca3b91df" />

42. **Репликация базы данных с разрешением конфликтов:** Синхронизируйте две асинхронные очереди изменений с временными метками (Timestamps).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b76749fd-79f3-44c4-abc1-023aa6bd9f02" />



43. **Асинхронная очередь сохранения офлайн-заказов:** Сохраняйте оформленные без интернета заказы в локальную очередь и автоматически отправляйте их по одному при восстановлении сети.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/460fe2d6-ed97-4a8b-9c1d-de74fb718614" />


44. **Паттерн Circuit Breaker:** Реализуйте защиту от падения сервера: если 5 запросов подряд завершились ошибкой, переведите сервис в режим «Разрыв цепи» на 30 секунд.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b6d637a8-5cb6-4576-94f9-9eea8ad2644c" />

45. **Асинхронный семафор (Rate Limiter):** Ограничьте число одновременных сетевых обращений к API до 3 параллельных потоков.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0d87dbe2-faee-46ba-a213-7108c22d6eb2" />

46. **Гонка состояний при быстром вводе (Race Condition Fix):** Защитите поисковую строку: гарантируйте, что старый медленный ответ сервера не перезапишет новый быстрый ответ.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/555024d9-30e7-4743-b18f-5cfc5609d975" />

47. **Слушатель жизненного цикла приложения:** Свяжите события сворачивания приложения с мгновенной приостановкой всех активных `StreamSubscription`.
48. **Конечный автомат асинхронной синхронизации:** Реализуйте переключение стейтов `Idle` -> `Syncing(progress)` -> `Success` -> `Error` с защитой от повторного запуска во время синхронизации.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d8d8ab02-360b-4108-9c39-b2c0876c7f07" />

49. **Многоуровневый кэш (Memory -> Disk -> Network):** Спроектируйте функцию, последовательно проверяющую быструю оперативную память, затем локальный диск и только потом идущую в сеть.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4a36a625-69fd-42ac-ab74-fe11d9949347" />

50. **Итоговый реактивный модуль финансового терминала (Production Architecture):**
    - Подключите непрерывный поток биржевых котировок через WebSocket-имитатор.
    - Передайте расчет скользящей волатильности в параллельный изолят (`Isolate.run`).
    - Ограничьте частоту обновления графического экрана до 60 кадров/сек с помощью троттлинга.
    - Реализуйте автоматическое восстановление соединения при обрыве и безопасный вызов `dispose()` для всех контроллеров и подписок.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8fb52699-461e-4599-9c36-218f2df7589e" />


---
