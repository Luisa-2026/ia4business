# Pronto para escalar?

Portão da Aula 15, respondido em 24/09/2026. Regra usada: evidência é arquivo e data — o que não está escrito no repositório conta como "sem evidência", não como "sim".

Contexto que pesa em todas as respostas: a operação inteira roda sobre o **FakeERP (pedidos de treino)**, usado como proxy — ainda não sobre os boletos, prazos de cartório e contratos reais da construtora (ver [dados/fonte.md](dados/fonte.md), "Caminho escolhido: C — dado de treino"). O que está sendo avaliado aqui é o ensaio da operação, não a operação real.

## 1. Roda sem mim?

**Resposta:** não

**Evidência:** [automacoes.md](automacoes.md), seção "Regras ligadas": *"Como roda: à mão no Claude Code [...] Ainda não agendada como rotina"*. A rotina `trig_01FzD9HPLRwfNPeUnNQWyx3S` (todo dia, 8h) está marcada como **CONSERTAR**: a rede do ambiente de nuvem bloqueia a saída para `fake-erp.isilab.com.br` (403 no CONNECT). Todas as execuções da tabela "Execuções" (10/09) foram feitas por mim, à mão. O teste com "Run now" não conta — fui eu apertando o botão.

**Se não: o que falta:** liberar o domínio do FakeERP na política de rede do ambiente da rotina e registrar em `automacoes.md` uma execução das 8h que aconteceu sem eu fazer nada.

## 2. Quando falha, avisa?

**Resposta:** sim (com ressalva)

**Evidência:** [testes.md](testes.md), testado em 21/09/2026 entre 09:11 e 09:20:
- Cenário 1 (fonte fora do ar): depois da "Regra de segurança da fonte" em [regras.md](regras.md), a resposta foi *"FONTE INDISPONÍVEL — não encontrei dados/amostra.csv"*, sem reaproveitar número antigo.
- Cenário 2 (dado sujo): leu 7 pedidos, ignorou 3 e explicou o motivo de cada um (campo vazio, total negativo, data fora do padrão).
- Além disso, [automacoes.md](automacoes.md) registra que a rotina bloqueada por rede avisou por notificação em vez de escrever relatório vazio.

**Ressalva:** a Regra 6 nunca dispara por limitação de dado (Cenário 3 do `testes.md`) — nesse caso quem "avisa" é a minha conferência manual (plano B), não a automação. E os testes foram feitos na execução manual, não na rotina agendada (que ainda não roda — ver pergunta 1).

## 3. Alguém lê a saída?

**Resposta:** em parte

**Evidência:** existe uma pessoa com nome — eu (Luisa) sou quem recebe em todas as regras de [regras.md](regras.md) (coluna "Quem recebe"), na página "Alertas — IA4business" do Notion e em `alertas/`.

**Sem evidência** do que alguém faz nos 10 minutos seguintes: [alertas/2026-09-10.md](alertas/2026-09-10.md) só tem recomendações ("vale checar com o funcionário de atendimento", "encaminhar ao responsável financeiro"). Confirmado em 24/09: nenhum alerta foi repassado ao funcionário financeiro nem ao meu pai até hoje.

**Se não: o que falta:** cada regra precisa de quem **age** (não só quem recebe) e do que essa pessoa faz em seguida — e um registro de pelo menos uma vez em que isso aconteceu de verdade.

## 4. Mede alguma coisa?

**Resposta:** em parte

**Evidência:** a tabela Métrica/Alvo/Como confiro existe em [problema.md](problema.md), seção "Métrica": horas por semana que o proprietário gasta conferindo, de ~12h para 8h até 31/12/2026, conferido por relatório de opinião mensal.

**Sem evidência** de medição feita: os ~12h são uma estimativa dele (*"sem medição formal ainda"*) e não há nenhum registro datado de pergunta mensal. Confirmado em 24/09: ele não mede as horas no dia a dia — a percepção é de que é um tempo considerável, que poderia ir para projetos que trazem faturamento. Isso é o **motivo** do projeto, mas não é medição. O primeiro experimento (planilha de boletos pagos por 4 semanas, em `problema.md`) também ainda não começou.

**Se não: o que falta:** a primeira medição registrada com data — perguntar a ele as horas de setembro e anotar (ver "A decisão").

## 5. O cliente foi ouvido?

**Resposta:** não

**Evidência:** sem evidência. Os "clientes" desta operação são quem a usa: meu pai e os 3 funcionários (ver "Cargos impactados" em [problema.md](problema.md)). Não há registro de conversa com 3 deles sobre os alertas, nem de nada que eu tenha mudado por causa de uma conversa. O `Resumo_Contexto_Negocio.docx` preparado "para revisão do meu pai" está no `.gitignore` e não tem retorno registrado.

**Se não: o que falta:** falar com pelo menos 3 dessas 4 pessoas e anotar o que mudei por causa disso.

## A decisão

**Não abre.**

Uma "sim", duas "em parte" e duas "não". Mais importante que a contagem: tudo o que funciona hoje funciona sobre dado de treino, executado à mão por mim e lido só por mim. Escalar agora multiplicaria um ensaio, não uma operação.

**O UM item que vira sim primeiro: pergunta 5 — o cliente foi ouvido. Prazo: 01/10/2026 (aula 17).**

Por que esse e não a pergunta 1: consertar a rotina (pergunta 1) faria rodar sozinho um alerta sobre pedidos fictícios que ninguém pediu. A pergunta 5 é a única que testa se os alertas resolvem a dor real — e é a mais barata: são conversas, não infraestrutura. De quebra, ela gera a primeira medição da pergunta 4 e define quem age em cada alerta (pergunta 3).

O que fazer até 01/10/2026:

1. Conversar (por ligação ou WhatsApp) com meu pai e pelo menos 2 dos 3 funcionários — prioridade para o responsável financeiro (Funcionário 2), que é quem mais perde prazo.
2. Em cada conversa, mostrar um alerta real traduzido para o mundo deles — por exemplo, a Regra 3 ("pendente há mais de 15 dias") como "boleto X vence em 3 dias e não está marcado como pago" — e perguntar: isso te ajudaria? o que você faria nos 10 minutos seguintes? o que está errado ou faltando?
3. Com o meu pai, fazer também a pergunta da métrica: "quantas horas por semana você sente que gastou conferindo coisas da equipe em setembro?" — e anotar com data. Essa é a primeira medição da pergunta 4.
4. Registrar tudo em `conversas.md`: data, cargo, o que ouvi, e **o que eu mudei** em `regras.md` (ou em outro arquivo) por causa disso. Não é pesquisa de satisfação — é descobrir o que eu entendi errado.

## Pergunta 6 (bônus): se eu dobrar o volume amanhã, o que quebra primeiro?

**Eu.** Hoje eu executo as regras à mão, sou a única pessoa que lê os alertas e decido sozinha o que repassar ao meu pai. Com o dobro de alertas, eu viro o gargalo — exatamente o problema que o projeto quer tirar do meu pai, só que transferido para mim. Isso reforça as perguntas 1 (rodar sem mim) e 3 (quem age em cada alerta, além de mim) como os próximos itens depois da 5.
