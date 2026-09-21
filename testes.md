# Testes de falha

Testado em 21/09/2026, entre 09:11 e 09:20 (horário local, São Paulo). Os três testes foram executados de verdade contra `dados/amostra.csv` — a fonte que alimenta o `painel.html` (ver [dados/fonte.md](dados/fonte.md)) — restaurada ao conteúdo original logo depois de cada teste. Nenhum resultado abaixo foi inventado ou escrito antes de rodar o teste.

## Cenário 1 · A fonte saiu do ar

**O que eu testei:** renomeei `dados/amostra.csv` para `dados/amostra_OLD.csv` (simulando a fonte fora do ar) e tentei ler o arquivo, do mesmo jeito que a rotina real faz quando alguém pede para regenerar o `painel.html` a partir de `dados/fonte.md` + `dados/amostra.csv` (processo descrito em `automacoes.md`, seção "Painel").

**O que aconteceu — ANTES** (com o `regras.md` de antes desta rodada, sem a cláusula de segurança): a tentativa de leitura do arquivo devolveu um erro técnico explícito (`File does not exist`) — isso já é melhor do que "relatório vazio com cara de normal". Mas o `regras.md` de então não tinha nenhuma instrução escrita dizendo o que fazer a seguir. O risco real, que fiz questão de registrar: eu já tinha lido `dados/amostra.csv` antes nesta mesma conversa (4 pedidos, receita paga R$ 1.430,00). Sem uma regra escrita proibindo isso, nada impedia que eu "resolvesse" a falha respondendo com esses números memorizados, como se fosse uma leitura nova — exatamente o tipo de falha silenciosa que o enunciado da Aula 14 descreve ("resumo vazio ou com cara de normal, ninguém percebe").

**O que eu consertei:** acrescentei ao `regras.md` a seção **"Regra de segurança da fonte"**, logo depois da introdução (antes da Regra 1), com os princípios pedidos: se a fonte não existir/vier vazia, escrever "FONTE INDISPONÍVEL" e parar; nunca inventar número; sempre informar quantos pedidos/linhas foram lidos e quantos foram ignorados, e por quê; e explicitamente proibi reaproveitar números de uma leitura anterior da conversa.

**O que aconteceu — DEPOIS** (mesma simulação, com a regra nova em vigor): renomeei `dados/amostra.csv` de novo e tentei ler o arquivo. Resposta real, seguindo a regra nova:

> "FONTE INDISPONÍVEL — não encontrei dados/amostra.csv. Pedidos lidos: 0. Pedidos ignorados: 0. Não vou estimar nem reaproveitar os números de uma leitura anterior desta conversa. O painel não pode ser regenerado até a fonte voltar."

**Antes e depois:** antes, a única proteção era o erro técnico do sistema de arquivos — nenhuma regra escrita impedia um fallback silencioso para números memorizados. Depois, existe uma frase específica no `regras.md` proibindo esse fallback e exigindo a contagem de linhas lidas/ignoradas, então a mesma situação agora produz uma recusa explícita em vez de uma resposta de aparência normal.

Ao final do teste, `dados/amostra.csv` foi restaurado ao nome e conteúdo originais (conferido com `diff`, sem nenhuma diferença).

## Cenário 2 · Chegou dado inesperado

**O que eu testei:** com o conteúdo original de `dados/amostra.csv` já salvo (visto acima), substituí o arquivo por uma versão com os 4 pedidos originais **mais 3 linhas problemáticas**: pedido 1005 com `value`/`discount`/`total` em branco (campo vazio), pedido 1006 com `total` negativo (`-250.00`, quando um pedido `PAID` nunca deveria ter total negativo) e pedido 1007 com a data em formato `18/01/2026` em vez do padrão ISO 8601 usado no resto do arquivo (`2026-01-18T...`). Depois rodei a rotina real de cálculo dos 3 números descritos em `dados/fonte.md`, já seguindo a regra nova do `regras.md` (contar linhas lidas/ignoradas e por quê). Rodei o código de verdade (Python, via terminal) para conferir a linha a linha, não só de cabeça.

**O que aconteceu:** a rotina leu 7 pedidos no arquivo. Ignorou 3:
- `orderId 1005` — campo vazio (`value`/`discount`/`total` em branco);
- `orderId 1006` — total negativo (`-250.00`);
- `orderId 1007` — data fora do padrão ISO 8601 (`18/01/2026`).

Usou só os 4 pedidos válidos (1001 a 1004) para calcular: **Pedidos no mês: 4 · Receita paga: R$ 1.430,00 · Ticket médio pago: R$ 715,00** — os mesmos números que o `painel.html` já mostra, porque os 3 pedidos problemáticos foram excluídos do cálculo, não "consertados" ou estimados. Nenhum valor foi inventado para as linhas ignoradas (não tentei adivinhar o `total` do 1005, não usei o valor absoluto do 1006, não tentei interpretar a data do 1007).

**O que eu consertei:** nada precisou ser corrigido no `regras.md` — a cláusula acrescentada no Cenário 1 ("nunca inventar número" + "informar quantas linhas foram lidas e ignoradas, e por quê") já cobre esse caso, e o teste confirmou que ela é suficiente: a rotina detectou os 3 problemas, informou o motivo de cada um e não inventou nada.

Ao final do teste, `dados/amostra.csv` foi restaurado ao conteúdo original de 4 linhas (confirmado com `git status`/`git diff`, sem diferença em relação à versão já commitada).

## Cenário 3 · A condição nunca dispara

**O que eu testei:** analisei as 6 regras de `regras.md` e a tabela de execuções em `automacoes.md` para achar uma condição que nunca disparou de verdade.

**O que aconteceu:** a **Regra 6 — "Pedido cancelado com reclamação de cliente por e-mail"** é a candidata clara, com evidência real já registrada no repositório (não inventada agora): ela cruza um pedido `CANCELLED` do FakeERP com um e-mail de cancelamento/reembolso no Gmail no mesmo dia. Foi testada de verdade duas vezes (registrado em `automacoes.md`, execuções de 10/09/2026): pedido 1003 cancelado em 20/01/2026 → busca no Gmail entre 18–23/01/2026 → 0 e-mails encontrados; pedido 1010 cancelado em 30/03/2026 → busca entre 28/03–02/04/2026 → 0 e-mails encontrados. Nas duas vezes a condição completa (fonte 1 **e** fonte 2) não foi satisfeita, então a regra ficou calada — não é um teste que fiz agora, é o teste real já documentado, com data.

A regra em si **não está quebrada**: a lógica (E lógico entre duas fontes, com fallback documentado para quando uma fonte falha) está correta e bem estruturada. O motivo estrutural de nunca disparar é outro: a fonte 2 (minha caixa de Gmail pessoal, usada como dado de treino) não tem nenhuma relação real com os pedidos fictícios do FakeERP — são dois datasets de treino desconectados. Isso já está registrado no próprio `regras.md`: *"aqui a fonte 2 não tem, de fato, nenhum dado correspondente [...] Isso é uma 'regra impossível' na prática atual"*.

**Não pude comprovar com uma execução de hoje** (21/09/2026) se ela continua nunca disparando — não rodei a Regra 6 de novo agora, só usei a evidência já registrada de 10/09/2026. Se algo mudou no Gmail ou no FakeERP desde então, isso não foi conferido nesta rodada.

**O que eu consertei:** não é um bug para corrigir — é uma limitação de dado de treino que só se resolve quando a fonte 2 virar o e-mail real de atendimento da empresa (ligado ao Kommo), como o próprio `regras.md` já previa. **Plano B, enquanto isso não acontece:** toda vez que um pedido for cancelado no FakeERP (ou, na operação real, um contrato/pagamento for cancelado), eu confiro manualmente — nos primeiros minutos, no meu próprio e-mail ou no Kommo — se existe reclamação de cliente relacionada, em vez de esperar o alerta automático da Regra 6, porque hoje ele estruturalmente não vai disparar sozinho.

## Resumo

| Cenário | A automação acusou o problema? | Precisou de conserto? |
|---|---|---|
| 1 · Fonte fora do ar | Só depois de eu escrever a regra explícita no `regras.md` — antes, a proteção dependia só do erro técnico do sistema de arquivos, sem instrução escrita contra reaproveitar número memorizado | Sim — cláusula "Regra de segurança da fonte" adicionada |
| 2 · Dado sujo | Sim, já com a regra nova em vigor — identificou e explicou as 3 linhas ruins, sem inventar valor | Não — a cláusula do Cenário 1 já cobriu este caso |
| 3 · Condição nunca dispara | A regra em si está correta; o motivo de nunca disparar é a fonte 2 ser dado de treino sem relação real com a fonte 1 | Não é bug — plano B é conferência manual até a fonte 2 ser trocada pelo e-mail real de atendimento |
