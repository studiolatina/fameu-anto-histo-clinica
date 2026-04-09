# Decisões de Arquitetura de Dados
## Plataforma Digital de Integração Morfo-Clínica

**Data:** Abril/2026
**Responsável:** Julia Sprioli
**Status:** Aprovado

---

## Contexto

Durante a fase de Define do Double Diamond, após benchmark de plataformas
existentes (Kenhub, BioDigital, Primal, Complete Anatomy) e pesquisa sobre
ontologias anatômicas (FMA, SNOMED CT, TA2), foram tomadas as seguintes
decisões de arquitetura para o banco de dados da plataforma.

O objetivo central foi definir uma estrutura que:
- Funcione bem para a Etapa 1 (morfologia) sem impedir as etapas seguintes
- Seja editável por membros não-técnicos da equipe
- Seja versionável no Git com rastreabilidade de autoria
- Escale de JSON → SQLite → PostgreSQL conforme o projeto cresce

---

## Decisão 1 — Estratégia de identificação de entidades

### Decisão
Cada estrutura anatômica terá **três identificadores com papéis distintos:**

| Identificador | Tipo | Uso | Exemplo |
|--------------|------|-----|---------|
| `id` | UUID v4 | Interno — relações entre entidades | `a3f2c1d4-7e8b-...` |
| `slug` | String latim kebab-case | Público — URLs e arquivos JSON | `os-frontale` |
| `ta2_id` | Integer | Metadado científico — ancoragem na TA2 | `1121` |

### Justificativa
- **UUID interno:** IDs baseados em significado (nome, slug) parecem intuitivos
  mas se tornam frágeis quando nomenclaturas mudam ou erros são corrigidos.
  O UUID garante que renomear uma estrutura nunca quebre referências no banco.
- **Slug público:** Legível, amigável em URLs, editável no VSCode, compreensível
  por membros não-técnicos da equipe. Ex.: `os-frontale`, `foramen-magnum`.
- **TA2 ID como metadado:** Ancora o projeto na Terminologia Anatômica
  Internacional (TA2, FIPAT/IFAA 2019) sem criar dependência operacional.
  Adicionado como campo opcional — nem toda estrutura terá TA2 ID na Etapa 1.

### Alternativas descartadas
- **Slug como ID primário:** Frágil a mudanças de nomenclatura
- **TA2 ID como ID primário:** Nem toda estrutura tem TA2 ID; documento não
  é uma ontologia OWL formal
- **UUID visível nas URLs:** Ilegível e não amigável para usuários e SEO

---

## Decisão 2 — Formato de armazenamento

### Decisão
**JSON por região anatômica**, um arquivo por região, armazenados na pasta
`plataforma/dados/` do repositório.
plataforma/
└── dados/
├── neurocrânio.json
├── viscerocrânio.json
├── coluna-vertebral.json
├── torax.json
├── membro-superior.json
├── membro-inferior.json
└── pelve.json

### Justificativa
- **Versionável no Git:** Cada commit registra exatamente quais estruturas
  foram adicionadas ou modificadas, com autoria e data — essencial para
  comprovação de trabalho acadêmico
- **Editável sem código:** A Amanda pode editar os JSONs diretamente no VSCode
  sem precisar interagir com banco de dados
- **Granularidade adequada:** Um JSON por região evita um arquivo gigante
  ilegível e permite que cada membro da equipe trabalhe numa região sem
  conflito de merge no Git
- **Migração trivial:** Quando a plataforma precisar de consultas complexas,
  a migração JSON → SQLite → PostgreSQL é direta — o schema já estará definido

### Plano de escalabilidade
Etapa 1–2 (agora)     → JSON por região no repositório Git
Etapa 3–4 (plataforma online) → SQLite para consultas e filtros locais
Etapa 5 (produção)    → PostgreSQL com múltiplos usuários simultâneos
### Alternativas descartadas
- **Um JSON único:** Ilegível, conflitos de merge frequentes, difícil de editar
- **SQLite agora:** Desnecessário para o volume atual; exige ferramentas
  específicas para edição; não editável por membros não-técnicos
- **Dados embutidos no HTML:** Já validado no protótipo v1; não escala para
  260+ estruturas

---

## Decisão 3 — Schema da entidade anatômica

### Schema completo comentado

```json
{
  "id": "uuid-v4-gerado-automaticamente",
  "slug": "os-frontale",
  "ta2_id": 1121,

  "names": {
    "la": "Os frontale",
    "pt": "Osso frontal",
    "en": "Frontal bone"
  },

  "hierarchy": {
    "parent_id": "uuid-do-neurocrânio",
    "parent_slug": "neurocranium",
    "relation_type": "part_of"
  },

  "classification": {
    "sistema": "osteoarticular",
    "regiao": "neurocrânio",
    "tipo": "osso",
    "subtipo": null
  },

  "morphology": {
    "descricao": "Osso plano que forma a fronte e o teto das órbitas...",
    "funcao": "Proteção do lobo frontal e formação do teto orbitário",
    "partes": [
      {
        "slug": "squama-frontalis",
        "names": { "la": "Squama frontalis", "pt": "Escama frontal" },
        "tipo": "região"
      }
    ],
    "articulacoes": ["os-parietale", "os-sphenoidale", "os-ethmoidale"],
    "insercoes_musculares": []
  },

  "histology": null,

  "embryology": null,

  "clinical": null,

  "media": {
    "imagens": [],
    "matrix360_id": null
  },

  "references": [
    { "obra": "Gray's Anatomy", "edicao": "42ª ed.", "capitulo": "3" },
    { "obra": "Netter", "edicao": "7ª ed.", "prancha": "12-14" }
  ],

  "metadata": {
    "status": "rascunho",
    "revisado_por": null,
    "criado_em": "2026-04-09",
    "atualizado_em": "2026-04-09",
    "versao": 1
  }
}
```

### Campos obrigatórios na Etapa 1

| Campo | Obrigatório | Motivo |
|-------|:-----------:|--------|
| `id` | ✅ | Chave primária interna |
| `slug` | ✅ | Identificador público e URL |
| `names.la` | ✅ | Nomenclatura oficial TAI |
| `names.pt` | ✅ | Língua principal da plataforma |
| `hierarchy.parent_id` | ✅ | Posição na hierarquia anatômica |
| `hierarchy.relation_type` | ✅ | Tipo de relação com o pai |
| `classification.sistema` | ✅ | Navegação por sistema |
| `classification.regiao` | ✅ | Navegação por região |
| `classification.tipo` | ✅ | Filtros e badges na interface |
| `morphology.descricao` | ✅ | Conteúdo principal da Etapa 1 |
| `references` | ✅ | Validação científica |
| `metadata.status` | ✅ | Controle editorial |

### Campos opcionais que não quebram nas etapas seguintes

| Campo | Etapa | Descrição |
|-------|-------|-----------|
| `ta2_id` | 1+ | Adicionado conforme consulta à TA2 |
| `names.en` | 1+ | Expansão futura para inglês |
| `morphology.partes` | 1+ | Acidentes ósseos e sub-estruturas |
| `morphology.articulacoes` | 1+ | Relações com outras estruturas |
| `histology` | 2 | Caracterização dos tecidos constituintes |
| `embryology` | 3 | Origem embriológica e folheto germinativo |
| `clinical` | 4 | Condições clínicas associadas (CID-10/SNOMED) |
| `media.matrix360_id` | 1+ | Referência à imagem no banco Matrix 360 |

---

## Decisão 4 — Relações entre entidades

As relações entre estruturas seguem o modelo do FMA (Foundational Model
of Anatomy), usando os seguintes tipos:

| relation_type | Significado | Exemplo |
|---------------|-------------|---------|
| `part_of` | Parte de uma estrutura maior | Escama frontal é `part_of` osso frontal |
| `is_a` | É um tipo de | Forame magno `is_a` forame |
| `articulates_with` | Articula-se com | Frontal `articulates_with` parietal |
| `gives_attachment_to` | Inserção muscular | Mastóide `gives_attachment_to` ECM |

---

## Referências consultadas

- FIPAT/IFAA. *Terminologia Anatomica 2ª ed. (TA2)*, 2019.
  https://bearboat.net/TerminologiaAnatomicaTA2/
- Rosse & Mejino. *The Foundational Model of Anatomy Ontology*, 2007.
  https://ifaa.unifr.ch/Public/TNAEntryPage/ref/Rosse2007.pdf
- FMA SQLite (open source). https://github.com/mhalle/fma-sqlite
- SNOMED CT. *Anatomical Concept Model*.
  https://confluence.ihtsdotools.org/display/DOCEG/Anatomical+Concept+Model
- Perplexity AI. *Benchmark de arquitetura de dados anatômicos*, abril/2026