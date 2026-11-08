# SPRINT 3/5 — Views e Abstração de Consultas

**Disciplina:** Laboratório de Banco de Dados  
**Módulo:** 2  
**Modalidade:** Atividade individual  
**Entrega desta Sprint:** `SPRINT3-5.md` + `SPRINT3-5.sql`

---

# Objetivo da Sprint 3/5

Nesta etapa, cada aluno deverá criar **Views** para representar consultas relevantes e reutilizáveis do seu banco.

Serão trabalhados:

```sql
CREATE VIEW
CREATE OR REPLACE VIEW
SELECT
DROP VIEW
SHOW FULL TABLES
```

O aluno deverá compreender que uma `VIEW` representa uma consulta armazenada que pode ser utilizada como uma tabela virtual.

---

# 1. Identificação

**Nome completo:**

> Iuri Bravo Carvalho

**Banco utilizado:**

```text
Gerenciamento salão de beleza
```

---

# 2. Consultas do projeto que merecem reutilização

Identifique pelo menos três consultas das Sprints anteriores que são importantes para o sistema.

| Consulta | Por que é útil? | Será transformada em VIEW? |
|---|---|---|
| Relatório completo de agendamentos com dados dos clientes e serviços | Consolida o nome e telefone do cliente, a data e o valor do serviço agendado, facilitando a rotina da recepção. | Sim (`vw_relatorio_agendamentos`) |
| Faturamento/Receita estimada agrupada por tipo de serviço | Permite acompanhar a demanda e o retorno financeiro por categoria de serviço oferecido no salão. | Sim (`vw_faturamento_por_servico`) |
| Listagem de clientes inativos (sem agendamentos registrados) | Identifica os clientes que cadastraram perfil mas ainda não agendaram, ideal para campanhas de marketing. | Sim (`vw_clientes_sem_agendamento`) |
|  |  |  |

---

# 3. Criando uma VIEW

Estrutura geral:

```sql
CREATE VIEW nome_view AS
SELECT ...
FROM ...
WHERE ...;
```

Exemplo:

```sql
CREATE VIEW vw_clientes_pedidos AS
SELECT
    c.id_cliente,
    c.nome,
    p.id_pedido,
    p.data_pedido
FROM cliente AS c
INNER JOIN pedido AS p
    ON c.id_cliente = p.id_cliente;
```

---

# 4. VIEW 1 — relacionamento entre tabelas

**Nome da VIEW:**

```text
vw_relatorio_agendamentos
```

**Pergunta que ela representa:**

> Como obter uma visão consolidada de todos os agendamentos realizados no salão, exibindo o nome e telefone do cliente, a data agendada, o serviço contratado e o preço cobrado?

**SQL:**

```sql
CREATE VIEW vw_relatorio_agendamentos AS
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

**Tabelas utilizadas:**

> CLIENTES, AGENDAMENTO e SERVICOS

**Como consultar essa VIEW?**

```sql
SELECT * FROM vw_relatorio_agendamentos;
```

---

# 5. VIEW 2 — agregação ou resumo

Esta VIEW deverá possuir, quando aplicável:

- relacionamento entre tabelas;
- `COUNT`, `SUM`, `AVG`, `MIN` ou `MAX`;
- `GROUP BY`.

**Pergunta:**

> Qual é o resumo de faturamento estimado e o total de marcações agrupados por tipo de serviço prestado no salão?

```sql
CREATE VIEW vw_faturamento_por_servico AS
SELECT 
    s.NOME_SERVICO,
    COUNT(a.NOME_SERVICO) AS TOTAL_AGENDAMENTOS,
    SUM(s.PRECO_SERVICOS) AS FATURAMENTO_ESTIMADO
FROM SERVICOS AS s
INNER JOIN AGENDAMENTO AS a 
    ON s.NOME_SERVICO = a.NOME_SERVICO
GROUP BY s.NOME_SERVICO;
```

**Explique:**

> Esta VIEW utiliza a cláusula `GROUP BY s.NOME_SERVICO` em conjunto com o `INNER JOIN` para agrupar as informações por tipo de serviço. A função de agregação `COUNT()` contabiliza o volume total de atendimentos de cada categoria, enquanto `SUM()` calcula a receita estimada, permitindo acompanhar o desempenho comercial do salão de forma simplificada.

---

# 6. VIEW 3 — consulta operacional do sistema

Crie uma VIEW que represente uma informação útil para um usuário real.

Exemplos:

```text
estoque baixo
empréstimos em aberto
pedidos pendentes
alunos matriculados
consultas futuras
reservas ativas
pagamentos pendentes
```

**Nome da VIEW:**

```text
vw_produtos_margem_lucro
```

```sql
CREATE VIEW vw_produtos_margem_lucro AS
SELECT 
    ID_PRODUTO,
    NOME_PRODUTO,
    PRECO_COMPRA,
    PRECO_VENDA,
    (PRECO_VENDA - PRECO_COMPRA) AS LUCRO_UNITARIO
FROM PRODUTOS
WHERE PRECO_VENDA > PRECO_COMPRA;
```

**Por que essa VIEW é útil?**

> Esta VIEW é útil para a gestão financeira do salão, pois calcula em tempo real o lucro unitário de cada produto comercializado (`PRECO_VENDA - PRECO_COMPRA`)[cite: 14]. Ela permite identificar rapidamente quais itens geram maior rentabilidade, evitando cálculos manuais repetitivos por parte da equipa.
---

# 7. Consultando uma VIEW

Execute:

```sql
SELECT *
FROM nome_view;
```

Depois faça um filtro:

```sql
SELECT *
FROM nome_view
WHERE ...;
```

**SQL executado:**

```sql
SELECT 
    ID_PRODUTO,
    NOME_PRODUTO,
    PRECO_VENDA,
    LUCRO_UNITARIO
FROM vw_produtos_margem_lucro
WHERE LUCRO_UNITARIO >= 15.00
ORDER BY LUCRO_UNITARIO DESC;
```

**Resultado observado:**

> O MySQL Workbench executou a consulta sobre a VIEW aplicando com sucesso o filtro de rentabilidade e a ordenação decrescente. O resultado exibiu apenas os produtos cujo lucro unitário é igual ou superior a R$ 15,00, demonstrando que uma VIEW pode ser consultada e filtrada exatamente como se fosse uma tabela física do sistema.

---

# 8. CREATE OR REPLACE VIEW

Escolha uma VIEW e faça uma alteração coerente.

Pode ser:

- adicionar coluna;
- alterar filtro;
- incluir um `JOIN`;
- adicionar cálculo;
- alterar uma agregação.

**VIEW original:**

```sql
CREATE VIEW vw_produtos_margem_lucro AS
SELECT 
    ID_PRODUTO,
    NOME_PRODUTO,
    PRECO_COMPRA,
    PRECO_VENDA,
    (PRECO_VENDA - PRECO_COMPRA) AS LUCRO_UNITARIO
FROM PRODUTOS
WHERE PRECO_VENDA > PRECO_COMPRA;
```

**Nova versão:**

```sql
CREATE OR REPLACE VIEW vw_produtos_margem_lucro AS
SELECT 
    ID_PRODUTO,
    NOME_PRODUTO,
    PRECO_COMPRA,
    PRECO_VENDA,
    (PRECO_VENDA - PRECO_COMPRA) AS LUCRO_UNITARIO,
    ROUND(((PRECO_VENDA - PRECO_COMPRA) / PRECO_COMPRA) * 100, 2) AS MARGEM_PERCENTUAL
FROM PRODUTOS
WHERE PRECO_VENDA > PRECO_COMPRA;
```

**O que mudou?**

> Foi utilizada a instrução CREATE OR REPLACE VIEW para redefinir a estrutura da VIEW sem a necessidade de removê-la previamente. Adicionou-se o cálculo MARGEM_PERCENTUAL, que calcula a percentagem de lucro sobre o preço de compra de cada produto e arredonda o resultado a duas casas decimais.

---

# 9. DROP VIEW — exercício controlado

Crie uma VIEW temporária:

```sql
CREATE VIEW vw_teste AS
SELECT ...;
```

Depois remova:

```sql
DROP VIEW vw_teste;
```

**Código utilizado:**

```sql
-- 1. Criação da VIEW temporária para teste
CREATE VIEW vw_teste AS
SELECT 
    ID_PRODUTO,
    NOME_PRODUTO
FROM PRODUTOS;

-- 2. Remoção controlada da VIEW temporária
DROP VIEW vw_teste;
```

**Qual a diferença entre `DROP VIEW` e `DROP TABLE`?**

> O comando `DROP VIEW` elimina apenas a estrutura virtual (a consulta salva no dicionário de dados), sem apagar ou afetar nenhum dado armazenado nas tabelas base do banco de dados. Já o comando `DROP TABLE` remove permanentemente a tabela física do disco, juntamente com todos os registos e dados armazenados nela.

---

# 10. Validando as Views

Use:

```sql
SHOW FULL TABLES
WHERE Table_type = 'VIEW';
```

**Views encontradas:**

1. vw_relatorio_agendamentos

2. vw_faturamento_por_servico

3. vw_produtos_margem_lucro

---

# 11. Teste de atualização dos dados-base

Faça um teste:

1. consulte a VIEW;
2. altere ou insira um dado em uma tabela base;
3. consulte a VIEW novamente.

**VIEW testada:**

```text
vw_produtos_margem_lucro
```

**Alteração realizada:**

```sql
-- 
UPDATE PRODUTOS 
SET PRECO_COMPRA = 10.00 
WHERE ID_PRODUTO = 1;
```

**Resultado observado:**

> Atualização do preço de compra de um produto na tabela base

---

# 12. Validação prática obrigatória

Escolha uma VIEW.

```sql
-- Cole aqui a definição.
```

Explique:

1. de quais tabelas ela depende;
2. qual relacionamento utiliza;
3. quais campos apresenta;
4. qual problema resolve;
5. o que muda se os dados das tabelas originais forem alterados.

> De quais tabelas ela depende: Depende das tabelas CLIENTES, AGENDAMENTO e SERVICOS. Qual relacionamento utiliza: Utiliza junções internas (INNER JOIN), relacionando CLIENTES.CPF com AGENDAMENTO.CPF e SERVICOS.NOME_SERVICO com AGENDAMENTO.NOME_SERVICO. Quais campos apresenta: Apresenta os campos NOME_CLIENTE (aliado de NOME), TELEFONE, DATA_AGENDAMENTO, NOME_SERVICO e PRECO_SERVICOS. Qual problema resolve: Elimina a necessidade de escrever comandos JOIN complexos repetidamente na receção do salão, centralizando a consulta operacional de agendamentos numa única fonte confiável. O que muda se os dados das tabelas originais forem alterados: Como a VIEW é uma tabela virtual que consulta os dados em tempo real, qualquer alteração, inserção ou remoção efetuada nas tabelas base é refletida imediatamente na consulta da VIEW.

---

# 13. Quantidade mínima exigida

O projeto deverá possuir no mínimo:

```text
3 VIEWs úteis
1 VIEW com relacionamento
1 VIEW com agregação ou resumo
1 CREATE OR REPLACE VIEW
1 teste com DROP VIEW
```

---

# 14. Estrutura recomendada do SPRINT3-5.sql

```sql
-- MODULE 2 — SPRINT 3/5
-- VIEWS

-- Aluno:
-- Banco:

USE nome_do_banco;

-- VIEW 1

-- VIEW 2

-- VIEW 3

-- CONSULTAS SOBRE AS VIEWS

-- CREATE OR REPLACE VIEW

-- VIEW TEMPORÁRIA

-- DROP VIEW

-- VALIDAÇÃO
```

---

# 15. Problemas encontrados

| Problema | Causa | Solução |
|---|---|---|
| Erro ao tentar atualizar ou inserir dados através de uma VIEW com agregação | VIEWs que utilizam `GROUP BY`, `SUM()`, `COUNT()` ou `JOIN` são classificadas como não-atualizáveis no MySQL | Realizar inserções ou atualizações diretamente nas tabelas base de dados (`SERVICOS`, `AGENDAMENTO`, `PRODUTOS`) |
| Erro de coluna desconhecida (`Unknown column`) ao criar a VIEW | Referência a nomes de colunas inexistentes ou incorretas nas tabelas de origem | Verificar a estrutura real do esquema com o comando `DESCRIBE nome_tabela;` e ajustar os campos no `SELECT` |
| Conflito de nomes ao tentar recriar uma VIEW com estrutura diferente | Utilização da sintaxe simples `CREATE VIEW` em uma VIEW que já existe na base de dados | Utilizar a sintaxe `CREATE OR REPLACE VIEW` para redefinir a estrutura da VIEW existente |

---

# 16. Uso de LLMs

O uso de LLMs pode ocorrer como apoio, mas o aluno deverá compreender e validar todo o código.

Fluxo obrigatório:

```text
COMPREENDER
→ ADAPTAR
→ EXECUTAR
→ TESTAR
→ VALIDAR
```

---

# 17. Checklist

- [v] utilizei o banco do projeto;
- [v] criei pelo menos 3 Views;
- [v] pelo menos uma View usa JOIN;
- [v] pelo menos uma View usa agregação ou resumo;
- [v] consultei as Views;
- [v] utilizei `CREATE OR REPLACE VIEW`;
- [v] pratiquei `DROP VIEW`;
- [v] validei as Views;
- [v] testei mudança em tabela base;
- [v] compreendo de onde vêm os dados de cada View;
- [v] salvei `SPRINT3-5.md`;
- [v] salvei `SPRINT3-5.sql`.

---

# 18. Git/GitHub

Continue utilizando:

```text
team-XX
```

Arquivos:

```text
Module-2/SPRINT3-5.md
Module-2/SPRINT3-5.sql
```

Commit sugerido:

```text
Conclui Module 2 Sprint 3 de 5 - views
```

**Ainda não abra o Pull Request final.**

---

# Próxima etapa

Na Sprint 4/5 serão trabalhados:

```sql
CREATE PROCEDURE
CALL
IN
OUT
CREATE FUNCTION
RETURN
IF
ELSE
```
