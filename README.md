# pos-golang-sqlc

Repositório para as aulas referentes ao SQLC


## SQLC



Pode ser instalado via go ou via snap no ubuntu. Também a a possibilide de utilizar através de container.

```bash

go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest

```

No momento, ele suporta apenas PostgreSQL, MySQL e SQLite.


```bash

sqlc generate

```

Serão gerados 3 arquivos com base nas configurações definidas no sqlc.yaml: db.go, models.go e querys.sql.go

## Anotações


Instlar o Migrte via apt:

```bash

curl -L https://packagecloud.io/golang-migrate/migrate/gpgkey | apt-key add -
echo "deb https://packagecloud.io/golang-migrate/migrate/ubuntu/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/migrate.list
apt-get update
apt-get install -y migrate

```


Executando o container do MySQL


```bash

wander@bsnote283:~/pos-golang-sqlc$ docker-compose up -d
Creating network "pos-golang-sqlc_default" with the default driver
Creating pos-golang-sqlc_mysql_1 ... done
wander@bsnote283:~/pos-golang-sqlc$ 

```




### Migrations

Exemplo de execução do migrate

```bash

wander@bsnote283:~/pos-golang-sqlc$ migrate create -ext=sql -dir=sql/migrations -seq init
/home/wander/pos-golang-sqlc/sql/migrations/000001_init.up.sql
/home/wander/pos-golang-sqlc/sql/migrations/000001_init.down.sql
wander@bsnote283:~/pos-golang-sqlc$ 
wander@bsnote283:~/pos-golang-sqlc$ tree
.
├── README.md
└── sql
    └── migrations
        ├── 000001_init.down.sql
        └── 000001_init.up.sql

2 directories, 3 files
wander@bsnote283:~/pos-golang-sqlc$ 


```
O migrate pode trabalhar com sequencial o timestamp.

No arquivo 000001_init.up.sql colocamos o SQL para rodar na primeira migration. No 000001_init.down.sql colocamos o sql que destroi o que está no arquivo anterior.

Executando o migrate up no MySQL

```bash

wander@bsnote283:~/pos-golang-sqlc$ migrate -path=sql/migrations -database "mysql://root:root@tcp(localhost:3306)/courses" -verbose up
2024/05/26 09:57:42 Start buffering 1/u init
2024/05/26 09:57:42 Read and execute 1/u init
2024/05/26 09:57:42 Finished 1/u init (read 3.122575ms, ran 22.38539ms)
2024/05/26 09:57:42 Finished after 29.468238ms
2024/05/26 09:57:42 Closing source and database
wander@bsnote283:~/pos-golang-sqlc$ 


```
Conferindo a execução:

```bash

docker-compose exec mysql bash
mysql -uroot -p courses
show tables;
desc courses;
desc categories;
select * from categories;
select * from courses;
```

Executando o migrate down no MySQL

```bash

wander@bsnote283:~/pos-golang-sqlc$ migrate -path=sql/migrations -database "mysql://root:root@tcp(localhost:3306)/courses" -verbose down
2024/05/26 10:03:44 Are you sure you want to apply all down migrations? [y/N]
y
2024/05/26 10:03:46 Applying all down migrations
2024/05/26 10:03:46 Start buffering 1/d init
2024/05/26 10:03:46 Read and execute 1/d init
2024/05/26 10:03:46 Finished 1/d init (read 9.016795ms, ran 12.86414ms)
2024/05/26 10:03:46 Finished after 2.549929556s
2024/05/26 10:03:46 Closing source and database
wander@bsnote283:~/pos-golang-sqlc$ 

```


Executando o migrate (via Makefile):

```bash

make migrate
make migratedown

```


## Referências

Migrate

https://github.com/golang-migrate/migrate

CLI Migrate

https://github.com/golang-migrate/migrate/tree/master/cmd/migrate


sqlx

https://github.com/jmoiron/sqlx

sqlc

https://sqlc.dev/

https://github.com/sqlc-dev/sqlc
