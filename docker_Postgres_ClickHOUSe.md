# Migrar Dados do PostgreSQL para ClickHouse (Local)

Este guia demonstra como criar dois contentores Docker (PostgreSQL e ClickHouse), gerar dados de teste e migrá‑los diretamente usando a função `postgresql()` do ClickHouse.

---

## 🐳 1. Contentores Docker em Execução

```bash
CONTAINER ID   NAMES           STATUS
ac52f92a4818   demo_postgres   Up 27 minutes
6244d4b6cbcc   ch_server       Up 27 minutes
```

---

## 🗄️ 2. Criar Base de Dados de Teste no PostgreSQL

Ficheiro: **init.sql**

```sql
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
```

---

## 🐳 3. docker-compose.yml com PostgreSQL + ClickHouse

```yaml
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
```

---

## 🔗 4. Consultar Dados do PostgreSQL a partir do ClickHouse

```sql
SELECT *
FROM postgresql('postgres:5432', 'demo_db', 'users', 'demo', 'demo123')
LIMIT 5;
```

Exemplo de saída:

```
┌─id─┬─name──┬─email─────────────┬─age─┐
│  1 │ User1 │ user1@example.com │  31 │
│  2 │ User2 │ user2@example.com │  21 │
│  3 │ User3 │ user3@example.com │  65 │
│  4 │ User4 │ user4@example.com │  21 │
│  5 │ User5 │ user5@example.com │  45 │
└────┴───────┴───────────────────┴─────┘
```

---

## 🏗️ 5. Criar Tabela no ClickHouse

```sql
CREATE TABLE ch_users
(
    id UInt32,
    name String,
    email String,
    age UInt8
)
ENGINE = MergeTree
ORDER BY id;
```

---

## 📥 6. Copiar Dados do PostgreSQL para ClickHouse

```sql
INSERT INTO ch_users
SELECT *
FROM postgresql(
    'postgres:5432',
    'demo_db',
    'users',
    'demo',
    'demo123'
);
```

---

## 🔍 7. Verificar Dados Migrados

```sql
SELECT * FROM ch_users;
```

Exemplo de saída:

```
┌─id─┬─name───┬─email──────────────┬─age─┐
│  1 │ User1  │ user1@example.com  │  31 │
│  2 │ User2  │ user2@example.com  │  21 │
│  3 │ User3  │ user3@example.com  │  65 │
│  4 │ User4  │ user4@example.com  │  21 │
│  5 │ User5  │ user5@example.com  │  45 │
│  6 │ User6  │ user6@example.com  │  23 │
│  7 │ User7  │ user7@example.com  │  33 │
│  8 │ User8  │ user8@example.com  │  43 │
│  9 │ User9  │ user9@example.com  │  65 │
│ 10 │ User10 │ user10@example.com │  64 │
└────┴────────┴────────────────────┴─────┘
```



