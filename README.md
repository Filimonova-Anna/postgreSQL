# Работа с уровнями изоляции транзакции в PostgreSQL

создать новый проект в Google Cloud Platform, например postgres2022-, где yyyymm год, месяц вашего рождения (имя проекта должно быть уникально на уровне GCP), Яндекс облако или на любых ВМ, докере

- Создана виртуальная машина postgres2001-05 в программе VMware с Ubuntu

далее создать инстанс виртуальной машины с дефолтными параметрами
добавить свой ssh ключ в metadata ВМ
зайти удаленным ssh (первая сессия), не забывайте про ssh-add
поставить PostgreSQL
зайти вторым ssh (вторая сессия)
запустить везде psql из под пользователя postgres
выключить auto commit

сделать в первой сессии новую таблицу и наполнить ее данными create table persons(id serial, first_name text, second_name text); insert into persons(first_name, second_name) values('ivan', 'ivanov'); insert into persons(first_name, second_name) values('petr', 'petrov'); commit;

- Таблица заполнилась данными

посмотреть текущий уровень изоляции: show transaction isolation level

- Чтение и коммиты

начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции

в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sergey', 'sergeev');
сделать select * from persons во второй сессии

видите ли вы новую запись и если да то почему?

- Новой записи нет, т.к не было коммита в первой сессии 

завершить первую транзакцию - commit;
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?

- Новая запись появилась, т.к сделали коммит в первой сессии

завершите транзакцию во второй сессии
начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;
в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?

- Новой записи нет, т.к транзакции идут последовательно, а не параллельно, фантомное чтение отключено, а коммита в обоих сессиях не было

завершить первую транзакцию - commit;
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?

- Новой записи нет, т.к транзакции идут последовательно, а не параллельно, фантомное чтение отключено, а коммита в обоих сессиях не было

завершить вторую транзакцию
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?

- Да, т.к сделали commit для транзакций в обоих сессиях
