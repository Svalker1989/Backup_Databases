# Домашнее задание к занятию "`Резервное копирование баз данных`" - `Стрекозов Владимир`
### Задание 1. Резервное копирование
Кейс  
Финансовая компания решила увеличить надёжность работы баз данных и их резервного копирования.  
Необходимо описать, какие варианты резервного копирования подходят в случаях:  
1.1. Необходимо восстанавливать данные в полном объёме за предыдущий день.  
Я бы использовал инкрементный ежедневный бэкап, с требуемой глубиной хранения от 1 до 7 дней.  
1.2. Необходимо восстанавливать данные за час до предполагаемой поломки.  
Если поломка предполагалась, то можно использовать снепшот ВМ перед работами. Если это случайная поломка, то можно использовать переключение на реплику. Так же можно через планировщик выгружать изменения в файл на ежечасно основе.  
1.3.* Возможен ли кейс, когда при поломке базы происходило моментальное переключение на работающую или починенную базу данных.  
Да возможен, можно использовать реплицирование, переключая роль мастера между репликами можно достичь желаемого результата.  
### Задание 2. PostgreSQL
2.1. С помощью официальной документации приведите пример команды резервирования данных и восстановления БД (pgdump/pgrestore).  
Резервирование в sql скрипт:  
`$ pg_dump mydb > db.sql`  
Резервирование в архив:  
`$ pg_dump -Fc mydb > db.dump`  
2.1.* Возможно ли автоматизировать этот процесс? Если да, то как?  
Можно, через баш скрипт + задание в crontab.  
Script:  
```bash
#!/bin/bash
BACKUP_DIR="/data/psql-db-backup/"
FILE_NAME=$BACKUP_DIR`date +%d-%m-%Y-%I-%M-%S-%p`.sql
pg_dump -U db_user db_name > $FILE_NAME
```
Crontab entry:  
```0 */3 * * * /data/postgresql-backup.s```  
### Задание 3. MySQL
3.1. С помощью официальной документации приведите пример команды инкрементного резервного копирования базы данных MySQL.  
Создаст новый бинарный лог изменений(по сути инкремент), сохранив предыдущий бинарный лог.  
```mysqladmin -uroot -p flush-logs```  
Чтобы восстановить инкрементный бэкап:  
```mysqlbinlog /var/log/mysql/mysql-bin.000002 | mysql -uroot -p mydb```  
3.1.* В каких случаях использование реплики будет давать преимущество по сравнению с обычным резервным копированием?  
Реплика позволит практически моментально переключить пользователей на работающую БД. В случае с бэкапом потребуется время на восстановление.  
