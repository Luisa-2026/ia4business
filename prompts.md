# Biblioteca de prompts

## Sistema integrado de automação (problema.md)
Atualizado em: 24/08

### O pedido que funciona

**TAREFA**
Explicar, em um único texto corrido, como as três frentes de automação descritas em `problema.md` (assistente de orientação, validação/alertas automáticos, checagem de contratos/processos) funcionam como um único sistema integrado — sem eleger uma frente para começar primeiro e sem separar em fases.

**FORMATO**
Markdown, texto corrido (sem tabela, sem lista longa), ~250–300 palavras. Precisa mostrar o que muda na rotina de cada um dos quatro papéis impactados (proprietário, financeiro, atendimento, contratos/cartório) e amarrar o resultado à meta de reduzir 30–40% do tempo do proprietário em conferência.

**AMOSTRA**
```
Pense em um único assistente interno [...] Quando o responsável financeiro vai pagar
um boleto [...] o sistema confere sozinho, antes da execução, se aquele boleto já foi
pago [...] O proprietário só aparece quando alguém [...] de fato precisa da aprovação
dele [...] Nenhuma dessas três frentes executa nada sozinha [...] quem aperta o botão
final é sempre humano.
```

**LIMITE**
Não sequenciar nem fasear a implementação. Não eleger uma frente como "primeira". Não misturar com marketing/geração de leads. Não propor ação autônoma — aprovação humana final continua obrigatória em qualquer execução. Não focar no cliente externo.

**Resposta:**

Imagine um único assistente interno, acessível pelo canal que a equipe já usa no dia a dia, que serve de porta de entrada para qualquer dúvida, boleto, prazo ou contrato. Por trás dele roda uma base só, reunindo o que hoje está espalhado em planilhas soltas — e as três frentes de `problema.md` não são três ferramentas: são três formas de uso dessa mesma base, atuando ao mesmo tempo, não em ordem.

Quando o **financeiro** vai pagar um boleto — de material, de conta geral ou até pessoal da família — o sistema confere sozinho, antes da execução, se aquele boleto já foi pago antes. É a validação automática agindo exatamente no ponto onde o erro mais recorrente (duplicidade) já aconteceu. Ao mesmo tempo, ele monitora os prazos de cartório e contrato e avisa com antecedência, sem que ninguém precise lembrar manualmente.

Quando o **atendimento** tem dúvida de procedimento — como responder um cliente insatisfeito com a demora no Kommo, por exemplo — pergunta ali mesmo e recebe resposta na hora, sem abrir WhatsApp para o proprietário. Quando a **responsável por contratos/cartório** sobe um novo contrato, o mesmo sistema passa a acompanhar os prazos derivados dele e sinaliza cláusulas fora do padrão para ela revisar — apoiando quem já é a mais capacitada dos três, não substituindo o julgamento dela.

O **proprietário** só entra quando alguém de fato precisa da aprovação dele — pagamento a confirmar, exceção que o sistema não resolve sozinho. É aí que mora a meta de reduzir 30–40% do tempo dele em conferência: hoje ele confere tudo porque não existe camada nenhuma entre a equipe e o erro; com o sistema absorvendo o que é rotina e checável, só sobe pra ele o que exige julgamento humano de verdade — e mesmo aí, quem aprova a ação final é sempre uma pessoa, nunca o sistema sozinho.

### O que aprendi

A V2 venceu, não a V3: como ainda não defini a solução final, a resposta mais aberta (V2) serve melhor agora do que a V3, que já assume decisões de apresentação (fixar o pai como leitor, formato de fechamento com pré-requisito e próximo passo) que fazem mais sentido quando a solução já estiver escolhida. Fica registrado que "vencedora" é sobre a resposta certa para o estágio atual do projeto, não sobre qual pedido é mais bem escrito.

---

## Histórico do experimento (V1 → V2 → V3)

### V1 — sem técnica nenhuma

**Pedido:**
> eu preciso que a ia me entregue uma analise de oportunidade segundo meu problema

**Resposta (resumo):** análise de oportunidade em 7 seções — evidência do problema, tamanho do impacto, viabilidade técnica por frente, riscos, gaps a levantar — terminando em recomendação de **sequenciamento**: "comece pela frente X, depois Y, depois Z". Essa saída motivou a V2: sequenciar não era o que fazia sentido para a Luisa; o objetivo é entender como as frentes funcionam juntas, não escolher uma para começar.

### V2 — briefing completo (TAREFA/FORMATO/AMOSTRA/LIMITE) — **vencedora**

Pedido e resposta completos na seção "O pedido que funciona" acima.

### V3 — reescrita pela IA (meta-prompt)

**Pedido usado (técnica 4):**
> "Este é um pedido que eu vou usar toda semana: [colar a V2 acima]. Critique como um revisor exigente: o que está ambíguo, o que falta, o que sobra? Depois reescreva na melhor versão possível."

**Crítica da IA sobre a V2:**
- **Ambíguo:** não definia quem é o leitor do texto (o tom saiu "pra explicar pro pai" por suposição, não por pedido); a amostra colada era quase o texto inteiro da resposta anterior, fazendo a resposta nova repetir frases quase iguais a ela.
- **Falta:** nenhum fechamento com próximo passo verificável; não tratava reuso semanal (o que já foi implementado desde a última vez); não marcava a suposição de canal (WhatsApp) como suposição a validar.
- **Sobra:** amostra longa demais para um prompt de uso recorrente — arrisca fazer a IA reciclar a mesma resposta toda semana em vez de atualizar com a realidade da empresa.

**Pedido reescrito (V3):**
> **TAREFA** Explicar, em um único texto corrido, como as três frentes de automação de `problema.md` funcionam como um único sistema integrado — escrito para o proprietário entender em poucos minutos, sem eleger uma frente como primeira e sem separar em fases. Se algo já foi implementado desde a última vez que esse pedido foi usado, mencionar o que mudou e focar no que ainda falta.
>
> **FORMATO** Markdown, texto corrido, ~250–300 palavras, tom direto — como se estivesse explicando pro pai, não pra um técnico. Fechar com: (1) o que precisa existir primeiro (que dado, que canal), e (2) um próximo passo concreto para essa semana.
>
> **AMOSTRA** Só de tom, uma frase: "O proprietário só entra quando alguém realmente precisa da aprovação dele — o resto o sistema resolve sozinho, mas nunca executa sozinho."
>
> **LIMITE** Não sequenciar nem fasear. Não eleger uma frente como "primeira". Não misturar com marketing/geração de leads. Não propor ação autônoma — aprovação humana final sempre. Não focar no cliente externo. Se inventar canal, marcar como suposição a validar.

**Resposta:**

> Pai, imagina um único lugar pra falar de boleto, prazo ou contrato — não três sistemas, um só, alimentado pelos mesmos dados que hoje ficam soltos em planilha. Quando alguém da equipe pergunta algo, paga um boleto ou sobe um contrato novo, é ali que acontece, e é dali que vem a validação, o alerta e a checagem — as três coisas rodando juntas, não uma depois da outra.
>
> O financeiro vai pagar um boleto e o sistema confere sozinho se aquele boleto já não foi pago antes — pega a duplicidade antes de o dinheiro sair. O atendimento tem dúvida de procedimento e pergunta ali mesmo, sem te interromper. A responsável por contratos sobe um contrato novo e o sistema já passa a acompanhar os prazos dele e avisar se tem cláusula fora do padrão — ela decide, o sistema só aponta.
>
> Você só entra quando alguém realmente precisa da sua aprovação — o resto o sistema resolve sozinho, mas nunca executa sozinho. É isso que deve tirar boa parte das 30–40% do seu tempo hoje gasto conferindo: o que é rotina fica com o sistema, o que exige seu julgamento sobe pra você.
>
> Pra isso funcionar de verdade, duas coisas precisam existir antes: um lugar único onde os dados de boletos, prazos e contratos fiquem centralizados (hoje estão espalhados em planilhas separadas), e o canal por onde a equipe vai interagir com o sistema — aqui assumi WhatsApp, por ser o que todos já usam, mas isso é suposição minha, não decisão tomada.
>
> Próximo passo dessa semana: decidir com a equipe onde esses dados vão morar (pode ser uma planilha única compartilhada, pra começar simples) e confirmar se o canal de interação vai ser mesmo o WhatsApp ou outro.

### Comparação

| Versão | O que faltou / acertou |
|---|---|
| V1 (sem técnica) | Análise completa, mas termina em sequenciamento — direção que não era a pretendida. |
| **V2 (briefing completo) — vencedora** | Corrige a direção (sistema único integrado), mantém a explicação aberta e flexível — sem assumir leitor fixo nem forçar fechamento em decisão que ainda não existe. |
| V3 (meta-prompt) | Tecnicamente mais completo (fixa leitor, marca suposições, fecha com próximo passo), mas assume decisões de apresentação prematuras para o estágio atual do projeto — mais adequado para quando a solução final já estiver definida. |

**Vencedora: V2.** Justificativa em uma linha: a solução ainda não está definida, então a resposta mais aberta (V2) serve melhor agora do que a V3, que já assume decisões de apresentação que fazem mais sentido numa fase posterior.
