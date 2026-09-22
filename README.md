# Nova AI Solutions — página de pagamento pendente (404)

Página que substitui o site de um cliente enquanto a mensalidade está em aberto. O personagem da Nova procura o site, dá de ombros, olha o relógio e pede a regularização. A fala muda sincronizada com o vídeo, e o personagem recortado fica na frente do 404.

HTML estático puro: sem build, sem dependências.

## Estrutura

```
index.html                  página completa (CSS e JS embutidos)
404.html                    cópia do index (GitHub Pages e Netlify usam para rotas inexistentes)
assets/nova-character.mp4   vídeo usado na página: cor à esquerda, máscara à direita (1440×1280, 29,6 s, loop)
assets/nova-character-master.mp4   master colorido 720×1280 em loop, para redes sociais ou outros usos
assets/poster-plate.webp    primeiro frame (fundo), aparece antes do vídeo e como estático
assets/poster-actor.webp    primeiro frame (personagem recortado com transparência)
fonts/archivo-var.woff2     Archivo variável (largura + peso), subset latino
og.jpg                      imagem de compartilhamento 1200×630
favicon.svg
vercel.json                 manda qualquer rota para a página e define cache
_redirects                  mesma regra para Netlify
```

## Deploy na Vercel

1. Suba esta pasta para um repositório no GitHub.
2. Na Vercel: **Add New → Project**, importe o repositório.
3. Framework Preset: **Other**. Deixe build e output em branco.
4. Deploy.

Para colocar a página no ar no domínio de um cliente: no projeto da Vercel, vá em **Settings → Domains**, adicione o domínio do cliente e troque o DNS dele para a Vercel. Qualquer caminho do domínio (`/`, `/contato`, `/blog/qualquer-coisa`) abre esta página. Quando o cliente pagar, é só apontar o domínio de volta para o site dele.

A página lê o domínio sozinha: o texto vira "O site **dominiodocliente.com.br** está pausado…" e as mensagens do WhatsApp já chegam com o domínio.

## Onde mudar as coisas

- **Número do WhatsApp:** procure `5511951007967` no `index.html` (constante `WA` no script e os `href` padrão) e o número visível `(11) 95100-7967` no topo. Depois copie o `index.html` por cima do `404.html`.
- **Falas do personagem:** array `BEATS` no script. Cada linha é `[frame inicial, frame final, texto]`, a 24 fps. Os cortes seguem a ação real do vídeo:
  - 0–134: procura o site
  - 134–372: dá de ombros e baixa os braços
  - 372–470: olha o relógio
  - 470–614: se assusta, vira de costas e coça a cabeça
  - 614–710: volta de frente
- **Imagem de compartilhamento:** `og:image` está como `/og.jpg`. Alguns apps (WhatsApp, por exemplo) só mostram a prévia com URL absoluta. Se quiser a prévia, troque para `https://seudominio/og.jpg`.

## Como a página se comporta

- **Normal:** o vídeo toca mudo em loop. Um canvas desenha o fundo e outro (WebGL) desenha só o personagem, usando a máscara. Os dois saem do mesmo frame, então não há dessincronia.
- **Movimento reduzido ativado no sistema, modo economia de dados ou conexão 2G:** o vídeo nem é baixado. Fica a cena estática com a última fala.
- **Sem WebGL, erro no vídeo ou contexto gráfico perdido:** volta para a cena estática, sem quebrar o layout.
- **Autoplay bloqueado** (modo economia de bateria no iPhone): mostra a cena estática e o vídeo começa no primeiro toque.
- **Aba em segundo plano:** o vídeo pausa e volta ao reabrir.
- **SEO:** `noindex, nofollow` na página e no cabeçalho `X-Robots-Tag`, para o Google não indexar a página de cobrança no lugar do site do cliente.

## Testar localmente

Os caminhos são absolutos (`/assets/...`), então abra por um servidor, não direto pelo arquivo:

```
npx serve .
```

ou

```
python3 -m http.server 8080
```
