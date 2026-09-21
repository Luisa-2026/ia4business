# Regras — IA4business

Regras que a rotina usa para decidir quando falar e quando ficar calada. Fonte de dado: FakeERP (ver `fake-erp.md`), usado aqui como proxy de "pedidos" — cancelamento, receita paga e pendência em aberto são os três tipos de exceção que hoje só chegam ao meu pai porque alguém percebeu manualmente.

Em nenhuma regra a ação é executar algo sozinha: o máximo que a automação faz é escrever o alerta. Quem decide e quem age continua sendo uma pessoa — [problema.md](problema.md) e o `CLAUDE.md` do projeto exigem revisão humana como etapa final.

## Regra de segurança da fonte (vale para todas as regras abaixo, e para o `painel.html`)

Toda regra deste arquivo depende de conseguir ler uma fonte de verdade primeiro — o FakeERP (`GET /report/{year}/{month}`) para as regras 1 a 6, ou `dados/amostra.csv` para os 3 números do `painel.html`. Isso só é confiável se a leitura da fonte for tratada com a mesma seriedade da regra em si:

- **Se a fonte não existir, vier vazia, ou a chamada falhar** (arquivo não encontrado, API fora do ar, resposta sem pedidos): escrever **"FONTE INDISPONÍVEL"** e parar. Nenhuma regra é avaliada nesse ciclo. Isso vale mesmo que eu (Claude) já tenha visto os números de uma execução anterior na mesma conversa — números antigos não podem ser reaproveitados como se fossem uma leitura nova.
- **Nunca inventar número.** Se um valor não veio da fonte nesta execução, ele não aparece no relatório — nem estimado, nem "deduzido por parecer razoável". A única exceção documentada até hoje (o pedido 1002 em `dados/fonte.md`, deduzido por diferença de agregados) foi marcada explicitamente como dedução e depois reconfirmada contra a API real antes de virar dado definitivo — isso não é a regra, é o caso excepcional que prova por que a regra existe.
- **Sempre informar quantos pedidos/linhas foram lidos e quantos foram ignorados, e por quê** (linha vazia, valor negativo onde não deveria existir, data em formato errado, `orderId` duplicado, etc.) — tanto para o `GET /report/{year}/{month}` do FakeERP quanto para `dados/amostra.csv`.

Isso é o que decide se a rotina para ou continua. As colunas "Se não disparar" e "Se uma fonte estiver fora do ar" de cada regra abaixo continuam existindo para o registro em `automacoes.md`, mas só depois que a fonte já foi confirmada disponível — se a fonte falhou, a resposta é sempre "FONTE INDISPONÍVEL", nunca "0 pedidos encontrados" (que é uma leitura válida da fonte, não uma falha dela).

## Regra 1 — Venda cancelada no mês

| Campo | Valor |
|---|---|
| Nome | Venda cancelada no mês |
| Gatilho | Tempo. Todo dia às 8h (ou à mão, toda segunda) |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | Existe pelo menos 1 pedido com `status: "CANCELLED"` no mês consultado |
| Ação | Criar uma seção nova na página "Alertas — IA4business" do Notion, com `orderId`, valor (`total`) e data de cada pedido cancelado |
| Quem recebe | Eu (Luisa), na página ["Alertas — IA4business"](https://app.notion.com/p/3d7fb134714b81ca9eeaef83df2a9cce) do Notion. Repasso ao meu pai só se a causa não for óbvia |
| Se não disparar | Gravar em `automacoes.md` a data, o mês consultado e quantos pedidos foram olhados |

Degrau 1 da faixa rápida: a ação desta regra saiu do repositório. Antes escrevia em `alertas/`; agora cria a seção direto na página do Notion que eu já abro — a mesma lógica de gatilho/condição, só o destino mudou. Rodei de verdade contra janeiro/2026 e a seção "10/09/2026 — Regra 1" já está publicada na página.

## Regra 2 — Receita paga do mês abaixo da meta

| Campo | Valor |
|---|---|
| Nome | Receita paga do mês abaixo da meta |
| Gatilho | Tempo. Todo dia às 8h, a partir do dia 20 do mês |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | Receita paga (soma do campo `total` só dos pedidos com `status: "PAID"`, ignorando `CANCELLED` e `PENDING`) abaixo de R$ 2.000, faltando 10 dias ou menos para fechar o mês |
| Ação | Escrever alerta com a receita paga atual, quanto falta para R$ 2.000 e os pedidos `PENDING` que poderiam fechar a diferença |
| Quem recebe | Eu (Luisa), no arquivo `alertas/`. Repasso ao meu pai, já que é uma decisão estratégica dele |
| Se não disparar | Gravar em `automacoes.md` a data, a receita paga apurada e quantos pedidos foram olhados |

## Regra 3 — Pagamento pendente há mais de 15 dias

| Campo | Valor |
|---|---|
| Nome | Pagamento pendente há mais de 15 dias |
| Gatilho | Tempo. Todo dia às 8h |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | Existe pedido com `status: "PENDING"` cujo `orderDateTime` é de 15 dias atrás ou mais, contando a partir de hoje |
| Ação | Escrever alerta com `orderId`, valor e há quantos dias está pendente — pronto para eu enviar ao responsável financeiro conferir antes que vire prazo perdido |
| Quem recebe | Eu (Luisa), no arquivo `alertas/`. Repasso ao funcionário financeiro por WhatsApp, mantendo a revisão antes de qualquer cobrança |
| Se não disparar | Gravar em `automacoes.md` a data, quantos pedidos pendentes existiam e o mais antigo deles |

## Regra 4 — Desconto fora do padrão em um pedido

| Campo | Valor |
|---|---|
| Nome | Desconto fora do padrão em um pedido |
| Gatilho | Tempo. Todo dia às 8h |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | Existe pedido com `status: "PAID"` ou `"PENDING"` cujo desconto (`discount`/`value`) é 10% ou mais do valor bruto. Pedidos `CANCELLED` ficam de fora — desconto de venda que não se concretizou não conta |
| Ação | Escrever alerta com `orderId`, `value`, `discount` e o percentual calculado |
| Quem recebe | Eu (Luisa), no arquivo `alertas/`. Repasso ao meu pai — desconto acima do padrão é decisão dele, não da equipe |
| Se não disparar | Gravar em `automacoes.md` a data, o mês consultado e o maior percentual de desconto encontrado |

Testada contra os 4 meses reais do FakeERP: dispara em fevereiro/2026 (pedido 1007, 10% de desconto), fica calada em janeiro/2026 (maior desconto é 9,12%).

## Regra 5 — Concentração de faturamento em um único pedido

| Campo | Valor |
|---|---|
| Nome | Concentração de faturamento em um único pedido |
| Gatilho | Tempo. Todo dia às 8h |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | O mês tem 2 ou mais pedidos com `status: "PAID"` **e** existe um deles cujo `total` é 75% ou mais da soma de `total` de todos os pedidos `PAID` do mês |
| Ação | Escrever alerta com `orderId`, `total` do pedido e o percentual que ele representa do faturamento pago do mês |
| Quem recebe | Eu (Luisa), no arquivo `alertas/`. Repasso ao meu pai — um único pedido concentrando a maior parte do faturamento é risco se ele cancelar ou atrasar |
| Se não disparar | Gravar em `automacoes.md` a data, o mês consultado e a maior concentração encontrada |

Testada contra os 4 meses reais do FakeERP: dispara em julho/2026 (pedido 1012, 93% do faturamento pago do mês), fica calada em fevereiro/2026 (maior concentração é 71,4%, mesmo com 3 pedidos pagos). Esta regra já nasce da observação registrada em [fake-erp.md](fake-erp.md) — "o pedido 1007 sozinho foi 71% do faturamento de fevereiro/2026" — só que agora vira alerta automático em vez de achado manual.

## Regra 6 — Pedido cancelado com reclamação de cliente por e-mail (duas fontes)

| Campo | Valor |
|---|---|
| Nome | Pedido cancelado com reclamação de cliente por e-mail |
| Gatilho | Tempo. Todo dia às 8h |
| Fonte | Duas, cruzadas: **(1)** FakeERP, pedidos `CANCELLED` do mês corrente — `GET /report/{year}/{month}`; **(2)** Gmail, e-mails recebidos no mesmo dia da data do pedido cancelado, com "cancelamento", "cancelado" ou "reembolso" no assunto |
| Condição | Existe pelo menos 1 pedido `CANCELLED` no mês (fonte 1) **e** existe pelo menos 1 e-mail com essas palavras-chave recebido no mesmo dia da data desse pedido (fonte 2) |
| Ação | Escrever alerta cruzando as duas: data, `orderId`, valor do pedido e assunto/remetente do e-mail encontrado |
| Quem recebe | Eu (Luisa), na página ["Alertas — IA4business"](https://app.notion.com/p/3d7fb134714b81ca9eeaef83df2a9cce) do Notion |
| Se não disparar | Gravar em `automacoes.md` a data, quantos pedidos `CANCELLED` existiam (fonte 1) e quantos e-mails com a palavra-chave foram encontrados no mesmo dia (fonte 2) — separado por fonte, para saber qual das duas ficou muda |
| Se uma fonte estiver fora do ar | **FakeERP fora do ar:** a regra não avalia nada e registra "FakeERP indisponível, condição não verificada" — nunca assume que não houve cancelamento. **Gmail fora do ar** (sem permissão, erro de API): a regra ainda escreve o alerta só com o pedido cancelado do FakeERP, mas marca "correlação com e-mail não verificada" — não finge silêncio nem finge confirmação |

Degrau 2 da faixa rápida. Testada de verdade contra as duas fontes, sem inventar número: pedido 1003 cancelado em 20/01/2026 (fonte 1) → busquei e-mails reais na minha caixa entre 18/01 e 23/01/2026 com essas palavras-chave (fonte 2) → 0 encontrados. Pedido 1010 cancelado em 30/03/2026 → busquei entre 28/03 e 02/04/2026 → 0 encontrados. As duas fontes responderam normalmente (nenhuma fora do ar) — ficou calada nos dois meses testados, mas por um motivo diferente do degrau 3: aqui a fonte 2 não tem, de fato, nenhum dado correspondente, porque a caixa de e-mail é pessoal e de treino, sem relação real com os pedidos fictícios do FakeERP. Isso é uma "regra impossível" na prática atual — só passa a fazer sentido de verdade quando a fonte 2 virar o e-mail real de atendimento da empresa, ligado ao Kommo.

## Por que essas seis

Regra 3 é a mais próxima do problema real descrito em `problema.md`: lá, o erro recorrente é prazo perdido no pagamento de boletos pelo Funcionário 2. Aqui, "pedido pendente há mais de 15 dias" é a mesma lógica aplicada ao dado que já tenho testado (FakeERP) — a mesma checagem, com um `customerId`/`items` de verdade, é candidata a virar a regra real de boletos quando o sistema financeiro da empresa estiver conectado.

Regras 4 e 5 exploram os dois campos do `OrderDTO` que as três primeiras não usam: `discount` e a distribuição de `total` entre pedidos do mês. Não mapeiam para um processo específico do `problema.md` ainda — servem para mostrar que a mesma lógica de condição-com-número funciona em qualquer campo numérico da fonte, mesmo sem `customerId`. A Regra 5 em especial nasceu de um achado que já estava documentado em texto (`fake-erp.md`) e virou alerta automático.

Regra 6 é a mais honesta das seis: cruza FakeERP com Gmail de verdade, mas com dados de treino que não têm relação real entre si, então fica calada sempre — o valor dela não é disparar hoje, é mostrar a estrutura (duas fontes, condição em "e" lógico, e o que fazer se uma fonte cair) que vira útil quando a fonte 2 for trocada pelo e-mail real de atendimento da empresa.
