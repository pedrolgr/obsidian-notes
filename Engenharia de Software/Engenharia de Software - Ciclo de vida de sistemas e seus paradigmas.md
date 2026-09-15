## 1. Conceitos Fundamentais (a base que a banca sempre testa primeiro)

| Termo | Definição | Pegadinha comum |
|---|---|---|
| **Engenharia de Software** | Aplicação de uma abordagem sistemática, disciplinada e quantificável ao desenvolvimento, operação e manutenção de software (definição IEEE). | Confundir com "programação" — Engenharia de Software é mais amplo, envolve gestão, qualidade, processo. |
| **Processo de Software** | Conjunto estruturado de atividades necessárias para desenvolver um sistema (Sommerville). Atividades fundamentais: **Especificação, Projeto (Design), Implementação, Validação, Evolução**. | A banca troca "Especificação" por "Levantamento" ou omite "Evolução". |
| **Modelo de Processo (ou Modelo de Ciclo de Vida)** | Representação abstrata/simplificada de um processo de software, descrita de um ponto de vista específico. | Modelo de processo ≠ Metodologia. Metodologia é a aplicação prática/detalhada de um modelo (ex.: Scrum é uma metodologia/framework ágil, RUP é um processo). |
| **Ciclo de Vida do Software** | Conjunto de fases pelas quais um software passa, do nascimento (concepção) até a aposentadoria (descontinuação), incluindo requisitos, projeto, construção, testes, implantação, manutenção e retirada. | Confundir "ciclo de vida" (fases do produto) com "modelo de processo" (como as atividades se organizam no tempo) — são conceitos próximos, mas a banca cobra a diferenciação. |

> **Dica de prova:** CESGRANRIO costuma cobrar a **atividade genérica de um processo de software** segundo Sommerville: *Especificação → Desenvolvimento (Projeto+Implementação) → Validação → Evolução*. E segundo Pressman, a estrutura de processo genérica é: **Comunicação → Planejamento → Modelagem → Construção → Implantação (Entrega)**.

---

## 2. Modelos de Processo — Visão Geral (Pressman)

Pressman classifica os modelos de processo de software (também chamados de **paradigmas de engenharia de software**) em:

```
MODELOS DE PROCESSO
│
├── 1) MODELOS PRESCRITIVOS (tradicionais/planejados)
│     ├── Modelo Cascata (Waterfall)
│     ├── Modelos Incrementais
│     │     ├── Modelo Incremental
│     │     └── Modelo RAD
│     ├── Modelos Evolucionários
│     │     ├── Prototipação
│     │     └── Modelo Espiral
│     └── Modelos Concorrentes
│
├── 2) MODELOS ESPECIALIZADOS
│     ├── Desenvolvimento Baseado em Componentes
│     ├── Métodos Formais
│     └── Desenvolvimento Orientado a Aspectos
│
└── 3) MODELOS ÁGEIS (Manifesto Ágil, 2001)
      ├── XP (Extreme Programming)
      ├── Scrum
      ├── Kanban
      ├── FDD, DSDM, Crystal, ASD etc.
```

> **Muito cobrado (CESGRANRIO/FCC/IESES já usaram literalmente esta alternativa):**
> *"São modelos de processos prescritivos: Modelo Cascata, Modelo Incremental, Modelos Evolucionários e Modelos Concorrentes."*

---

## 3. Modelo Cascata (Waterfall / Ciclo de Vida Clássico)

**O primeiro modelo publicado de processo de software.** Também chamado de "modelo clássico" ou "sequencial linear".

### Características
- Abordagem **sistemática e sequencial**: uma fase só começa quando a anterior termina (idealmente com aprovação e documentação completa).
- Não há retorno formal a fases anteriores (na versão pura); overlaps causam retrabalho.
- Fluxo de trabalho **linear**.

### Fases — atenção às duas nomenclaturas cobradas em prova

**Segundo Pressman** (5 fases):
1. Comunicação
2. Planejamento
3. Modelagem (Análise + Projeto)
4. Construção (Código + Teste)
5. Implantação/Entrega (Emprego)

**Segundo Sommerville** (5 fases "clássicas"):
1. Análise e definição de requisitos
2. Projeto de sistema e de software
3. Implementação e teste unitário
4. Integração e teste de sistema
5. Operação e manutenção

### Vantagens
- Fácil de entender e gerenciar; bom para requisitos estáveis e bem compreendidos.
- Impôs pela primeira vez disciplina e planejamento ao processo de software.
- Boa rastreabilidade de documentação (útil em contratos, órgãos públicos).

### Desvantagens
- Modelo **rígido**: difícil acomodar mudanças de requisitos.
- Cliente só vê o produto funcionando no final — risco alto.
- Problemas descobertos tarde (na fase de testes) são caros de corrigir.
- Não reflete a realidade iterativa da maioria dos projetos.

### Modelo em V (V-Model)
- Variação do modelo cascata com **foco em verificação e validação (V&V)**.
- Cada fase de desenvolvimento (esquerda do "V") é associada a uma fase de teste correspondente (direita do "V"):
  - Requisitos ↔ Testes de aceitação
  - Projeto do sistema ↔ Testes de sistema
  - Projeto detalhado ↔ Testes de integração
  - Codificação ↔ Testes unitários
- Reforça o planejamento de testes desde o início do projeto.

---

## 4. Modelos Incrementais

![[Pasted image 20260909213656.png|422]]

### 4.1 Modelo Incremental (puro)
- Combina elementos de fluxos **lineares e paralelos**.
- O sistema é dividido em **incrementos** (builds), cada um agregando um conjunto de funcionalidades.
- Cada incremento passa por um ciclo similar ao cascata (requisitos → projeto → codificação → teste) em pequena escala.
- Requisitos são **priorizados**; os de maior prioridade entram nos primeiros incrementos.

**Vantagens (muito cobradas):**
- Reduz o custo de acomodar mudanças de requisitos (comparado ao cascata).
- Feedback mais rápido do cliente.
- Entrega de valor mais cedo (funcionalidades já utilizáveis antes do fim do projeto).
- Menor risco geral do projeto (problemas aparecem cedo).

**Desvantagens:**
- Necessidade de uma arquitetura bem definida desde o início (senão, cada incremento pode comprometer a estrutura).
- Difícil aplicar contratos com preço fechado tradicional.

### 4.2 Modelo RAD (Rapid Application Development)

![[Pasted image 20260909221330.png|369]]

- Versão "em alta velocidade" do modelo incremental.
- Cada componente/incremento pode ser desenvolvido por **equipes diferentes em paralelo**, com forte uso de reuso de componentes e ferramentas CASE.
- Ciclo de desenvolvimento curto (60 a 90 dias, segundo Pressman).
- **Só funciona bem** quando: o sistema pode ser modularizado, há recursos/equipes suficientes, e os requisitos são bem compreendidos (baixo risco técnico).
- **Não é adequado** para projetos com forte necessidade de adaptação técnica ou com risco técnico elevado.

---

## 5. Modelos Evolucionários

Modelos **iterativos** que permitem desenvolver versões cada vez mais completas do software.

### 5.1 Prototipação (Prototyping)
- Usada quando os **requisitos não estão claros** ou o cliente tem dificuldade de especificar suas necessidades detalhadamente.
- Fluxo: Comunicação → Plano rápido → Modelagem/projeto rápido → Construção do protótipo → Entrega e feedback do cliente → (repete).

**Dois tipos (cobrado em prova!):**

| Tipo | Descrição |
| --- | --- |
| **Prototipação Descartável (throwaway)** | O protótipo serve só para esclarecer requisitos; depois é **jogado fora** e o sistema real é construído do zero. |
| **Prototipação Evolucionária** | O protótipo é **refinado sucessivamente** até se tornar o próprio sistema final. |

> **Questão CESGRANRIO (ELETROBRAS/2010)** cobrou justamente que a Prototipação Evolucionária **não deve ser usada** quando os requisitos mudam MUITO rapidamente e o cliente não aceita fechar um conjunto de requisitos — nesse caso, o protótipo nunca converge para uma solução estável.

**Riscos:** o cliente pode achar que o protótipo (muitas vezes malfeito internamente/"gambiarra") já é o produto final, gerando expectativa equivocada quanto a prazo e qualidade.

### 5.2 Modelo Espiral (Boehm, 1986/1988)
- Proposto por **Barry Boehm**.
- Combina a natureza **iterativa da prototipação** com os aspectos **sistemáticos e controlados do modelo cascata**.
- Ideal para sistemas grandes, complexos e de alto risco.
- Cada volta (loop) da espiral representa uma fase do processo, e o raio da espiral representa o custo acumulado do projeto.

**As 4 regiões/atividades de cada ciclo (loop):**
1. **Comunicação com o cliente / Definição de objetivos**
2. **Análise de risco** (o "carro-chefe" do modelo — atividade explícita de gerência de riscos)
3. **Engenharia** (desenvolvimento/construção — pode empregar cascata, incremental etc. dentro de cada loop)
4. **Avaliação do cliente / Planejamento do próximo ciclo**

**Ponto-chave de prova:** o Modelo Espiral é o modelo que **explicitamente incorpora gerenciamento de risco** como atividade central — diferencial em relação a todos os demais modelos prescritivos.

**Vantagens:** flexível, reduz riscos progressivamente, adequado a requisitos incertos.
**Desvantagens:** difícil de gerenciar, requer expertise em análise de risco, pode ser caro e demorado se mal aplicado.

### 5.3 Modelos Concorrentes (Concurrent Development)
- Representa o processo como uma **rede de atividades** em paralelo, e não como sequência de fases.
- Cada atividade (ex.: modelagem, construção) pode estar simultaneamente em diferentes estados: *em desenvolvimento, esperando mudanças, em revisão, sob controle de versão, concluída* etc.
- Muito usado em desenvolvimento com **várias equipes trabalhando em paralelo**.

---

## 6. Modelos Especializados

- **Desenvolvimento Baseado em Componentes (CBSE):** o sistema é construído a partir de componentes de software já existentes/reutilizáveis.
- **Métodos Formais:** uso de notações matemáticas rigorosas para especificar, desenvolver e verificar sistemas (alto rigor, comum em sistemas críticos — aviação, sistemas embarcados de segurança).
- **Desenvolvimento Orientado a Aspectos (AOSD):** modulariza "aspectos" (características transversais como log, segurança) que cortam múltiplos módulos do sistema.

---

## 7. Processo Unificado / RUP (Rational Unified Process) — TEMA CAMPEÃO NA CESGRANRIO

O RUP é citado repetidamente em provas CESGRANRIO (Petrobras, Transpetro, IBGE, Caixa). É um **processo híbrido**, combinando características de modelos incremental, iterativo e evolucionário, sendo **dirigido a casos de uso, centrado na arquitetura e iterativo/incremental**.

### As 4 Fases (organizadas no tempo — "eixo horizontal")
| Fase                          | principal                                                                                                                                                                     |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Concepção (Inception)**     | Definir escopo, viabilidade do projeto, identificar riscos principais, elaborar visão inicial (documento de visão).                                                           |
| **Elaboração (Elaboration)**  | Detalhar requisitos, **definir a arquitetura estável do sistema** (Baseline Arquitetural), construir protótipo executável da arquitetura, mitigar riscos técnicos principais. |
| **Construção (Construction)** | Desenvolver iterativamente todos os componentes/funcionalidades restantes; sistema evolui até estar pronto para os usuários (versão beta).                                    |
| **Transição (Transition)**    | Colocar o sistema nas mãos dos usuários finais: testes beta, correção de defeitos, treinamento, implantação em produção.                                                      |

> **Pegadinha clássica CESGRANRIO (Petrobras/2012):** a construção de um **protótipo que demonstre viabilidade** acontece na fase de **Elaboração**, não na Concepção nem na Construção! E o **Documento de Arquitetura de Software (SAD)** deve estar pronto/estável ao final da fase de Elaboração.

### As Disciplinas (eixo vertical / "workflows")
Modelagem de Negócios, **Requisitos**, Análise e Projeto (Design), Implementação, Teste, Implantação, Gerência de Configuração e Mudanças, Gerência de Projeto, Ambiente.

> Atenção: cada disciplina **atravessa várias fases** com intensidades diferentes — por exemplo, a disciplina de Requisitos tem grande esforço na Concepção/Elaboração, mas ainda existe (residualmente) na Construção. Isso é frequentemente cobrado ("Em quais fases o workflow de requisitos é executado?" → resposta: em todas, com intensidade variável, e não apenas em uma).

### Características do RUP (citação literal já cobrada pela banca)
> *"É iterativo quanto às fases, utiliza matriz de fases por disciplinas/atividades (workflows), ilustra boas práticas na especificação e no projeto, e apoia a prototipação e a entrega incremental. Não é adequado a todos os tipos de desenvolvimento (ex.: sistemas embarcados). Representa uma abordagem de processo híbrido, combinando aspectos de outros modelos genéricos (cascata, incremental, espiral)."*

- **Dirigido por casos de uso** (use cases guiam todo o desenvolvimento).
- **Centrado na arquitetura**.
- **Iterativo e incremental**.

---
## 10. Quadro-Resumo Definitivo (revisão relâmpago antes da prova)

| Modelo | Palavra-chave / "pegada" | Quando usar |
|---|---|---|
| **Cascata** | Sequencial, linear, rígido, documentação pesada | Requisitos claros e estáveis |
| **Modelo em V** | Cascata + foco em testes/verificação | Sistemas onde qualidade/teste é crítico |
| **Incremental** | Entregas parciais e priorizadas | Requisitos parcialmente conhecidos, entrega de valor cedo |
| **RAD** | Incremental + rapidez + componentes + equipes paralelas | Projeto modularizável, baixo risco técnico, prazo curto |
| **Prototipação** | Esclarecer requisitos incertos | Cliente não sabe exatamente o que quer |
| **Espiral** | Iterativo + **gestão explícita de risco** | Projetos grandes, complexos, de alto risco |
| **Concorrente** | Rede de atividades paralelas, não sequência | Múltiplas equipes trabalhando simultaneamente |
| **RUP/UP** | Iterativo, incremental, casos de uso, centrado em arquitetura, 4 fases (Concepção, Elaboração, Construção, Transição) | Projetos médios/grandes, orientados a objetos |
| **Ágil (Scrum/XP)** | Valoriza indivíduos, software funcionando, colaboração, resposta à mudança | Requisitos mutáveis, entregas contínuas de valor |

---

## 11. Referências utilizadas

**Livros / obras clássicas (base teórica principal):**
1. PRESSMAN, Roger S. **Engenharia de Software: uma abordagem profissional**. 7ª/8ª ed. Porto Alegre: AMGH/Bookman.
2. SOMMERVILLE, Ian. **Engenharia de Software**. 9ª/10ª ed. São Paulo: Pearson. (Capítulo sobre Processos de Software).
3. BOEHM, Barry W. "A Spiral Model of Software Development and Enhancement", artigo original sobre o Modelo Espiral (1986/1988).

**Normas e documentos oficiais:**
4. Manifesto for Agile Software Development — https://agilemanifesto.org/
5. Scrum Guide (Ken Schwaber e Jeff Sutherland) — https://www.scrum.org
6. ISO/IEC 12207 — Systems and software engineering — Software life cycle processes.

**Conteúdo de apoio, questões e artigos usados na pesquisa (contexto de concurso e complementação didática):**
7. DevMedia — "Introdução aos Processos de Software e o Modelo Incremental e Evolucionário": https://www.devmedia.com.br/introducao-aos-processos-de-software-e-o-modelo-incremental-e-evolucionario/29839
8. DevMedia — "Ciclos de Vida do Software": https://www.devmedia.com.br/ciclos-de-vida-do-software/21099
9. Medium (Contexto Delimitado) — "O Modelo Incremental" e "O Modelo em Espiral de Boehm", por Ricardo Dias: https://medium.com/contexto-delimitado/
10. LinkedIn/Medium — Mauricio Lacerda, "Guia de Engenharia de Software para Concursos Públicos" (Capítulo 2 — Cascata Pressman x Sommerville).
11. IFRN — Material didático "Modelos Prescritivos de Processo" (Prof. Fábio Procópio): https://docente.ifrn.edu.br/givanaldorocha/
12. Qconcursos.com e Mapa da Prova — bancos de questões reais da banca CESGRANRIO (Petrobras, Transpetro, IBGE, ELETROBRAS, Caixa Econômica Federal) usados para identificar o padrão de cobrança da banca sobre RUP, Prototipação, Modelo Cascata e Modelos Evolucionários.
13. Grancursos Online — artigos sobre Manifesto Ágil e Scrum voltados a concursos.
14. Objective / Aliança Ágil — histórico do Manifesto Ágil: https://www.objective.com.br/insights/manifesto-agil/

---

### 💡 Dica final de quem "passa em todo concurso"
A CESGRANRIO adora **misturar diálogos entre personagens** (analista, usuário, diretor) descrevendo uma situação prática e pedir para você **identificar o modelo de processo** ou **a fase correta**. Portanto: não decore só nomes — entenda o **cenário típico** de cada modelo (ex.: "requisitos incertos + cliente indeciso" = Prototipação/Ágil; "sistema grande e arriscado" = Espiral; "entregas parciais priorizadas" = Incremental).