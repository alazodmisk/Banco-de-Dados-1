# Exercícios de Funções de Agregação em SQL (PostgreSQL)
 
## Índice
 
- [Nível 1: Funções básicas](#nível-1-funções-básicas)
- [Nível 2: GROUP BY](#nível-2-group-by)
- [Nível 3: WHERE, HAVING e FILTER](#nível-3-where-having-e-filter)
- [Nível 4: JOIN e agregação](#nível-4-join-e-agregação)
- [Nível 5: Modelagem com cliente, pedido e item_pedido](#nível-5-modelagem-com-cliente-pedido-e-item_pedido)
---
 
## Nível 1: Funções básicas
 
### Exercício 1
 
```sql
SELECT COUNT(*) AS total_produtos
FROM produto_agregacao;
```
 
### Exercício 2
 
```sql
SELECT COUNT(DISTINCT categoria) AS qtd_categorias
FROM produto_agregacao;
```
 
### Exercício 3
 
```sql
SELECT SUM(estoque) AS qtd_estoque
FROM produto_agregacao;
```
 
### Exercício 4
 
```sql
SELECT SUM(estoque * preco) AS valor_estoque
FROM produto_agregacao;
```
 
### Exercício 5
 
```sql
SELECT ROUND(AVG(preco)) AS preco_medio
FROM produto_agregacao;
```
 
### Exercício 6
 
```sql
SELECT MAX(preco) AS maior_preco
FROM produto_agregacao;
```
 
```sql
SELECT MIN(preco) AS menor_preco
FROM produto_agregacao;
```
 
### Exercício 7
 
```sql
SELECT nome, preco
FROM produto_agregacao
WHERE preco = (SELECT MIN(preco) FROM produto_agregacao);
```
 
---
 
## Nível 2: GROUP BY
 
### Exercício 8
 
```sql
SELECT
    categoria,
    COUNT(*) AS total_produtos
FROM produto_agregacao
GROUP BY categoria;
```
 
### Exercício 9
 
```sql
SELECT id_categoria, SUM(estoque) AS unidades
FROM produto
GROUP BY id_categoria
ORDER BY id_categoria;
```
 
### Exercício 10
 
```sql
SELECT
    categoria,
    ROUND(AVG(preco), 2) AS preco_medio,
    MIN(preco) AS menor_preco,
    MAX(preco) AS maior_preco
FROM produto_agregacao
GROUP BY categoria;
```
 
### Exercício 11
 
```sql
SELECT id_categoria,
    SUM(preco * estoque) AS valor_estoque
FROM produto_agregacao
GROUP BY id_categoria
ORDER BY id_categoria;
```
 
### Exercício 12
 
```sql
SELECT
    categoria,
    STRING_AGG(nome, ', ' ORDER BY nome) AS produtos
FROM produto_agregacao
GROUP BY categoria;
```
 
---
 
## Nível 3: WHERE, HAVING e FILTER
 
### Exercício 13
 
```sql
SELECT
    COUNT(*) FILTER (WHERE preco > 1000) AS produtos_caros
FROM produto_agregacao;
```
 
### Exercício 14
 
```sql
SELECT id_categoria, COUNT(*) AS total_produtos
FROM produto
GROUP BY id_categoria
HAVING COUNT(*) >= 3;
```
 
### Exercício 15
 
```sql
SELECT id_categoria, ROUND(AVG(preco), 2) AS preco_medio
FROM produto
GROUP BY id_categoria
HAVING AVG(preco) >= 500;
```
 
### Exercício 16
 
```sql
SELECT
    COUNT(*) AS total_produtos,
    COUNT(*) FILTER (WHERE preco > 1000) AS produtos_caros,
    COUNT(*) FILTER (WHERE estoque < 10) AS estoques_baixos
FROM produto_agregacao;
```
 
### Exercício 17
 
```sql
SELECT
    COUNT(*) FILTER (WHERE preco * estoque > 10000) AS produtos_caros
FROM produto_agregacao;
```
 
---
 
## Nível 4: JOIN e agregação
 
### Exercício 18
 
```sql
SELECT
    c.id_categoria,
    c.nome AS categoria,
    COUNT(p.id_produto) AS total_produtos
FROM categoria c
LEFT JOIN produto p
    ON p.id_categoria = c.id_categoria
GROUP BY c.id_categoria, c.nome
ORDER BY c.nome;
```
 
### Exercício 19
 
```sql
SELECT
    c.id_categoria,
    c.nome AS categoria,
    COUNT(p.id_produto) AS total_produtos
FROM categoria c
LEFT JOIN produto p
    ON p.id_categoria = c.id_categoria
GROUP BY c.id_categoria, c.nome
HAVING COUNT(p.id_produto) = 0
ORDER BY c.nome;
```
 
### Exercício 20
 
```sql
SELECT
    c.nome AS categoria,
    COUNT(p.id_produto)                    AS total_produtos,
    ROUND(AVG(p.preco), 2)                 AS preco_medio,
    COALESCE(SUM(p.preco * p.estoque), 0)  AS valor_estoque
FROM categoria c
LEFT JOIN produto p ON p.id_categoria = c.id_categoria
GROUP BY c.id_categoria, c.nome
ORDER BY valor_estoque DESC, c.nome;
```
 
### Exercício 21
 
Explique por que `COUNT(*)` pode dar resultado incorreto em um `LEFT JOIN`.
 
Porque o `COUNT(*)` conta linhas do resultado, não linhas da tabela do lado direito. No `LEFT JOIN`, uma linha sem correspondência continua existindo no resultado, com `NULL` nas colunas da outra tabela, e o `COUNT(*)` conta essa linha como se fosse um registro real.
 
---
 
## Nível 5: Modelagem com cliente, pedido e item_pedido
 
### Exercício 22
 
Modele `cliente`, `pedido` e `item_pedido` e produza as consultas dos itens **a** a **f**.
 
> **Pré-requisitos**
>
> - Os comandos abaixo usam a tabela `produto` que já existe no banco, com os produtos `Notebook` (R$ 3500,00), `Mouse` (R$ 80,00) e `Caneta` (R$ 5,00). Se os nomes ou preços forem outros, ajuste os `INSERT`.
> - As tabelas `cliente`, `pedido` e `item_pedido` precisam ser criadas do zero. Se `cliente` ou `pedido` já existirem de outro exercício, apague-as antes. Os ids de pedido usados nos `INSERT` de itens (1, 2 e 3) assumem tabelas recém-criadas.
 
#### Modelo
 
```
cliente 1 ──── N pedido 1 ──── N item_pedido N ──── 1 produto
```
 
- Um cliente tem vários pedidos (1:N).
- Um pedido tem vários itens (1:N).
- Pedido e produto têm relação N:N, resolvida pela tabela `item_pedido`.
- O valor de um pedido não é guardado: ele é calculado a partir dos itens.
- `preco_unitario` guarda o preço no momento da venda, para que uma mudança futura no preço do produto não altere pedidos antigos.
#### Criação das tabelas
 
```sql
CREATE TABLE cliente (
    id_cliente BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    nome       VARCHAR(100) NOT NULL,
    email      VARCHAR(150) NOT NULL UNIQUE
);
 
CREATE TABLE pedido (
    id_pedido   BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    data_pedido DATE NOT NULL DEFAULT CURRENT_DATE,
    id_cliente  BIGINT NOT NULL,
    CONSTRAINT fk_pedido_cliente
        FOREIGN KEY (id_cliente)
        REFERENCES cliente (id_cliente)
);
 
CREATE TABLE item_pedido (
    id_item        BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    id_pedido      BIGINT NOT NULL,
    id_produto     BIGINT NOT NULL,
    quantidade     INTEGER NOT NULL CHECK (quantidade > 0),
    preco_unitario NUMERIC(10, 2) NOT NULL CHECK (preco_unitario >= 0),
    CONSTRAINT fk_item_pedido
        FOREIGN KEY (id_pedido)
        REFERENCES pedido (id_pedido),
    CONSTRAINT fk_item_produto
        FOREIGN KEY (id_produto)
        REFERENCES produto (id_produto),
    CONSTRAINT uq_item_pedido_produto
        UNIQUE (id_pedido, id_produto)
);
```
 
A restrição `UNIQUE (id_pedido, id_produto)` impede o mesmo produto de aparecer duas vezes no mesmo pedido. Para comprar mais unidades, aumenta-se a `quantidade`.
 
#### Dados de exemplo
 
```sql
INSERT INTO cliente (nome, email)
VALUES
    ('Ana Souza',    'ana@email.com'),
    ('Bruno Lima',   'bruno@email.com'),
    ('Carla Mendes', 'carla@email.com');
 
INSERT INTO pedido (data_pedido, id_cliente)
VALUES
    ('2026-11-03', (SELECT id_cliente FROM cliente WHERE email = 'ana@email.com')),
    ('2026-11-10', (SELECT id_cliente FROM cliente WHERE email = 'ana@email.com')),
    ('2026-11-01', (SELECT id_cliente FROM cliente WHERE email = 'bruno@email.com'));
 
INSERT INTO item_pedido (id_pedido, id_produto, quantidade, preco_unitario)
VALUES
    (1, (SELECT id_produto FROM produto WHERE nome = 'Notebook'),  1, 3500.00),
    (1, (SELECT id_produto FROM produto WHERE nome = 'Mouse'),     8,   80.00),
    (2, (SELECT id_produto FROM produto WHERE nome = 'Mouse'),     2,   80.00),
    (2, (SELECT id_produto FROM produto WHERE nome = 'Caneta'),   10,    5.00),
    (3, (SELECT id_produto FROM produto WHERE nome = 'Mouse'),    10,   80.00),
    (3, (SELECT id_produto FROM produto WHERE nome = 'Caneta'),   10,    5.00);
```
 
Resumo dos dados: Ana tem 2 pedidos, Bruno tem 1 e Carla não tem nenhum. Mouse e Caneta empatam em 20 unidades vendidas.
 
#### a) Total de pedidos por cliente, incluindo clientes sem pedidos
 
```sql
SELECT
    c.nome AS cliente,
    COUNT(p.id_pedido) AS total_pedidos
FROM cliente c
LEFT JOIN pedido p ON p.id_cliente = c.id_cliente
GROUP BY c.id_cliente, c.nome
ORDER BY c.nome;
```
 
| cliente      | total_pedidos |
| ------------ | ------------- |
| Ana Souza    | 2             |
| Bruno Lima   | 1             |
| Carla Mendes | 0             |
 
O `LEFT JOIN` mantém Carla, e o `COUNT(p.id_pedido)` ignora o `NULL`, dando 0. Com `COUNT(*)`, ela apareceria com 1.
 
#### b) Valor total de cada pedido
 
```sql
SELECT
    p.id_pedido,
    c.nome AS cliente,
    p.data_pedido,
    COALESCE(SUM(i.quantidade * i.preco_unitario), 0) AS valor_total
FROM pedido p
INNER JOIN cliente c ON c.id_cliente = p.id_cliente
LEFT JOIN item_pedido i ON i.id_pedido = p.id_pedido
GROUP BY p.id_pedido, c.nome, p.data_pedido
ORDER BY p.id_pedido;
```
 
| id_pedido | cliente    | data_pedido | valor_total |
| --------- | ---------- | ----------- | ----------- |
| 1         | Ana Souza  | 2026-11-03  | 4140.00     |
| 2         | Ana Souza  | 2026-11-10  | 210.00      |
| 3         | Bruno Lima | 2026-11-01  | 850.00      |
 
A multiplicação `quantidade * preco_unitario` é feita item a item, e depois somada por pedido. O `LEFT JOIN` com `COALESCE` faz um pedido sem itens aparecer com valor 0.
 
#### c) Total gasto por cliente
 
```sql
SELECT
    c.nome AS cliente,
    COALESCE(SUM(i.quantidade * i.preco_unitario), 0) AS total_gasto
FROM cliente c
LEFT JOIN pedido p ON p.id_cliente = c.id_cliente
LEFT JOIN item_pedido i ON i.id_pedido = p.id_pedido
GROUP BY c.id_cliente, c.nome
ORDER BY total_gasto DESC;
```
 
| cliente      | total_gasto |
| ------------ | ----------- |
| Ana Souza    | 4350.00     |
| Bruno Lima   | 850.00      |
| Carla Mendes | 0.00        |
 
#### d) Ticket médio por cliente
 
O ticket médio é o total gasto dividido pelo número de **pedidos**. Fazer `AVG(i.quantidade * i.preco_unitario)` daria a média por **item**, e não por pedido. Como o `JOIN` com `item_pedido` multiplica as linhas, o número de pedidos precisa ser contado com `COUNT(DISTINCT p.id_pedido)`.
 
```sql
SELECT
    c.nome AS cliente,
    COUNT(DISTINCT p.id_pedido) AS total_pedidos,
    COALESCE(SUM(i.quantidade * i.preco_unitario), 0) AS total_gasto,
    ROUND(
        SUM(i.quantidade * i.preco_unitario) / NULLIF(COUNT(DISTINCT p.id_pedido), 0),
        2
    ) AS ticket_medio
FROM cliente c
LEFT JOIN pedido p ON p.id_cliente = c.id_cliente
LEFT JOIN item_pedido i ON i.id_pedido = p.id_pedido
GROUP BY c.id_cliente, c.nome
ORDER BY c.nome;
```
 
| cliente      | total_pedidos | total_gasto | ticket_medio |
| ------------ | ------------- | ----------- | ------------ |
| Ana Souza    | 2             | 4350.00     | 2175.00      |
| Bruno Lima   | 1             | 850.00      | 850.00       |
| Carla Mendes | 0             | 0.00        | null         |
 
O `NULLIF(..., 0)` evita divisão por zero. Carla, sem pedidos, fica com ticket médio `NULL`, porque não existe média de zero pedidos.
 
#### e) Clientes com total gasto acima de R$ 1.000
 
```sql
SELECT
    c.nome AS cliente,
    SUM(i.quantidade * i.preco_unitario) AS total_gasto
FROM cliente c
INNER JOIN pedido p ON p.id_cliente = c.id_cliente
INNER JOIN item_pedido i ON i.id_pedido = p.id_pedido
GROUP BY c.id_cliente, c.nome
HAVING SUM(i.quantidade * i.preco_unitario) > 1000
ORDER BY total_gasto DESC;
```
 
| cliente   | total_gasto |
| --------- | ----------- |
| Ana Souza | 4350.00     |
 
O filtro fica no `HAVING`, porque a condição é sobre o total do grupo, que só existe depois do `GROUP BY`. O `WHERE` não consegue usar `SUM`. Aqui o `INNER JOIN` basta, já que um cliente sem compras nunca passa de R$ 1.000.
 
#### f) Produto mais vendido por quantidade, preservando empates
 
```sql
SELECT
    p.nome AS produto,
    SUM(i.quantidade) AS total_vendido
FROM produto p
INNER JOIN item_pedido i ON i.id_produto = p.id_produto
GROUP BY p.id_produto, p.nome
HAVING SUM(i.quantidade) = (
    SELECT MAX(total)
    FROM (
        SELECT SUM(quantidade) AS total
        FROM item_pedido
        GROUP BY id_produto
    ) t
)
ORDER BY p.nome;
```
 
| produto | total_vendido |
| ------- | ------------- |
| Caneta  | 20            |
| Mouse   | 20            |
 
A subconsulta calcula o maior total vendido entre todos os produtos, e o `HAVING` mantém todos os produtos que atingem esse valor. Um `ORDER BY total_vendido DESC LIMIT 1` devolveria só um dos dois e descartaria o empate.
 
Alternativa com `WITH TIES` (PostgreSQL 13 ou superior):
 
```sql
SELECT
    p.nome AS produto,
    SUM(i.quantidade) AS total_vendido
FROM produto p
INNER JOIN item_pedido i ON i.id_produto = p.id_produto
GROUP BY p.id_produto, p.nome
ORDER BY total_vendido DESC
FETCH FIRST 1 ROWS WITH TIES;
```
 