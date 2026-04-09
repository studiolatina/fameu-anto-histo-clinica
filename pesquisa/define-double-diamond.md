# Define — Double Diamond
## Plataforma Digital de Integração Morfo-Clínica

**Data:** Abril/2026
**Responsável:** Julia Sprioli
**Baseado em:** Discovery (benchmark), protótipo v1

---

## 1. Declaração do problema (Problem Statement)

> Estudantes de medicina no ciclo básico não têm acesso a um recurso
> digital gratuito, em português, que conecte anatomia macroscópica,
> histologia, embriologia e correlação clínica numa navegação contínua
> a partir da estrutura anatômica como ponto de entrada.

---

## 2. Persona principal

**Nome fictício:** Beatriz, 19 anos
**Contexto:** 1º período de medicina, ciclo básico
**Situação:** Estudando para prova prática de anatomia do neurocrânio

**Frustrações:**
- O Netter mostra a estrutura mas não explica o tecido
- O Gray's tem tudo mas é denso demais para revisão rápida
- O Kenhub é em inglês e não conecta com o que viu na peça real
- Não existe nada gratuito e em PT-BR com esse nível de integração

**O que ela precisa:**
- Partir de uma estrutura que viu na peça e encontrar tudo sobre ela
- Entender a morfologia, o tecido, a origem e a relevância clínica
- Confiar que o conteúdo é validado cientificamente

---

## 3. Decisões de design já tomadas (extraídas do protótipo v1)

### 3.1 Ponto de entrada
O usuário entra pela **estrutura anatômica**, não pelo sistema ou pela
condição clínica. Essa é a principal diferença em relação ao benchmark:
todas as outras plataformas organizam por sistema (Kenhub, Complete
Anatomy) ou por especialidade clínica (BioDigital, Primal). Aqui, a
peça anatômica é o ponto de partida.

### 3.2 Layout de 3 colunas
- **Esquerda:** navegação por estruturas do grupo atual (ex.: ossos do neurocrânio)
- **Centro:** visualização SVG interativa da região — clicável, com highlight e dimming
- **Direita:** painel de detalhes da estrutura selecionada

Essa arquitetura mantém o contexto espacial (onde está no crânio) enquanto
o usuário lê os detalhes — algo que nenhuma plataforma do benchmark faz bem.

### 3.3 Navegação vertical por tabs
O painel direito usa tabs para separar as camadas de conhecimento:
- **Morfologia** — disponível desde a Etapa 1
- **Histologia** — previsto para a Etapa 2
- **Clínica** — previsto para a Etapa 4

As etapas futuras são sinalizadas com badge "em desenvolvimento" —
decisão de transparência que evita conteúdo vazio e comunica o
roadmap ao usuário.

### 3.4 Estrutura dos dados por estrutura
Cada estrutura no banco contém:
- Nome em latim (Terminologia Anatômica Internacional)
- Nome em português
- Tipo (osso, processo, forame, região)
- Sistema e região de pertencimento
- Descrição morfológica
- Partes e acidentes ósseos relacionados
- Articulações ou inserções musculares
- Referências bibliográficas (Gray's, Netter)
- Status de revisão (rascunho / revisado)

### 3.5 Codificação visual por cor
Cada estrutura tem uma cor própria, usada consistentemente na
navegação lateral, no SVG e nos badges — criando uma linguagem visual
coerente que ajuda na orientação espacial.

---

## 4. Requisitos funcionais — Etapa 1

### Must have (essencial para o MVP)
- [ ] Navegação lateral por estruturas do grupo selecionado
- [ ] Visualização SVG interativa com highlight e dimming ao selecionar
- [ ] Painel de detalhes com nome em latim, português, tipo e região
- [ ] Descrição morfológica validada
- [ ] Partes e acidentes ósseos relacionados
- [ ] Articulações e inserções musculares
- [ ] Referências bibliográficas por estrutura
- [ ] Status de revisão visível (rascunho / revisado)
- [ ] Tabs sinalizando etapas futuras (Histologia, Clínica)
- [ ] Cobertura completa: neurocrânio, viscerocrânio, coluna, tórax, membro superior, membro inferior, pelve

### Should have (importante mas não bloqueante)
- [ ] Busca por nome em português ou latim
- [ ] Filtro por status de revisão
- [ ] Indicador de progresso da catalogação por região

### Won't have na Etapa 1
- Conteúdo histológico (Etapa 2)
- Conteúdo embriológico (Etapa 3)
- Correlações clínicas (Etapa 4)
- Modelos 3D (fora do escopo — foco em imagens reais do Matrix 360)
- Sistema de quiz ou avaliação

---

## 5. Requisitos não funcionais

- **Acesso:** 100% gratuito e aberto, sem cadastro
- **Idioma:** Português (PT-BR), com nomenclatura em latim
- **Dispositivo:** Web-first, responsivo para tablet e desktop
- **Performance:** Funcionar sem dependência de bibliotecas pesadas
- **Manutenção:** Estrutura de dados separada da interface (JSON ou similar)
  para facilitar adição de conteúdo sem alterar código
- **Versionamento:** Todo conteúdo e código versionado no GitHub
  com rastreabilidade de autoria por commit

---

## 6. Arquitetura de informação — hierarquia de navegação
Plataforma
└── Sistema (ex.: osteoarticular)
└── Região (ex.: neurocrânio)
└── Estrutura (ex.: osso frontal)
├── Morfologia
│   ├── Descrição
│   ├── Partes e acidentes
│   ├── Articulações / inserções
│   └── Referências
├── Histologia (Etapa 2)
├── Embriologia (Etapa 3)
└── Clínica (Etapa 4)
---

## 7. Princípios de design

1. **A estrutura como centro** — tudo parte da peça anatômica, não da doença nem do sistema
2. **Contexto espacial sempre visível** — o SVG central mantém o usuário orientado enquanto lê
3. **Transparência de progresso** — o que ainda não existe é sinalizado, não omitido
4. **Confiança científica** — cada descrição tem referência e status de revisão visíveis
5. **Gratuito e em português** — sem barreiras de acesso para estudantes brasileiros

---

## 8. Próximos passos — Etapa de Develop

- [ ] Expandir o SVG do neurocrânio para cobertura completa das 8 estruturas
- [ ] Criar SVGs para viscerocrânio e coluna vertebral
- [ ] Estruturar os dados em JSON separado da interface
- [ ] Definir protocolo de catalogação para as demais regiões
- [ ] Iniciar catalogação no Matrix 360 seguindo o protocolo