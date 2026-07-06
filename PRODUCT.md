# Product

## Register

product

## Users

Marina, e só ela. Contexto: iPhone, momentos espontâneos do dia (rua, cama, faculdade,
trabalho). Ela abre o ícone "Diário", despeja um pensamento (digitando ou ditando pelo
microfone do teclado) e fecha. O job é capturar sem fricção: zero decisões, zero
organização, zero releitura. A organização acontece depois, feita pelo Claude no vault.

## Product Purpose

Porta de entrada do diário virtual dela. A página grava a reflexão como arquivo `.md`
na pasta `caixa-de-entrada/` do repositório privado `marinasegre-dev/vault` via API do
GitHub, usando um token fino guardado só no navegador do celular. Sucesso = ela confiar
que "enviou, tá salvo" em menos de 10 segundos, todas as vezes.

## Brand Personality

Terminal preto minimalista (escolha explícita dela). Três palavras: íntimo, silencioso,
confiável. Deve parecer um lugar privado e quieto para pensar, não um app social ou
uma ferramenta de produtividade.

## Anti-references

- Apps de notas cheios de recursos (Notion, Evernote): nada de menus, listas, histórico.
- Estética "hacker verde-matrix" caricata: é terminal, mas elegante e quente, não cosplay.
- Qualquer coisa que pareça formulário corporativo (labels, bordas de input, botões azuis).

## Design Principles

1. **Uma tela, uma ação.** Abriu → digitou/ditou → enviou. Qualquer coisa além disso é ruído.
2. **A palavra dela é sagrada.** O app nunca altera, sugere ou corrige o texto.
3. **Confiança visível.** O estado de envio (enviando / salvo / erro) é sempre inequívoco, e um rascunho nunca se perde (persistido localmente até confirmar o envio).
4. **Privado por construção.** Nenhum conteúdo ou credencial no código público; token só no dispositivo dela.

## Accessibility & Inclusion

Uso noturno frequente (é um diário): preto verdadeiro, contraste de texto ≥ 4.5:1,
alvos de toque ≥ 44px, respeitar `prefers-reduced-motion`. Português em toda a interface.
