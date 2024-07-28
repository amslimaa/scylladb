## Scyla DB
Banco de dados NoSQL Distribuído.

- [x] Deploy -> [Metodo de Deploy](https://university.scylladb.com/setup-a-scylla-cluster/)
- [x] CQLSH
- [x] Keyspace
- [x] Tabelas
- [x] Inserir registros
- [x] Select + INDEX

## Pre-requisitos

Antes de iniciar o cluster, certifique-se de que o valor [aio-max-nr](https://www.kernel.org/doc/Documentation/sysctl/fs.txt) seja alto o suficiente (1048576 ou mais). 

Este parâmetro determina o número máximo de solicitações simultâneas de E/S assíncronas não bloqueantes (AIO) permitidas pelo kernel do Linux e ajuda o ScyllaDB a ter desempenho em um ambiente de carga de trabalho de E/S pesada.


```bash
git clone https://github.com/scylladb/scylla-code-samples.git ## clonar repositorio com exemplos

cat /proc/sys/fs/aio-max-nr ##verificar o valor de aio-max-nr

echo "fs.aio-max-nr = 1048576" >> /etc/sysctl.conf #alterar para...

sysctl -p /etc/sysctl.conf 
```

## Deploy
```bash
docker compose up ## deploy
docker exec -it scylladb-node-1 nodetool status ## check services
``` 

## CQLSH
```bash
docker exec -it scylla-node1 cqlsh
```

## Keyspace
```sql
CREATE KEYSPACE pets_db WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor':3};

USE pets_db
```
## Table

```sql
CREATE TABLE heartrate(
    pet_chip_id text,
    owner text,
    time timestamp,
    heart_rate int,
    PRIMARY KEY (pet_chip_id, time)
);

```


## Inserrir Registros

```sql
    INSERT INTO heartrate(pet_chip_id, owner, time, heart_rate) VALUES ('c3e8b6a0-8e1a-4f0e-9e3d-6d8e5c2b9a1f','a1f9b8c7-2e3d-4c5b-9a0f-6d8e1a3c7b9', '2022-01-01 12:00:00', 80);
    INSERT INTO heartrate(pet_chip_id, owner, time, heart_rate) VALUES ('f9d4a7b2-6e8c-4d3f-9a5b-1e2c0f8d7a6e', 'a1f9b8c7-2e3d-4c5b-9a0f-6d8e1a3c7b9', '2022-01-02 09:30:00', 75);
    INSERT INTO heartrate(pet_chip_id, owner, time, heart_rate) VALUES ('d5b3e8c1-9a2f-4e7c-8b6d-0f1a3c5e2d9b', 'e2d9c8b7-5a4f-3e1d-9c0b-6f8a7d2e1c3b', '2022-01-03 15:45:00', 85);
```

## Select

``` sql
SELECT * FROM heartrate where pet_chip_id = 'c3e8b6a0-8e1a-4f0e-9e3d-6d8e5c2b9a1f';

SELECT * FROM heartrate where owner = 'a1f9b8c7-2e3d-4c5b-9a0f-6d8e1a3c7b9'; --ERRO

SELECT * FROM heartrate where time >= '2022-01-01 12:00:00'; --WARNING

SELECT * FROM heartrate where pet_chip_id = 'f9d4a7b2-6e8c-4d3f-9a5b-1e2c0f8d7a6e' AND time >= '2022-01-01 12:00:00';

```

## Index

```sql
SELECT * FROM heartrate where owner = 'a1f9b8c7-2e3d-4c5b-9a0f-6d8e1a3c7b9'; --ERRO

CREATE INDEX pet_owner ON heartrate(owner);
  
```