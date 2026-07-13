# Diário · gravar áudio e anexar foto — design

Data: 2026-07-13

## Objetivo

O diário (index.html, PWA de tela única) hoje só captura texto. Marina quer, de
verdade, **gravar a própria voz** (arquivo de áudio, que o Claude transcreve depois
no vault) e **anexar foto(s)**. Tudo sem quebrar o princípio "uma tela, uma ação".

## Decisão de produto (aprovada no preview)

- **Tudo junto numa entrada.** Texto + foto(s) + áudio viram UMA anotação quando ela
  aperta *enviar*. Não existe envio automático separado.
- Enviar funciona se houver **qualquer** um dos três (texto, foto ou áudio); não
  precisa ter texto.
- Estética terminal, **sem emojis**:
  - Botão de gravar = uma **bola (●)** no meio de uma caixinha; vira **quadrado (■)**
    vermelho enquanto grava.
  - Botão **foto** (palavra).
  - **enviar** (como hoje).
  - **cfg** foi para o **canto superior direito**, minúsculo e apagado (quase nunca
    usado; abre a tela da chave/token).
- Bandeja de anexos aparece acima do rodapé só quando há algo:
  - Linha de áudio estilo gravador: `● rec 0:12` (ponto vermelho piscando enquanto
    grava; verde quando pronto), com **ouvir** e **apagar**.
  - Miniaturas de foto (até **4**), cada uma com **×** para remover.

## Comportamento

### Áudio
- `getUserMedia({audio:true})` + `MediaRecorder`. Toque em ● inicia, toque em ■ para.
- Guarda o `Blob`. Cronômetro na bandeja. **ouvir** toca via `URL.createObjectURL`.
  **apagar** descarta e libera o objeto.
- Extensão pelo `mimeType` real do gravador: `audio/mp4` → `.m4a` (caso do iOS Safari);
  `audio/webm` → `.webm`. Um só áudio por entrada.
- Se o microfone for negado/indisponível: mensagem amigável, o resto (texto/foto)
  continua funcionando. Plano B permanente: ela ainda pode digitar/ditar pelo teclado
  como hoje.

### Foto
- `<input type="file" accept="image/*" multiple>` escondido, disparado pelo botão foto.
  No iPhone abre "tirar foto / escolher da galeria".
- Cada foto é **reduzida** no próprio aparelho via `<canvas>` (lado maior ≤ 1600px,
  JPEG ~0.82) antes de subir. Deixa leve e rápido no 4G. Limite 4; excedente é ignorado
  com aviso.
- Miniatura com × para remover.

### Envio (uma entrada = um carimbo de tempo)
- Base do nome: `AAAA-MM-DD-HHMMSS`.
- Sobe cada mídia para `caixa-de-entrada/` via GitHub Contents API (PUT, conteúdo em
  base64 do binário):
  - fotos: `<base>-1.jpg`, `<base>-2.jpg`, …
  - áudio: `<base>.<ext>`
- Por último sobe a nota `<base>.md` com frontmatter (data, origem: iphone) + o texto +
  links Obsidian para os anexos (`![[<base>-1.jpg]]`, link do áudio). A nota só cita
  arquivos que subiram com sucesso.
- **Ordem importa:** mídias primeiro, `.md` por último. Se alguma mídia falhar, aborta
  antes da nota, mostra erro e **mantém tudo em memória** para reenviar. Sem nota órfã.

### Confiança / estado
- Texto continua persistido em `localStorage` (rascunho nunca se perde), como hoje.
- Blobs de foto/áudio ficam em memória durante a sessão para retry; **não** sobrevivem
  ao fechar o app (evita estourar o limite de armazenamento do navegador). Aceitável:
  se fechar antes de enviar, ela refaz a foto/áudio.
- Status inequívoco: `enviando…` / `salvo ✓` / erros claros (chave inválida, sem
  internet, falha ao subir anexo). Envio desabilita o botão até terminar.
- `prefers-reduced-motion` desliga o piscar do ponto e cursor.

## Token
- **Nada muda.** O mesmo PAT de hoje (permissão de Contents no repo `vault`) já cobre
  áudio e foto, porque é o mesmo endpoint. Ela não precisa gerar chave nova.

## Fora de escopo (YAGNI)
- Múltiplos áudios por entrada.
- Editar/recortar foto ou áudio dentro do app.
- Histórico / releitura de entradas (a organização é no vault, pelo Claude).
- Persistir mídia entre fechamentos do app.

## Riscos e verificação
- **Único ponto que exige testar no aparelho dela:** gravar voz pelo navegador em PWA
  no iPhone (precisa HTTPS — ok, é GitHub Pages — e iOS recente). Se falhar, cai no
  plano B (teclado) sem perder o texto.
- Verificação: no iPhone da Marina, gravar um áudio curto + anexar 1 foto + escrever
  uma linha e enviar; confirmar que a nota `.md` e os anexos aparecem na
  `caixa-de-entrada/` do vault. Confirmar que o fluxo só-texto continua igual.
- Tamanho: fotos reduzidas ~200–400KB, áudio ~1MB/min; dentro do que a Contents API
  aceita tranquilo para um diário.
