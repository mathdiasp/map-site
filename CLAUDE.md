# MAP Alimentação para Eventos

Site institucional da MAP, consultoria e curadoria de alimentação para eventos corporativos em São Paulo (coffee break, brunch, coquetel e kit lanche). Domínio de produção: `mapalimentacao.com.br`.

## Regra de escrita

**Nunca usar travessão.** Vale para todo texto do site, posts do blog, propostas geradas, commits e respostas. No lugar dele, use vírgula, dois pontos, ponto final ou parênteses.

Errado: "A comida é o que mais aparece na pesquisa pós-evento — e o que menos recebe atenção."
Certo: "A comida é o que mais aparece na pesquisa pós-evento, e o que menos recebe atenção."

## Arquitetura

HTML estático puro. Sem framework, sem build, sem dependências de npm. Cada página é um arquivo `.html` na raiz que carrega `/styles.css`; os posts ficam em `blog/`. Para publicar, basta commitar os arquivos.

```
index.html                     Home
como-funciona.html             Processo: briefing, proposta, confirmação, entrega
coffee-break-corporativo.html  Landing de serviço
brunch-corporativo.html        Landing de serviço
coquetel-corporativo.html      Landing de serviço
kit-lanche-para-eventos.html   Landing de serviço
blog.html                      Índice do blog
blog/*.html                    Posts
styles.css                     Folha de estilo única de todo o site público
gerador.html                   Ferramenta interna (não usa styles.css)
img/foto-1..6.jpg              Fotos das páginas
sitemap.xml                    Precisa ser atualizado a cada página nova
```

## Marca

### Cores

Definidas como tokens em `:root` no topo do `styles.css`. Sempre use a variável, nunca o hex literal no CSS de componente.

| Token | Hex | Uso |
|---|---|---|
| `--green` | `#1C2E4A` | Navy, cor primária: botões, fundos escuros |
| `--green-deep` / `--pine` | `#142238` | Navy escuro, hover e seções densas |
| `--brass` | `#9A6F30` | Dourado, cor de destaque: eyebrows, detalhes |
| `--brass-lt` | `#D8B784` | Dourado claro sobre fundo escuro |
| `--ink` | `#1B1A16` | Texto |
| `--paper` | `#F6F2EA` | Fundo padrão |
| `--kraft` / `--line` | `#E7DCC8` / `#DED5C4` | Blocos de apoio e bordas |

O dourado é acento, não cor de área. Use em traços finos, eyebrows e ícones, não em fundos grandes.

### Tipografia

- **Títulos** (`h1`, `h2`, `h3`): DM Serif Display, via `--display`.
- **Corpo e interface**: Questrial, via `--body`. Inclui botões, labels e a classe `.eyebrow`.

As duas famílias vêm do Google Fonts, carregadas no `<head>` de cada página. Ao criar uma página nova, copie o bloco de `<link>` de uma existente.

## Convenções de página

Cada landing e post traz JSON-LD próprio. As landings usam `Service` e `FAQPage`; os posts usam `BlogPosting`; a home traz `Organization`, `OfferCatalog` e `AggregateRating`. Ao criar uma página, replique o padrão da página irmã mais próxima, inclua `<title>`, `<meta name="description">`, Open Graph apontando para `/og-cover.jpg`, e adicione a URL ao `sitemap.xml`.

Os CTAs levam ao WhatsApp (`https://wa.me/5511950827474`) ou à âncora `/#contato` da home.

## gerador.html

Ferramenta interna, chamada "MAP · Central de Propostas". É um app single-file de 679 KB com três telas: gerador de propostas (`view-gerador`), CRM e funil de leads (`view-crm`) e relatórios (`view-rel`).

Pontos a respeitar ao mexer nele:

- **Acesso protegido por login Supabase** (email e senha, `grant_type=password`). O token fica em `localStorage` sob `map_sb_token`. Sem token, `cloudOn()` é falso e a nuvem fica bloqueada.
- Dados em Supabase nas tabelas `map_propostas` e `map_leads`. A proteção real depende de RLS no banco, já que a anon key é pública por natureza.
- Não é linkado de nenhuma página pública e não usa o `styles.css` do site. Tem tipografia própria (Poiret One, Questrial, Inter). Não aplique aqui as mudanças de estilo do site público.
- A linha 246 é um array `PHOTOS` com imagens em base64 e sozinha responde por 576 KB do arquivo. Evite abrir o arquivo inteiro sem necessidade e nunca reformate essa linha.
- Chaves em `localStorage`: `map_apikey`, `map_precos`, `map_propostas`, `map_sb_url`, `map_sb_key`, `map_sb_token`, `map_sb_refresh`.

Duas ressalvas conhecidas, ainda não resolvidas: o arquivo chama a API da Anthropic direto do browser com a chave guardada em `localStorage`, e fica acessível em `mapalimentacao.com.br/gerador.html` mesmo sem link, por estar no mesmo deploy do site.
