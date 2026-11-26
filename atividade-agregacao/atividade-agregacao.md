### Nível 1 — Funções básicas

1-

    SELECT COUNT(*) AS total_produtos
    FROM produto_agregacao;

2- 

    SELECT COUNT(DISTINCT categoria) AS qtd_categorias
    FROM produto_agregacao;

3-
    
    SELECT SUM(estoque) AS qtd_estoque
    FROM produto_agregacao;

4-
    
    SELECT SUM(estoque * preco) AS valor_estoque
    FROM produto_agregacao;

5-

    SELECT ROUND(AVG(preco)) AS preco_medio
    FROM produto_agregacao;

6-

    SELECT MAX(preco) AS maior_preco
    FROM produto_agregacao;

    SELECT MIN(preco) AS menor_preco
    FROM produto_agregacao;

7-

    SELECT nome, preco
    FROM produto_agregacao
    WHERE preco = (SELECT MIN(preco) FROM produto_agregacao);


### NÍVEL 2 - GROUP BY

8-

    SELECT
        categoria,
        COUNT(*) AS total_produtos
    FROM categoria
    GROUP BY categoria;

9-

    SELECT id_categoria, SUM(estoque) AS unidades
    FROM produto
    GROUP BY id_categoria
    ORDER BY id_categoria;

10-

    SELECT
        categoria,
        ROUND(AVG(preco), 2) AS preco_medio,
        MIN(preco) AS menor_preco,
        MAX(preco) AS maior_preco
    FROM categoria
    GROUP BY categoria;

11-
    
    SELECT id_categoria,
        SUM(preco * estoque) AS valor_estoque
    FROM produto_agregacao
    GROUP BY id_categoria
    ORDER BY id_categoria;

12- 

    SELECT
        categoria,
        STRING_AGG(nome, ', ' ORDER BY nome) AS produtos
    FROM produto_agregacao
    GROUP BY categoria;

### NÍVEL 3 - WHERE, HAVING e FILTER

13- 

