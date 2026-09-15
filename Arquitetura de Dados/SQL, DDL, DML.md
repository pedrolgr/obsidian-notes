> Este material foi escrito para ser **exaustivo**: toda função e todo comando citado vem com sintaxe completa, exemplo prático e resultado calculado a partir de um conjunto de dados fixo, usado do início ao fim do resumo. A ideia é que você consiga tanto **escrever** quanto **ler/interpretar** qualquer query cobrada em prova.

---

## 0. Conjunto de dados usado em todos os exemplos

Para que você veja o **resultado real** de cada comando (e não apenas a sintaxe "seca"), vamos usar sempre estas três tabelas:

**Tabela `departamento`**

|id_depto|nome_depto|
|---|---|
|10|TI|
|20|RH|
|30|Financeiro|
|40|Marketing|

> Repare: o departamento 40 (Marketing) **não tem nenhum funcionário** — isso será importante para entender LEFT/RIGHT/FULL JOIN mais adiante.

**Tabela `funcionario`**

|id_func|nome|salario|id_depto|id_gerente|
|---|---|---|---|---|
|1|Ana|5000|10|NULL|
|2|Bruno|4500|10|1|
|3|Carla|3000|20|NULL|
|4|Diego|3000|20|3|
|5|Elaine|7000|NULL|NULL|
|6|Felipe|4000|30|NULL|

> Repare: Elaine (id 5) **não tem departamento** (`id_depto` é NULL). Bruno é subordinado de Ana; Diego é subordinado de Carla (a coluna `id_gerente` aponta para o `id_func` do chefe).

**Tabela `dependente`**

|id_dep|nome_dep|id_func|
|---|---|---|
|101|Joao|1|
|102|Maria|1|
|103|Pedro|3|
|104|Ana|4|

> Repare: Ana (funcionária) e "Ana" (dependente de Diego) são pessoas diferentes com o mesmo nome — isso vai servir para explicar `UNION` x `UNION ALL` mais adiante.

Sempre que um exemplo abaixo usar essas tabelas, o resultado mostrado foi **calculado manualmente** a partir desses dados, para você acompanhar o raciocínio.

---

## 1. O que é SQL e como ele se divide

**SQL (Structured Query Language – Linguagem de Consulta Estruturada)** é a linguagem padronizada (ANSI/ISO 9075) usada para criar, alterar, consultar e manipular dados em bancos de dados relacionais. Ela é **declarativa**: você descreve _o que_ quer obter, e o SGBD (Sistema Gerenciador de Banco de Dados — Oracle, PostgreSQL, MySQL, SQL Server etc.) decide _como_ buscar isso internamente (plano de execução, índices etc.).

A SQL se divide em sublinguagens, de acordo com a **finalidade** de cada comando. Essa classificação é cobrada em praticamente toda prova de banco de dados:

|Sigla|Nome completo|Nome em português|Comandos|Finalidade|Exemplo|
|---|---|---|---|---|---|
|**DDL**|Data Definition Language|Linguagem de Definição de Dados|`CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME`|Cria/altera/remove a **estrutura** (esquema): tabelas, views, índices, colunas|`CREATE TABLE cliente (...)`|
|**DML**|Data Manipulation Language|Linguagem de Manipulação de Dados|`INSERT`, `UPDATE`, `DELETE`|Insere, altera e remove **linhas de dados** dentro das estruturas já existentes|`UPDATE cliente SET nome = 'X'`|
|**DQL**|Data Query Language|Linguagem de Consulta de Dados|`SELECT`|Consulta/recupera dados, sem alterá-los|`SELECT * FROM cliente`|
|**DCL**|Data Control Language|Linguagem de Controle de Dados|`GRANT`, `REVOKE`|Controla permissões e privilégios de acesso a objetos do banco|`GRANT SELECT ON cliente TO usuario1`|
|**DTL / TCL**|Data/Transaction Control Language|Linguagem de Controle de Transação|`COMMIT`, `ROLLBACK`, `SAVEPOINT`|Confirma ou desfaz um conjunto de operações (transação)|`ROLLBACK;`|

### 1.1 Por que o `SELECT` gera confusão em prova

Tecnicamente, `SELECT` pertence à **DQL** (Data Query Language), pois apenas consulta, não modifica dados. Porém, **muitas bancas (inclusive provas que caem na prática em concursos de TI) tratam o `SELECT` como parte da DML**, por ele também ser considerado um comando de "manipulação/recuperação" de dados em sentido amplo. **Regra prática:** leia sempre o enunciado da questão — se ele definir explicitamente "DML inclui SELECT" ou "DML não inclui SELECT", siga a definição dada pela própria questão.

### 1.2 Pegadinhas clássicas de classificação

- **`TRUNCATE TABLE` é DDL**, não DML — mesmo parecendo (na prática) um "apagar tudo". Ele remove todas as linhas de uma vez, reinicia colunas de auto-incremento/sequências e, na maioria dos SGBDs, **não permite `ROLLBACK`** porque provoca _commit_ automático.
- **`ALTER TABLE` é DDL**, mesmo quando a tabela já tem dados dentro.
- **`GRANT` e `REVOKE` são DCL**, não DDL nem DML — cuidado com questões que tentam confundir "conceder permissão para criar tabela" com um comando de definição de dados.
- **`COMMIT`, `ROLLBACK` e `SAVEPOINT` são DTL/TCL**, e não DML — controlam a transação, não os dados diretamente.
- Em vários SGBDs (com destaque para o Oracle), **todo comando DDL realiza um `COMMIT` implícito** antes e depois de rodar — ou seja, se você tiver uma transação DML aberta e rodar um `CREATE`/`ALTER`/`DROP` no meio, essa transação é confirmada automaticamente (você perde a possibilidade de dar `ROLLBACK` no que já tinha feito).

---

## 2. Tipos de dados mais usados (para entender o `CREATE TABLE`)

|Tipo|Uso|Exemplo de valor|
|---|---|---|
|`INT` / `INTEGER`|Números inteiros|`10`, `-5`|
|`DECIMAL(p,s)` / `NUMERIC(p,s)`|Números decimais exatos, com `p` dígitos totais e `s` casas decimais|`DECIMAL(10,2)` guarda até `99999999.99`|
|`FLOAT` / `DOUBLE`|Números decimais de ponto flutuante (aproximados)|`3.14159`|
|`VARCHAR(n)`|Texto de tamanho **variável**, até `n` caracteres|`VARCHAR(100)`|
|`CHAR(n)`|Texto de tamanho **fixo** (completa com espaços até `n`)|`CHAR(2)` para sigla de estado|
|`TEXT`|Texto longo, sem limite prático de tamanho|Descrição de um produto|
|`DATE`|Data (ano-mês-dia)|`2024-05-10`|
|`DATETIME` / `TIMESTAMP`|Data e hora|`2024-05-10 14:30:00`|
|`BOOLEAN`|Verdadeiro/falso (em alguns SGBDs, como Oracle, não existe nativamente e se simula com `NUMBER(1)` ou `CHAR(1)`)|`TRUE` / `FALSE`|

---

## 3. DDL — Linguagem de Definição de Dados (detalhado)

### 3.1 `CREATE TABLE` — sintaxe completa

```sql
CREATE TABLE nome_da_tabela (
    coluna1 tipo_dado [restrição_de_coluna],
    coluna2 tipo_dado [restrição_de_coluna],
    ...
    [CONSTRAINT nome_restricao FOREIGN KEY (nome_coluna) REFERENCES (nometa_bela)((nome_coluna))]
);
```

**Exemplo completo, usando nossas tabelas de referência:**

```sql
CREATE TABLE departamento (
    id_depto     INT PRIMARY KEY,
    nome_depto   VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE funcionario (
    id_func      INT PRIMARY KEY,
    nome         VARCHAR(100) NOT NULL,
    salario      DECIMAL(10,2) DEFAULT 0 CHECK (salario >= 0),
    id_depto     INT,
    id_gerente   INT,
    CONSTRAINT fk_funcionario_depto
        FOREIGN KEY (id_depto) REFERENCES departamento(id_depto),
    CONSTRAINT fk_funcionario_gerente
        FOREIGN KEY (id_gerente) REFERENCES funcionario(id_func)
);

CREATE TABLE dependente (
    id_dep     INT PRIMARY KEY,
    nome_dep   VARCHAR(100) NOT NULL,
    id_func    INT NOT NULL,
    CONSTRAINT fk_dependente_func
        FOREIGN KEY (id_func) REFERENCES funcionario(id_func)
);
```

### 3.2 Todas as _constraints_ (restrições) explicadas uma a uma

|Constraint|O que faz|Explicação fácil|Exemplo|
|---|---|---|---|
|`PRIMARY KEY`|Identifica **unicamente** cada linha da tabela|É como o "RG" da linha: não pode repetir e não pode ser nulo. Implica automaticamente `NOT NULL` + `UNIQUE`|`id_func INT PRIMARY KEY`|
|`FOREIGN KEY ... REFERENCES`|Garante **integridade referencial**: o valor da coluna precisa existir na tabela referenciada|Impede, por exemplo, cadastrar um funcionário em um departamento que não existe|`FOREIGN KEY (id_depto) REFERENCES departamento(id_depto)`|
|`NOT NULL`|Impede que a coluna fique vazia (nula)|Obriga o preenchimento do campo|`nome VARCHAR(100) NOT NULL`|
|`UNIQUE`|Impede **valores duplicados** na coluna (mas permite um `NULL`, na maioria dos SGBDs)|Ex.: dois funcionários não podem ter o mesmo e-mail|`email VARCHAR(150) UNIQUE`|
|`CHECK (condição)`|Valida uma **regra de negócio** antes de aceitar o valor|Ex.: salário nunca pode ser negativo|`CHECK (salario >= 0)`|
|`DEFAULT valor`|Define um valor assumido automaticamente quando nada é informado no `INSERT`|Se você não informar o salário, ele vira `0`|`salario DECIMAL(10,2) DEFAULT 0`|

**Chave primária composta (mais de uma coluna):**

```sql
CREATE TABLE participacao_projeto (
    id_func    INT,
    id_projeto INT,
    horas      DECIMAL(5,2),
    PRIMARY KEY (id_func, id_projeto)
);
```

Aqui, a combinação de `id_func` + `id_projeto` precisa ser única (o mesmo funcionário pode aparecer em vários projetos, e o mesmo projeto pode ter vários funcionários, mas o **par** não pode se repetir).

### 3.3 `ALTER TABLE` — todas as variações

```sql
-- Adicionar coluna
ALTER TABLE funcionario ADD COLUMN email VARCHAR(150);

-- Remover coluna
ALTER TABLE funcionario DROP COLUMN email;

-- Renomear coluna
ALTER TABLE funcionario RENAME COLUMN nome TO nome_completo;

-- Renomear a própria tabela
ALTER TABLE funcionario RENAME TO colaborador;

-- Alterar o tipo de dado de uma coluna (sintaxe PostgreSQL/SQL Server)
ALTER TABLE funcionario ALTER COLUMN salario TYPE NUMERIC(12,2);

-- Alterar o tipo de dado de uma coluna (sintaxe Oracle/MySQL)
ALTER TABLE funcionario MODIFY salario NUMBER(12,2);

-- Adicionar uma nova restrição a uma tabela já existente
ALTER TABLE funcionario ADD CONSTRAINT uq_nome UNIQUE (nome);

-- Remover uma restrição existente
ALTER TABLE funcionario DROP CONSTRAINT uq_nome;

-- Adicionar uma chave estrangeira depois que a tabela já existe
ALTER TABLE funcionario
    ADD CONSTRAINT fk_depto FOREIGN KEY (id_depto) REFERENCES departamento(id_depto);
```

> **Atenção — pegadinha de banca:** a sintaxe para alterar o _tipo_ de uma coluna **muda de SGBD para SGBD**: `ALTER COLUMN ... TYPE` (PostgreSQL) / `ALTER COLUMN` sem `TYPE` (SQL Server) / `MODIFY` (Oracle e MySQL). A CESGRANRIO costuma informar o SGBD no enunciado — leia com atenção antes de responder.

### 3.4 `DROP` — removendo objetos inteiros

```sql
DROP TABLE funcionario;              -- apaga a tabela inteira (estrutura + todos os dados)
DROP TABLE IF EXISTS funcionario;    -- só apaga se ela existir (evita erro)
DROP VIEW vw_funcionarios_ti;        -- apaga uma view
DROP INDEX idx_nome_funcionario;     -- apaga um índice
DROP DATABASE empresa;               -- apaga o banco de dados inteiro
```

### 3.5 `TRUNCATE TABLE`

```sql
TRUNCATE TABLE funcionario;
```

Remove **todas as linhas** da tabela `funcionario`, mas mantém a estrutura (colunas, constraints, índices) intacta para uso futuro. É mais rápido que um `DELETE` sem `WHERE` porque, internamente, ele não registra a exclusão linha a linha — apenas libera as páginas de dados de uma vez.

### 3.6 Comparativo definitivo: `DROP` × `TRUNCATE` × `DELETE`

| Característica                                 | `DROP TABLE`                | `TRUNCATE TABLE`                | `DELETE FROM`                        |
| ---------------------------------------------- | --------------------------- | ------------------------------- | ------------------------------------ |
| Sublinguagem                                   | DDL                         | DDL                             | DML                                  |
| Remove a estrutura (tabela deixa de existir)?  | **Sim**                     | Não                             | Não                                  |
| Remove todas as linhas?                        | Sim (junto com a tabela)    | Sim                             | Depende — todas, ou só as do `WHERE` |
| Aceita cláusula `WHERE`?                       | Não                         | Não                             | **Sim**                              |
| Pode ser desfeito com `ROLLBACK`?              | Geralmente não              | Geralmente não (varia por SGBD) | **Sim**, dentro da mesma transação   |
| Reinicia colunas de auto-incremento/sequência? | Não se aplica (tabela some) | **Sim**, normalmente            | Não                                  |
| Dispara _triggers_ de `DELETE`?                | Não                         | Geralmente não                  | **Sim**                              |
| Velocidade em tabelas grandes                  | Rápido                      | Muito rápido                    | Mais lento (opera linha a linha)     |

### 3.7 `CREATE VIEW` (view — visão)

Uma _view_ é uma consulta salva, que se comporta como uma tabela virtual:

```sql
CREATE VIEW vw_funcionarios_ti AS
SELECT id_func, nome, salario
FROM funcionario
WHERE id_depto = 10;

-- Depois de criada, você consulta a view como se fosse uma tabela:
SELECT * FROM vw_funcionarios_ti;
```

A view **não guarda dados fisicamente** (na forma "comum"): toda vez que é consultada, a query original é executada novamente por trás dos panos.

### 3.8 `CREATE INDEX`

```sql
CREATE INDEX idx_funcionario_nome ON funcionario (nome);
CREATE UNIQUE INDEX idx_departamento_nome ON departamento (nome_depto);
```

Um índice acelera buscas (`WHERE`, `JOIN`, `ORDER BY`) na coluna indicada, funcionando de forma parecida com o índice remissivo de um livro — mas tem custo de espaço em disco e de manutenção a cada `INSERT`/`UPDATE`/`DELETE`.

---

## 4. DML — Linguagem de Manipulação de Dados (detalhado)

### 4.1 `INSERT INTO` — todas as formas

```sql
-- Forma completa, informando todas as colunas na ordem da tabela
INSERT INTO funcionario VALUES (1, 'Ana', 5000, 10, NULL);

-- Forma recomendada: informando explicitamente as colunas (mais segura e legível)
INSERT INTO funcionario (id_func, nome, salario, id_depto, id_gerente)
VALUES (2, 'Bruno', 4500, 10, 1);

-- Inserindo várias linhas de uma vez
INSERT INTO departamento (id_depto, nome_depto) VALUES
    (10, 'TI'),
    (20, 'RH'),
    (30, 'Financeiro'),
    (40, 'Marketing');

-- INSERT ... SELECT: inserir o resultado de uma consulta em outra tabela
INSERT INTO funcionario_bkp (id_func, nome)
SELECT id_func, nome
FROM funcionario
WHERE salario > 4000;
```

> **Boa prática cobrada em prova:** sempre que possível, liste as colunas explicitamente no `INSERT`. Se a tabela ganhar uma nova coluna no futuro (via `ALTER TABLE ADD COLUMN`), um `INSERT` sem lista de colunas pode quebrar ou inserir valores na ordem errada.

### 4.2 `UPDATE` — todas as formas

```sql
-- Atualizando uma coluna, para todas as linhas que atendem à condição
UPDATE funcionario
SET salario = salario * 1.10
WHERE id_depto = 10;

-- Atualizando várias colunas ao mesmo tempo
UPDATE funcionario
SET salario = 5500, id_gerente = 3
WHERE id_func = 2;

-- Atualizando com base em subquery
UPDATE funcionario
SET salario = salario * 1.05
WHERE id_depto IN (SELECT id_depto FROM departamento WHERE nome_depto = 'TI');
```

> **Pegadinha de prova:** um `UPDATE` **sem `WHERE`** afeta **todas** as linhas da tabela — por exemplo, `UPDATE funcionario SET salario = 0;` zeraria o salário de todo mundo. É um erro clássico explorado em questões de "qual o efeito deste comando?".

### 4.3 `DELETE` — todas as formas

```sql
-- Apagando linhas específicas
DELETE FROM funcionario
WHERE salario < 1500;

-- Apagando com base em subquery
DELETE FROM dependente
WHERE id_func NOT IN (SELECT id_func FROM funcionario);

-- Apagando todas as linhas (mas mantendo a estrutura) — equivalente "lento" ao TRUNCATE
DELETE FROM funcionario;
```

---

## 5. `SELECT` — sintaxe completa e ordem lógica de execução

### 5.1 Sintaxe completa de escrita

```sql
SELECT [DISTINCT] coluna1 [AS apelido1], coluna2 [AS apelido2], ...
FROM tabela1 [AS apelido]
[INNER|LEFT|RIGHT|FULL|CROSS JOIN tabela2 ON condição]
[WHERE condição]
[GROUP BY coluna1, coluna2, ...]
[HAVING condição_de_grupo]
[ORDER BY coluna1 [ASC|DESC], coluna2 [ASC|DESC], ...]
[LIMIT n [OFFSET m]];
```

### 5.2 A ordem em que o SGBD realmente processa a query (ordem lógica)

Isso é diferente da ordem em que você **escreve** o comando, e é a chave para **ler** qualquer query em prova:

1. **`FROM`** (+ `JOIN`s) → monta a "matéria-prima": todas as linhas de origem, já combinadas entre tabelas.
2. **`WHERE`** → filtra linhas individuais, **antes** de qualquer agrupamento.
3. **`GROUP BY`** → agrupa as linhas restantes de acordo com os valores indicados.
4. **`HAVING`** → filtra **os grupos** já formados (não linhas individuais).
5. **`SELECT`** → calcula e escolhe as colunas/expressões finais.
6. **`DISTINCT`** → remove linhas duplicadas do resultado.
7. **`ORDER BY`** → ordena o resultado final.
8. **`LIMIT`/`OFFSET`** → corta a quantidade de linhas exibidas.

**Consequências práticas dessa ordem (muito cobradas em prova):**

- `WHERE` **não pode** conter uma função de agregação (`COUNT`, `SUM`...) porque ele roda **antes** do agrupamento — quem filtra agregações é o `HAVING`.
- Um apelido (`AS`) criado no `SELECT` normalmente **não pode** ser reaproveitado no `WHERE` da mesma consulta (pois `WHERE` executa antes do `SELECT`), mas **pode** ser usado no `ORDER BY` (que executa depois).
- `DISTINCT` age sobre o **resultado já calculado**, então ele enxerga os apelidos e expressões do `SELECT`.

### 5.3 `WHERE` — todos os operadores usados para filtrar

|Operador|Significado|Exemplo|Resultado (nas tabelas de referência)|
|---|---|---|---|
|`=`|Igual|`WHERE id_depto = 10`|Ana, Bruno|
|`<>` ou `!=`|Diferente|`WHERE id_depto <> 10`|Carla, Diego, Felipe (Elaine com NULL não entra — ver nota abaixo)|
|`>`, `<`, `>=`, `<=`|Maior, menor, maior/igual, menor/igual|`WHERE salario > 4000`|Ana, Bruno, Elaine|
|`BETWEEN x AND y`|Intervalo (inclusive)|`WHERE salario BETWEEN 3000 AND 4500`|Bruno, Carla, Diego, Felipe|
|`IN (lista)`|Pertence a uma lista de valores|`WHERE id_depto IN (10, 30)`|Ana, Bruno, Felipe|
|`LIKE 'padrão'`|Compara texto usando curingas|`WHERE nome LIKE 'A%'`|Ana|
|`IS NULL` / `IS NOT NULL`|Testa se o valor é nulo (não use `=` com NULL!)|`WHERE id_depto IS NULL`|Elaine|
|`AND`, `OR`, `NOT`|Combinações lógicas|`WHERE salario > 3000 AND id_depto = 10`|Ana, Bruno|

**Curingas do `LIKE`:**

- `%` → qualquer sequência de zero ou mais caracteres. Ex.: `'A%'` casa com "Ana", "Alberto"; `'%a'` casa com nomes terminados em "a".
- `_` (underline) → exatamente **um** caractere qualquer. Ex.: `'A__'` casa com nomes de exatamente 3 letras começando com "A".

**Cuidado com `NULL` — a maior pegadinha de `WHERE`:**

```sql
SELECT * FROM funcionario WHERE id_depto = NULL;      -- NUNCA retorna linhas (errado!)
SELECT * FROM funcionario WHERE id_depto IS NULL;      -- forma correta -> retorna Elaine
```

Isso acontece porque, em lógica SQL de três valores (verdadeiro/falso/**desconhecido**), qualquer comparação com `NULL` usando `=` ou `<>` resulta em "desconhecido", e nunca em "verdadeiro". Por isso `id_depto <> 10` também **não** traz de volta a linha da Elaine (id_depto NULL), mesmo NULL sendo tecnicamente "diferente de 10".

### 5.4 `DISTINCT`

```sql
SELECT DISTINCT id_depto FROM funcionario;
```

Resultado: `10, 20, NULL, 30` (cada valor aparece **uma única vez**, mesmo que vários funcionários compartilhem o mesmo departamento).

### 5.5 Apelidos (`AS`) — para colunas e para tabelas

```sql
SELECT nome AS nome_funcionario, salario AS salario_mensal
FROM funcionario AS f
WHERE f.salario > 3000;
```

A palavra `AS` é opcional na maioria dos SGBDs (`SELECT nome nome_funcionario ...` também funciona), mas usá-la deixa a leitura mais clara — e é a forma mais cobrada em prova por ser mais explícita.

### 5.6 `ORDER BY`

```sql
-- Ordem crescente (padrão, ASC pode ser omitido)
SELECT nome, salario FROM funcionario ORDER BY salario;

-- Ordem decrescente
SELECT nome, salario FROM funcionario ORDER BY salario DESC;

-- Múltiplos critérios de ordenação (o segundo desempata o primeiro)
SELECT nome, id_depto, salario FROM funcionario ORDER BY id_depto ASC, salario DESC;

-- Ordenando pela posição da coluna no SELECT (funciona na maioria dos SGBDs, mas é evitado em código de produção)
SELECT nome, salario FROM funcionario ORDER BY 2 DESC;
```

### 5.7 Limitando a quantidade de linhas — sintaxe muda por SGBD

```sql
-- PostgreSQL / MySQL
SELECT * FROM funcionario ORDER BY salario DESC LIMIT 3;
SELECT * FROM funcionario ORDER BY salario DESC LIMIT 3 OFFSET 1; -- pula a primeira, pega as 3 seguintes

-- SQL Server
SELECT TOP 3 * FROM funcionario ORDER BY salario DESC;

-- Padrão ANSI SQL / Oracle 12c+ / PostgreSQL / SQL Server
SELECT * FROM funcionario ORDER BY salario DESC
OFFSET 0 ROWS FETCH FIRST 3 ROWS ONLY;

-- Oracle (versões antigas, via pseudocoluna ROWNUM)
SELECT * FROM (SELECT * FROM funcionario ORDER BY salario DESC) WHERE ROWNUM <= 3;
```

> **Muito cobrado:** cada SGBD tem sua própria forma de "trazer só as N primeiras linhas". A CESGRANRIO costuma perguntar isso associado a um SGBD específico mencionado no enunciado.

---

## 6. `GROUP BY` e `HAVING`

`GROUP BY` agrupa linhas que compartilham o mesmo valor em uma (ou mais) coluna(s), permitindo aplicar funções de agregação **por grupo**, e não sobre a tabela inteira.

```sql
SELECT id_depto, COUNT(*) AS qtd_funcionarios
FROM funcionario
GROUP BY id_depto;
```

**Resultado (calculado a partir das nossas tabelas):**

|id_depto|qtd_funcionarios|
|---|---|
|10|2|
|20|2|
|NULL|1|
|30|1|

> Repare que o `NULL` (Elaine, sem departamento) forma **seu próprio grupo** — ele não é descartado pelo `GROUP BY`.

### 6.1 `HAVING` — filtrando grupos (não linhas)

```sql
SELECT id_depto, COUNT(*) AS qtd_funcionarios
FROM funcionario
GROUP BY id_depto
HAVING COUNT(*) > 1;
```

**Resultado:**

|id_depto|qtd_funcionarios|
|---|---|
|10|2|
|20|2|

**Leitura passo a passo, seguindo a ordem lógica (seção 5.2):**

1. `FROM funcionario` → pega as 6 linhas da tabela.
2. `GROUP BY id_depto` → forma 4 grupos: {10}, {20}, {NULL}, {30}.
3. `HAVING COUNT(*) > 1` → descarta os grupos com 1 linha só (NULL e 30), sobrando só 10 e 20.
4. `SELECT id_depto, COUNT(*)` → exibe o identificador do grupo e a contagem.

### 6.2 Regra de ouro do `GROUP BY` (cobradíssima em prova)

> **Toda coluna que aparece no `SELECT` fora de uma função de agregação precisa estar também no `GROUP BY`.**

```sql
-- ERRADO em SQL padrão: "nome" não está agregado nem no GROUP BY
SELECT id_depto, nome, COUNT(*) FROM funcionario GROUP BY id_depto;

-- CERTO
SELECT id_depto, COUNT(*) FROM funcionario GROUP BY id_depto;

-- CERTO (nome dentro de uma função de agregação, aqui juntando os nomes)
SELECT id_depto, COUNT(*), STRING_AGG(nome, ', ') AS nomes  -- STRING_AGG é do PostgreSQL/SQL Server
FROM funcionario
GROUP BY id_depto;
```

> **Observação:** MySQL, em modo "não estrito", tolera colunas fora do `GROUP BY` (retornando um valor arbitrário de cada grupo) — mas isso é considerado um comportamento não padrão e é o tipo de detalhe que a prova explora para confundir o candidato.

---

## 7. Funções de agregação — sintaxe, exemplo e resultado, uma a uma

### 7.1 `COUNT(*)`

Conta o número de **linhas**, independentemente de haver valores nulos em qualquer coluna.

```sql
SELECT COUNT(*) FROM funcionario;
```

Resultado: **6** (as 6 linhas da tabela, incluindo Elaine, mesmo com id_depto nulo).

### 7.2 `COUNT(coluna)`

Conta apenas as linhas em que a coluna indicada **não é nula**.

```sql
SELECT COUNT(id_depto) FROM funcionario;
```

Resultado: **5** (ignora a linha da Elaine, cujo `id_depto` é `NULL`).

### 7.3 `COUNT(DISTINCT coluna)`

Conta quantos **valores diferentes**, não nulos, existem na coluna.

```sql
SELECT COUNT(DISTINCT id_depto) FROM funcionario;
```

Resultado: **3** (os valores distintos são 10, 20 e 30 — o `NULL` da Elaine não é contado).

### 7.4 `SUM(coluna)`

Soma os valores numéricos da coluna.

```sql
SELECT SUM(salario) FROM funcionario;
```

Resultado: **5000 + 4500 + 3000 + 3000 + 7000 + 4000 = 26500**.

### 7.5 `AVG(coluna)`

Calcula a média aritmética (soma dividida pela quantidade de valores **não nulos**).

```sql
SELECT AVG(salario) FROM funcionario;
```

Resultado: **26500 / 6 ≈ 4416,67**.

### 7.6 `MAX(coluna)` e `MIN(coluna)`

Retornam, respectivamente, o maior e o menor valor da coluna.

```sql
SELECT MAX(salario) AS maior_salario, MIN(salario) AS menor_salario FROM funcionario;
```

Resultado: **maior_salario = 7000** (Elaine); **menor_salario = 3000** (Carla e Diego, empatados).

### 7.7 Combinando várias agregações e `GROUP BY` de uma vez

```sql
SELECT id_depto,
       COUNT(*)      AS qtd_funcionarios,
       SUM(salario)  AS soma_salarios,
       AVG(salario)  AS media_salarios,
       MAX(salario)  AS maior_salario
FROM funcionario
GROUP BY id_depto;
```

**Resultado:**

|id_depto|qtd_funcionarios|soma_salarios|media_salarios|maior_salario|
|---|---|---|---|---|
|10|2|9500|4750|5000|
|20|2|6000|3000|3000|
|NULL|1|7000|7000|7000|
|30|1|4000|4000|4000|

---

## 8. Funções escalares comuns (aplicadas linha a linha)

Diferente das funções de agregação (que resumem várias linhas em uma só), as funções **escalares** transformam **cada linha individualmente**. São muito usadas em questões que pedem para "tratar" texto ou substituir valores nulos.

|Função|O que faz|Exemplo|Resultado|
|---|---|---|---|
|`UPPER(texto)`|Converte para maiúsculas|`UPPER('ana')`|`'ANA'`|
|`LOWER(texto)`|Converte para minúsculas|`LOWER('ANA')`|`'ana'`|
|`TRIM(texto)`|Remove espaços em branco do início e do fim|`TRIM(' ana ')`|`'ana'`|
|`SUBSTRING(texto, início, tamanho)` (ou `SUBSTR` em Oracle/MySQL)|Extrai parte de um texto|`SUBSTRING('funcionario', 1, 4)`|`'func'`|
|`CONCAT(texto1, texto2, ...)`|Junta textos (em vários SGBDs também é possível usar `\|`)|`CONCAT(nome, ' - ', nome_depto)`|`'Ana - TI'`|
|`LENGTH(texto)` (ou `LEN` no SQL Server)|Retorna o tamanho do texto|`LENGTH('Ana')`|`3`|
|`COALESCE(valor1, valor2, ...)`|Retorna o **primeiro valor não nulo** da lista (padrão ANSI, funciona em quase todos os SGBDs)|`COALESCE(id_depto, 0)`|Para Elaine: `0`|
|`NVL(valor, substituto)`|Equivalente ao `COALESCE`, mas específico do **Oracle**, com apenas 2 argumentos|`NVL(id_depto, 0)`|Para Elaine: `0`|
|`ISNULL(valor, substituto)`|Equivalente ao `COALESCE`, específico do **SQL Server**|`ISNULL(id_depto, 0)`|Para Elaine: `0`|
|`CASE WHEN ... THEN ... ELSE ... END`|Estrutura condicional (equivalente a um "se/então/senão" dentro do SQL)|ver exemplo abaixo|—|

**Exemplo prático combinando `COALESCE` e `CASE WHEN`:**

```sql
SELECT nome,
       COALESCE(id_depto, 0) AS depto_ou_zero,
       CASE
           WHEN salario >= 5000 THEN 'Alto'
           WHEN salario >= 3500 THEN 'Médio'
           ELSE 'Baixo'
       END AS faixa_salarial
FROM funcionario;
```

**Resultado:**

|nome|depto_ou_zero|faixa_salarial|
|---|---|---|
|Ana|10|Alto|
|Bruno|10|Médio|
|Carla|20|Baixo|
|Diego|20|Baixo|
|Elaine|0|Alto|
|Felipe|30|Médio|

> `CASE WHEN` é avaliado **em ordem**: assim que a primeira condição verdadeira é encontrada, o `CASE` para e retorna aquele valor — por isso a ordem das condições importa (coloque sempre a mais restritiva primeiro).

---

## 9. JOINs — todos os tipos, com sintaxe, exemplo e resultado

Todos os exemplos abaixo usam `funcionario` (6 linhas, uma delas — Elaine — sem departamento) e `departamento` (4 linhas, uma delas — Marketing — sem nenhum funcionário).

### 9.1 `INNER JOIN`

**O que faz:** retorna apenas as linhas que têm correspondência **nas duas tabelas ao mesmo tempo**.

```sql
SELECT f.nome, d.nome_depto
FROM funcionario f
INNER JOIN departamento d ON f.id_depto = d.id_depto;
```

**Resultado (5 linhas):**

|nome|nome_depto|
|---|---|
|Ana|TI|
|Bruno|TI|
|Carla|RH|
|Diego|RH|
|Felipe|Financeiro|

> Elaine (sem departamento) **não aparece**, e Marketing (sem funcionário) **não aparece**.

### 9.2 `LEFT JOIN` (ou `LEFT OUTER JOIN`)

**O que faz:** retorna **todas** as linhas da tabela à esquerda do `JOIN` (a que vem logo depois do `FROM`), completando com `NULL` quando não há correspondência na tabela da direita.

```sql
SELECT f.nome, d.nome_depto
FROM funcionario f
LEFT JOIN departamento d ON f.id_depto = d.id_depto;
```

**Resultado (6 linhas):**

|nome|nome_depto|
|---|---|
|Ana|TI|
|Bruno|TI|
|Carla|RH|
|Diego|RH|
|Elaine|**NULL**|
|Felipe|Financeiro|

> Uso típico em prova: "listar todos os funcionários, mesmo os que não têm departamento" → `LEFT JOIN` a partir de `funcionario`.

### 9.3 `RIGHT JOIN` (ou `RIGHT OUTER JOIN`)

**O que faz:** o espelho do `LEFT JOIN` — mantém **todas** as linhas da tabela à direita.

```sql
SELECT f.nome, d.nome_depto
FROM funcionario f
RIGHT JOIN departamento d ON f.id_depto = d.id_depto;
```

**Resultado (6 linhas):**

|nome|nome_depto|
|---|---|
|Ana|TI|
|Bruno|TI|
|Carla|RH|
|Diego|RH|
|Felipe|Financeiro|
|**NULL**|Marketing|

> Aqui aparece Marketing (departamento sem ninguém), e Elaine (funcionária sem departamento) **desaparece**, porque agora quem manda é a tabela da direita (`departamento`).

### 9.4 `FULL JOIN` (ou `FULL OUTER JOIN`)

**O que faz:** combina o `LEFT JOIN` com o `RIGHT JOIN` — traz **tudo** de ambos os lados, casando quando é possível e preenchendo com `NULL` quando não há correspondência de nenhum dos dois lados.

```sql
SELECT f.nome, d.nome_depto
FROM funcionario f
FULL JOIN departamento d ON f.id_depto = d.id_depto;
```

**Resultado (7 linhas):**

|nome|nome_depto|
|---|---|
|Ana|TI|
|Bruno|TI|
|Carla|RH|
|Diego|RH|
|Elaine|**NULL**|
|Felipe|Financeiro|
|**NULL**|Marketing|

> **Atenção — muito cobrado:** o **MySQL não possui `FULL JOIN` nativamente**. Para simular, é preciso fazer um `UNION` de `LEFT JOIN` com `RIGHT JOIN`:
> 
> ```sql
> SELECT f.nome, d.nome_depto FROM funcionario f LEFT JOIN departamento d ON f.id_depto = d.id_depto
> UNION
> SELECT f.nome, d.nome_depto FROM funcionario f RIGHT JOIN departamento d ON f.id_depto = d.id_depto;
> ```

### 9.5 `CROSS JOIN`

**O que faz:** produto cartesiano — cada linha de uma tabela é combinada com **todas** as linhas da outra, sem nenhuma condição de junção.

```sql
SELECT f.nome, d.nome_depto
FROM funcionario f
CROSS JOIN departamento d;
```

Como `funcionario` tem 6 linhas e `departamento` tem 4, o resultado terá **6 × 4 = 24 linhas** (todas as combinações possíveis de funcionário com departamento, façam sentido ou não).

### 9.6 `SELF JOIN`

Não é um comando novo — é apenas uma tabela unida **com ela mesma**, usando apelidos diferentes para cada "cópia". Muito usado para relações hierárquicas (funcionário → gerente).

```sql
SELECT e.nome AS funcionario, g.nome AS gerente
FROM funcionario e
LEFT JOIN funcionario g ON e.id_gerente = g.id_func;
```

**Resultado:**

|funcionario|gerente|
|---|---|
|Ana|NULL|
|Bruno|Ana|
|Carla|NULL|
|Diego|Carla|
|Elaine|NULL|
|Felipe|NULL|

### 9.7 `NATURAL JOIN`

Faz o `JOIN` automaticamente usando **todas as colunas com o mesmo nome** nas duas tabelas — sem precisar escrever `ON`.

```sql
SELECT f.nome, d.nome_depto
FROM funcionario f
NATURAL JOIN departamento d;
```

> **Cuidado — muito explorado em prova:** o `NATURAL JOIN` é "perigoso" porque casa **automaticamente** por qualquer coluna de mesmo nome nas duas tabelas — se por acaso ambas tiverem uma coluna `nome`, por exemplo, ela também entraria na condição de junção, gerando resultado inesperado. Por isso, em ambientes profissionais, prefere-se sempre `JOIN ... ON` ou `JOIN ... USING`, explícitos.

### 9.8 `JOIN ... USING (coluna)`

Uma forma simplificada de `ON` quando a coluna de ligação tem o **mesmo nome** nas duas tabelas:

```sql
SELECT f.nome, d.nome_depto
FROM funcionario f
JOIN departamento d USING (id_depto);
```

Equivale a `ON f.id_depto = d.id_depto`, mas com sintaxe mais curta.

### 9.9 JOIN "implícito" (sintaxe antiga, ainda muito cobrada)

```sql
SELECT f.nome, d.nome_depto
FROM funcionario f, departamento d
WHERE f.id_depto = d.id_depto;
```

Isso equivale a um `INNER JOIN`, mas coloca as tabelas separadas por vírgula no `FROM` e a condição de junção dentro do `WHERE`. É comum a prova pedir para "converter" essa sintaxe antiga para a sintaxe moderna com `JOIN...ON`, ou o contrário.

### 9.10 Quadro-resumo de todos os JOINs

|JOIN|O que preserva|Quantas linhas no nosso exemplo|
|---|---|---|
|`INNER JOIN`|Só quem casa nos dois lados|5|
|`LEFT JOIN`|Tudo da esquerda + o que casar da direita|6|
|`RIGHT JOIN`|Tudo da direita + o que casar da esquerda|6|
|`FULL JOIN`|Tudo dos dois lados|7|
|`CROSS JOIN`|Todas as combinações possíveis (produto cartesiano)|24|
|`SELF JOIN`|Tabela unida com ela mesma (para hierarquias)|6|
|`NATURAL JOIN`|Como o INNER, mas casando automaticamente por colunas de mesmo nome|5|

---

## 10. Subqueries (subconsultas) — todos os tipos, detalhados

Uma **subquery** é um `SELECT` "embutido" dentro de outro comando (`SELECT`, `INSERT`, `UPDATE`, `DELETE`, ou dentro de outra subquery). Serve para resolver perguntas que dependem de um resultado intermediário.

### 10.1 Subquery escalar (retorna um único valor) — usada em comparações no `WHERE`

```sql
SELECT nome, salario
FROM funcionario
WHERE salario > (SELECT AVG(salario) FROM funcionario);
```

**Como ler:** primeiro o SGBD resolve a parte de dentro (`SELECT AVG(salario) FROM funcionario`), que dá **4416,67** (calculado na seção 7.5). Depois, a consulta externa vira, na prática:

```sql
SELECT nome, salario FROM funcionario WHERE salario > 4416.67;
```

**Resultado:**

|nome|salario|
|---|---|
|Ana|5000|
|Bruno|4500|
|Elaine|7000|

### 10.2 Subquery com `IN` (retorna múltiplos valores)

```sql
SELECT nome
FROM funcionario
WHERE id_depto IN (SELECT id_depto FROM departamento WHERE nome_depto IN ('TI', 'RH'));
```

A subquery retorna `(10, 20)`. A consulta externa então traz quem está nesses departamentos. **Resultado:** Ana, Bruno, Carla, Diego.

### 10.3 Subquery com `NOT IN` — e o cuidado com `NULL`

```sql
SELECT nome
FROM funcionario
WHERE id_depto NOT IN (SELECT id_depto FROM departamento WHERE nome_depto = 'TI');
```

Aqui é preciso ter cuidado: se a subquery **pudesse** retornar algum `NULL` misturado na lista, o `NOT IN` deixaria de funcionar corretamente e **não retornaria nenhuma linha** (por causa da lógica de três valores explicada na seção 5.3). Neste exemplo específico não há esse risco (a coluna `id_depto` da tabela `departamento` é chave primária, sem `NULL`), mas **é um erro clássico** quando a subquery é feita sobre uma coluna que aceita `NULL` (por exemplo, se fosse `NOT IN (SELECT id_depto FROM funcionario)`, e algum funcionário tivesse `id_depto` nulo, como a Elaine — nesse caso o `NOT IN` "quebraria" e não retornaria nada).

### 10.4 Subquery com `EXISTS` / `NOT EXISTS`

`EXISTS` testa apenas **se a subquery retorna alguma linha** — não importa o valor, só importa se existe ou não. É a alternativa **segura** ao `IN`/`NOT IN` quando há risco de `NULL`.

```sql
-- Funcionários que TÊM pelo menos um dependente
SELECT f.nome
FROM funcionario f
WHERE EXISTS (
    SELECT 1 FROM dependente d WHERE d.id_func = f.id_func
);
```

**Resultado:** Ana (2 dependentes: Joao e Maria), Carla (1 dependente: Pedro), Diego (1 dependente: "Ana").

```sql
-- Funcionários que NÃO TÊM nenhum dependente
SELECT f.nome
FROM funcionario f
WHERE NOT EXISTS (
    SELECT 1 FROM dependente d WHERE d.id_func = f.id_func
);
```

**Resultado:** Bruno, Elaine, Felipe.

> **Diferença fundamental `IN`/`NOT IN` × `EXISTS`/`NOT EXISTS`:** `IN` compara **valores**; `EXISTS` testa **existência de linhas**. Por isso `NOT EXISTS` é sempre seguro em relação a `NULL`, enquanto `NOT IN` pode "quebrar" silenciosamente. Essa diferença é um dos tópicos mais cobrados em prova sobre subqueries.

### 10.5 Subquery correlacionada

É uma subquery que **faz referência a uma coluna da consulta externa**, sendo reexecutada, conceitualmente, uma vez para **cada linha** da consulta principal (os exemplos com `EXISTS` acima já são correlacionados). Outro exemplo, criando uma coluna calculada:

```sql
SELECT f.nome,
       (SELECT COUNT(*) FROM dependente d WHERE d.id_func = f.id_func) AS qtd_dependentes
FROM funcionario f;
```

**Resultado:**

|nome|qtd_dependentes|
|---|---|
|Ana|2|
|Bruno|0|
|Carla|1|
|Diego|1|
|Elaine|0|
|Felipe|0|

### 10.6 Subquery no `FROM` (tabela derivada / _inline view_)

```sql
SELECT depto_resumo.id_depto, depto_resumo.total
FROM (
    SELECT id_depto, COUNT(*) AS total
    FROM funcionario
    GROUP BY id_depto
) AS depto_resumo
WHERE depto_resumo.total > 1;
```

**Resultado:**

|id_depto|total|
|---|---|
|10|2|
|20|2|

> Toda subquery usada no `FROM` **precisa de um apelido** (`AS depto_resumo`, no exemplo), pois o SGBD passa a tratá-la como se fosse uma tabela comum, temporária, existente só durante a execução da consulta.

### 10.7 `ANY` / `SOME` e `ALL`

```sql
-- Salário MAIOR que PELO MENOS UM salário do departamento 20 (ou seja, maior que o menor deles)
SELECT nome FROM funcionario
WHERE salario > ANY (SELECT salario FROM funcionario WHERE id_depto = 20);
-- Subquery retorna (3000, 3000). "> ANY" equivale a "> 3000" (o menor valor da lista)

-- Salário MAIOR que TODOS os salários do departamento 20
SELECT nome FROM funcionario
WHERE salario > ALL (SELECT salario FROM funcionario WHERE id_depto = 20);
-- "> ALL" equivale a "> 3000" também, pois os dois valores do depto 20 são iguais (3000)
```

> `SOME` é sinônimo de `ANY` no padrão SQL (mesma coisa, nomes diferentes).

### 10.8 Subquery dentro de `INSERT`, `UPDATE` e `DELETE`

```sql
-- Dentro de INSERT
INSERT INTO funcionario_bkp (id_func, nome)
SELECT id_func, nome FROM funcionario WHERE salario > (SELECT AVG(salario) FROM funcionario);

-- Dentro de UPDATE
UPDATE funcionario
SET salario = salario * 1.10
WHERE id_depto = (SELECT id_depto FROM departamento WHERE nome_depto = 'TI');

-- Dentro de DELETE
DELETE FROM dependente
WHERE id_func IN (SELECT id_func FROM funcionario WHERE salario < 3500);
```

---

## 11. Operadores de conjunto — `UNION`, `UNION ALL`, `INTERSECT`, `EXCEPT`/`MINUS`

Esses operadores combinam o **resultado de duas (ou mais) consultas `SELECT`**. Regras obrigatórias:

- O número de colunas selecionadas deve ser **igual** em todas as consultas;
- Os tipos de dados das colunas correspondentes precisam ser **compatíveis**;
- Os nomes de coluna do resultado final seguem os da **primeira** consulta.

Para estes exemplos, vamos comparar os nomes de `funcionario` com os nomes de `dependente`:

- Nomes em `funcionario`: Ana, Bruno, Carla, Diego, Elaine, Felipe
- Nomes em `dependente`: Joao, Maria, Pedro, **Ana** (repare que "Ana" aparece nas duas listas)

### 11.1 `UNION` — une e remove duplicatas

```sql
SELECT nome FROM funcionario
UNION
SELECT nome_dep FROM dependente;
```

**Resultado (9 linhas, "Ana" aparece uma única vez):** Ana, Bruno, Carla, Diego, Elaine, Felipe, Joao, Maria, Pedro

### 11.2 `UNION ALL` — une e mantém duplicatas

```sql
SELECT nome FROM funcionario
UNION ALL
SELECT nome_dep FROM dependente;
```

**Resultado (10 linhas, "Ana" aparece duas vezes):** Ana, Bruno, Carla, Diego, Elaine, Felipe, Joao, Maria, Pedro, Ana

> **Pegadinha de prova:** como o `UNION` precisa comparar todas as linhas para eliminar duplicatas, ele é **mais custoso** (mais lento) que o `UNION ALL`. Quando você sabe que não haverá duplicatas, ou quando duplicatas não atrapalham, `UNION ALL` é a escolha mais eficiente.

### 11.3 `INTERSECT` — apenas o que aparece nas duas consultas

```sql
SELECT nome FROM funcionario
INTERSECT
SELECT nome_dep FROM dependente;
```

**Resultado:** Ana (é o único nome que aparece nas duas listas ao mesmo tempo).

### 11.4 `EXCEPT` (PostgreSQL/SQL Server) ou `MINUS` (Oracle) — o que está na primeira e não está na segunda

```sql
-- PostgreSQL / SQL Server
SELECT nome FROM funcionario
EXCEPT
SELECT nome_dep FROM dependente;

-- Oracle
SELECT nome FROM funcionario
MINUS
SELECT nome_dep FROM 

dependente;
```

**Resultado:** Bruno, Carla, Diego, Elaine, Felipe ("Ana" é removida, pois aparece também na segunda consulta).

> **Atenção — muito cobrado:** o **MySQL histórico** não possuía `INTERSECT` nem `EXCEPT` — era preciso simular com `JOIN`/`NOT EXISTS`. A partir da versão **8.0.31**, o MySQL passou a suportar ambos nativamente. Provas mais antigas (ou que mencionam versões antigas do MySQL) exploram essa ausência.

### 11.5 Quadro-resumo dos operadores de conjunto

|Operador|Remove duplicatas?|Equivalente em teoria de conjuntos|Resultado no exemplo|
|---|---|---|---|
|`UNION`|Sim|A ∪ B|9 nomes distintos|
|`UNION ALL`|Não|A ∪ B (com repetição)|10 nomes (com repetição)|
|`INTERSECT`|Sim|A ∩ B|Apenas "Ana"|
|`EXCEPT` / `MINUS`|Sim|A − B|5 nomes (funcionários cujo nome não é de nenhum dependente)|

---

## 12. Comparativo mental: quando usar JOIN, Subquery ou Conjunto

|Você quer...|Use|
|---|---|
|Trazer colunas de duas tabelas relacionadas, lado a lado, na mesma linha|`JOIN`|
|Filtrar uma tabela com base em condição de outra, **sem** trazer colunas dela no resultado|Subquery (`IN`, `EXISTS`)|
|Empilhar (um embaixo do outro) resultados de consultas com a **mesma estrutura** de colunas|`UNION` / `UNION ALL`|
|Saber o que existe nas duas listas ao mesmo tempo|`INTERSECT`|
|Saber o que existe em uma lista mas não na outra|`EXCEPT` / `MINUS`|
|Criar uma coluna calculada, dependente de dados de outra tabela, linha a linha|Subquery correlacionada no `SELECT`|
|Pré-calcular/agrupar dados antes de filtrar de novo|Subquery no `FROM` (inline view)|

---

## 13. DCL e DTL/TCL — para completar o panorama

### 13.1 `GRANT` e `REVOKE` (DCL)

```sql
-- Conceder privilégio de leitura na tabela funcionario ao usuário "joao"
GRANT SELECT ON funcionario TO joao;

-- Conceder vários privilégios de uma vez
GRANT SELECT, INSERT, UPDATE ON funcionario TO joao;

-- Revogar (retirar) um privilégio concedido anteriormente
REVOKE INSERT ON funcionario FROM joao;
```

### 13.2 `COMMIT`, `ROLLBACK`, `SAVEPOINT` (DTL/TCL)

```sql
BEGIN;  -- ou START TRANSACTION, dependendo do SGBD

UPDATE funcionario SET salario = salario * 1.10 WHERE id_depto = 10;
SAVEPOINT antes_do_bonus;

UPDATE funcionario SET salario = salario + 500 WHERE id_depto = 10;

ROLLBACK TO SAVEPOINT antes_do_bonus;  -- desfaz só o bônus, mantém o reajuste de 10%

COMMIT;  -- confirma definitivamente o que restou (o reajuste de 10%)
```

- `COMMIT` → confirma (grava definitivamente) todas as alterações feitas na transação atual.
- `ROLLBACK` → desfaz todas as alterações feitas desde o início da transação (ou até um `SAVEPOINT`, se indicado).
- `SAVEPOINT` → cria um "ponto de controle" dentro da transação, para permitir um `ROLLBACK` parcial.

---

## 14. Checklist de revisão rápida (véspera de prova)

- [ ] Sei diferenciar DDL, DML, DQL, DCL e DTL de cabeça — e sei que `TRUNCATE` é DDL, `GRANT`/`REVOKE` são DCL, e `COMMIT`/`ROLLBACK` são DTL.
- [ ] Sei a ordem lógica de execução: `FROM → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT`.
- [ ] Sei explicar por que `WHERE` não aceita função de agregação, e por que isso é papel do `HAVING`.
- [ ] Sei todas as _constraints_ de `CREATE TABLE` (PK, FK, NOT NULL, UNIQUE, CHECK, DEFAULT) e sei escrever cada uma.
- [ ] Sei todas as variações de `ALTER TABLE` (ADD/DROP COLUMN, RENAME, MODIFY/ALTER COLUMN TYPE, ADD/DROP CONSTRAINT).
- [ ] Sei a diferença entre `DROP`, `TRUNCATE` e `DELETE` (estrutura, WHERE, ROLLBACK, velocidade).
- [ ] Sei todos os JOINs (INNER, LEFT, RIGHT, FULL, CROSS, SELF, NATURAL, USING) e sei desenhar mentalmente o resultado de cada um.
- [ ] Sei a diferença entre `COUNT(*)`, `COUNT(coluna)` e `COUNT(DISTINCT coluna)`.
- [ ] Sei calcular `SUM`, `AVG`, `MAX`, `MIN` combinados com `GROUP BY`.
- [ ] Sei usar `COALESCE`/`NVL`/`ISNULL` e `CASE WHEN` para tratar valores e criar categorias.
- [ ] Sei montar e ler uma subquery escalar, uma com `IN`, uma com `EXISTS`/`NOT EXISTS` (e sei por que `NOT EXISTS` é mais seguro que `NOT IN` com `NULL`), uma correlacionada e uma no `FROM`.
- [ ] Sei diferenciar `UNION` de `UNION ALL` (remoção de duplicatas) e sei o que `INTERSECT` e `EXCEPT`/`MINUS` fazem.
- [ ] Sei que a sintaxe de vários comandos (`MODIFY` vs `ALTER COLUMN`, `MINUS` vs `EXCEPT`, `TOP` vs `LIMIT` vs `FETCH FIRST`, `FULL JOIN` ausente no MySQL) muda entre Oracle, PostgreSQL, MySQL e SQL Server — **leio sempre o SGBD indicado no enunciado antes de responder**.

---

## 15. Referências técnicas utilizadas

**Documentação oficial e material técnico:**

1. PostgreSQL Global Development Group. _PostgreSQL Documentation — SELECT, Data Definition, Set Operations, Functions and Operators_. https://www.postgresql.org/docs/current/
2. Oracle Corporation. _Oracle Database SQL Language Reference_. https://docs.oracle.com/en/database/oracle/oracle-database/
3. Microsoft. _Transact-SQL (T-SQL) Reference — SQL Server_. https://learn.microsoft.com/pt-br/sql/t-sql/
4. MySQL / Oracle Corporation. _MySQL 8.0 Reference Manual — SQL Statement Syntax, Set Operations_. https://dev.mysql.com/doc/refman/8.0/en/sql-statements.html
5. ISO/IEC 9075 (SQL Standard) — referência conceitual sobre a padronização ANSI/ISO da linguagem SQL.
6. Elmasri, R.; Navathe, S. B. _Sistemas de Banco de Dados_ — capítulos sobre SQL, DDL/DML, JOINs e Álgebra Relacional (base teórica clássica usada como referência acadêmica em concursos de TI).
7. Date, C. J. _Introdução a Sistemas de Bancos de Dados_ — conceitos de linguagem relacional, junções, subconsultas e operadores de conjunto.
8. Silberschatz, A.; Korth, H.; Sudarshan, S. _Sistema de Banco de Dados_ — capítulo de SQL avançado (subqueries, agregação, views).

**Artigos técnicos usados como apoio (padrões de sintaxe e exemplos de subquery/JOIN):** 9. DevMedia. _Subqueries: Concurso CGU e TCMSP com questões resolvidas_. https://www.devmedia.com.br/subqueries-questoes-de-concurso-resolvidas/40159 10. DevMedia. _Subqueries na prática: quando usar onde o JOIN não se aplica_. https://www.devmedia.com.br/trabalhando-com-subqueries/40134

**Sites de questões de concurso consultados apenas como referência de estilo/recorrência de temas (não como fonte técnica principal):** 11. Qconcursos — banco de questões de Banco de Dados/SQL. https://www.qconcursos.com/ 12. TecConcursos — questões de sublinguagens SQL (DDL, DML, DQL, DCL, DTL). https://www.tecconcursos.com.br/ 13. Gran Cursos Online — banco de questões. https://questoes.grancursosonline.com.br/ 14. Resolva Mais — questões comentadas de SQL. https://resolvamais.com.br/

> Observação: os itens 11–14 foram usados apenas para mapear **quais tópicos e pegadinhas são mais recorrentes** em provas de TI (inclusive de bancas como CESGRANRIO, CESPE/CEBRASPE e FCC) — a base conceitual e todos os exemplos deste resumo foram elaborados a partir da documentação oficial dos SGBDs e da bibliografia acadêmica listada acima.

---

## 16. Sites para treinar SQL, leitura de queries, DDL e DML

**Prática interativa (com banco de dados de verdade no navegador):**

- **SQLZoo** — https://sqlzoo.net/ — exercícios progressivos de SELECT, JOIN, agregação e subquery, com correção automática.
- **SQLBolt** — https://sqlbolt.com/ — lições curtas e interativas, ótimo para fixar sintaxe básica de SELECT/JOIN/agrupamento.
- **DB Fiddle** — https://www.db-fiddle.com/ — roda scripts DDL + DML + SELECT em MySQL, PostgreSQL, SQL Server e SQLite direto no navegador (ótimo para testar as diferenças de sintaxe entre SGBDs citadas neste resumo).
- **SQL Fiddle** — http://sqlfiddle.com/ — similar ao DB Fiddle, útil para montar rapidamente um schema de teste.
- **Mode SQL Tutorial** — https://mode.com/sql-tutorial/ — tutorial completo com foco em leitura/análise de queries reais.
- **W3Schools SQL Tryit Editor** — https://www.w3schools.com/sql/trysql.asp — editor online simples para testar comandos rapidamente.
- **HackerRank (trilha SQL)** — https://www.hackerrank.com/domains/sql — exercícios com nível de dificuldade crescente, muito usados para praticar JOIN, subquery e agregação.
- **LeetCode (seção Database)** — https://leetcode.com/problemset/database/ — problemas de SQL mais próximos do "mundo real", bons para treinar subqueries e funções de janela (window functions).
- **SQL Murder Mystery** — https://mystery.knightlab.com/ — um "jogo" investigativo que exige encadear vários SELECTs, WHEREs e JOINs para resolver um caso; ótimo para treinar leitura de queries.
- **Exercism (trilha SQL)** — https://exercism.org/tracks/sql — exercícios com mentoria/feedback, bom para entender o "porquê" de cada solução.

**Documentação para consulta de sintaxe enquanto pratica:**

- PostgreSQL Tutorial (comunidade) — https://www.postgresqltutorial.com/
- Oracle Live SQL (execute SQL/PL-SQL Oracle direto no navegador, gratuito, exige cadastro) — https://livesql.oracle.com/
- MySQL Tutorial (comunidade) — https://www.mysqltutorial.org/

> **Dica de estudo:** ao treinar, force-se a escrever a query **e também** a "traduzir" queries prontas para português, linha a linha, seguindo a ordem lógica `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY`. Essa é exatamente a habilidade cobrada nas questões da CESGRANRIO que pedem "o que este comando retorna?" ou "qual o resultado desta consulta?".