## 1. Conceito (William Inmon) — CAI MUITO

A CESGRANRIO adora cobrar a definição clássica de Inmon, criador do termo. Um DW é um repositório de dados com **4 características** (decore a sigla mentalmente: **O-I-V-N**):

|Característica|O que significa|Pegadinha comum|
|---|---|---|
|**Orientado a Assunto** (Subject-Oriented)|Organizado por temas de negócio (vendas, clientes), não por processos/aplicações|Provas trocam por "orientado a processos" ou "operacional" — ERRADO|
|**Integrado**|Dados de várias fontes são padronizados (mesma unidade de medida, mesma nomenclatura) antes de entrar no DW|Confundir com "dados brutos sem tratamento"|
|**Variante no Tempo** (Time-Variant)|Guarda histórico (dados de vários períodos), não apenas o valor atual|Achar que o DW mantém "sempre a versão mais recente e única" (isso é característica de OLTP)|
|**Não Volátil** (Non-Volatile)|Depois de carregado, o dado não é alterado nem apagado; só se insere (não há UPDATE/DELETE normal)|Dizer que "dados podem ser atualizados mantendo versão única" — ERRADO (questão real da CESGRANRIO já usou essa pegadinha)|

> **Questão real CESGRANRIO (Petrobras, 2010):** "Data warehouse tem por característica ser..." → resposta: **orientado a assuntos**.

---

## 2. Data Warehouse x Data Mart

- **Data Warehouse:** visão corporativa, integra toda a empresa.
- **Data Mart:** subconjunto do DW, focado em **um departamento ou área de negócio** (ex.: Data Mart de RH, de Vendas).
- Pode ser **dependente** (alimentado a partir do DW central — abordagem Inmon) ou **independente** (construído direto das fontes — comum na abordagem Kimball).

---

## 3. Duas Arquiteturas Clássicas (tema recorrente em provas de nível superior)

|                  |**Inmon (Top-Down)**|**Kimball (Bottom-Up)**|
|---|---|---|
| Abordagem        |Constrói o DW corporativo primeiro (modelo normalizado, 3FN), depois derivam-se os Data Marts|Constrói Data Marts dimensionais primeiro e os integra via "barramento"|
| Modelo           |Entidade-Relacionamento (normalizado) no DW central|Modelagem dimensional (Estrela) desde o início|
| Ferramenta-chave |—|**Matriz de Barramento (Bus Matrix)**: linhas = processos de negócio, colunas = dimensões conformadas|
| Vantagem         |Consistência corporativa forte|Implementação mais rápida, entrega valor incremental|

> **Questão real CESGRANRIO (Banco do Brasil, 2021)** cobrou a Matriz de Barramento — linhas = **processos de negócio**, colunas = **dimensões**.

---

## 4. Arquitetura em Camadas (fluxo de dados)

```
Fontes de Dados (OLTP/ERP/CRM/arquivos)
        ↓  EXTRAÇÃO
Staging Area (Área de Preparação/Transiente)
        ↓  TRANSFORMAÇÃO e LIMPEZA (Data Cleansing)
        ↓  CARGA
Data Warehouse (repositório central)
        ↓
Data Marts / Camada de Apresentação
        ↓
Ferramentas OLAP, Relatórios, Dashboards, Data Mining
```

- **ETL** = Extract, Transform, Load (extração → transformação → carga). A CESGRANRIO já cobrou variações como **ELT** (carga antes da transformação, comum em arquiteturas mais modernas/cloud) e **extração incremental** (só captura os dados que mudaram desde a última carga, geralmente via _timestamp_, _log de transação_ ou _trigger_).
- **Staging Area (Data Staging Area):** camada intermediária onde os dados ficam temporariamente para tratamento antes de irem ao DW. **Já foi gabarito direto em questão CESGRANRIO/Transpetro (2018).**
- **Metadados:** "dados sobre os dados" — descrevem origem, estrutura, regras de transformação e significado dos dados do DW. Servem para o usuário entender e navegar na estrutura, e para governança/auditoria. (Cobrado em questão CESGRANRIO 2018.)

---

## 5. Modelagem Dimensional

### Tabela Fato

- Contém as **medidas numéricas** (métricas) do negócio: valores, quantidades.
- Possui chaves estrangeiras para as dimensões.
- Tipos de fatos quanto à aditividade:
    - **Aditivos:** podem ser somados em qualquer dimensão (ex.: valor de venda).
    - **Semiaditivos:** podem ser somados em algumas dimensões, mas não em todas (ex.: saldo em conta — não soma no tempo).
    - **Não aditivos:** não podem ser somados (ex.: percentuais, razões).
- **Granularidade:** define o nível de detalhe de cada linha da fato (ex.: venda por item, por dia, por loja).

### Tabela Dimensão

- Contém os **atributos descritivos** usados para filtrar/agrupar (ex.: dimensão Tempo: dia, mês, ano; dimensão Produto: nome, categoria).
- Normalmente **desnormalizada**.
- **Slowly Changing Dimensions (SCD)** — tema clássico:
    - **Tipo 1:** sobrescreve o valor antigo (perde histórico).
    - **Tipo 2:** cria novo registro (nova linha) preservando o histórico (mais usado).
    - **Tipo 3:** cria nova coluna para guardar valor anterior (histórico limitado).

#### Tipos especiais de dimensão (também cai em prova)

- **Dimensão Conformada (Conformed Dimension):** dimensão compartilhada por várias tabelas fato, com o mesmo significado e estrutura em todas elas (ex.: a dimensão "Tempo" usada tanto na fato de Vendas quanto na fato de Estoque). É a peça-chave da **Matriz de Barramento** de Kimball, pois permite comparar/combinar diferentes processos de negócio.
- **Dimensão Degenerada (Degenerate Dimension):** um atributo que fica **na própria tabela fato** (não tem tabela de dimensão associada), geralmente um número de identificação como número da nota fiscal ou do pedido. Não possui atributos descritivos adicionais, por isso não justifica uma tabela separada.
- **Dimensão Papel (Role-Playing Dimension):** uma única tabela de dimensão física é usada **várias vezes** na mesma fato, com significados diferentes (ex.: a dimensão "Data" usada como Data do Pedido, Data do Envio e Data da Entrega na mesma tabela fato).
- **Junk Dimension:** agrupa vários atributos textuais/flags de baixa cardinalidade (ex.: indicadores sim/não, códigos de status) em **uma única tabela de dimensão**, evitando poluir a fato com várias chaves estrangeiras pequenas.
- **Hierarquia de dimensão:** relação de níveis dentro de uma dimensão, do mais genérico ao mais específico (ex.: na dimensão Tempo → Ano → Trimestre → Mês → Dia; na dimensão Localidade → País → Estado → Cidade). É essa hierarquia que permite as operações de **Drill Down** e **Roll Up**.

### Esquema Estrela x Floco de Neve (Snowflake)

|Esquema Estrela|Esquema Floco de Neve|
|---|---|
|Dimensões desnormalizadas (uma tabela só por dimensão)|Dimensões normalizadas em sub-tabelas (hierarquias separadas)|
|Menos joins, consultas mais rápidas|Mais joins, mas menos redundância de dados|
|Mais simples e mais usado na prática|Economiza espaço de armazenamento|

> **Questão real CESGRANRIO (2018):** apresentou um modelo com tabela fato "Venda" e dimensões associadas — testando se o candidato reconhece o esquema estrela.

---

## 6. OLAP x OLTP (comparação certeira em prova)

|Característica|**OLTP**|**OLAP**|
|---|---|---|
|Finalidade|Operações do dia a dia (transações)|Análise e suporte à decisão|
|Dados|Atuais, detalhados|Históricos, sumarizados|
|Modelo|Normalizado (3FN)|Dimensional (Estrela/Floco de Neve)|
|Operações|INSERT/UPDATE/DELETE frequentes|Consultas complexas, leitura predominante|
|Usuários|Funcionários operacionais|Gestores, analistas|
|Volume por transação|Pequeno|Grande (varredura de muitos registros)|

### Operações OLAP (decore para questões de "identifique a operação")

- **Drill Down:** aumenta o nível de detalhe (ex.: de ano → mês).
- **Roll Up (Drill Up):** diminui o detalhe, agrega dados (ex.: de mês → ano).
- **Slice:** "fatia" o cubo fixando um valor de uma dimensão.
- **Dice:** seleciona um subcubo, filtrando valores de duas ou mais dimensões.
- **Pivot (Rotate):** gira os eixos do cubo para outra perspectiva de visualização.
- **Drill Across:** navega entre diferentes tabelas fato usando dimensões conformadas.

### Tipos de sistemas OLAP

- **MOLAP** (Multidimensional): dados pré-calculados em cubos — consulta rápida, mas maior custo de armazenamento/processamento na carga.
- **ROLAP** (Relational): usa banco relacional comum para simular o cubo via SQL — mais escalável, porém consultas mais lentas.
- **HOLAP** (Hybrid): combina os dois.

---

## 7. Data Mining

- Frequentemente citado como etapa **posterior** ao DW: usa técnicas estatísticas/algoritmos (associação, clusterização, classificação) para descobrir **padrões ocultos** nos dados armazenados.
- Não confundir: DW é o **repositório**; Data Mining é o **processo analítico** que explora esse repositório.

---

## 8. Ciclo de Vida: Kimball x Inmon

Além das arquiteturas (Top-Down x Bottom-Up), as duas metodologias propõem **ciclos de vida de projeto** diferentes. É comum a prova pedir para reconhecer qual etapa pertence a qual metodologia.

### 8.1 Ciclo de Vida de Kimball (Business Dimensional Lifecycle)

É um roteiro com **três trilhas paralelas** (tecnologia, dados, e BI/aplicações) que convergem na implantação. Ordem geral:

1. **Planejamento do Projeto**
2. **Definição dos Requisitos de Negócio** (levantados junto às áreas de negócio — não é "adivinhado" pela TI)
3. Três trilhas em paralelo, a partir dos requisitos:
    - **Trilha de Tecnologia:** Desenho da Arquitetura Técnica → Seleção e Instalação de Produtos
    - **Trilha de Dados:** Modelagem Dimensional → Projeto Físico → Desenho e Desenvolvimento do ETL
    - **Trilha de BI:** Especificação das Aplicações de BI → Desenvolvimento das Aplicações de BI
4. **Implantação (Deployment)** — só ocorre quando as três trilhas convergem
5. **Manutenção**
6. **Crescimento (Growth)** — novos ciclos incrementais

> Ponto-chave: o ciclo de Kimball é **orientado a requisitos de negócio desde o início** e entrega valor de forma **incremental** (por Data Mart/processo de negócio).

### 8.2 Ciclo de Vida de Inmon (CLDS)

Inmon propõe o **CLDS**, que é basicamente o **inverso do clássico SDLC** (Systems Development Life Cycle, usado em sistemas OLTP tradicionais):

- **SDLC tradicional (orientado a requisitos):** requisitos → desenho → construção → dados. Faz sentido para sistemas transacionais, cujos requisitos são conhecidos de antemão.
- **CLDS (orientado a dados):** primeiro **coleta e integra os dados** (Code/Collect → Load) para só depois, **analisando os dados já carregados**, descobrir os requisitos e possibilidades analíticas (Data → Specification).

> Ponto-chave: no CLDS de Inmon, os requisitos **não são totalmente conhecidos no início** — eles emergem à medida que o usuário explora os dados já integrados no DW corporativo. É um processo mais **exploratório e iterativo**, coerente com a abordagem Top-Down (construir a base corporativa primeiro).

### 8.3 Quadro-resumo

|                  | **Kimball**                      | **Inmon**                       |
| ---------------- | -------------------------------- | ------------------------------- |
| Ciclo            | Business Dimensional Lifecycle   | CLDS                            |
| Ponto de partida | Requisitos de negócio            | Dados (coleta e integração)     |
| Entrega          | Incremental, por Data Mart       | Corporativa, mais lenta         |
| Filosofia        | "De baixo para cima" (Bottom-Up) | "De cima para baixo" (Top-Down) |

---

## 9. Pegadinhas Favoritas da CESGRANRIO (resumo rápido antes da prova)

1. Dizer que o DW é usado para **operações do dia a dia** → ERRADO (isso é OLTP).
2. Dizer que os dados do DW podem ser **atualizados/alterados livremente** → ERRADO (não volátil).
3. Confundir **Data Mart** com **Data Warehouse** (Data Mart é subconjunto/departamental).
4. Trocar as operações OLAP entre si (ex.: chamar Drill Down de Roll Up).
5. Esquema Estrela tem dimensões **desnormalizadas**; Floco de Neve tem dimensões **normalizadas** — inverter isso é armadilha clássica.
6. Achar que ETL só ocorre uma vez — na prática pode ser **incremental** e recorrente.
7. Achar que metadado é o "dado de negócio" — na verdade é o dado **sobre a estrutura/origem** dos dados.

---

## Fontes utilizadas

**Livros/referências teóricas (base conceitual):**

- Kimball, Ralph & Ross, Margy — _The Data Warehouse Toolkit_ (conceitos de modelagem dimensional, esquema estrela, Matriz de Barramento, SCD).
- Inmon, William H. — _Building the Data Warehouse_ (definição clássica das 4 características do DW: orientado a assunto, integrado, variante no tempo, não volátil).
- Elmasri, R. & Navathe, S. — _Fundamentals of Database Systems_ (capítulos sobre Data Warehousing e OLAP, usados como referência comum em concursos de TI).
- Date, C. J. — _Introdução a Sistemas de Bancos de Dados_ (comparação OLTP x OLAP, conceitos de modelagem de dados).

**Questões reais da banca CESGRANRIO usadas para calibrar o resumo (localizadas via pesquisa):**

- CESGRANRIO — Petrobras, Analista de Sistemas Júnior (2010): característica do Data Warehouse.
- CESGRANRIO — Transpetro, Analista de Sistemas Júnior (2018): Data Staging Area no processo de ETL.
- CESGRANRIO (2018): metadados e sua importância na estrutura do DW.
- CESGRANRIO (2018): modelo dimensional com tabela fato "Venda" e dimensões.
- CESGRANRIO — Banco da Amazônia, Técnico Científico em TI (2018): modelagem multidimensional e ETL incremental.
- CESGRANRIO — Banco do Brasil, Escriturário/Agente de Tecnologia (2021): Matriz de Barramento do Data Warehouse (modelo estrela).
- CESGRANRIO — Petrobras, Analista de Sistemas Júnior (2012): armazenamento e arquitetura de sistemas de informação envolvendo DW.

_Observação: as questões foram identificadas por meio de busca em bancos de questões (Gran Cursos, QConcursos, Estude Grátis, Aprova Concursos), que replicam o texto oficial das provas da banca. Recomendo resolver essas questões na íntegra em um desses sites para fixar o padrão de redação da CESGRANRIO._