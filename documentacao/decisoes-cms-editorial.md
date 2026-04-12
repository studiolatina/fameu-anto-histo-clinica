# Decisões — CMS Editorial do Morphé

**Data:** Abril/2026  
**Responsável:** Julia Sprioli  
**Status:** Aprovado  

---

## Contexto

Com o banco de imagens do Gray's 1918 estabelecido e o schema de dados
definido, o próximo passo é criar uma interface editorial que permita
à equipe preencher e revisar o conteúdo das estruturas anatômicas sem
precisar editar JSONs diretamente no VSCode.

A equipe é composta por três pessoas com perfis técnicos distintos:
- **Julia Sprioli** — desenvolvedora, controle total do repositório
- **Amanda Carvalho** — redatora de conteúdo, sem experiência técnica
- **Profa. Barbara Cristina** — revisora científica, sem experiência técnica

---

## Decisão 1 — Não usar CMS externo

### Decisão
Construir um mini CMS próprio em vez de adotar plataformas como
Strapi, Sanity ou Contentful.

### Justificativa
CMSs externos resolvem problemas de escala e múltiplos usuários que
o projeto não tem. As desvantagens para o perfil do projeto são:
- Custo recorrente (planos pagos ou limites restritivos no gratuito)
- Dependência de plataforma externa (vendor lock-in)
- Complexidade de configuração e manutenção desproporcional ao tamanho da equipe
- Dados fora do repositório Git — perde rastreabilidade e autoria

### Alternativas descartadas
- **Strapi** — self-hosted mas exige servidor Node.js rodando 24h
- **Sanity** — free tier adequado mas dados ficam no Content Lake deles
- **Contentful** — pago para workflow editorial real
- **Decap CMS** — promissor mas requer configuração YAML complexa

---

## Decisão 2 — Arquitetura do CMS próprio

### Stack
| Camada | Tecnologia | Custo |
|--------|-----------|-------|
| Interface | HTML + CSS + JavaScript puro | Grátis |
| Hospedagem | GitHub Pages | Grátis |
| Dados | JSONs no repositório Git existente | Grátis |
| API | GitHub REST API v3 | Grátis (5.000 req/hora) |
| Autenticação | GitHub Personal Access Token compartilhado | Grátis |

### Como funciona
1. A interface é uma página estática hospedada no GitHub Pages
2. Ao abrir, pede o token de acesso (senha compartilhada entre as três)
3. Lê os JSONs do repositório via GitHub API
4. Amanda e Julia editam os cards → salvam como `status: em_revisao`
5. Cada save escreve direto no JSON via GitHub API — vira um commit automático
6. Barbara abre cards `em_revisao`, lê, clica em "Validar" → `status: revisado`
7. Julia vê os cards validados e faz o deploy da plataforma pública

### Vantagens para o projeto
- Zero custo — sem servidor, sem banco de dados, sem assinatura
- Zero manutenção de infraestrutura
- Todo conteúdo versionado no Git com autoria rastreável por commit
- Comprovação acadêmica automática — cada edição tem data, hora e autora
- Funciona em qualquer browser, sem instalação para Amanda e Barbara

---

## Decisão 3 — Workflow editorial

### Estados de um card
rascunho → em_revisao → revisado
| Estado | Quem define | Significado |
|--------|------------|-------------|
| `rascunho` | Julia / Amanda | Conteúdo em preenchimento, não pronto |
| `em_revisao` | Julia / Amanda | Conteúdo completo, aguarda Barbara |
| `revisado` | Barbara | Validado cientificamente, pronto para deploy |

### Perfis de uso
A interface tem três telas de trabalho acessíveis para qualquer pessoa
logada com a senha compartilhada:
- **Tela de edição** — preencher e salvar cards
- **Tela de revisão** — validar cards prontos
- **Tela de status** — ver o que está pronto para deploy

### Autenticação
Senha única compartilhada entre Julia, Amanda e Barbara — um GitHub
Personal Access Token com permissão de leitura e escrita no repositório.
Sem perfis, sem roles, sem sistema de usuários. Qualquer uma das três
pode editar, revisar ou validar qualquer card.e para equipe de 3 pessoas em projeto
acadêmico fechado.

---

## Decisão 4 — Hospedagem da plataforma pública

A plataforma pública do Morphé (não o CMS) seguirá a mesma lógica:

| Etapa | Hospedagem | Custo |
|-------|-----------|-------|
| Desenvolvimento | Local (VSCode + Live Server) | Grátis |
| Staging / Revisão | GitHub Pages | Grátis |
| Produção (futuro) | Vercel ou Netlify | Grátis (free tier) |

A progressão é linear e sem migração de dados — os JSONs ficam no
repositório em todas as etapas.

---

## Próximos passos

- [ ] Baixar banco completo de imagens do Gray's Anatomy 1918
- [ ] Rodar LaMa Cleaner em lote em todas as imagens
- [ ] Construir interface do CMS editorial (GitHub Pages)
- [ ] Configurar GitHub Personal Access Token para a equipe
- [ ] Testar workflow completo rascunho → revisão → validado