---
title: Лучшие практики
---

# Лучшие практики по написанию автоматических тестов

## Тест-клиенты

1. Шаг для подключения тест-клиента

    ```gherkin
    Дано Я подключаю клиент тестирования с параметрами:
    | 'Имя'           | 'Логин'         | 'Пароль' | 'Дополнительные параметры строки запуска' |
    | 'Администратор' | 'Администратор' | ''       |  '/CРежимОтладки'                         |
    ```

2. Для подготовки пользователей используйте шаги из библиотеки по созданию групп доступа и пользователей. Например:

    ```gherkin
    И Я создаю группу доступа "Базовый профиль" с профилем "Базовый профиль"
    И Я создаю группу доступа "Планирование разруба" с профилем "Планирование разруба"
    И Я создаю пользователя "Планирование разруба" с профилями "Базовый профиль;Планирование разруба"
    ```

3. Если тест-клиент больше не нужен - его нужно закрыть, кроме администратора. Но если нужен, то закрывать не нужно, т.к. запуск тест-клиента занимает до 20 секунд

4. Шаг для закрытия тест-клиента

    ```gherkin
    И я закрываю TestClient "Планирование разруба"
    ```

## Шаги

1. Шаг начинается с ключевого слова:

    1. `Допустим`, `Дано`, `Пусть`  - шаг устанавливает начальные настройки
    2. `Когда`, `И`, `К тому же`, `Также`  - шаг выполняет действие
    3. `То`, `Затем`, `Тогда` - шаг проверяет результат

    В начале шага можно использовать любое ключевое слово и это не будет синтаксической ошибкой. Различные шаги позволяют - более удобно читать текст и различать поведения: `Ваша программа не работает` и `Ваша программа работает, но неправильно`. Первое легко ловится и правится, т.к. выскакивает ошибка. Второе может годами оставаться в системе и никак себя не выдавать, поэтому это ошибки более серьезные.

2. Шаги можно сгруппировать. Написать любой текст, а следующие шаги в группировке писать с отступом.

    ??? info "Пример"
        ```gherkin hl_lines="2 6"
        И Я ввожу комментарий "Создание простой партии"
        * я добавляю сырье # Это группировка. Вместо "* я добавляю     сырье" можно писать любой текст
            И в таблице "Сырье" я нажимаю на кнопку с именем     'СырьеДобавить' # шаг с отступом
            И в таблице "Сырье" я нажимаю кнопку выбора у реквизита с именем "СырьеЗаписьСкладскогоЖурнала"
            Тогда открылось окно 'Меркурий: Журнал продукции'
            * я устанавливаю отбор журналу продукции # Группировка
                И Я указываю отборы по датам в списке записей журнала # Экспортный шаг с отступом
                Тогда открылось окно 'Меркурий: Журнал продукции'
                И я меняю значение переключателя 'ОтборТолькоВНаличии' на 'Только в наличии'
                И я нажимаю кнопку выбора у поля "ОтборТипПродукции"
                И из выпадающего списка "ОтборТипПродукции" я выбираю по строке 'пищевые продукты'
                И из выпадающего списка "ОтборПродукция" я выбираю по строке 'молоко и молочная продукция'
                И из выпадающего списка "ОтборВидПродукции" я выбираю по строке 'молоко сырое'
                И из выпадающего списка "ОтборНаименованиеПродукции" я выбираю по строке 'молоко сырое Глазов'
                И в поле 'ОтборМаркировка' я ввожу текст '22'
                И в поле 'ОтборИдентификаторПартии' я ввожу текст '321123'
                И из выпадающего списка "ОтборВладелец" я выбираю по строке 'ОАО \"МИЛКОМ\"'
                И из выпадающего списка "ОтборПроизводитель" я выбираю по строке 'ОАО \"Милком\" Сарапул'
            И в таблице "Список" я перехожу к строке: # Это НЕ группировка, а шаг
                | 'ИД'     | 'Наименование'                | 'Объем' | 'Срок годности'              |
                | '321123' | 'молоко сырое Глазов 2 /3 кг' | '2, кг' | '$$ОкончаниеСрокаГодности$$' |
            И в таблице "Список" я выбираю текущую строку
        ```

3. Для правильной работы группировок нужно весь фиче-файл форматировать либо табами, либо пробелами. Правильнее табами. VA поругается, если в фиче-файле есть и табы и пробелы.
4. Группировку нужно начинать со "*", чтобы было понятно, что это группировка
5. Любую повторяющуюся операцию нужно выносить или в экспортные фичи (должно лежать в этом же каталоге или в подкаталогах текущей папки, иначе не найдет) или в библиотеку. Это позволяет потом править в одном месте изменение теста и такой сценарий проще найти.
6. Вместо пауз лучше использовать асинхронные шаги. Асинхронными называются шаги, которые выполняются в течение какого-то времени. Например, проверка значения поля с использованием асинхронного шага будет выглядеть следующим образом:

    === "Значение в таблице"

        ```gherkin
        И в таблице "ИмяТаблицы" у поля "Заголовок элемента" я жду значения "Значение" в течение 20 секунд
        ```

    === "Закрытие окна"

        ```gherkin
        И я жду закрытия окна 'Профиль групп доступа (создание)' в течение 20 секунд
        ```

    === "Открытие окна"

        ```gherkin
        И я жду открытия окна "Пользователи" в течение 20 секунд
        ```

    === "Доступность элемента"

        ```gherkin
        И я жду доступности элемента "Действует с" в течение 20 секунд
        ```

    === "Ожидание формирования отчета"

        ```gherkin
        И я жду когда в табличном документе "ОтчетТабличныйДокумент" заполнится ячейка "R12C3" в течение 20 секунд
        ```

    === "Сообщение пользователю"

        ```gherkin
        Затем я жду, что в сообщениях пользователю будет подстрока "не заполнено" в течение 10 секунд
        ```

## Поля

1. Выбирать значение в поле лучше всего шагом `И из выпадающего списка "Поле" я выбираю по строке 'Значение'`. Чтобы этот шаг записался кнопконажималкой - нужно в поле ввести текст полностью и нажать Ентер.
2. Открывать форму выбора у элемента нужно только в том случае, если это основной пользовательский сценарий работы с этим полем.
3. Создавать новые элементы при выборе можно только тогда, когда это основной пользовательский сценарий. Во всех остальных случаях данные для выбора уже должны быть созданы.
    1. Это ускоряет и упрощает сценарий
    2. Это разделяет проверки на создание НСИ и провери функционала. Упасть создание НСИ в случае ошибки должно при проверке НСИ, а не при проверке документа.
    3. Тест-клиент очень не любит много открытых вложенных окон и если сценарий открывает элемент, в нем открывает форму выбора, в нем создает новый элемент, в нем открывает новую форму и в нем опять создает новый элемент - тест-клиент теряет окно и все падает.
4. Выбирать даты нужно через ввод значения, а не через выбор в календаре. `И в таблице "Продукция" в поле с именем 'ПродукцияДатаОкончанияСрокаГодности' я ввожу текст '$$ОкончаниеСрокаГодности$$'`
5. Для установки флага использовать шаг `И я устанавливаю флаг "Заголовок флага"`. Для снятия `И я снимаю флаг "Заголовок флага"`. Автоподставляемый шаг `И я изменяю флаг "Заголовок флага"` меняет значение флага и если флаг стоит не в нужном положении - будет ошибка.
6. Окончание ввода полей нужно завершать шагом `И я перехожу к следующему реквизиту`. Иначе не сработают обработчики изменения у последнего поля.
7. Для получения шагов, проверяющих значения в полях, удобно использовать команду "Получить состояние всей формы" на закладке "Работа с UI" в подменю "Форма". Или комбинация команд "Запомнить состояние формы TestClient" и "Получить изменения формы".
    Лишнее или непостоянное, типа даты документа или его номера, можно скорректировать вручную.
    ![2020-05-25_1440](best_practices_of_tests.assets/2020-05-25_1440.png).
8. Проверка, что  элемент, который скрыт через функциональные опции, условное оформление или видимость группы, не видим:

    ```gherkin
    И поле с именем "ИмяПоля" существует
    Затем Проверяю шаги на Исключение:
        |'И в поле с именем "ИмяПоля" я ввожу текст "Текст"'|
    ```

9. Если значение гиперссылки постоянно меняется, то можно открывать гиперссылку по порядковому номеру:

    ```gherkin
        И у поля "Заголовок поля" я нажимаю гиперссылку 0
    ```

## Значения

1. Учитывайте, что тест может выполнятся как один, так и в рамках прогона всех тестов по конфигурации. Тест может выполнятся на границе дня ночью. Может выполнятся повторно.
2. Не используйте в качестве значений для выбора/подстановки автогенерируемые системой значения - номер и дата документа, код справочника и аналоги. Заменяйте эти значения на * и используйте сравнение по шаблону
3. Символ `*` интерпретируется как произвольный набор символов в шагах. Некоторые шаги сразу поддерживают символ `*`, например:

    === "Некоторые шаги сразу поддерживают символ *"

        ```gherkin
        И я нажимаю на кнопку "Провести и закр*"
        ```

    === "Для некоторых используется сигнатура "по шаблону""

        ```gherkin
        И я меняю значение переключателя 'Заголовок элемента' на 'Значение переключателя(*)' по шаблону
        ```

4. Для работы с локальными переменными используются шаги в которых переменная оборачивается в символ $:

    ```gherkin
    И я запоминаю имя текущего поля как "$ИмяПеременной$"
    И в поле с именем "ИмяПоля" ввожу значение переменной "$ИмяПеременной$"
    ```

5. Для работы с глобальными переменными используются шаги в которых переменная оборачивается в символы $$:

    ```gherkin
    И я запоминаю имя текущего поля как "$$ИмяПеременной$$"
    И в поле с именем "ИмяПоля" ввожу значение переменной "$$ИмяПеременной$$"
    ```

6. Для сохранения в переменную произвольного выражения используется шаг:

    ```gherkin
    И Я запоминаю значение выражения 'Выражение' в переменную "ИмяПеременной"
    ```

## Таблицы

1. Для проверки таблиц нужно использовать следующие шаги, а не прокликивание строк:

    === "Порядок строк не важен"

        ```gherkin
        И таблица "ИмяТаблицы" содержит строки:
            |"ИмяКолонки1"|"ИмяКолонки2"|
            |"Значение1"  |"Значение2"  |
        Тогда в таблице "ИмяТаблицы" количество строк "равно" N
        ```

    === "Порядок строк важен"

        ```gherkin
        И таблица "ИмяТаблицы" стала равной:
            |"ИмяКолонки1"|"ИмяКолонки2"|
            |"Значение1"  |"Значение2"  |
        ```

2. Окончание ввода строки нужно завершить шагом `И в таблице "Таблица" я завершаю редактирование строки`. Чтобы отработали обработчики.
3. Убирайте незначительные колонки из таблицы - пустые колонки, номера строк.
4. Скрываайте звездочками переменые данные - номера документов, коды справочников, даты. Но тогда нужно использовать вариант шагов с шаблоном

    === "Порядок строк важен"

        ```gherkin
        И таблица "ИмяТаблицы" стала равной по шаблону:
        | ИмяКолонки1 | ИмяКолонки2 |
        | Значение1   | Значение2   |
        ```

    === "Шаг для макета"

        ```gherkin
        И таблица "ИмяТаблицы" содержит строки из макета "ИмяМакета" по шаблону
        ```

5. Добавлять несколько строк в таблицу можно специальным шагом. Это проще читается и сопровождается. Но не накликивается.

    ```gherkin
    И я заполняю таблицу "ИмяТаблицы" данными
    | 'ИмяКолонки1' | 'ИмяКолонки2' |
    | 'Значение1'   | 'Значение2'   |
    ```

6. При выборе значения из таблицы значений необходимо дописать искусственный шаг перехода к нужной строке, чтобы не возникало ошибки, когда строки в таблице меняются местами:

    ```gherkin
    И в таблице "ИмяТаблицы" я перехожу к строке:
        | 'ИмяКолонки'      |
        | 'ЗначениеКолонки' |
    ```

## Табличные документы

1. Большие табличные документы лучше проверять через макет, используя шаг:

    ```gherkin
    Дано табличный документ "ИмяРеквизита" равен макету "ИмяМакета" по шаблону
    ```

2. При проверке отчетов, если отчет до формирования был пустой, то дополнительные проверки не нужны. Если отчет уже сформирован, тогда нужно ждать окончания его переформирования, для этого использовать шаг:

    ```gherkin
    И я жду, что в табличном документе "ИмяРеквизита" ячейка "АдресЯчейки" станет равна "Значение" в течение 20 секунд
    ```

## Окна

1. При открытии окон обязательно проверять, что открылось новое окно с помощью шага:

    === "Синхронно"

        ```gherkin
        Тогда открылось окно "ИмяОкна"
        ```

    === "Асинхронно"

        ```gherkin
        И я жду открытия окна "ИмяОкна" в течение 20 секунд
        ```

2. Аналогично при закрытии окна нужно проверять, что оно закрылось:

    === "Асинхронно"

        ```gherkin
        И я жду закрытия окна "ИмяОкна" в течение 20 секунд
        ```

3. Команды в командном интерфейсе нажимать за один шаг, а не за два. Между шагами может обновится интерфейс и тест упадет.

    === "Неправильно в два шага"

        ```gherkin
        И В панели разделов я выбираю 'Нормативно-справочная информация'
        И В панели функций я выбираю 'Номенклатура'
        ```

    === "Правильно в один шаг"

        ```gherkin
        И В командном интерфейсе я выбираю 'Нормативно-справочная информация' 'Номенклатура'
        ```

## Динамически добавленные элементы

1. Поля должны иметь воспроизводимые имена. Если открыть одну и ту же форму, ничего не меняя в системе - то все имена полей должны быть такими же. Не должно быть полей с именем на основе уникального идентификатора.
2. Поля, которые строятся на основе дат - должны содержать эти даты в имени.

    === "В дереве есть колонки по датам"

        ```gherkin
        Тогда в таблице "ДеревоОтгрузок" поле с именем "ДеревоОтгрузокПлан_190204" имеет значение "300"
        ```

3. Поля, которые строятся по справочникам - должны содержать код справочника в имени. При создании такого справочника нужно принудительно устанавливать код на что-то уникальное.

    ??? info "Пример из ценообразования"
        Если в вид цен устанавливать код = 2 - может возникнуть ситуация, когда какие-то тесты до нашего так же создавали виды цен и код "2" уже занят. Поэтому код нужно устанавливать сильно с запасом.

        === "Установка кода"

            ```gherkin
            И я разворачиваю группу "Подробное описание"
            И в поле 'Код' я ввожу текст '0'
            Тогда открылось окно '1С:Предприятие'
            И я нажимаю на кнопку 'Да'
            Тогда открылось окно 'Вид цены (создание) *'
            И в поле 'Код' я ввожу текст '113'
            И я нажимаю на кнопку 'Записать и закрыть'
            ```

        === "Использование в дереве"

            ```gherkin
            И в таблице "ДеревоЦен" в поле с именем "ДеревоЦенНоваяЦена_113" я ввожу текст '100,00'
            И в таблице "ДеревоЦен" в поле с именем "ДеревоЦенНоваяЦена_114" я ввожу текст '80,00'
            И в таблице "ДеревоЦен" в поле с именем "ДеревоЦенНоваяЦена_115" я ввожу текст '110,00'
            И в таблице "ДеревоЦен" я завершаю редактирование строки
            ```

## Документы

1. Проведение документов лучше проверять через *"Провести и закрыть"*, проверить, что форма закрылась, и потом открыть документ заново. Если использовать *"Провести"*, то управление менеджеру может перейти раньше, чем проведение будет выполнено.
2. Движения документов нужно проверять через отчеты.
3. При переходе к документу нельзя обращаться к номеру документа, он может изменится. Нужно выбирать документ на основе тех значений на которые смотрит обычный пользователь: тип, ответственный, комментарий, важные поля шапки итп.

## Файлы

1. Шаг выбора файла надо вызывать до того, как появился диалог выбора файлов.

    === "Шаг"

        ```gherkin
        И я буду выбирать внешний файл "ИмяФайла"
        ```

    === "Пример загрузки заказов из файла"

        ```gherkin
        Сценарий: Планирование переработки. Загрузка заказов
            
            И В командном интерфейсе я выбираю 'Исполнение заказов клиентов' 'Заказы клиентов'
            Тогда открылось окно 'Заказы клиентов'
            И я нажимаю на кнопку 'Загрузить заказы из файла'
            Тогда открылось окно 'Загрузка заказов клиента из файла'
            И я меняю значение переключателя 'Вариант загрузки' на 'Из внешнего файла'
            И я перехожу к закладке "Страница вариант загрузка из файла"
            Тогда открылось окно 'Загрузка заказов клиента из файла'
            И Я буду читать файл "Заказы.csv"
            И я нажимаю на кнопку 'Загрузить таблицу из файла...'
            И я жду открытия окна 'Заказы клиентов' в течение 60 секунд
            И Я закрываю окно 'Заказы клиентов'

        # Этот сценарий расположен в библиотеке
        Сценарий: Я буду читать файл "ИмяФайла"

            И Я запоминаю значение выражения 'ОбщегоНазначенияКлиентСервер.РазложитьПолноеИмяФайла( Ванесса.ПолучитьСостояниеVanessaAutomation().ТекущаяФича.ПолныйПуть ).Путь + "\"' в переменную "КаталогТеста"
            И я буду выбирать внешний файл "$КаталогТеста$""ИмяФайла"
        ```

2. Нужные файлы для теста должны лежать в той же папке. Рядом с фичей.
