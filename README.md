# Домашнее задание к занятию «Базы данных, их типы»

### Задание 1. СУБД

### Кейс
Крупная строительная компания, которая также занимается проектированием и девелопментом, решила создать 
правильную архитектуру для работы с данными. Ниже представлены задачи, которые необходимо решить для
каждой предметной области. 

Какие типы СУБД, на ваш взгляд, лучше всего подойдут для решения этих задач и почему? 
 
1.1. Бюджетирование проектов с дальнейшим формированием финансовых аналитических отчётов и прогнозирования рисков.
СУБД должна гарантировать целостность и чёткую структуру данных.

1.1.* Хеширование стало занимать длительно время, какое API можно использовать для ускорения работы? 

1.2. Под каждый девелоперский проект создаётся отдельный лендинг, и все данные по лидам стекаются в CRM к 
маркетологам и менеджерам по продажам. Какой тип СУБД лучше использовать для лендингов и для CRM? 
СУБД должны быть гибкими и быстрыми.

1.2.* Можно ли эту задачу закрыть одной СУБД? И если да, то какой именно СУБД и какой реализацией?

1.3. Отдел контроля качества решил создать базу по корпоративным нормам и правилам, обучающему материалу 
и так далее, сформированную согласно структуре компании. СУБД должна иметь простую и понятную структуру.

1.3.* Можно ли под эту задачу использовать уже существующую СУБД из задач выше и если да, то как лучше это 
реализовать?

1.4. Департамент логистики нуждается в решении задач по быстрому формированию маршрутов доставки материалов 
по объектам и распределению курьеров по маршрутам с доставкой документов. СУБД должна уметь быстро работать
со связями.

1.4.* Можно ли к этой СУБД подключить отдел закупок или для них лучше сформировать свою СУБД в связке с СУБД 
логистов?

1.5.* Можно ли все перечисленные выше задачи решить, используя одну СУБД? Если да, то какую именно?

*Приведите ответ в свободной форме.*

---

1.1. Бюджетирование, аналитика и прогнозирование рисков.
Для этой задачи лучше всего подойдёт реляционная СУБД, такая как PostgreSQL.
Она обеспечивает:
чёткую и фиксированную структуру данных,
поддержку транзакций,
гарантии целостности и согласованности (ACID),
что особенно важно для финансовых данных и отчётности.

1.1* Хеширование стало медленным - что делать?
Если хеширование стало занимать много времени, можно использовать Redis) для хранения промежуточных результатов. Это позволит сократить повторные вычисления. Также стоит рассмотреть использование более быстрых библиотек хеширования и алгоритмов, подходящих под конкретную задачу.

1.2. Лендинги и CRM (маркетинг и продажи)
Под эту задачу тоже подойдёт реляционная СУБД с поддержкой JSON, например PostgreSQL.
Почему:
лендинги часто содержат разнообразные и гибкие данные (например, формы заявок),
CRM требует чёткой структуры, связей и отчётности.
PostgreSQL позволяет объединить обе задачи:
для лендингов - можно использовать JSON-поля,
для CRM - классические таблицы с отношениями.

1.2* Можно ли использовать одну СУБД?
Да, это удобно и практично.
В PostgreSQL можно разделить разные направления - лендинги и CRM - по схемам или таблицам, не cоздавая отдельные базы.

1.3. База корпоративных норм и обучающих материалов (контроль качества)
Здесь также подойдёт реляционная СУБД.
Материалы можно структурировать по темам, категориям, ответственным отделам и связать между собой через таблицы. Это соответствует описанной в лекции реляционной модели данных.

1.3* Можно ли использовать уже существующую СУБД?
Да, можно.
В PostgreSQL можно создать отдельную схему или таблицы внутри уже работающей базы - например, той, что используется для CRM. Это позволит не усложнять инфраструктуру, но при этом чётко разграничить области.

1.4. Логистика и маршруты доставки.
Для этой задачи подойдёт графовая СУБД,потому что маршруты и связи между точками - это типичная графовая структура.
Также возможно использовать PostgreSQL с расширением PostGIS - для хранения координат, маршрутов и расчёта расстояний.

1.4* Закупки - подключать к логистике или отделять?
Если логистика и закупки связаны (например, закупленные материалы нужно доставить), их можно вести в одной базе, но в разных схемах.
Если процессы сильно отличаются - можно использовать две базы и синхронизировать их по нужным точкам (например, складам или заказам).

1.5* Можно ли всё реализовать в одной СУБД?
Да, можно.
PostgreSQL - универсальная СУБД, которая:
поддерживает транзакции и строгую структуру (для бюджета, CRM),
позволяет хранить гибкие данные (для лендингов),
расширяется модулями (для графов и геоданных),
даёт возможность разделять схемы по отделам.
Таким образом, все задачи можно решить в единой системе, если грамотно организовать архитектуру.



### Задание 2. Транзакции

2.1. Пользователь пополняет баланс счёта телефона, распишите пошагово, какие действия должны произойти для того, чтобы 
транзакция завершилась успешно. Ориентируйтесь на шесть действий.

2.1.* Какие действия должны произойти, если пополнение счёта телефона происходило бы через автоплатёж?

*Приведите ответ в свободной форме.*

---

2.1. Ручное пополнение баланса:
Когда пользователь вручную пополняет баланс телефона, можно выделить 6 шагов, соответствующих логике транзакции:

1. Пользователь вводит номер телефона и сумму пополнения через сайт или приложение.
Формирование платёжного запроса;

2. Система формирует запрос к платёжному шлюзу и передаёт данные пользователя и сумму;

3. Платёжный шлюз направляет запрос в банк. Банк проверяет карту и, если всё в порядке, списывает средства;

4. Система получает подтверждение от платёжного шлюза, что платёж прошёл успешно;

5. Биллинг-система оператора связи увеличивает баланс пользователя на указанную сумму;

6. Пользователь получает сообщение (SMS или уведомление в приложении) о пополнении.
Всё проходит как единая транзакция - либо успешно, либо ничего не меняется.
Если на любом этапе произойдёт ошибка - операция отменяется, и деньги не списываются.

2.1* Автоплатёж:

В случае автоплатежа шаги аналогичные, но платёж инициирует не пользователь, а система:


1. Пользователь заранее настраивает автоплатёж - указывает сумму, порог баланса и привязывает карту;

2. Система автоматически отслеживает условия -  например, наступление даты или снижение баланса;


3. При выполнении условия система инициирует платёж - как будто пользователь его запустил вручную;


4. Платёж проходит через шлюз и банк, где подтверждается и списываются средства;

5. Средства автоматически зачисляются на баланс абонента;

6. Пользователю отправляется уведомление о выполнении автоплатежа.

Система всё так же использует транзакционную модель - либо всё прошло успешно, либо нет изменений.

### Задание 3. SQL vs NoSQL

3.1. Напишите пять преимуществ SQL-систем по отношению к NoSQL. 

3.1.* Какие, на ваш взгляд, преимущества у NewSQL систем перед SQL и NoSQL.

*Приведите ответ в свободной форме.*

---

3.1. Пять преимуществ SQL-систем по отношению к NoSQL:
Транзакционность (ACID):

SQL-системы поддерживают строгие транзакции - все действия выполняются полностью или не выполняются вовсе. Это важно для финансов, учёта и других критичных операций.

Фиксированная структура данных:

SQL-базы используют схемы - заранее определённую структуру таблиц и полей, что повышает надёжность и предотвращает хаос в данных.

Стандартизированный язык SQL:

SQL - универсальный язык запросов, понятный большинству разработчиков и поддерживаемый в разных СУБД.

Зрелость и стабильность:

Реляционные СУБД существуют десятилетиями, вокруг них развитая экосистема: средства отчётности, мониторинга, резервного копирования и интеграции.

Интеграция с другими системами:

SQL легко подключается к BI-системам, CRM, ERP и другим бизнес-инструментам. Это упрощает отчётность и аналитику.

NoSQL - гибкий, но менее структурированный.

3.1* NewSQL сочетает надёжность SQL и масштабируемость NoSQL. Такие системы поддерживают транзакции (ACID), но могут эффективно работать на большом количестве серверов, что делает их подходящими для высоконагруженных приложений.

### Задание 4. Кластеры

Необходимо производить большое количество вычислений при работе с огромным количеством данных, под эту задачу выделено 1000 машин. 

На основе какого критерия будете выбирать тип СУБД и какая модель *распределённых вычислений* 
здесь справится лучше всего и почему?

*Приведите ответ в свободной форме.*

---

Если задача требует большого количества вычислений и под неё выделено 1000 машин, при выборе СУБД основное внимание следует уделить масштабируемости и возможности распределённой обработки данных.

Критерий выбора СУБД - способность эффективно работать с большими объёмами информации в распределённой среде. Для этого лучше всего подходят NoSQL-СУБД, особенно колоночного типа, так как они оптимальны для аналитических задач и хорошо масштабируются по узлам.

Подходящий тип СУБД - колоночная NoSQL. Такие базы:

хорошо справляются с большими данными,

не требуют строгой схемы,

легко разворачиваются на большое количество машин.

Модель вычислений должна быть распределённой, с параллельной обработкой данных на множестве серверов. Это позволяет эффективно использовать все ресурсы кластера и ускоряет обработку.

Вывод:
Для такой задачи подойдёт колоночная NoSQL-СУБД в сочетании с распределённой моделью вычислений, обеспечивающей масштабируемость и высокую производительность системы.