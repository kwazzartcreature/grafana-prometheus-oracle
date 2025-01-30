# Выполнение задач по практике

Данные репозиторий представляет из себя набор решений, которые могут быть полезны при решении некоторых IT задач заказчика

Каждая папка, кроме /infra, представляет из себя решений одной конкретной задачи. Папка /infra используется для проверки решений, инциализации oracledb с изначальными данными в init.sql

## Важные ограничения!

- У меня не было доступа к инфраструктуре компании, поэтому я выполнял и тестировал решения только на своих серверах. Из-за этого я был вынужден использовать oracledb-xe, при этом я не имею возможности зарегистрировать аккаунт oracle. Поэтому я использовал открытый и самый свежий из найденных образ, который смог найти: gvenzl/oracle-free:latest

### /prometheus_exporter

Задача заключалась в поиске open source способа подключения grafana к oracledb. Моё решение заключается в использовании prometheus для получения метрик из oracle и построения дашборда для этих метрик. При этом используется open source exporter iamseth/oracledb_exporter:latest (https://github.com/iamseth/oracledb_exporter). В теории можно написать свой exporter (который должен предоставлять GET /metrics ручку для prometheus).

##### ИНФЕРЕНС И НАСТРОЙКА PROMETHEUS

в compose.yaml сервис prometheus показывает пример настройки, с монтированием конфига и данных в volume
prometheus/prometheus.yaml содержит простейший пример конфига. Для сбора метрик из oracledb_exporter необходимо указать URL этого сервиса так, что prometheus его видел.
Например, в моём конфиге указано имя и внутренний порт oracledb_exporter, поскольку оба сервиса находятся в одной docker-подсети по-умолчанию

##### ИНФЕРЕНС И НАСТРОЙКА EXPORTER

Для примера используется найденный open source exporter с возможностью кастомизировать метрики
https://github.com/iamseth/oracledb_exporter (503 звезды github, реализация на golang)
На основе общего образа создаётся кастомный в exporter/Dockerfile (как в примере на github, чтобы прокинуть кастомные метрики)
Эти метрики могут быть любыми SQL запросами

При подключении exporter ключевым является правильно настроить DATA*SOURCE_NAME. В compose файле предложена настройка. Важно, что EXPORTER_DBNAME:EXPORTER_DBPASSWORD - это креденшиалс пользователя oracledb, у которого должны быть следующие права:
GRANT CONNECT TO exporter;
GRANT SELECT ANY DICTIONARY TO exporter;
GRANT SELECT ON V*$SESSION TO exporter;
GRANT SELECT ON V_$SYSSTAT TO exporter;
GRANT SELECT ON V*$INSTANCE TO exporter;
GRANT SELECT ON V*$LOG TO exporter;
GRANT SELECT ON V_$LOG*HISTORY TO exporter;
GRANT SELECT ON V*$SYSTEM_EVENT TO exporter;
GRANT SELECT ON V_$EVENT*NAME TO exporter;
GRANT SELECT ON V*$WAITCLASSMETRIC TO exporter;
GRANT SELECT ON V_$SYS*TIME_MODEL TO exporter;
GRANT SELECT ON V*$SESSTAT TO exporter;
GRANT SELECT ON V_$SQL TO exporter;
GRANT SELECT ON V\_$SQLAREA TO exporter;

##### НАСТРОЙКА GRAFANA

1. Добавить prometheus в data source

- Указать URL подключения к prometheus и сохранить

2. Подключить пример дашборда для oracle

- В меню дашборды выбрать "import dashboard"
- Указать в поле id 3333
- Выбрать источник данных prometheus
- Подтвердить

### /metabase

### /2d-visual

### /oracledb_stats
