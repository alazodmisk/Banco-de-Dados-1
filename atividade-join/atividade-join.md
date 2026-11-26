# Exercícios de JOIN em SQL (PostgreSQL)
 
## Índice
 
- [Nível 1: INNER JOIN, filtros e ordenação](#nível-1-inner-join-filtros-e-ordenação)
- [Nível 2: LEFT JOIN](#nível-2-left-join)
- [Nível 3: RIGHT JOIN, INNER JOIN e CROSS JOIN](#nível-3-right-join-inner-join-e-cross-join)
- [Nível 4: Clientes e pedidos (relacionamento 1:N)](#nível-4-clientes-e-pedidos-relacionamento-1n)
---
 
## Nível 1: INNER JOIN, filtros e ordenação
 
### Exercício 1
 
```sql
SELECT p.nome AS produto, c.nome AS categoria
FROM produto p
INNER JOIN categoria c ON c.id_categoria = p.id_categoria;
```
 
| produto  | categoria   |
| -------- | ----------- |
| Notebook | Informática |
| Mouse    | Informática |
| Mesa     | Móveis      |
| Cadeira  | Móveis      |
| Caneta   | Papelaria   |
 
### Exercício 2
 
```sql
SELECT p.nome, p.preco, p.id_categoria AS produto
FROM produto p
INNER JOIN categoria c ON c.id_categoria = p.id_categoria
ORDER BY p.preco ASC;
```
 
| nome     | preco   | produto |
| -------- | ------- | ------- |
| Caneta   | 5.00    | 3       |
| Mouse    | 80.00   | 1       |
| Cadeira  | 600.00  | 2       |
| Mesa     | 900.00  | 2       |
| Notebook | 3500.00 | 1       |
 
### Exercício 3
 
```sql
SELECT p.nome, p.preco AS produto, c.nome AS categoria
FROM produto p
INNER JOIN categoria c ON c.id_categoria = p.id_categoria
WHERE c.nome = 'Informática';
```
 
| nome     | produto | categoria   |
| -------- | ------- | ----------- |
| Notebook | 3500.00 | Informática |
| Mouse    | 80.00   | Informática |
 
### Exercício 4
 
```sql
SELECT p.nome AS produto, c.nome AS categoria
FROM produto p
INNER JOIN categoria c ON c.id_categoria = p.id_categoria
WHERE preco >= 500.00;
```
 
| produto  | categoria   |
| -------- | ----------- |
| Notebook | Informática |
| Mesa     | Móveis      |
| Cadeira  | Móveis      |
 
### Exercício 5
 
```sql
SELECT p.nome AS produto, c.nome AS categoria
FROM produto p
INNER JOIN categoria c ON c.id_categoria = p.id_categoria
WHERE c.nome IN ('Móveis', 'Informática')
ORDER BY p.nome ASC;
```
 
| produto  | categoria   |
| -------- | ----------- |
| Cadeira  | Móveis      |
| Mesa     | Móveis      |
| Mouse    | Informática |
| Notebook | Informática |
 
---
 
## Nível 2: LEFT JOIN
 
### Exercício 6
 
```sql
SELECT c.nome AS categoria
FROM categoria c
LEFT JOIN produto p ON p.id_categoria = c.id_categoria
ORDER BY c.nome;
```
 
| categoria   |
| ----------- |
| Informática |
| Informática |
| Limpeza     |
| Móveis      |
| Móveis      |
| Papelaria   |
 
### Exercício 7
 
```sql
SELECT c.nome AS categoria
FROM categoria c
LEFT JOIN produto p ON p.id_categoria = c.id_categoria
WHERE p.id_produto IS NULL;
```
 
| categoria |
| --------- |
| Limpeza   |
 
### Exercício 8
 
```sql
SELECT c.nome AS categoria, p.nome AS produto, p.preco
FROM categoria c
LEFT JOIN produto p
    ON p.id_categoria = c.id_categoria
    AND p.preco > 100
ORDER BY c.nome, p.nome;
```
 
| categoria   | produto  | preco   |
| ----------- | -------- | ------- |
| Informática | Notebook | 3500.00 |
| Limpeza     | null     | null    |
| Móveis      | Cadeira  | 600.00  |
| Móveis      | Mesa     | 900.00  |
| Papelaria   | null     | null    |
 
---
 
## Nível 3: RIGHT JOIN, INNER JOIN e CROSS JOIN
 
### Exercício 9
 
Com `LEFT JOIN`:
 
```sql
SELECT c.nome AS categoria, p.nome AS produto, p.preco
FROM categoria c
LEFT JOIN produto p
    ON p.id_categoria = c.id_categoria;
```
 
| categoria   | produto  | preco   |
| ----------- | -------- | ------- |
| Informática | Notebook | 3500.00 |
| Informática | Mouse    | 80.00   |
| Móveis      | Mesa     | 900.00  |
| Móveis      | Cadeira  | 600.00  |
| Papelaria   | Caneta   | 5.00    |
| Limpeza     | null     | null    |
 
Com `JOIN` (equivalente a `INNER JOIN`):
 
```sql
SELECT c.nome AS categoria, p.nome AS produto, p.preco
FROM categoria c
JOIN produto p
    ON p.id_categoria = c.id_categoria;
```
 
| categoria   | produto  | preco   |
| ----------- | -------- | ------- |
| Informática | Notebook | 3500.00 |
| Informática | Mouse    | 80.00   |
| Móveis      | Mesa     | 900.00  |
| Móveis      | Cadeira  | 600.00  |
| Papelaria   | Caneta   | 5.00    |
 
### Exercício 10
 
Com `LEFT JOIN`:
 
```sql
SELECT c.nome AS categoria, p.nome AS produto, p.preco
FROM categoria c
LEFT JOIN produto p
    ON p.id_categoria = c.id_categoria
ORDER BY c.nome, p.nome;
```
 
Reescrita com `RIGHT JOIN` (as tabelas trocam de lugar):
 
```sql
SELECT c.nome AS categoria, p.nome AS produto, p.preco
FROM produto p
RIGHT JOIN categoria c
        ON p.id_categoria = c.id_categoria
ORDER BY c.nome, p.nome;
```
 
As duas consultas retornam o mesmo resultado:
 
| categoria   | produto  | preco   |
| ----------- | -------- | ------- |
| Informática | Mouse    | 80.00   |
| Informática | Notebook | 3500.00 |
| Limpeza     | null     | null    |
| Móveis      | Cadeira  | 600.00  |
| Móveis      | Mesa     | 900.00  |
| Papelaria   | Caneta   | 5.00    |
 
### Exercício 11
 
```sql
SELECT
    c.nome AS categoria,
    p.nome AS produto
FROM categoria c
CROSS JOIN produto p;
```
 
| categoria   | produto  |
| ----------- | -------- |
| Informática | Notebook |
| Informática | Mouse    |
| Informática | Mesa     |
| Informática | Cadeira  |
| Informática | Caneta   |
| Móveis      | Notebook |
| Móveis      | Mouse    |
| Móveis      | Mesa     |
| Móveis      | Cadeira  |
| Móveis      | Caneta   |
| Papelaria   | Notebook |
| Papelaria   | Mouse    |
| Papelaria   | Mesa     |
| Papelaria   | Cadeira  |
| Papelaria   | Caneta   |
| Limpeza     | Notebook |
| Limpeza     | Mouse    |
| Limpeza     | Mesa     |
| Limpeza     | Cadeira  |
| Limpeza     | Caneta   |
 
**Explicação:** pois é o produto cartesiano da primeira tabela para a segunda tabela.
 
---
 
## Nível 4: Clientes e pedidos (relacionamento 1:N)
 
### Exercício 12
 
**Criação das tabelas**
 
```sql
CREATE TABLE cliente (
    id_cliente BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    nome       VARCHAR(100) NOT NULL,
    email      VARCHAR(150) NOT NULL UNIQUE
);
 
CREATE TABLE pedido (
    id_pedido   BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    data_pedido DATE NOT NULL DEFAULT CURRENT_DATE,
    valor       NUMERIC(10, 2) NOT NULL CHECK (valor >= 0),
    id_cliente  BIGINT NOT NULL,
    CONSTRAINT fk_pedido_cliente
        FOREIGN KEY (id_cliente)
        REFERENCES cliente (id_cliente)
);
```
 
**Cadastro dos dados**
 
```sql
INSERT INTO cliente (nome, email)
VALUES
    ('Ana Souza',    'ana@email.com'),
    ('Bruno Lima',   'bruno@email.com'),
    ('Carla Mendes', 'carla@email.com');
 
INSERT INTO pedido (data_pedido, valor, id_cliente)
VALUES
    ('2026-11-03',  250.00, (SELECT id_cliente FROM cliente WHERE email = 'ana@email.com')),
    ('2026-11-01',   89.90, (SELECT id_cliente FROM cliente WHERE email = 'bruno@email.com')),
    ('2026-11-10', 1200.00, (SELECT id_cliente FROM cliente WHERE email = 'ana@email.com')),
    ('2026-11-07',   45.50, (SELECT id_cliente FROM cliente WHERE email = 'bruno@email.com'));
```
 
#### a) Pedidos com seus clientes
 
```sql
SELECT p.id_pedido, p.data_pedido, p.valor, c.nome AS cliente
FROM pedido p
INNER JOIN cliente c ON c.id_cliente = p.id_cliente;
```
 
| id_pedido | data_pedido | valor   | cliente    |
| --------- | ----------- | ------- | ---------- |
| 1         | 2026-11-03  | 250.00  | Ana Souza  |
| 2         | 2026-11-01  | 89.90   | Bruno Lima |
| 3         | 2026-11-10  | 1200.00 | Ana Souza  |
| 4         | 2026-11-07  | 45.50   | Bruno Lima |
 
#### b) Todos os clientes, inclusive os sem pedidos
 
```sql
SELECT c.nome AS cliente, p.id_pedido, p.data_pedido, p.valor
FROM cliente c
LEFT JOIN pedido p ON p.id_cliente = c.id_cliente
ORDER BY c.nome, p.data_pedido;
```
 
| cliente      | id_pedido | data_pedido | valor   |
| ------------ | --------- | ----------- | ------- |
| Ana Souza    | 1         | 2026-11-03  | 250.00  |
| Ana Souza    | 3         | 2026-11-10  | 1200.00 |
| Bruno Lima   | 2         | 2026-11-01  | 89.90   |
| Bruno Lima   | 4         | 2026-11-07  | 45.50   |
| Carla Mendes | null      | null        | null    |
 
#### c) Apenas clientes sem pedidos
 
```sql
SELECT c.nome AS cliente
FROM cliente c
LEFT JOIN pedido p ON p.id_cliente = c.id_cliente
WHERE p.id_pedido IS NULL;
```
 
| cliente      |
| ------------ |
| Carla Mendes |
 
#### d) Pedidos ordenados por data
 
```sql
SELECT p.id_pedido, p.data_pedido, p.valor, c.nome AS cliente
FROM pedido p
INNER JOIN cliente c ON c.id_cliente = p.id_cliente
ORDER BY p.data_pedido ASC;
```
 
| id_pedido | data_pedido | valor   | cliente    |
| --------- | ----------- | ------- | ---------- |
| 2         | 2026-11-01  | 89.90   | Bruno Lima |
| 1         | 2026-11-03  | 250.00  | Ana Souza  |
| 4         | 2026-11-07  | 45.50   | Bruno Lima |
| 3         | 2026-11-10  | 1200.00 | Ana Souza  |
 
#### e) Pedidos de um cliente específico
 
```sql
SELECT p.id_pedido, p.data_pedido, p.valor
FROM pedido p
INNER JOIN cliente c ON c.id_cliente = p.id_cliente
WHERE c.nome = 'Ana Souza'
ORDER BY p.data_pedido;
```
 
| id_pedido | data_pedido | valor   |
| --------- | ----------- | ------- |
| 1         | 2026-11-03  | 250.00  |
| 3         | 2026-11-10  | 1200.00 |
 