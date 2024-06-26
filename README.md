# Назаров Рустам Тестовое задание в VK Go разработчик
## Stack: gRPC, PostgreSQL, Protobuf, Go, Docker, Postman

## gRPC + Protobuf
Почему я решил работать с gRPC? Очень удобно, особо не думаешь о http запросах, как когда я делал проекты на RestApi, так как proto файлы делают все за тебя. 
Навыки в этом получил на курсе Go ИТМО + гугл. 

## PostgreSQL
Решил работать с PostgreSQL. Если опыт подключения на Docker. Решил тут не эксперементировать с новым, ведь Postrges находится в стеке вакансии)

## Docker + Postman
Тестировал программу этими технологиями. Postman в удобном приложении ловит мой localhost и позволяет отправлять множество запросов. Ну и докер позволяет поднять базу данных, чтобы не делать это вручную локально.

## Конкретно. Что за проект, как работает, как запускал
Нужный из задания интерфейс переехал в ./internal/service/service.go. Логика такая: Нам приходит запрос Check. Мы запускаем в базе addResponse(), добавляем новый запрос в БД. Дальше вызываем GetStat(), там SQL запросом получаем строки с нужным id и временем большим нашего интервала. Интервал и Макс. запросов прописаны в структуре Service. Тем самым мы получаем число подходящий строк, то есть число запросов. И отвечаем на вопрос

Работал на Windows. Код запускал в Ubuntu

#### gRPC
В репозитории есть MakeFile. Он нужен чтобы установить proto и сгенерировать необходимые proto файлы, как раз, чтобы не задумываться о http, там это и происходит. Дальше билдим и получаем app ZIP файл. Поэтому заранее командой устанавливаю unzip.

(\internal)
Что касается кода. Стандартно, пишем наш service, прописываем работу с Repository. Генерируем код по нашему Proto (написан по интерфейсу Сервиса). Осталось только связать gRPC с Service. Тут просто делаем небольшой api.go. Имплементируем нужный интерфейс, ждем вызова Check, вызываем Check в Service. Всу

#### Postgres
Постарался отразить свою работу в MakeFile. Но опишу конкретные шаги. 

1) docker pull postgres - Качаем postgres
2) docker run --name=flood-controll-task -e POSTGRES_PASSWORD=admin -p 5432:5432 -d --rm postgres - Запускаем контейнер с БД
3) migrate create -ext sql -dir ./schema -seq init - Создали файлы, чтобы прописать миграцию. SQL Код для создания и удаления БД. (./schema)
4) migrate -path ./schema -database 'postgres://postgres:admin@localhost:5432/postgres?sslmode=disable' up - Подняли наши таблицы
5) migrate -path ./schema -database 'postgres://postgres:admin@localhost:5432/postgres?sslmode=disable' down - Убрали наши таблицы

Что касается кода. Стандартно в (./repository/) Пишем интерфейс и прописываем реализацию с query запросами. Таблица выглядит прост: id-time. С каждым новым запросом появляется новая строка для него. В итоге удобно считать строки по времени.

#### Docker 
Запускаю make build. Получаю Zip app. Делаю chmod, чтобы дальше запустить программу.

# ---------

Когда завершите задачу, в этом README опишите свой ход мыслей: как вы пришли к решению, какие были варианты и почему выбрали именно этот. 

# Что нужно сделать

Реализовать интерфейс с методом для проверки правил флуд-контроля. Если за последние N секунд вызовов метода Check будет больше K, значит, проверка на флуд-контроль не пройдена.

- Интерфейс FloodControl располагается в файле main.go.

- Флуд-контроль может быть запущен на нескольких экземплярах приложения одновременно, поэтому нужно предусмотреть общее хранилище данных. Допустимо использовать любое на ваше усмотрение. 

# Необязательно, но было бы круто

Хорошо, если добавите поддержку конфигурации итоговой реализации. Параметры — на ваше усмотрение.
