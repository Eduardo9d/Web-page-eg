### Migrar dados do Postgress para Clickhouse Local 


# VM com docker um Contentor com postgres outro com click House 
''' bash
CONTAINER ID   NAMES           STATUS
ac52f92a4818   demo_postgres   Up 27 minutes
6244d4b6cbcc   ch_server       Up 27 minutes
''' 


# Criar uma base dados de teste com nome users no postgres no ficheiro init.sql

''' sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50),
    email VARCHAR(100),
    age INT
);

INSERT INTO users (name, email, age)
SELECT
    'User' || i,
    'user' || i || '@example.com',
    (random() * 50 + 18)::int
FROM generate_series(1, 10) AS s(i);
'''

# Adicionar a Ligação dos containers em  docker-compose.yml
''' md
services:
  postgres:
    image: postgres:16
    container_name: demo_postgres
    environment:
      POSTGRES_USER: demo
      POSTGRES_PASSWORD: demo123
      POSTGRES_DB: demo_db
    ports:
      - "5432:5432"
    volumes:
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
      - demo_data:/var/lib/postgresql/data
  
  clickhouse:
    image: clickhouse/clickhouse-server:24
    container_name: ch_server
    ports:
      - "8123:8123"
      - "9000:9000"
    volumes:
      - ./clickhouse-data:/var/lib/clickhouse/
volumes: 
  demo_data:
'''
# Fazer Queies a Postgres atravez da Click House
''' sql 
SELECT *
FROM postgresql('postgres:5432', 'demo_db', 'users', 'demo', 'demo123')
LIMIT 5

Query id: 96059abe-b777-47ec-8e5f-ae3f7956e333

   ┌─id─┬─name──┬─email─────────────┬─age─┐
1. │  1 │ User1 │ user1@example.com │  31 │
2. │  2 │ User2 │ user2@example.com │  21 │
3. │  3 │ User3 │ user3@example.com │  65 │
4. │  4 │ User4 │ user4@example.com │  21 │
5. │  5 │ User5 │ user5@example.com │  45 │
   └────┴───────┴───────────────────┴─────┘
'''

# Criar uma tabela no Clickhouse para copiar os dados do Postgres

''' sql 
CREATE TABLE ch_users
(
    `id` UInt32,
    `name` String,
    `email` String,
    `age` UInt8
)
ENGINE = MergeTree
ORDER BY id
'''

''' sql 

INSERT INTO ch_users
SELECT *
FROM postgresql(
    'postgres:5432',
    'demo_db',
    'users',
    'demo',
    'demo123'
);

'''

# Verificar dados copiados para a DB ClickHouse

''' sql 
SELECT *
FROM ch_users

Query id: 2b7d22ae-478a-4ee3-9ff1-83f145895f70

    ┌─id─┬─name───┬─email──────────────┬─age─┐
 1. │  1 │ User1  │ user1@example.com  │  31 │
 2. │  2 │ User2  │ user2@example.com  │  21 │
 3. │  3 │ User3  │ user3@example.com  │  65 │
 4. │  4 │ User4  │ user4@example.com  │  21 │
 5. │  5 │ User5  │ user5@example.com  │  45 │
 6. │  6 │ User6  │ user6@example.com  │  23 │
 7. │  7 │ User7  │ user7@example.com  │  33 │
 8. │  8 │ User8  │ user8@example.com  │  43 │
 9. │  9 │ User9  │ user9@example.com  │  65 │
10. │ 10 │ User10 │ user10@example.com │  64 │
    └────┴────────┴────────────────────┴─────┘
'''


