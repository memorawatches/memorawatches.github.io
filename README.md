# Memora Watches — Catálogo

Catálogo online da Memora Watches: uma vitrine pública de relógios e um painel de administração. O site é estático e fica no GitHub Pages. Não existe servidor nem banco de dados. Todo o conteúdo mora em arquivos JSON neste repositório, e o painel grava as mudanças direto aqui pela API do GitHub.

Site: https://memorawatches.github.io

## Arquivos

| Arquivo | O que é |
|---|---|
| `index.html` | Vitrine para clientes: busca, filtros, detalhes do relógio, simulação de parcelamento e botão de WhatsApp. |
| `admin.html` | Painel para cadastrar, editar e remover relógios, subir fotos, fazer cotações e travar o site. |
| `catalog.json` | Lista de relógios. É gerado pelo admin: evite editar à mão. |
| `config.json` | Taxas de parcelamento, contatos e trava do site. |
| `images/` | Fotos dos relógios, enviadas pelo admin. |

As fotos são servidas pelo jsDelivr (`cdn.jsdelivr.net/gh/...`). Depois de publicar, uma foto nova pode levar alguns minutos para aparecer.

## Usando o admin

1. Abra `admin.html` e informe usuário, repositório, branch e um **fine-grained token** do GitHub com permissão de escrita em *Contents* **somente neste repositório**.
2. O token fica guardado no navegador (localStorage). Por isso, use o admin só em computador de confiança. Se perder o acesso a esse computador, revogue o token em GitHub → Settings → Developer settings.
3. Cada vez que você salva, o admin faz um commit com o `catalog.json` e as fotos juntos. O GitHub Pages leva cerca de um minuto para publicar.

### Cadastro de um relógio

- **Marca** e **Modelo** são campos separados. A marca sugere as que já existem, e o nome completo é salvo como "Marca Modelo".
- **Referência** e **Tamanho** são os da versão principal.
- **Outros tamanhos** (opcional): cada um tem medida, referência e preço próprios. A medida é obrigatória, e o admin não salva enquanto ela estiver vazia. Em "Especificações diferentes neste tamanho", preencha só o que muda. Campo vazio herda o valor da versão principal.
- **Pulseiras** (opcional): cada uma tem nome, preço, fotos e uma referência por tamanho. Campo vazio usa a referência do tamanho.
- **Status**: "Em estoque" exige quantidade maior que zero.

### Busca

A busca encontra pelo nome e por **qualquer** referência: a principal, as dos outros tamanhos e as das pulseiras. Ela ignora pontos, espaços e traços, então `L3.830.4.92.6` e `L38304926` dão o mesmo resultado. Quando a busca encontra uma referência secundária, o card mostra essa versão, e o relógio abre já no tamanho e na pulseira dela.

## Formato do `catalog.json`

```json
{
  "id": 12,
  "brand": "Longines",
  "name": "Longines Conquest Azul",
  "ref": "L3.720.4.92.6",
  "medida": "38mm",
  "movimento": "Automático",
  "espessura": "10.9mm",
  "status": "Sob encomenda",
  "qty": null,
  "price": 15490,
  "orderCount": 3,
  "postedAt": "2026-09-27",
  "images": ["images/12-1790000000000-0.jpg"],

  "sizes": [
    { "medida": "41mm", "ref": "L3.830.4.92.6", "price": 0,
      "specs": { "espessura": "11.4mm" } }
  ],

  "strapBaseName": "Aço",
  "straps": [
    { "nome": "Borracha", "price": 14990, "images": [],
      "refs": ["L3.720.4.92.9", "L3.830.4.92.9"] }
  ]
}
```

- `price` é em reais (`15490` = R$ 15.490,00). `0` num tamanho ou numa pulseira significa "mesmo preço da versão principal".
- Especificações aceitas: `movimento`, `material`, `vidro`, `espessura`, `lugToLug`, `resistencia`, `reservaMarcha` e `acompanha`. Todas são opcionais.
- `sizes`, `specs`, `straps`, `refs` e `brand` só aparecem quando foram preenchidos. Relógios antigos sem esses campos continuam funcionando.
- `straps[].refs` segue a ordem dos tamanhos: a posição 0 é o tamanho principal, a 1 é o primeiro de `sizes`, e assim por diante.
- `orderCount` alimenta a ordenação "Mais pedidos" e o selo "Mais pedido".

## Formato do `config.json`

| Campo | Para que serve |
|---|---|
| `rates` | Taxa da maquininha por número de parcelas (`"1"` a `"12"`), em %. É usada no parcelamento da vitrine e na cotação. |
| `whatsappSellerNumber` | Número que recebe as mensagens do botão "Falar com vendedor". |
| `whatsappGroupUrl`, `instagramUrl` | Links do topo do site. |
| `prazoEntregaDias` | Prazo exibido nas peças "Sob encomenda". |
| `locked` | Quando `true`, a vitrine mostra "Catálogo indisponível". |
| `previewKey` | Com o site travado, quem abrir `?preview=<chave>` vê o catálogo normalmente. |

Este repositório é público, então qualquer pessoa consegue ler o `config.json`. A `previewKey` serve para manter o catálogo fora da vista de quem só visita o site, mas não protege nada de verdade.

## Marcas com nome composto

Para separar a marca do modelo em relógios antigos, que não têm o campo `brand`, o site usa a lista `MARCAS_COMPOSTAS` no início do script de `index.html` e de `admin.html`. Relógios cadastrados com o campo Marca não dependem dessa lista. Ela só precisa de uma marca nova quando houver peças antigas dessa marca sem o campo Marca. Nesse caso, adicione o nome **nos dois arquivos**.

## Cuidados

- Nunca coloque o token do GitHub em arquivo do repositório.
- Ao editar `catalog.json` à mão, valide o JSON antes do commit. Um erro de vírgula derruba a vitrine inteira.
- Os termos do GitHub Pages não permitem usá-lo como plataforma de comércio. Ele serve bem como vitrine, mas, se o site crescer, vale migrar para uma hospedagem própria.
