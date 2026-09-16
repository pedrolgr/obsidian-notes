> **Como usar este resumo:** o conteúdo está organizado do que a CESGRANRIO **mais cobra** para o que **cai com menos frequência**. Trechos marcados com 🟡 **NUNCA CAIU / RARO EM CESGRANRIO** são conceitos que aparecem em livros e certificações (PSM, PMI-ACP), mas que eu não encontrei em provas documentadas da banca — decida se quer estudá-los como "gordura extra" ou cortar para otimizar tempo.

---

## 0. Como a CESGRANRIO cobra Engenharia de Software Ágil

- Provas **objetivas, de múltipla escolha (5 alternativas)**, enunciados **curtos e diretos**, sem o "certo/errado" da CEBRASPE.
- A banca gosta de **pegadinhas conceituais**: trocar o nome de um papel, artefato ou evento por outro parecido (ex.: dizer que o _Scrum Master_ aprova o _Product Backlog_, ou que o Kanban usa _Sprint_).
- Cobra muito **definição literal de papel/artefato/evento** e **associação correta entre conceitos** (ex.: relacionar cada artefato ao seu "compromisso").
- Cobra também **comparações Scrum x Kanban x Cascata** (diferenças de estrutura, cadência, papéis).
- Costuma citar explicitamente o **Guia do Scrum (Scrum Guide)** como referência normativa — por isso a terminologia oficial da versão vigente importa muito.
- Aparece em concursos como **Caixa, Banco do Brasil, Transpetro, AGERIO, IBGE, Petrobras**, entre outros, na disciplina de TI/Desenvolvimento de Sistemas.

---

## 1. SCRUM

### 1.1 Definição (o que a banca pede decorado)

Scrum é um **framework leve** (não uma metodologia completa, nem um processo prescritivo) que ajuda pessoas, times e organizações a **gerar valor por meio de soluções adaptativas para problemas complexos**. Não é um método, técnica ou processo definitivo — é intencionalmente **incompleto**, deixando outras práticas serem agregadas conforme o contexto.

> ⚠️ Pegadinha clássica: Scrum **não é uma metodologia**, é um **framework**. Bancas adoram trocar essas palavras.

### 1.2 Valores do Scrum

1. **Comprometimento**
2. **Foco**
3. **Abertura**
4. **Respeito**
5. **Coragem**

O sucesso no uso do Scrum depende das pessoas se tornarem mais proficientes em viver esses cinco valores.

### 1.3 Pilares do Scrum (empirismo)

O Scrum se baseia no **empirismo** e no **pensamento enxuto (lean thinking)**. Os três pilares do empirismo são:

1. **Transparência** – o processo e o trabalho devem ser visíveis a quem realiza e a quem recebe o trabalho.
2. **Inspeção** – artefatos e progresso devem ser inspecionados com frequência para detectar variações indesejadas.
3. **Adaptação** – se um processo se desvia dos limites aceitáveis, deve-se ajustar o quanto antes.

### 1.4 O Time Scrum (Scrum Team) — atenção à versão do Guia!

> 🔑 **Ponto crítico de prova:** o **Guia do Scrum 2020** (versão vigente, de Schwaber e Sutherland) **eliminou o termo "Time de Desenvolvimento"** como subgrupo à parte. Hoje existe **um único Scrum Team**, com três conjuntos de responsabilidades (accountabilities):
> 
> - **Scrum Master**
> - **Product Owner**
> - **Developers** (Desenvolvedores) — não é mais chamado de "Time de Desenvolvimento" separado do PO/SM.
> 
> Provas mais antigas (baseadas no Guia 2017) ainda falam em "Time de Desenvolvimento" como um papel distinto do PO e do SM. Questões recentes de bancas como a CESGRANRIO já cobram a redação 2020 (ex.: relacionar artefato → compromisso). **Estude os dois nomes**, mas priorize a terminologia 2020.

Características do Scrum Team:

- **Pequeno o suficiente para permanecer ágil, grande o suficiente para entregar trabalho significativo** — geralmente **10 pessoas ou menos** (o Guia 2020 removeu a antiga recomendação fixa de "3 a 9", adotando uma linguagem mais flexível, mas a ideia de time pequeno permanece).
- **Multifuncional (cross-functional)**: possui todas as competências necessárias para criar valor a cada Sprint.
- **Autogerenciável (self-managing)**: decide internamente quem faz o quê, quando e como — não é mais "autoorganizável" apenas, o termo 2020 é **autogerenciamento**.
- Não há subtimes nem hierarquias dentro do Scrum Team.
- O Scrum Team é responsável por **todas** as atividades relacionadas ao produto: colaboração com stakeholders, verificação, manutenção, operação, experimentação, pesquisa e desenvolvimento.

#### Product Owner (PO)

- **Responsável por maximizar o valor do produto** resultante do trabalho do Scrum Team.
- Gerencia o **Product Backlog**, o que inclui:
    - Desenvolver e comunicar explicitamente a **Meta do Produto (Product Goal)**;
    - Criar e comunicar claramente os itens do Product Backlog;
    - Ordenar (priorizar) os itens do Product Backlog;
    - Garantir que o Product Backlog seja transparente, visível e compreendido.
- O PO **pode delegar** esse trabalho a outros, mas continua sendo o **responsável final**.
- Para que o PO tenha sucesso, **toda a organização deve respeitar suas decisões**, que são visíveis no conteúdo e na ordem do Product Backlog.
- **É uma pessoa, não um comitê.**

#### Scrum Master (SM)

- Responsável por **estabelecer o Scrum** conforme definido no Guia do Scrum, ajudando o Scrum Team e a organização a entenderem a teoria e a prática do Scrum.
- Trabalha para o **benefício do Scrum Team** de várias formas:
    - Treinando os membros em autogerenciamento e interdisciplinaridade;
    - Ajudando o Scrum Team a focar na criação de Incrementos de alto valor;
    - Removendo impedimentos ao progresso;
    - Garantindo que todos os eventos aconteçam, sejam positivos, produtivos e dentro do time-box.
- É **líder servidor (servant leader)** do Scrum Team e da organização.
- Ajuda o **Product Owner**: técnicas de definição de metas, ordenação eficaz do Product Backlog etc.
- Ajuda a **organização**: liderar, treinar e conduzir a adoção do Scrum.
- 🚫 **Pegadinha:** o Scrum Master **não é o gerente do projeto**, **não aprova entregas técnicas**, **não define escopo** e **não distribui tarefas** para o time.

#### Developers (Desenvolvedores)

- Pessoas do Scrum Team **comprometidas em criar qualquer aspecto de um Incremento utilizável a cada Sprint**.
- Sempre responsáveis por:
    - Criar o plano da Sprint: o **Sprint Backlog**;
    - Incorporar qualidade seguindo uma **Definição de Pronto (Definition of Done)**;
    - Adaptar seu plano diariamente rumo à **Meta da Sprint**;
    - Prestar contas uns aos outros e a terceiros como profissionais.

### 1.5 Eventos do Scrum

Todos os eventos são **oportunidades formais de inspecionar e adaptar** artefatos do Scrum e têm **time-box** (duração máxima fixa).

|Evento|Duração máxima (Sprint de 1 mês)|Objetivo|Quem participa|
|---|---|---|---|
|**Sprint**|1 mês ou menos (o "container" de todos os outros eventos)|Transformar ideias em valor|Todo o Scrum Team|
|**Sprint Planning**|8 horas|Planejar o trabalho da Sprint (o quê, por quê e como)|Todo o Scrum Team|
|**Daily Scrum**|15 minutos|Inspecionar o progresso rumo à Meta da Sprint e adaptar o Sprint Backlog|Developers (PO/SM participam se estiverem trabalhando em itens)|
|**Sprint Review**|4 horas|Inspecionar o resultado (Incremento) e adaptar o Product Backlog|Scrum Team + stakeholders|
|**Sprint Retrospective**|3 horas|Planejar formas de aumentar qualidade e eficácia|Todo o Scrum Team|

> Time-boxes acima valem para Sprints de **1 mês**; para Sprints mais curtas, o evento costuma ser **menor**, mas ainda time-boxed.

#### Sprint

- Coração do Scrum: um evento de **duração fixa de 1 mês ou menos** que contém todos os demais eventos.
- Durante a Sprint:
    - **Nenhuma mudança é feita que possa colocar em risco a Meta da Sprint**;
    - A qualidade não diminui;
    - O Product Backlog é refinado conforme necessário;
    - O escopo pode ser esclarecido e renegociado com o PO à medida que se aprende mais.
- Uma nova Sprint começa imediatamente após a conclusão da anterior.
- **Sprints mais curtas** podem ser usadas para gerar mais ciclos de aprendizado e limitar riscos de custo/esforço a um período menor.
- **Cancelamento de Sprint**: só o **Product Owner** tem autoridade para cancelar uma Sprint (mesmo que a decisão possa ser influenciada por stakeholders, SM ou Developers).

#### Sprint Planning

- Inicia a Sprint, definindo o trabalho a ser realizado.
- Aborda três tópicos:
    1. **Por que essa Sprint é valiosa?** (PO propõe como o produto pode aumentar valor; time define a **Meta da Sprint**)
    2. **O que pode ser feito nesta Sprint?** (Developers selecionam itens do Product Backlog)
    3. **Como o trabalho será realizado?** (Developers planejam o trabalho item a item)
- A **Meta da Sprint** deve estar finalizada até o fim do Sprint Planning.

#### Daily Scrum

- Evento de **15 minutos** para os **Developers**.
- Ocorre **todos os dias úteis da Sprint, mesmo horário e local**, para reduzir complexidade.
- Foca no **progresso rumo à Meta da Sprint** e produz um plano acionável para o próximo dia.
- 🚫 **Pegadinha:** o Guia 2020 **removeu** o antigo formato fixo das "3 perguntas" (o que fiz ontem / o que farei hoje / há impedimentos?) como estrutura obrigatória — hoje o time escolhe a estrutura, desde que o foco seja a Meta da Sprint. Ainda assim, é comum a banca cobrar essas 3 perguntas como _prática usual_.
- Não é a única ocasião em que os Developers podem ajustar o plano.

#### Sprint Review

- Realizada ao final da Sprint para **inspecionar o resultado** e determinar adaptações futuras.
- Scrum Team apresenta os resultados do trabalho aos stakeholders-chave; **o progresso rumo à Meta do Produto é discutido**.
- **Não é apenas uma demonstração** — é uma sessão de trabalho colaborativa.

#### Sprint Retrospective

- Encerra a Sprint; tem como objetivo **planejar formas de aumentar a qualidade e a eficácia**.
- O Scrum Team inspeciona como foi a última Sprint em relação a indivíduos, interações, processos, ferramentas e Definição de Pronto.
- Identifica os ajustes mais úteis para melhorar sua eficácia; os mais impactantes podem ser adicionados ao Sprint Backlog seguinte.
- Conclui o ciclo (Sprint) formalmente.

### 1.6 Artefatos e Compromissos (Commitments)

Cada artefato do Scrum possui um **compromisso**, que serve para reforçar transparência e foco:

|Artefato|O que representa|Compromisso (Commitment)|
|---|---|---|
|**Product Backlog**|Lista emergente e **ordenada** de tudo que é necessário para melhorar o produto|**Meta do Produto (Product Goal)**|
|**Sprint Backlog**|Conjunto de itens do Product Backlog selecionados + plano para entregar o Incremento + Meta da Sprint|**Meta da Sprint (Sprint Goal)**|
|**Incremento**|Um "degrau" concreto rumo à Meta do Produto; deve ser **inspecionável** e cumprir a Definição de Pronto|**Definição de Pronto (Definition of Done)**|

> 🔑 **Muito cobrado em prova:** essa associação artefato → compromisso, incluindo pegadinhas que trocam a ordem (ex.: dizer que o Product Backlog tem como compromisso a Definição de Pronto).

#### Product Backlog

- Lista **emergente, ordenada e dinâmica** de tudo que é conhecido ser necessário no produto.
- É a **única fonte de trabalho** do Scrum Team.
- Itens de maior prioridade costumam estar mais **refinados e detalhados** (claros, específicos) que os de menor prioridade.
- O **refinamento (refinement)** é o ato de detalhar, estimar e ordenar itens continuamente — não é um evento formal com time-box fixo, é uma atividade contínua.
- **Meta do Produto (Product Goal)** – introduzida no Guia 2020: descreve um **estado futuro do produto** que serve como alvo de longo prazo para o Scrum Team planejar.

#### Sprint Backlog

- Composto por: **Meta da Sprint** (por quê) + **itens do Product Backlog selecionados** (o quê) + **plano de entrega** (como).
- É um **plano em tempo real**, altamente visível, atualizado durante toda a Sprint pelos Developers.

#### Incremento

- É uma **soma concreta e cumulativa** de todos os itens do Product Backlog completados durante uma Sprint e o valor dos incrementos de todas as Sprints anteriores.
- Deve estar em **condição utilizável**, independentemente de o PO decidir liberá-lo.
- **Múltiplos Incrementos** podem ser criados dentro de uma Sprint.
- Um item só é considerado parte de um Incremento se atender à **Definição de Pronto**.

#### Definição de Pronto (Definition of Done - DoD)

- Descrição **formal do estado do Incremento** quando atende às medidas de qualidade exigidas.
- No momento em que um item do Product Backlog atende à DoD, **nasce um Incremento**.
- Cria transparência ao dar a todos entendimento comum do que significa "trabalho concluído".
- Se não existir uma DoD organizacional, o **Scrum Team deve criar** uma apropriada ao produto.

### 1.7 Comparativo Guia 2017 x Guia 2020 (útil para entender o "porquê" das pegadinhas)

|Aspecto|Guia 2017|Guia 2020|
|---|---|---|
|Estrutura de papéis|PO, Scrum Master e **Time de Desenvolvimento** (subgrupo à parte)|**Um único Scrum Team**: SM, PO e **Developers** (sem subtime)|
|Autonomia|"Autoorganizável"|**"Autogerenciável" (self-managing)** — decide quem, como e o quê|
|Meta de longo prazo|Não existia formalmente|Introduz a **Meta do Produto (Product Goal)**|
|Daily Scrum|3 perguntas sugeridas como formato|Formato livre, desde que focado na Meta da Sprint|
|Linguagem|Mais prescritiva|Mais enxuta, menos prescritiva ("minimamente viável e suficiente")|

### 1.8 Outros pontos que a CESGRANRIO gosta de testar

- **Sprint 0**: 🟡 **NÃO É PARTE OFICIAL DO GUIA DO SCRUM** — é uma prática de mercado usada para preparação inicial (ambiente, backlog inicial). Bancas às vezes cobram isso como pegadinha para você negar que existe oficialmente.
- **Story Points / Planning Poker**: técnicas de estimativa comuns na prática ágil, mas **não fazem parte do Guia do Scrum** — são complementares. 🟡 Cobrança pontual, verifique o edital.
- **Velocity (Velocidade)**: métrica de quantidade de trabalho concluído por Sprint; também **não está no Guia do Scrum**, é prática de mercado. 🟡
- Scrum é aplicável a "trabalho complexo" em geral, não somente a desenvolvimento de software.

---

## 2. KANBAN

### 2.1 Origem e definição

Kanban é um **método de gestão visual do fluxo de trabalho**, originado no **Sistema Toyota de Produção** (manufatura enxuta / _lean_), depois adaptado para desenvolvimento de software e trabalho de conhecimento. Ao contrário do Scrum, o Kanban **não define papéis fixos nem eventos obrigatórios com time-box** — é um método de **mudança evolutiva**, aplicado _sobre_ o processo já existente do time, sem exigir reestruturação abrupta.

### 2.2 Princípios fundamentais do Kanban

1. **Comece com o que você faz agora** – não é necessário redesenhar o processo do zero; parte-se do fluxo atual.
2. **Concorde em buscar mudança evolutiva e incremental** – mudanças pequenas e contínuas, não uma reestruturação radical.
3. **Respeite o processo atual, papéis, responsabilidades e cargos** – não se elimina nada de imediato; adapta-se aos poucos.
4. **Incentive a liderança em todos os níveis** – melhoria contínua não é responsabilidade só da gerência.

### 2.3 Práticas centrais (as mais cobradas em prova)

1. **Visualizar o fluxo de trabalho** – usar um **quadro Kanban** com colunas representando etapas do processo (ex.: A Fazer, Em Progresso, Concluído) e **cartões** representando itens de trabalho.
2. **Limitar o Trabalho em Progresso (WIP – Work In Progress)** – definir um número máximo de itens permitidos em cada etapa (coluna) do fluxo simultaneamente.
3. **Gerenciar o fluxo (Manage Flow)** – monitorar e otimizar a movimentação dos itens de trabalho, identificando gargalos.
4. **Tornar as políticas do processo explícitas** – as regras de cada etapa (critérios para mover um cartão) devem ser claras para todos.
5. **Implementar ciclos de feedback** – reuniões/revisões periódicas para avaliar o funcionamento do fluxo.
6. **Melhorar colaborativamente, evoluir experimentalmente** – usando modelos e método científico para guiar a melhoria contínua.

### 2.4 Conceito de WIP Limit (Work In Progress)

- É o **número máximo de itens de trabalho permitido em uma etapa (coluna)** do fluxo ao mesmo tempo.
- **Objetivo:** evitar sobrecarga da equipe, reduzir troca de contexto (_multitasking_), expor gargalos e aumentar a eficiência/previsibilidade da entrega.
- Quando uma coluna atinge seu limite, o time deve **"suar a camisa" (swarm)** para concluir aquele trabalho antes de puxar um novo item.
- 🚫 **Pegadinha:** o WIP Limit é uma prática do **Kanban**, não do Scrum. O Scrum controla a quantidade de trabalho por meio da **Sprint Planning** (seleção do que cabe na Sprint), não por limites de WIP explícitos em colunas.

### 2.5 Sistema Puxado (Pull System)

- No Kanban, o trabalho é **"puxado"** pela equipe conforme a capacidade se torna disponível — diferente de um sistema "empurrado" (_push_), em que o trabalho é atribuído independentemente da capacidade atual.
- Isso está diretamente ligado ao limite de WIP: só se puxa um novo item quando há espaço na etapa seguinte.

### 2.6 Quadro Kanban (Kanban Board)

- Ferramenta visual (física ou digital) dividida em **colunas** (etapas do fluxo).
- **Cartões (cards)** representam os itens de trabalho e se movem da esquerda para a direita conforme avançam.
- Cada coluna pode ter seu **próprio limite de WIP**.

### 2.7 Métricas do Kanban 🟡 (cobrança mais rara, mas pode aparecer em provas de nível analista)

- **Lead Time**: tempo total desde a solicitação/entrada do item no sistema até sua conclusão.
- **Cycle Time**: tempo que um item leva **em processamento ativo**, geralmente do início do trabalho até a conclusão (um subconjunto do Lead Time).
- **Diagrama de Fluxo Cumulativo (Cumulative Flow Diagram – CFD)**: gráfico que mostra a quantidade de itens em cada etapa do fluxo ao longo do tempo, usado para identificar **gargalos** (bandas que se alargam indicam acúmulo de trabalho).
- **Lei de Little (Little's Law)** 🟡 **NUNCA VI EM PROVA CESGRANRIO** — fórmula da teoria das filas (Lead Time = WIP / Taxa de Vazão) usada para explicar por que limitar o WIP reduz o tempo de entrega. Avalie se o edital pede "métricas Lean/Kanban avançadas" antes de estudar a fundo.
- **Classes de Serviço (Classes of Service)** 🟡 **RARO EM CONCURSO** — categorização de itens por urgência (ex.: Expedite, Fixed Date, Standard, Intangível), conceito do livro de David J. Anderson. Avalie a relevância pelo edital.

### 2.8 Scrumban 🟡 (raramente cobrado, mas pode aparecer como "pegadinha de nome")

Combinação híbrida de práticas do Scrum (cerimônias, papéis) com o fluxo contínuo e limites de WIP do Kanban. Se aparecer, normalmente é só para você **reconhecer o nome** e saber que é uma abordagem híbrida — dificilmente cai um detalhamento profundo.

---

## 3. Scrum x Kanban — Quadro Comparativo (muito cobrado)

|Critério|Scrum|Kanban|
|---|---|---|
|Natureza|**Framework prescritivo**, com papéis, eventos e artefatos definidos|Método **mais flexível e menos prescritivo**, aplicado sobre o processo existente|
|Cadência|**Iterações fixas (Sprints)**, geralmente 1 a 4 semanas, com objetivos e entrega ao final|**Fluxo contínuo**, sem iterações fixas — entrega assim que o item fica pronto|
|Papéis|Papéis definidos: **Product Owner, Scrum Master, Developers**|**Não define papéis específicos** — pode ser usado com os papéis já existentes da equipe|
|Controle de quantidade de trabalho|Por meio da **Sprint Planning** (o time se compromete com um conjunto de itens por Sprint)|Por meio do **limite de WIP** em cada etapa do fluxo|
|Mudanças durante o ciclo|**Não se muda o escopo comprometido durante a Sprint** (para não pôr em risco a Meta da Sprint)|**Mudanças podem ser inseridas a qualquer momento**, desde que respeitem o limite de WIP|
|Eventos/cerimônias|Sim: Sprint Planning, Daily Scrum, Sprint Review, Sprint Retrospective|Não exige eventos formais, mas pode incorporar reuniões de fluxo/feedback|
|Métricas típicas|**Velocity** (prática de mercado, não oficial do Guia)|**Lead Time, Cycle Time, Cumulative Flow Diagram**|
|Quadro visual|Sprint Backlog pode ser visualizado em quadro, mas não é obrigatório nem central ao framework|**Quadro Kanban é elemento central** do método|
|Ambos têm em comum|Ambos são **abordagens ágeis/lean**, valorizam **transparência**, **entrega contínua de valor**, **colaboração da equipe** e **melhoria contínua**||

---

## 4. Checklist rápido de revisão (véspera de prova)

- [ ] Sei os **5 valores do Scrum** de cor.
- [ ] Sei os **3 pilares do empirismo** e o que cada um significa.
- [ ] Sei que o **Scrum Team = 1 único time** (SM + PO + Developers), sem subtimes — terminologia **2020**.
- [ ] Sei as responsabilidades específicas do **PO** e do **Scrum Master**, e sei diferenciá-las de um "gerente de projetos".
- [ ] Sei os **5 eventos** e seus time-boxes máximos (para Sprint de 1 mês).
- [ ] Sei os **3 artefatos** e seus respectivos **compromissos** (Product Goal, Sprint Goal, Definition of Done).
- [ ] Sei que **Sprint 0, Velocity e Story Points não são oficiais do Guia do Scrum**.
- [ ] Sei os **4 princípios** e as **6 práticas** centrais do Kanban.
- [ ] Sei explicar **WIP Limit** e por que ele existe.
- [ ] Sei montar o **quadro comparativo Scrum x Kanban** de memória.

---

## 5. Referências

- **Schwaber, K.; Sutherland, J.** _The 2020 Scrum Guide_ / _O Guia do Scrum_. Disponível em: https://scrumguides.org/scrum-guide.html e versão em português: https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Portuguese-BR.pdf
- **Scrum.org.** _Scrum Guide Revisions_ (histórico das mudanças entre versões 2017 e 2020). Disponível em: https://scrumguides.org/revisions.html
- **Atlassian.** _4 kanban principles_. Disponível em: https://www.atlassian.com/agile/project-management/kanban-principles
- **Atlassian.** _What is a kanban board?_. Disponível em: https://www.atlassian.com/agile/kanban/boards
- **Atlassian.** _Kanban vs Scrum_. Disponível em: https://www.atlassian.com/agile/kanban/kanban-vs-scrum
- **Atlassian.** _What is Kanban in Project Management?_. Disponível em: https://www.atlassian.com/agile/kanban
- **Atlassian.** _Working with WIP limits for kanban_. Disponível em: https://www.atlassian.com/agile/kanban/wip-limits
- **Anderson, David J.** _Kanban: Successful Evolutionary Change for Your Technology Business_ (obra de referência que originou os princípios e práticas centrais do Kanban Method — recomendado para aprofundamento, especialmente se o edital citar "Método Kanban" nominalmente).
- Questões e enunciados reais da CESGRANRIO usados apenas como **amostra de estilo/padrão de cobrança** (não como fonte teórica): provas AGERIO (2023) e Caixa Econômica Federal (Técnico Bancário – TI), consultadas via bancos de questões públicos.

---

_Dica final: a CESGRANRIO tende a cobrar a "letra do Guia" do Scrum quase literalmente. Releia o Guia oficial (é curto, ~13 páginas) pelo menos duas vezes antes da prova — é a fonte primária mais rentável nessa disciplina._