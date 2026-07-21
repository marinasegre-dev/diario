# Aviso de insight na tela de escrever — design

**Data:** 2026-07-20
**App:** Diário (`index.html`, arquivo único), repo `marinasegre-dev/diario`.

## Objetivo

Quando o Claude cria um insight novo (arquivo em `pessoal/insights/` do vault),
a Marina quer ser avisada dentro do app, na tela de escrever, e conseguir ler
o insight ali mesmo. Sem servidor, sem robô: o app espia a pasta de insights
com a mesma chave que já usa.

## Comportamento

1. Ao abrir a tela de escrever (e ao voltar pra ela), o app lista
   `pessoal/insights/` e compara os arquivos com uma lista de "já vistos"
   guardada só no celular (`localStorage`, por `sha` — a mesma ideia de
   impressão digital do histórico).
2. Se houver ao menos um insight ainda não visto, aparece uma tira discreta
   acima dos botões: **"você recebeu um insight, deseja ler mais? ⋯"**, no tom
   terminal, com um ponto verde (`--ok`) sinalizando novidade. Sem insight
   novo, nada aparece.
3. Tocar na tira **expande o insight inline** (na própria tela de escrever),
   renderizado com o markdown que o app já tem (título, voz dela, "análise do
   Claude"). O corpo rola por dentro (`max-height`) pra não empurrar os botões.
4. Ao abrir, o insight é marcado como **visto**. Tocar de novo recolhe e o
   aviso some; se houver outro insight novo, o app mostra o próximo (um de cada
   vez, mais recente primeiro, ordenado pelo campo `criado` do frontmatter).

## Decisões

- **Offline:** se não alcançar o GitHub, o aviso simplesmente não aparece —
  nenhum erro polui a tela de escrever.
- **Novidade = por `sha`:** editar um insight muda o `sha`, então ela é avisada
  de novo (comportamento aceitável e simples).
- **Estreia:** como o recurso é novo, nenhum insight consta como "visto"; o
  insight `a alegria mora no caminho` aparece de imediato — serve de teste.
- **Onde:** bloco novo entre a bandeja de anexos e o `footer`, dentro de
  `#escrever`. Reusa `listarPasta`, `apiGet`, `separarFrontmatter`, `mdParaHtml`.
- **Escopo:** só leitura de insight. Nada de responder, comentar ou apagar
  insight pelo app.
