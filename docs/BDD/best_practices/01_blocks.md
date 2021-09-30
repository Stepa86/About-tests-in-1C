---
title: Блоки тестов
---

# Структура каталогов/блоки тестов

- По каждой проверяемой функциональности **должен быть свой каталог** с тестами полностью независимый от других тестов. Этот каталог буду называть `блок тестов`.

    Одна функциональность - одна листьевая функциональная подсистема.

- У каталога должно быть **короткое и понятное имя**, образованное от имени проверяемого функционала и цифровой префикс. Это и будет именем блока.
- Для каждого блока должен быть **свой json** файл. В имени сборки должен быть указано имя каталога/блока.
- Каждый фиче-файл в блоке **должен быть с цифровым префиксом**.

    Порядок запуска тестов определяется по сортировке по имени. Выбирать префиксы лучше с запасом и с учетом того, что могут появится новые тесты. Например, сделать нумерацию: 010, 020, 030, 040... Если понадобится добавить новый фиче-файл после 020, то ему можно присвоить префикс 025.

- Тесты внутри блока должны разделяться на:
    - Служебные файлы - каталог `internal` с фичами для экспортных сценариев, эталонными mxl и прочим.
    - Тесты для наполнения **внешних данных** (номенклатура, контрагенты, точки доставки и т.д.). Это данные, которые являются внешними по отношению к текущему функционалу.
    - Тесты для наполнения **входящих данных** (заказы, заявки, распоряжения и т.д.). Это данные, которые являются частью текущего функционала
    - Тесты, **проверяющие работу функционала**. Отдельная подготовка НСИ и входящих данных в базе позволяет гонять тесты несколько раз, не разворачивая базу заново.

- Блок должен быть коротким. Он должен выполнятся **не дольше 15 минут**. В идеале - 5 минут на блок.
- Блок тестов **полностью самостоятельная и независимая** структура. Он должен запускаться с подготовленной базы и не ожидать, что перед ним будет выполнен какой то другой блок тестов.
- Блок тестов должен быть **перезапускаемым**. Тесты должны быть написаны таким образом, чтобы результат прохождения тестов не менялся от количества перезапусков этого блока. Должен поддерживаться сценарий - запустили тест по блоку, упало на ошибке, поправили ошибку, обновили cf, запустили тесты с начала (данные остались от половины прошедшего блока) и они корректно отработали до ошибки и продолжили работу.
- В конце блока **все сеансы клиентов** тестирования **должны быть завершены**. Между запуском блоков обычно выполняется удаление базы и подготовка новой. Открытые сеансы могут этому мешать, особенно если не сработало принудительное завершение сеансов средствами VA.
- При начале тестов и при завершении в блоке следует **вызывать общие сценарии**. Сперва он может быть пустой, но потом в нем могут появиться шаги, применимые для всех блоков. Например, в сценарии "При начале блока тестов" могут быть шаги по установке заголовка приложения и установке времени начала. А в сценарии "При завершении блока тестов" - проверка на отсутствие ошибок в журнале регистрации и завершение сеанса Администратора.

Короткие, независимые блоки позволят:
- Проще разрабатывать. Блок маленький, и данных мало - только под этот блок.
- Иметь независимость в разработке. Можно отдельно пилить отдельный блок и быть уверенным, что на остальные это не повлияет. Разные блоки без проблем могут пилить разные люди.
- Проще расследовать ошибки. Потому что блок маленький и ошибка не выходит за границы этого блока.
- Проще перезапускать. Если у нас есть 20 блоков, из них 3 упало, а 17 отработало. То при исправлении ошибок нужно перезапускать только эти 3 блока. **НО** когда все ошибки будут исправлены, нужно запустить все 20, чтобы убедится, что мы не сломали другие блоки исправлениями.
- Быстрее реагировать на ошибки. Мы не ждем, пока отработает 2хчасовой блок кода, а узнаем об ошибках через 5-15 минут.
- Легче использовать при разработке. Когда программист дорабатывает подсистему, то ему значительно проще запустить один блок на 10 минут, чтобы узнать, что работает, чем тесты на пару часов.
- Распараллеливать. Если у нас 2 блока на час каждый, то даже при распараллеливании это будет выполнятся в лучшем случае час. Если 5 блоков по 10 минут, то скорость можно сократить добавлением мощностей.