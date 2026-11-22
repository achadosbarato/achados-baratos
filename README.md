# Achados Baratos 2.0

App + servidor em uma pasta só. O servidor entrega o app e busca os produtos nas 3 lojas.

## O que o app faz
- Você busca um produto, coloca o preço mínimo e o máximo.
- Aparecem 3 tópicos, com 3 produtos cada: **mais baratos**, **mais vendidos** e **mais indicados**.
- Embaixo, a **comparação de preços** (opcional): você digita o preço, o frete e a nota de cada opção. O app mostra quanto você economiza e qual é a opção ideal.
- Se o servidor estiver fora do ar, os tópicos mostram botões que abrem a busca já filtrada em cada loja.

## De onde vêm os dados
| Loja | O que traz | O que precisa |
|---|---|---|
| Shopee | preço, vendas, nota | App ID e Secret da API de Afiliados (conta aprovada em affiliate.shopee.com.br) |
| Mercado Livre | preço, vendas (em faixas) e posição de relevância. Sem nota | Client ID e Secret em developers.mercadolivre.com.br |
| Shein | só preço | Token do Apify (apify.com), pago por uso. Sem ele a Shein fica só com os botões de busca |

Como os tópicos são montados:
- **Mais baratos**: menor preço.
- **Mais vendidos**: maior número de vendas informado pela loja (a Shein não informa).
- **Mais indicados**: 50% nota, 30% vendas, 20% relevância na loja. Produto com nota abaixo de 4,0 fica de fora.
- **Opção ideal** (comparação): menor total (preço + frete) entre as opções com nota 4,5 ou mais. Se ninguém chegar a 4,5, vale 4,0 ou mais. Sem notas, é a mais barata.

## Colocar no ar (Render)
1. Crie um repositório novo em https://github.com/new e envie o conteúdo desta pasta (sem o arquivo `.env`).
2. No Render, abra https://dashboard.render.com/blueprints, clique em New Blueprint Instance e escolha o repositório.
3. Preencha as chaves quando o Render pedir. Quem ficar sem chave não entra nos tópicos.
4. Abra `/api/saude` no endereço novo: deve aparecer `"ok":true`.
5. Abra o endereço principal no celular. O app já vem junto, sem configurar mais nada.

## Virar aplicativo no celular (APK)
O app já é instalável (PWA): tem manifesto, ícones e service worker. O APK é gerado a partir do endereço do Render, então faça isso depois de colocar no ar.

**Jeito mais rápido, sem APK:** abra o endereço no Chrome do Android, toque nos 3 pontinhos e em "Instalar app" (ou "Adicionar à tela inicial"). Fica com ícone e abre em tela cheia.

**Para ter o arquivo .apk:**
1. Abra https://www.pwabuilder.com e cole o endereço do Render.
2. Toque em "Package for stores" e escolha Android.
3. Escolha o formato APK (para instalar direto no celular) e baixe o .zip.
4. Guarde o arquivo da chave de assinatura que vem junto. Sem ele você não consegue atualizar o app depois.
5. Instale o .apk no celular (o Android pede para liberar "instalar apps desconhecidos").

O app depende do servidor no ar. No plano grátis do Render a primeira busca depois de um tempo parado demora cerca de 30 segundos.

## Teste rápido
`/api/loja/shopee?q=bola&min=20&max=100` devolve os produtos em JSON, com `vendidos` e `nota`.

## Observações
- Cache de 10 minutos (30 na Shein) e limite de buscas por minuto por pessoa.
- No plano grátis do Render o servidor "dorme" depois de um tempo parado, e a primeira busca demora cerca de 30 segundos.
- Se a sua conta da Shopee não liberar os campos de vendas e nota, o servidor cai sozinho para a consulta simples: os tópicos de vendas e indicados ficam mais pobres, mas nada quebra.
