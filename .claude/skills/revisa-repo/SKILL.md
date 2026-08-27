---
name: revisa-repo
description: Revisa o repositório do projeto antes de qualquer entrega, como um leitor cético que não conhece a empresa — aponta o vago, o sem número, o desatualizado e os nomes de arquivo fora do padrão.
---

# revisa-repo

Leia todos os arquivos `.md` do repositório como um leitor cético, que não conhece
a empresa e não pode preencher lacunas por suposição.

## O que sempre apontar (nessa ordem de prioridade)

1. **Pendências sem dono ou prazo.** Qualquer trecho que declare algo como "ainda
   falta", "ainda não medido", "em aberto" etc. precisa dizer QUEM vai resolver e
   ATÉ QUANDO. Se faltar um dos dois, é um achado — sempre o primeiro da lista.
2. Afirmações vagas que deveriam ter número ou frequência (ex: "às vezes",
   "bastante", "muitos") quando um número tornaria a informação acionável.
3. Datas sem ano (ex: "Atualizado em: 24/08").
4. Nomes de arquivo fora do padrão do projeto.

## Como responder

Liste os achados do mais grave ao menos grave (pendências sem dono/prazo sempre
primeiro), citando o arquivo e a linha. Para cada achado, proponha a correção
específica — não só aponte o problema.
