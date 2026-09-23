### Блок 1: Авторизация, безопасность и валидация ввода

1. **Валидатор надежности пароля:** Проверьте пароль на соответствие критериям: минимум 8 символов, хотя бы одна заглавная буква, одна цифра и один специальный знак (`!@#$%^&*`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5d788473-051d-46ba-8857-2456dca75056" />

2. **Нормализатор телефонных номеров:** Принимайте строку в произвольном формате (например, `8 (999) 000-11-22` или `+7 999 000 11 22`) и приводите её к строгому стандарту E.164 (`+79990001122`).<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/402180ff-f7ca-4b20-9d2a-54759c420905" />

3. **Генератор одноразового SMS-кода:** Напишите метод генерации 6-значного числового OTP-кода. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/70726fc2-21f7-4b2c-a314-33e5e64582eb" /> 

4. **Проверка срока действия JWT-токена:** Дан Unix-таймстемп истечения токена в секундах. Определите, активен ли токен относительно текущего системного времени. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/118f426a-9235-4762-930d-ecab8ad8f103" />

5. **Маскировка персональных данных (PII):** Замаскируйте адрес электронной почты: `alexander.ivanov@mail.ru` -> `a***********v@mail.ru`.  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6ea9454b-049c-4823-8a69-389072650fe6" />

6. **Блокировщик брутфорса:** Реализуйте класс `LoginThrottler`, который после 5 неудачных попыток ввода пароля блокирует ввод на 60 секунд. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f0b7d953-34f9-497d-98f4-b9e5b6f25a4f" />

7. **Шифратор перестановкой для локальных заметок:** Напишите простейший обратимый шифратор строк для сокрытия черновиков в локальной базе.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5f264e4d-253b-4079-b90d-6ba6dae7ce8c" />

8. **Проверка биометрической готовности:** Напишите логику оценки возможности аутентификации по отпечатку пальца на основе набора булевых флагов оборудования и разрешений.<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/40b25749-357e-432d-be1b-e45eae0594cc" />

9. **Контроль сессии по тайм-ауту:** Реализуйте сброс состояния экрана в случае неактивности пользователя более 3 минут. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4f129958-5b43-4c27-9bc3-30f43c23b464" />

10. **Валидатор промокода:** Проверьте правильность промокода по маске: 4 заглавные латинские буквы, дефис, 4 цифры (например, `SALE-2026`). <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4215c503-ac61-4a87-b1ea-9425dbea2270" />



### Блок 2: Работа со списками, каталогами и кэшем (RecyclerView Logic)

11. **DiffUtil-компаратор элементов списка:** Напишите метод, принимающий старый и новый списки элементов `NewsItem` и возвращающий список ID измененных и удаленных позиций. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/96bac8bc-af31-4e69-9ab6-286b6f4df108" />

12. **Пагинация ленты новостей:** Напишите класс `PaginationHelper`, который по номеру страницы `page` и размеру страницы `pageSize = 20` извлекает нужный срез из общего массива новостей. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/072bd6d8-d85a-41ee-8148-e332c7b66264" />

13. **Группировка контактов по первой букве:** Дан список имен. Сгруппируйте их в `Map<Character, List<String>>` для отображения заголовков секций. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9953cd79-418d-4323-9db8-83b372262dfb" />

14. **Полнотекстовый фильтр списка:** Реализуйте метод фильтрации каталога товаров по вхождению подстроки в наименование или артикул без учета регистра. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3bf63a27-6d28-4806-851e-59271eb84204" />

15. **Карусель промо-баннеров:** Реализуйте циклическое получение следующего элемента баннера по индексу текущего клика. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d864c199-a6c2-4e16-9062-3316cee60c4d" />

16. **Подсчет суммарной стоимости корзины с учетом промокода:** Рассчитайте сумму позиций с учетом скидок на отдельные категории товаров. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4fad84ef-46f0-479c-bd6a-4459820b7c12" />

17. **Удаление свайпом с возможностью отмены (Undo):** Спроектируйте логику временного буфера удаленного элемента списка с таймером фиксации удаления. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/55e35041-271c-43f9-9eb4-116c798eee5b" />

18. **Сортировка чатов по времени последнего сообщения:** Отсортируйте список диалогов так, чтобы сверху оказались диалоги с самыми свежими сообщениями. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dcb353c3-e4f8-4d57-b7cc-5f37a96506f7" />

19. **Поиск дубликатов в галерее по контрольной сумме:** Напишите алгоритм поиска повторяющихся файлов по размеру и имени. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c4a05685-f492-4354-a4c2-789d2dd60aae" />

20. **Ограничитель емкости кэша картинок:** Реализуйте стратегию вытеснения самого старого файла (FIFO), если суммарный объем картинок превысил 100 МБ. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8db61c62-202a-4534-8265-0272cb743be1" />


### Блок 3: Сеть, парсинг данных и офлайн-синхронизация

21. **Парсер параметров диплинка (Deep Link):** Извлеките параметры маршрутизации из URL вида `app://shop/product?id=452&source=push`. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/17d12970-58f6-4bcd-92a3-388d650d90e6" />

22. **Симулятор Retry-политики запроса:** Смоделируйте выполнение сетевого вызова с 3 повторами при возникновении `SocketTimeoutException`. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3ffa7f63-c394-4b21-818e-cc7b64833a80" />

23. **Очередь отложенных офлайн-действий:** Создайте класс `OfflineActionQueue`, сохраняющий лайки и комментарии при отсутствии сети и отправляющий их пачкой при подключении. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/62554e8f-45e4-48bb-af92-7cdd7497a7ff" />

24. **Слияние локальных данных с сервером (Conflict Resolver):** Напишите резолвер конфликта версии заметки: если серверная версия новее локальной, обновлять локальную, иначе отправлять запрос на перезапись. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/5a73d230-ff79-4aab-8c6a-ad2fae73abd2" />

25. **Оценка скорости скачивания файла:** По количеству байт и времени в миллисекундах рассчитайте скорость передачи в КБ/с и Мбит/с. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/24853efc-0f2d-4f0d-8a5b-0e0f32a3ae7a" />

26. **Парсинг заголовков пагинации сервера:** Извлеките из заголовка `Link: <https://api.com/items?page=3>; rel="next"` номер следующей страницы. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/af1ca17c-56ed-418d-a689-59c8fa350224" />

27. **Проверка актуальности кэша по ETag:** Реализуйте логику: если переданный `ETag` совпадает с сохраненным, возвращать ответ `304 Not Modified` без загрузки тела. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c293614b-c54b-46f4-95f9-922d162da69e" />

28. **Форматирование байтов в читаемый вид:** Переведите число байт в строку: `1024` -> `"1.0 KB"`, `1048576` -> `"1.0 MB"`, `1536` -> `"1.5 KB"`. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f4d0f61f-03c1-4aa6-93f3-bf2ec76a50b4" />

29. **Имитация веб-сокета для биржевого виджета:** Напишите интерфейс слушателя котировок валют и генератор событий с интервалом. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a2e0acfb-e37c-424f-8b50-5885dd41a59e" />

30. **Валидатор ответа API:** Напишите метод проверки обязательных полей JSON-объекта профиля на `null`. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ecc9f5b7-e877-496a-a109-562828781f16" />


### Блок 4: Управление состоянием экрана и UI-архитектура
 

31. **Конечный автомат экрана загрузки (UI State MVI):** Реализуйте переключение состояний экрана через `sealed`-иерархию или `enum`: `Loading`, `Success(data)`, `Empty`, `Error(message)`. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f505a6ba-d063-444c-85ca-a30306ff0133" />

32. **Дебаунсер кликов (Anti-Double Click):** Напишите класс-обертку над кликом, предотвращающий повторный вызов действия, если с момента прошлого клика прошло менее 500 мс. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a9d89200-33c9-4dcf-a106-de1168dde566" />

33. **Стек навигации экранов (BackStack):** Реализуйте собственную структуру данных стека экранов с поддержкой операций `push(Screen)`, `pop()` и `popToRoot()`. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/b919d069-d22d-4f76-b5dd-2e6c344da2ea" />


34. **Моделирование темной и светлой темы:** Спроектируйте класс `ThemePalette`, возвращающий шестнадцатеричные коды цветов (HEX) в зависимости от выбранного режима оформления. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/79e590c5-ef58-49b6-81e6-021d0ca6c250" />

35. **Расчет прогресса заполнения профиля:** Определите процент заполненности аккаунта пользователя (аватар, био, телефон, почта, город). 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d9b36ae5-fa47-4281-bbf4-b762905c8f61" />

36. **Инвертор цвета текста для контрастности:** По заданному цвету фона (RGB) вычислите, какой цвет текста отображать для читаемости — белый или черный (по формуле яркости YIQ). 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2871c698-3d1d-4fcc-81da-d6d9fdae29f5" />

37. **Форматирование счетчика лайков:** Переведите большие числа в сокращения: `950` -> `"950"`, `1200` -> `"1.2K"`, `1500000` -> `"1.5M"`. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/02b88712-f235-4a46-b785-b6a99444d197" />

38. **Менеджер системных диалогов:** Реализуйте класс очереди показа диалоговых окон, исключающий перекрытие одного всплывающего окна другим. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c8fb1288-660f-40f5-94d1-663219c01034" />

39. **Валидатор состояния кнопки "Оплатить":** Кнопка активна только при условии: корзина не пуста, выбран способ оплаты, адрес доставки подтвержден. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/acdbbc89-be67-468a-b533-24d647a5a0ff" />

40. **Транслятор ошибок для пользователя:** Напишите метод, переводящий системные исключения (`TimeoutException`, `UnknownHostException`) в понятные человекочитаемые подсказки на русском языке. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9824daa6-7246-46eb-859d-25b27f12d82a" />


### Блок 5: Аппаратные функции, геолокация и фоновые задачи

41. **Расчет расстояния между двумя GPS-точками:** Реализуйте формулу гаверсинусов (Haversine formula) для определения дистанции в метрах между координатами пользователя и курьера. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4f9af018-05c2-40aa-945d-a464b43ebe06" />

42. **Определитель вхождения в геозону (Geofencing):** Напишите метод, определяющий, находится ли точка с координатами `(lat, lon)` внутри окружности радиуса `R` с центром в `(centerLat, centerLon)`. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bb80e7c4-10f6-4667-801c-c6ff92cf861d" />

43. **Энергосберегающий планировщик геолокации:** Изменяйте частоту опроса датчика GPS в зависимости от уровня заряда батареи: > 50% (каждые 5 сек), 15-50% (каждые 30 сек), < 15% (раз в 5 минут). <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f905637c-c8a3-4d2a-9ead-7d2471422001" />

44. **Детектор падения смартфона (акселерометр):** Напишите алгоритм, определяющий состояние невесомости / резкого ускорения по 3 осям `(X, Y, Z)` при превышении критического порога. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/86a0944c-3be0-41b8-bb7a-bae0bcf04f90" />

45. **Шагомер на основе пиковых амплитуд:** Дан массив показаний вертикального ускорения. Подсчитайте количество совершенных шагов по локальным максимумам выше заданного барьера. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2668ec31-63ac-4a40-9778-0e79d0f0ad3d" />

46. **Контроллер яркости по датчику освещенности:** Преобразуйте люксы (Lux) внешнего освещения в процент яркости экрана (0-100%) по логарифмической шкале. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/95c8ac46-4684-48ff-a31f-30612dc93936" />

47. **Планировщик фоновой синхронизации данных:** Проверьте совокупность системных условий для старта тяжелой синхронизации: подключение к Wi-Fi + устройство подключено к зарядному устройству. 
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2a34881c-17b0-4e9b-9eb8-576c662436be" />

48. **Монитор расхода мобильного трафика:** Спроектируйте класс, суммирующий трафик раздельно для мобильной сети и Wi-Fi с предупреждением при достижении лимита в 5 ГБ.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9d2dc059-27d4-44c4-9e54-b25d67075f88" />

49. **Плеер аудиофайлов (State Machine):** Смоделируйте жизненный цикл аудиоплеера: `IDLE` -> `INITIALIZED` -> `PREPARED` -> `PLAYING` -> `PAUSED` -> `STOPPED`. Блокируйте недопустимые переходы. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1dd9ba03-eec0-46eb-a294-7d33ced0bdc4" />

50. **Логгер крашей приложения:** Реализуйте запись стектрейса ошибки, версии Android, модели смартфона и свободного места на накопителе в форматированный отчет об аварийном завершении. <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/9a6726af-d423-4d50-b86c-3623788a3f2c" />



---
