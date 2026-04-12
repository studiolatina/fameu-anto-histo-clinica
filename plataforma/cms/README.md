# Morphé CMS — Guia de Uso

Plataforma editorial para preenchimento e revisão do conteúdo anatômico do Morphé.

**Acesso:** https://studiolatina.github.io/fameu-anto-histo-clinica/plataforma/cms/

---

## Como entrar

1. Acessa o link acima pelo browser (Chrome ou Safari)
2. Cola o token de acesso no campo que aparece
3. Aperta Enter ou clica em **Entrar**

O token fica salvo no browser — você só precisa colocar uma vez por dispositivo.

---

## O que cada tela faz

### Lista de estruturas
A tela principal mostra todas as estruturas anatômicas organizadas por região.
Cada card mostra o nome, o nome em latim e o status atual:

- **Rascunho** — em preenchimento, ainda não está pronto
- **Em revisão** — conteúdo completo, aguardando validação da Profa. Barbara
- **Revisado** — validado, pronto para publicar na plataforma

O aviso verde no topo indica quantas estruturas estão prontas para deploy.

Você pode filtrar por região (sidebar esquerda) ou por status.

---

### Tela de edição
Clica em qualquer estrutura com status **rascunho** para abrir a edição.

Campos disponíveis:
- **Descrição morfológica** — texto principal sobre a estrutura
- **Função** — função fisiológica/anatômica resumida
- **Articula-se com** — slugs das estruturas relacionadas (ex: `os-parietale`). Digite e pressione Enter para adicionar
- **Inserções musculares** — músculos que se inserem na estrutura. Digite e pressione Enter para adicionar
- **Referências** — obra, edição e capítulo/prancha

Ao terminar, você tem duas opções:
- **Salvar rascunho** — salva o progresso sem enviar para revisão
- **Enviar para revisão →** — marca como pronto e envia para a Profa. Barbara revisar

---

### Tela de revisão (Profa. Barbara)
Estruturas **em revisão** abrem em modo de leitura limpo.

A Profa. Barbara lê o conteúdo e tem duas opções:
- **✓ Validar estrutura** — aprova o conteúdo, status vira **revisado**
- **← Devolver para rascunho** — devolve para Julia/Amanda corrigirem

---

## Fluxo de trabalho
Julia ou Amanda preenche o card
↓
Envia para revisão
↓
Barbara abre e lê o card
↓
Valida ou devolve
↓
Julia vê o aviso verde e faz deploy
---

## Dúvidas

Qualquer problema, fala com a Julia.