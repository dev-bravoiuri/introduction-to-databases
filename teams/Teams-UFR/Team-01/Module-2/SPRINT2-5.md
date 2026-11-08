# SPRINT 2/5 — Subconsultas e Consultas Avançadas

**Disciplina:** Laboratório de Banco de Dados  
**Módulo:** 2  
**Modalidade:** Atividade individual  
**Entrega desta Sprint:** `SPRINT2-5.md` + `SPRINT2-5.sql`

---

# Objetivo da Sprint 2/5

Nesta etapa, cada aluno deverá aprofundar as consultas SQL por meio de **subconsultas**.

O objetivo é resolver perguntas em que uma consulta depende do resultado produzido por outra consulta.

Serão trabalhados:

```sql
SUBQUERY
IN
NOT IN
EXISTS
NOT EXISTS
AVG
MAX
MIN
COUNT
subconsulta correlacionada
```

O aluno deverá continuar utilizando o mesmo banco do `Module-1`.

---

# 1. Identificação

**Nome completo:**

> Iuri Bravo Carvalho

**Banco utilizado:**

```text
Gerenciamento salão de beleza
```

---

# 2. O que é uma subconsulta?

Uma subconsulta é um `SELECT` utilizado dentro de outro comando SQL.

Exemplo:

```sql
SELECT nome, preco
FROM produto
WHERE preco > (
    SELECT AVG(preco)
    FROM produto
);
```

Neste exemplo:

1. a consulta interna calcula a média;
2. a consulta externa utiliza esse resultado.

---

# 3. Perguntas que exigem subconsulta

Defina pelo menos cinco perguntas do seu domínio que possam ser resolvidas com subconsultas.

1. Quais serviços possuem preço de venda superior à média de preço de todos os serviços cadastrados no salão?
2. Quais produtos têm preço de venda maior do que o preço de compra do produto mais caro?
3. Quais clientes já realizaram ao menos um agendamento no salão (verificado via subconsulta na tabela de agendamentos)?
4. Qual é o produto que possui o menor preço de venda cadastrado no sistema?
5. Quais serviços cadastrados no salão ainda não possuem nenhum agendamento registrado?
---

# 4. Subconsulta com comparação

Crie uma consulta utilizando uma comparação com resultado agregado.

**Pergunta:**

> Quais serviços cadastrados no salão possuem preço acima da média de preços de todos os serviços oferecidos?

```sql
SELECT 
    NOME_SERVICO,
    PRECO_SERVICOS
FROM SERVICOS
WHERE PRECO_SERVICOS > (
    SELECT AVG(PRECO_SERVICOS)
    FROM SERVICOS
);
```

**Explique primeiro a consulta interna:**

> A consulta interna (SELECT AVG(PRECO_SERVICOS) FROM SERVICOS) executa a função de agregação AVG() sobre a coluna PRECO_SERVICOS, calculando o valor médio de todos os serviços do salão (retornando um único número como resultado).

**Depois explique a consulta externa:**

> A consulta externa lê a tabela SERVICOS e utiliza a cláusula WHERE PRECO_SERVICOS > para comparar o valor de cada serviço individual com o preço médio retornado pela consulta interna, exibindo no resultado final apenas os serviços que custam mais que a média geral.

---

# 5. Subconsulta com IN

Exemplo:

```sql
SELECT nome
FROM cliente
WHERE id_cliente IN (
    SELECT id_cliente
    FROM pedido
);
```

## Consulta obrigatória

**Pergunta:**

> Quais são os clientes do salão (nome e telefone) que possuem pelo menos um agendamento marcado no sistema?

```sql
SELECT 
    NOME,
    TELEFONE
FROM CLIENTES
WHERE CPF IN (
    SELECT CPF
    FROM AGENDAMENTO
);
```

**Explique:**

> A subconsulta interna `(SELECT CPF FROM AGENDAMENTO)` busca e retorna uma lista com todos os CPFs que possuem registro na tabela `AGENDAMENTO`. A consulta externa lê a tabela `CLIENTES` e usa o operador `IN` para filtrar e exibir apenas os clientes cujo `CPF` esteja presente nessa lista retornada pela subconsulta.
---

# 6. Subconsulta com NOT IN

**Pergunta:**

> Quais serviços do catálogo do salão de beleza ainda não possuem nenhum agendamento registrado no sistema?
```sql
SELECT 
    NOME_SERVICO,
    PRECO_SERVICOS
FROM SERVICOS
WHERE NOME_SERVICO NOT IN (
    SELECT DISTINCT NOME_SERVICO
    FROM AGENDAMENTO
    WHERE NOME_SERVICO IS NOT NULL
);
```

**Que registros você está procurando?**

> Estou procurando por serviços cadastrados na tabela SERVICOS que nunca foram escolhidos por nenhum cliente para um agendamento (ou seja, aqueles cujo nome não consta na tabela AGENDAMENTO).

---

# 7. EXISTS

`EXISTS` verifica se a subconsulta retorna pelo menos um registro.

## Consulta obrigatória

**Pergunta:**

> Quais são os clientes cadastrados no salão que possuem pelo menos um agendamento marcado no sistema?

```sql
SELECT 
    c.NOME,
    c.TELEFONE
FROM CLIENTES AS c
WHERE EXISTS (
    SELECT 1
    FROM AGENDAMENTO AS a
    WHERE a.CPF = c.CPF
);
```

---

# 8. NOT EXISTS

**Pergunta:**

> Quais clientes cadastrados no salão ainda não realizaram nenhum agendamento no sistema?

```sql
SELECT 
    c.NOME,
    c.TELEFONE
FROM CLIENTES AS c
WHERE NOT EXISTS (
    SELECT 1
    FROM AGENDAMENTO AS a
    WHERE a.CPF = c.CPF
);
```

**Explique a diferença em relação a `EXISTS`:**

> Enquanto o EXISTS verifica se a subconsulta correlacionada retorna pelo menos uma linha para o cliente avaliado (retornando verdadeiro e exibindo os clientes que possuem agendamento), o NOT EXISTS inverte essa lógica: ele retorna verdadeiro e exibe apenas os clientes para os quais a subconsulta não encontra nenhum registro correspondente (identificando os clientes inativos/sem agendamentos).

---

# 9. Subconsulta com MAX ou MIN

**Pergunta:**

> Qual é o produto que possui o menor preço de venda cadastrado no sistema do salão?

```sql
SELECT 
    NOME_PRODUTO,
    PRECO_VENDA
FROM PRODUTOS
WHERE PRECO_VENDA = (
    SELECT MIN(PRECO_VENDA)
    FROM PRODUTOS
);
```

**Explique:**

> A subconsulta interna (SELECT MIN(PRECO_VENDA) FROM PRODUTOS) utiliza a função de agregação MIN() para encontrar o menor valor de venda presente na tabela de produtos. A consulta externa lê a tabela PRODUTOS e compara o valor de cada produto com esse mínimo encontrado, retornando o nome e o valor exato do produto mais barato do salão.
---

# 10. Subconsulta correlacionada

Uma subconsulta correlacionada depende de valores da consulta externa.

## Consulta obrigatória

**Pergunta:**

> Quais serviços cadastrados no salão possuem um preço superior à média de preço de todos os outros serviços?

SQL

```sql
SELECT 
    s1.NOME_SERVICO,
    s1.PRECO_SERVICOS
FROM SERVICOS AS s1
WHERE s1.PRECO_SERVICOS > (
    SELECT AVG(s2.PRECO_SERVICOS)
    FROM SERVICOS AS s2
    WHERE s2.NOME_SERVICO <> s1.NOME_SERVICO
);
```

**Qual coluna da consulta externa é utilizada pela subconsulta?**

> A coluna s1.NOME_SERVICO da consulta externa é utilizada dentro da cláusula WHERE s2.NOME_SERVICO <> s1.NOME_SERVICO da subconsulta interna para garantir que a subconsulta seja reavaliada para cada linha processada na consulta principal.
---

# 11. Resolver a mesma pergunta de duas formas

Escolha duas perguntas e resolva cada uma utilizando:

```text
a) JOIN
b) SUBQUERY
```

## Pergunta 1

> Quais são os nomes e telefones dos clientes que possuem pelo menos um agendamento marcado no salão? 

### JOIN

```sql
SELECT DISTINCT
    c.NOME,
    c.TELEFONE
FROM CLIENTES AS c
INNER JOIN AGENDAMENTO AS a 
    ON c.CPF = a.CPF;
```

### SUBQUERY

```sql
SELECT 
    c.NOME,
    c.TELEFONE
FROM CLIENTES AS c
WHERE c.CPF IN (
    SELECT a.CPF
    FROM AGENDAMENTO AS a
);
```

### Qual abordagem ficou mais compreensível?

> A abordagem com SUBQUERY (IN) ficou mais intuitiva e compreensível para esta pergunta específica, pois expressa diretamente a ideia de filtrar clientes "cujo CPF esteja na lista de agendamentos". Além disso, dispensa a necessidade de usar o operador DISTINCT para evitar nomes repetidos na saída, tornando a leitura do comando SQL mais limpa.

---

## Pergunta 2

> Quais serviços do catálogo do salão ainda não possuem nenhum agendamento cadastrado?

### JOIN

```sql
SELECT 
    s.NOME_SERVICO,
    s.PRECO_SERVICOS
FROM SERVICOS AS s
LEFT JOIN AGENDAMENTO AS a 
    ON s.NOME_SERVICO = a.NOME_SERVICO
WHERE a.NOME_SERVICO IS NULL;
```

### SUBQUERY

```sql
SELECT 
    s.NOME_SERVICO,
    s.PRECO_SERVICOS
FROM SERVICOS AS s
WHERE s.NOME_SERVICO NOT IN (
    SELECT DISTINCT a.NOME_SERVICO
    FROM AGENDAMENTO AS a
    WHERE a.NOME_SERVICO IS NOT NULL
);
```

### Comparação

> A abordagem com **`NOT IN` (Subquery)** lê de forma mais clara a intenção da regra de negócio ("buscar serviços cujo nome NÃO esteja na lista de agendamentos"). Porém, a abordagem com **`LEFT JOIN` (com filtro `IS NULL`)** costuma ser mais performática em grandes volumes de dados no MySQL e evita comportamentos inesperados caso existam valores nulos na tabela consultada.

---

# 12. Quantidade mínima exigida

O `SPRINT2-5.sql` deverá conter no mínimo:

```text
1 subconsulta com comparação
1 subconsulta com IN
1 subconsulta com NOT IN
1 consulta com EXISTS
1 consulta com NOT EXISTS
1 subconsulta com MAX ou MIN
1 subconsulta correlacionada
2 problemas resolvidos com JOIN e SUBQUERY
```

---

# 13. Validação prática

Escolha uma subconsulta.

```sql
SELECT 
    NOME_SERVICO,
    PRECO_SERVICOS
FROM SERVICOS
WHERE PRECO_SERVICOS > (
    SELECT AVG(PRECO_SERVICOS)
    FROM SERVICOS
);
```

Responda:

1. Qual consulta é executada primeiro?
2. Qual valor ou conjunto de valores ela retorna?
3. Como esse resultado é utilizado pela consulta externa?

> Qual consulta é executada primeiro? A consulta interna (subconsulta) SELECT AVG(PRECO_SERVICOS) FROM SERVICOS é executada primeiro pelo SGBD. Qual valor ou conjunto de valores ela retorna? Ela retorna um único valor numérico escalar (a média aritmética dos preços de todos os serviços cadastrados na tabela SERVICOS). Como esse resultado é utilizado pela consulta externa. A consulta externa utiliza esse valor numérico como parâmetro de comparação na cláusula WHERE PRECO_SERVICOS >, filtrando a tabela SERVICOS para exibir apenas as linhas cujo valor seja estritamente maior do que a média calculada.
---

# 14. Teste operacional no Workbench

Execute uma consulta e altere temporariamente um valor de filtro.

**Consulta original:**

```sql
SELECT 
    NOME,
    TELEFONE
FROM CLIENTES
WHERE CPF IN (
    SELECT CPF
    FROM AGENDAMENTO
);
```

**Alteração realizada:**

> Alteração do operador IN para NOT IN na consulta externa, buscando identificar o oposto do filtro original: WHERE CPF NOT IN (SELECT CPF FROM AGENDAMENTO);.

**Mudança observada:**

> Ao alterar o filtro para NOT IN, a consulta deixou de retornar a lista de clientes com serviços agendados e passou a exibir exclusivamente os clientes cadastrados que não possuem nenhum agendamento registrado no salão.

---

# 15. Problemas encontrados

| Problema | Causa | Solução |
|---|---|---|
| Subconsulta retornando mais de uma linha em operador de comparação simples (=) | Utilização de um operador escalar (=, >, <) em vez de operador de conjunto (IN) quando a subconsulta retorna múltiplos registros  | Substituição do operador = pelo operador IN, ou adição de uma função de agregação (MIN, MAX, AVG) na subconsulta |
| Ineficiência/Lentidão em subconsultas correlacionadas |Reavaliação da subconsulta para cada linha da consulta externa em tabelas volumosas  | Reescrita da consulta utilizando a instrução JOIN adequada |
|Comportamento inesperado usando NOT IN com valores NULL  |Presença de valores nulos no conjunto de dados retornado pela subconsulta  | Adição da instrução WHERE campo IS NOT NULL dentro da subconsulta interna |

---

# 16. Estrutura recomendada do SPRINT2-5.sql

```sql
-- MODULE 2 — SPRINT 2/5
-- SUBCONSULTAS

-- Aluno: Iuri Bravo Carvalho
-- Banco: gerenciamento_salao_beleza

USE nome_do_banco;

-- SUBQUERY COM COMPARAÇÃO

-- IN

-- NOT IN

-- EXISTS

-- NOT EXISTS

-- MAX / MIN

-- SUBQUERY CORRELACIONADA

-- PROBLEMA 1 - JOIN

-- PROBLEMA 1 - SUBQUERY

-- PROBLEMA 2 - JOIN

-- PROBLEMA 2 - SUBQUERY
```

---

# 17. Checklist

- [v] utilizei o banco do projeto;
- [v] criei subconsulta com comparação;
- [v] utilizei `IN`;
- [v] utilizei `NOT IN`;
- [v] utilizei `EXISTS`;
- [v] utilizei `NOT EXISTS`;
- [v] utilizei `MAX` ou `MIN`;
- [v] criei subconsulta correlacionada;
- [v] resolvi duas perguntas usando JOIN e SUBQUERY;
- [v] expliquei o raciocínio;
- [v] testei no MySQL Workbench;
- [v] consigo explicar as consultas presencialmente;
- [v] salvei `SPRINT2-5.md`;
- [v] salvei `SPRINT2-5.sql`.

---

# 18. Git/GitHub

Continue na mesma branch:

```text
team-XX
```

Arquivos:

```text
Module-2/SPRINT2-5.md
Module-2/SPRINT2-5.sql
```

Commit sugerido:

```text
Conclui Module 2 Sprint 2 de 5 - subconsultas
```

**Não abra o Pull Request final.**

---

# Próxima etapa

Na Sprint 3/5 serão trabalhadas:

```sql
CREATE VIEW
CREATE OR REPLACE VIEW
SELECT em VIEW
DROP VIEW
```
