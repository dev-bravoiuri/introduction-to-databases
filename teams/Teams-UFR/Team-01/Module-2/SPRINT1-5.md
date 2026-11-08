# SPRINT 1/5 — JOINs e Consultas Relacionais

**Disciplina:** Laboratório de Banco de Dados  
**Módulo:** 2  
**Modalidade:** Atividade individual  
**Entrega desta Sprint:** `SPRINT1-5.md` + `SPRINT1-5.sql`

---

# Objetivo da Sprint 1/5

Nesta primeira Sprint do **Module-2**, cada aluno deverá continuar utilizando o mesmo banco de dados desenvolvido no `Module-1`.

O foco agora será a consulta de dados relacionados entre duas ou mais tabelas por meio de:

```sql
INNER JOIN
LEFT JOIN
RIGHT JOIN
ON
AS
WHERE
ORDER BY
GROUP BY
COUNT
SUM
AVG
MIN
MAX
```

Ao final da Sprint, o aluno deverá ser capaz de identificar as tabelas necessárias, reconhecer PK e FK envolvidas, construir consultas com `JOIN`, interpretar os resultados e explicar presencialmente como cada consulta funciona.

> **Importante:** não crie um novo banco. Utilize o mesmo projeto desenvolvido no `Module-1`.

---

# 1. Estrutura do repositório

Os arquivos desta Sprint deverão ficar em:

```text
teams/Teams-UNEMAT/team-XX/Module-2/
```

Ao final:

```text
Module-2/
├── SPRINT1-5.md
└── SPRINT1-5.sql
```

Não altere nem apague os arquivos do `Module-1`.

---

# 2. Identificação

**Nome completo:**

> Iuri Bravo Carvalho.

**Branch:**

```text
team-01
```

**Nome do banco:**

```text
Gerenciamento salão de beleza
```

**Tema do projeto:**

> gerenciamento.

---

# 3. Retomada do banco

Liste as principais tabelas que serão utilizadas.

| Nº | Tabela | PK | Principais FKs |
|---:|---|---|---|
| 1 |Agendamento|Data de agendamento|CPF e Nome do serviço|
| 2 |Clientes|CPF|  |
| 3 |Serviços|Nome serviço |  |
| 4 |  |  |  |
| 5 |  |  |  |

---

# 4. Relacionamentos existentes

| Tabela A | Cardinalidade | Tabela B | FK utilizada |
|---|---|---|---|
|Clientes|um pra um|Agendamento|CPF|
|Serviço|um pra um|Serviços|Nome do serviço|
|  |  |  |  |
|  |  |  |  |

---

# 5. INNER JOIN

O `INNER JOIN` retorna registros que possuem correspondência nas tabelas relacionadas.

Exemplo genérico:

```sql
SELECT
    a.campo,
    b.campo
FROM tabela_a AS a
INNER JOIN tabela_b AS b
    ON a.id = b.id_a;
```

## Consulta INNER JOIN 1

**Pergunta em linguagem natural:**

> Quais são os nomes e telefones dos clientes que possuem agendamentos registrados, juntamente com a data e hora marcada?.

**Tabelas utilizadas:**

```text
CLIENTES, AGENDAMENTO
```

**PK/FK utilizadas:**

```text
CLIENTES.CPF (PK) = AGENDAMENTO.CPF (FK)
```

**SQL:**

```sql
SELECT 
    c.NOME,
    c.TELEFONE,
    a.DATA_AGENDAMENTO,
    a.NOME_SERVICO
FROM CLIENTES AS c
INNER JOIN AGENDAMENTO AS a 
    ON c.CPF = a.CPF;
```

**Explique o resultado:**

> A consulta relaciona as tabelas `CLIENTES` e `AGENDAMENTO` através da chave estrangeira `CPF`. O resultado exibe apenas os clientes que possuem agendamento marcado no salão, trazendo o seu nome, telefone, a data/hora agendada e o serviço escolhido. Clientes cadastrados sem agendamento são omitidos.

## Consulta INNER JOIN 2

**Pergunta:**

> Quais agendamentos foram realizados, mostrando a data do agendamento, o nome do serviço e o preço cobrado por esse serviço?
```sql
SELECT 
    a.DATA_AGENDAMENTO,
    a.NOME_SERVICO,
    s.PRECO_SERVICOS
FROM AGENDAMENTO AS a
INNER JOIN SERVICOS AS s 
    ON a.NOME_SERVICO = s.NOME_SERVICO;
```

**Explique:**

> Esta consulta faz a junção da tabela AGENDAMENTO com a tabela SERVICOS utilizando a chave primária/estrangeira NOME_SERVICO. Ela permite visualizar o valor financeiro correspondente a cada agendamento realizado no salão.

---

# 6. LEFT JOIN

O `LEFT JOIN` mantém todos os registros da tabela à esquerda, mesmo quando não existe correspondência na tabela da direita.

## Consulta obrigatória

**Pergunta:**

> Quais clientes estão cadastrados no salão e quais agendamentos eles possuem (incluindo aqueles clientes que ainda não realizaram nenhum agendamento)?

SQL

```sql
-- SELECT 
    c.NOME,
    c.TELEFONE,
    a.DATA_AGENDAMENTO,
    a.NOME_SERVICO
FROM CLIENTES AS c
LEFT JOIN AGENDAMENTO AS a 
    ON c.CPF = a.CPF;
```

**O que o LEFT JOIN permite visualizar neste caso?**

> O LEFT JOIN exibe todos os clientes cadastrados no banco de dados na tabela CLIENTES (tabela da esquerda). Se o cliente tiver agendamento, seus dados de data e serviço aparecem normalmente. Caso o cliente ainda não tenha agendado nada (como o cliente restado após a exclusão/inserções), a consulta exibe os dados do cliente e preenche os campos do agendamento com NULL. Isso é essencial para identificar clientes inativos no sistema.

---

# 7. RIGHT JOIN

O `RIGHT JOIN` mantém todos os registros da tabela da direita, mesmo quando não existe correspondência na tabela da esquerda.

## Consulta obrigatória

**Pergunta:**

> Quais são todos os serviços oferecidos pelo salão e quais agendamentos estão vinculados a eles (incluindo serviços que ainda não têm nenhum agendamento marcado)?
```sql
SELECT 
    a.DATA_AGENDAMENTO,
    a.CPF,
    s.NOME_SERVICO,
    s.PRECO_SERVICOS
FROM AGENDAMENTO AS a
RIGHT JOIN SERVICOS AS s 
    ON a.NOME_SERVICO = s.NOME_SERVICO;
```

**Explique o resultado:**

> O RIGHT JOIN garante a exibição de todos os registros da tabela SERVICOS (tabela da direita), independentemente de estarem presentes na tabela AGENDAMENTO (tabela da esquerda). Quando um serviço cadastrado no salão não possui nenhum agendamento associado (como serviços recém-cadastrados ou com menor procura), a consulta exibe o nome e o preço do serviço normalmente, preenchendo as colunas referentes ao agendamento (DATA_AGENDAMENTO e CPF) com valores NULL. Isso ajuda a identificar quais serviços do catálogo não estão gerando demanda.

---

# 8. JOIN com três ou mais tabelas

Crie duas consultas envolvendo pelo menos três tabelas.

## Consulta 1

**Pergunta:**

> > Como relacionar os clientes aos seus respetivos agendamentos e visualizar o valor de cada serviço agendado?
```sql
SELECT 
    c.NOME AS NOME_CLIENTE,
    c.TELEFONE,
    a.DATA_AGENDAMENTO,
    s.NOME_SERVICO,
    s.PRECO_SERVICOS
FROM CLIENTES AS c
INNER JOIN AGENDAMENTO AS a 
    ON c.CPF = a.CPF
INNER JOIN SERVICOS AS s 
    ON a.NOME_SERVICO = s.NOME_SERVICO;
```

## Consulta 2

**Pergunta:**

> Como obter o relatório completo do atendimento, exibindo o nome do cliente, os dados do agendamento, o serviço contratado com seu preço e a lista de colaboradores do salão?
```sql
SELECT 
    c.NOME AS NOME_CLIENTE,
    a.DATA_AGENDAMENTO,
    s.NOME_SERVICO,
    s.PRECO_SERVICOS,
    col.NOME_COLABORADOR
FROM CLIENTES AS c
INNER JOIN AGENDAMENTO AS a 
    ON c.CPF = a.CPF
INNER JOIN SERVICOS AS s 
    ON a.NOME_SERVICO = s.NOME_SERVICO
CROSS JOIN COLABORADORES AS col;
```

---

# 9. JOIN + WHERE

**Pergunta:**

> QUAIS agendamentos foram realizados por clientes que possuem DDD de telefone igual a 66, trazendo o nome do cliente, o serviço agendado e a data?
```sql
SELECT 
    c.NOME AS NOME_CLIENTE,
    c.TELEFONE,
    a.NOME_SERVICO,
    a.DATA_AGENDAMENTO
FROM CLIENTES AS c
INNER JOIN AGENDAMENTO AS a 
    ON c.CPF = a.CPF
WHERE c.TELEFONE LIKE '66%';
```

**Explique o filtro:**

> A cláusula WHERE c.TELEFONE LIKE '66%' atua filtrando os resultados após a realização da junção (INNER JOIN), garantindo que apenas os clientes cujo número de telefone comece com os dígitos "66" sejam incluídos no relatório final dos agendamentos.

---

# 10. JOIN + ORDER BY

**Pergunta:**

> Quais são os agendamentos registrados no salão, exibindo o nome do cliente e do serviço contratado, ordenados em ordem cronológica da data do agendamento (do mais antigo para o mais recente)?
```sql
SELECT 
    a.DATA_AGENDAMENTO,
    c.NOME AS NOME_CLIENTE,
    a.NOME_SERVICO
FROM AGENDAMENTO AS a
INNER JOIN CLIENTES AS c 
    ON a.CPF = c.CPF
ORDER BY a.DATA_AGENDAMENTO ASC;
```

---

# 11. JOIN + GROUP BY + agregação

Crie uma consulta que combine tabelas e utilize ao menos uma função de agregação.

**Pergunta:**

> Qual é o valor total arrecadado (ou estimado) por cada tipo de serviço com base nos agendamentos realizados no salão?

```sql
SELECT 
    s.NOME_SERVICO,
    COUNT(a.NOME_SERVICO) AS TOTAL_AGENDAMENTOS,
    SUM(s.PRECO_SERVICOS) AS VALOR_TOTAL_ESTIMADO
FROM SERVICOS AS s
INNER JOIN AGENDAMENTO AS a 
    ON s.NOME_SERVICO = a.NOME_SERVICO
GROUP BY s.NOME_SERVICO;
```

**Explique o agrupamento:**

> A consulta combina as tabelas SERVICOS e AGENDAMENTO via INNER JOIN e aplica o agrupamento GROUP BY s.NOME_SERVICO. Para cada grupo de serviço, a função de agregação COUNT() calcula a quantidade total de marcações realizadas, enquanto a função SUM() soma os preços dos serviços para calcular a receita total gerada por cada categoria no salão.
---

# 12. Quantidade mínima exigida

O `SPRINT1-5.sql` deverá conter, no mínimo:

```text
2 INNER JOIN
1 LEFT JOIN
1 RIGHT JOIN
2 consultas envolvendo 3 ou mais tabelas
1 JOIN + WHERE
1 JOIN + ORDER BY
1 JOIN + GROUP BY + agregação
```

As consultas devem responder perguntas reais sobre o banco.

---

# 13. Consulta mais útil

**Pergunta:**

> Qual é o relatório completo dos agendamentos efetuados no salão, exibindo o nome e o telefone do cliente, o serviço contratado e o valor cobrado?

```sql
SELECT 
    c.NOME AS NOME_CLIENTE,
    c.TELEFONE,
    a.DATA_AGENDAMENTO,
    s.NOME_SERVICO,
    s.PRECO_SERVICOS
FROM CLIENTES AS c
INNER JOIN AGENDAMENTO AS a 
    ON c.CPF = a.CPF
INNER JOIN SERVICOS AS s 
    ON a.NOME_SERVICO = s.NOME_SERVICO
ORDER BY a.DATA_AGENDAMENTO ASC;
```

**Por que ela é útil?**

> Esta consulta é vital para a operação diária do salão de beleza, pois consolida em uma única visão os dados do cliente (para contato/confirmação), o horário marcado e a receita esperada por atendimento. Ela evita que a recepção precise consultar múltiplas tabelas separadamente para montar a agenda do dia.
---

# 14. Validação prática obrigatória

Escolha uma consulta produzida nesta Sprint.

```sql
SELECT 
    c.NOME AS NOME_CLIENTE,
    c.TELEFONE,
    a.DATA_AGENDAMENTO,
    a.NOME_SERVICO
FROM CLIENTES AS c
INNER JOIN AGENDAMENTO AS a 
    ON c.CPF = a.CPF;
```

Explique:

1. quais tabelas participam;
2. qual PK está sendo utilizada;
3. qual FK está sendo utilizada;
4. o que a cláusula `ON` faz;
5. o que ocorreria se a condição de relacionamento estivesse errada.

> Quais tabelas participam: CLIENTES e AGENDAMENTO. Qual PK está sendo utilizada: CLIENTES.CPF (Chave Primária na tabela CLIENTES). Qual FK está sendo utilizada: AGENDAMENTO.CPF (Chave Estrangeira na tabela AGENDAMENTO). O que a cláusula ON faz: Estabelece o critério de junção entre as duas tabelas, comparando o valor da chave CPF de cada cliente com a chave CPF registrada nos agendamentos, garantindo que os dados correspondam à mesma pessoa. O que ocorreria se a condição de relacionamento estivesse errada: Se a condição estivesse errada ou fosse omitida, ocorreria o fenômeno do Produto Cartesiano (todas as linhas de CLIENTES seriam combinadas com todas as linhas de AGENDAMENTO), resultando em um relatório incorreto, com dados cruzados de clientes associados a agendamentos de terceiros.

---

# 15. Teste no MySQL Workbench

**Consulta executada:**

```sql
SELECT 
    c.NOME AS NOME_CLIENTE,
    c.TELEFONE,
    a.DATA_AGENDAMENTO,
    a.NOME_SERVICO
FROM CLIENTES AS c
INNER JOIN AGENDAMENTO AS a 
    ON c.CPF = a.CPF;
```

**Resultado esperado:**

> A exibição de uma tabela contendo exclusivamente as colunas NOME_CLIENTE, TELEFONE, DATA_AGENDAMENTO e NOME_SERVICO, apenas para os clientes que possuem agendamentos registrados no banco de dados.

**Resultado obtido:**

> Execução com sucesso no MySQL Workbench (0 linhas de erro). A grade de resultados exibiu corretamente a lista de agendamentos associados aos nomes e telefones de cada cliente correspondente via CPF, sem redundâncias ou dados inconsistentes.

---

# 16. Problemas encontrados

| Problema | Causa | Solução |
|---|---|---|
Ambiguidade no nome de colunas durante o JOIN| Existência de colunas com o mesmo nome em tabelas diferentes (ex: CPF) | Utilização explícita de aliases/apelidos de tabelas (c.CPF e a.CPF) no comando SQL |
| Retorno de clientes duplicados em relatórios agrupados | Agrupamento incompleto na consulta com agregações | Aplicação correta do GROUP BY e seleção apenas dos campos necessários |
| Erro de sintaxe ao omitir a instrução ON | Esquecimento da chave de ligação no comando INNER JOIN  | Definição clara da equivalência de PK e FK após a instrução ON|

---

# 17. Uso de LLMs

LLMs podem ser utilizadas como apoio, mas todo código deverá ser:

```text
COMPREENDIDO
→ ADAPTADO
→ EXECUTADO
→ TESTADO
→ VALIDADO
```

O aluno deverá ser capaz de explicar presencialmente qualquer consulta entregue.

---

# 18. Estrutura recomendada do SPRINT1-5.sql

```sql
-- MODULE 2 — SPRINT 1/5
-- JOINS E CONSULTAS RELACIONAIS

-- Aluno:
-- Banco:

USE nome_do_banco;

-- INNER JOIN 1

-- INNER JOIN 2

-- LEFT JOIN

-- RIGHT JOIN

-- JOIN COM 3+ TABELAS 1

-- JOIN COM 3+ TABELAS 2

-- JOIN + WHERE

-- JOIN + ORDER BY

-- JOIN + GROUP BY + AGREGAÇÃO
```

---

# 19. Checklist

- [v] utilizei o mesmo banco do Module-1;
- [v] identifiquei PKs e FKs;
- [v] produzi 2 `INNER JOIN`;
- [v] produzi 1 `LEFT JOIN`;
- [v] produzi 1 `RIGHT JOIN`;
- [v] produzi consultas com 3 ou mais tabelas;
- [v] utilizei `WHERE`;
- [v] utilizei `ORDER BY`;
- [v] utilizei agregação e `GROUP BY`;
- [v] as consultas respondem perguntas reais;
- [v] testei tudo no MySQL Workbench;
- [v] consigo explicar as consultas;
- [v] salvei `SPRINT1-5.md`;
- [v] salvei `SPRINT1-5.sql`.

---

# 20. Git/GitHub

Continue utilizando:

```text
team-XX
```

Arquivos do commit:

```text
Module-2/SPRINT1-5.md
Module-2/SPRINT1-5.sql
```

Mensagem sugerida:

```text
Conclui Module 2 Sprint 1 de 5 - JOINs
```

**Não abra o Pull Request final nesta Sprint.**

---

# Próxima etapa

Na Sprint 2/5 serão trabalhadas subconsultas:

```sql
IN
NOT IN
EXISTS
NOT EXISTS
subconsultas correlacionadas
```
