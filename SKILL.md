---
name: descartes
description: Entrevista estruturada antes de criar uma função ou método novo — nome, intenção, lógica, contrato, localização, efeitos colaterais, casos de borda, erros e testes. Uma pergunta por vez, sempre com resposta recomendada, com regras técnicas gerais e independentes de linguagem. Mantém rascunho em disco para não perder decisões pelo caminho.
argument-hint: [nome ou descrição da função]
disable-model-invocation: true
allowed-tools: Read Grep Glob Write Edit
---

# Descartes — entrevista antes de criar uma função

Quem usa essa skill tem pensamento acelerado e perde o fio de decisões já tomadas. Siga as regras abaixo à risca, especialmente o rascunho em disco — ele é a memória externa desta entrevista, não um registro opcional.

Se `$ARGUMENTS` foi passado, use como ponto de partida do Bloco 0. Se não, pergunte primeiro o que ele quer construir.

## Regras de conduta

- Uma pergunta por vez. Nunca acumule mais de uma pergunta na mesma mensagem.
- Para cada pergunta, sugira uma resposta recomendada — o usuário confirma ou corrige, não parte do zero.
- Antes de perguntar algo que dá para descobrir explorando o código (convenção de nomes, padrão de erro já usado, se já existe função parecida), explore com Read/Grep/Glob primeiro. Só pergunte o que não deu para inferir. Exceção: a lógica desejada da função nunca é inferida — é sempre perguntada, mesmo que algo parecido já exista no código.
- Antes de cada pergunta nova, recapitule em 1-2 linhas o que já foi decidido.
- Se o usuário mudar de ideia sobre algo já decidido, atualize o rascunho e continue — não trate como recomeço.
- Se o usuário divagar para um assunto que não é a função atual, registre em "Estacionamento de ideias" no rascunho e volte para a pergunta em aberto.

## Rascunho em disco

Assim que souber o nome (mesmo provisório) da função, crie `.claude/rascunhos/<nome-da-funcao>.md`:

```markdown
# <nome-da-funcao>

## Decisões
(uma linha por decisão confirmada, por bloco)

## Estacionamento de ideias
(coisas que surgiram no meio e não são sobre essa função)

## Em aberto
(bloco/pergunta atual)
```

Atualize esse arquivo depois de cada resposta do usuário — não só no final.

## Bloco 0 — Vale a pena criar essa função?

Antes de abrir a entrevista, suba esta escada e pare no primeiro degrau que resolve. Só siga para o Bloco 1 se nenhum deles resolver:

1. **Necessidade real ou especulativa?** Se for "pode ser útil algum dia", diga isso em uma linha e não crie.
2. **Já existe algo assim no projeto?** Busque com Read/Grep/Glob. Se existir, reaproveite — não abra a entrevista.
3. **A stdlib da linguagem resolve?**
4. **Um recurso nativo da plataforma/framework resolve** (constraint de banco, validação nativa, recurso já pronto)?
5. **Uma dependência já instalada resolve?**
6. **Cabe em uma linha, sem virar função nomeada?**

Se parou em algum desses degraus, explique qual e encerre — a entrevista completa é só para quando a função realmente precisa ser escrita.

## Roteiro (6 blocos, nessa ordem)

1. **Contrato** — nome desejado; o que a função faz (intenção); qual a lógica (como deve funcionar por dentro); parâmetros (tipo, obrigatório/opcional); o que deve resultar (retorno/efeito esperado); o que significa "deu certo" x "deu errado".
2. **Onde mora** — qual classe/camada/arquivo; se já existe algo parecido (busque antes de perguntar).
3. **Efeitos colaterais** — pura, ou mexe em estado/banco/log/arquivo?
4. **Casos de borda** — nulo/vazio, valor limite, concorrência. KISS/YAGNI nunca simplifica validação de entrada em fronteira de confiança — isso não é excesso, é requisito.
5. **Erros** — que tipo de erro, quando dispara, o que loga. KISS/YAGNI nunca simplifica tratamento de erro que evita perda de dados nem checagem de segurança.
6. **Testes** — que cenários um teste precisa cobrir.

As regras da seção **Princípios técnicos**, logo abaixo, valem durante toda a entrevista — não são específicas de nenhum bloco.

## Princípios técnicos

Regras gerais, independentes de linguagem. Confira sempre se o projeto já segue um padrão diferente e, se sim, siga o que já existe.

**Erros:**
- "Não encontrado" é diferente de "erro": se for um caso esperado (ex.: busca que pode não achar nada), represente como ausência de valor — não como exceção. Se for violação de regra de negócio, é um erro de verdade.
- Separe erro esperado (regra de negócio, parte do fluxo normal) de erro inesperado (falha de infraestrutura, bug). Erro esperado geralmente não precisa de log em nível alto (ERROR/CRITICAL) — é fluxo normal, não falha.
- Nomeie erros pelo que deu errado, não pela camada onde aconteceu (ex.: "SaldoInsuficiente", não "ContaException").
- Lógica de negócio não deveria conhecer detalhes de camadas externas (HTTP, UI, apresentação) — traduzir o erro pra fora é responsabilidade de outra camada, não da função em si.

**Nomes e consistência:**
- Nomeie de forma consistente com o que já existe no projeto (verbo, idioma, prefixo de booleano) — busque exemplos reais antes de inventar um padrão novo.
- Retorno vazio só quando realmente não há nada útil pra devolver nem checar depois.
- Comentário só documenta o porquê, nunca o quê — a ordem de decisão de quando e como comentar fica no Entregável final, depois que o código existe.

**Simplicidade e escopo:**
- KISS/YAGNI: a solução mais simples que resolve o problema corretamente é a melhor. Não generalize, configure ou otimize para um caso que ainda não existe. (Exceções: ver os avisos nos Blocos 4 e 5 — validação e tratamento de erro não entram nesse corte.)
- Responsabilidade única: se a lógica descrita no Bloco 1 misturar mais de uma responsabilidade (ex.: salvar + notificar + gerar relatório), sinalize isso ao usuário e sugira separar em funções distintas antes de seguir.
- Baixo acoplamento: a função não deveria precisar conhecer detalhes internos de outras camadas além do que o contrato exige.
- DRY sem exagero: se a busca do Bloco 2 encontrar lógica equivalente em outro lugar do projeto, proponha reaproveitar em vez de duplicar.

**Legibilidade do corpo:**
- Guard clause em vez de aninhamento: trate caso de saída/erro cedo e retorne, não empilhe `if/else`.
- Condição complexa (mais de 2 termos combinados) vira variável ou função com nome que explica a intenção, em vez de expressão booleana inline.
- Nada de número ou string mágico sem nome — declare uma constante nomeada.

**Mudança cirúrgica (ao inserir o código):**
- Toque só no arquivo/trecho necessário. Não reformate nem "melhore" código vizinho, siga o estilo já existente mesmo que você faria diferente.
- Se notar código morto sem relação com a tarefa, mencione — não apague. Só remova import/variável que a própria inserção deixou órfão.
- Cobertura mínima de teste: caminho feliz, um teste por erro definido, valores limite dos parâmetros, e — se a função mexe em estado — conferir se o estado ficou correto depois.

## Coisas que o usuário costuma esquecer

(Preencha esta lista com o tempo — cada vez que perceber que o usuário esqueceu algo em uma função passada, anote aqui. Verifique esta lista a cada entrevista.)

-

## Entregável final

Antes de escrever qualquer código:
1. Mostre o contrato completo (recap dos 6 blocos) de uma vez.
2. Teste de elegância: alguém que nunca viu essa função entenderia a intenção (nome, parâmetros, retorno) em poucos minutos, sem precisar de comentário? Se não, ajuste nomes/contrato antes do recap, não depois.
3. Pergunte se pode escrever.
4. Só escreva o código depois do "sim".
5. **Comentários — decida nesta ordem, só depois de o código já existir:**
   1. Dá pra eliminar a necessidade do comentário com um nome melhor ou extraindo uma função? Se sim, faça isso em vez de comentar — comentário nunca é a primeira opção.
   2. Função pública/exportada? Promova o contrato do Bloco 1 (intenção, parâmetros, retorno, sucesso x erro) para docstring/javadoc no padrão da linguagem — isso substitui, não some com, os comentários inline.
   3. Sobrou alguma decisão não óbvia que nome e docstring não cobrem (regra de negócio, limitação, workaround)? Comente só isso — o porquê, nunca o quê.
   4. É uma simplificação deliberada (atalho com teto conhecido)? Marque com comentário nomeando o teto e o gatilho de upgrade — não deixe implícito.
   5. Releia cada comentário escrito: descarte o que repete o código, compensa nome ruim, ou vai ficar desatualizado rápido (ex.: referencia um valor que muda com frequência).
6. Depois de escrever, verifique o código contra cada cenário do Bloco 6 (Testes) um a um — se algum não se sustenta, corrija antes de considerar a função pronta.
7. Teste de elegância do corpo (não só do contrato): releia a implementação como se nunca tivesse visto. Ela flui sem esforço, sem depender dos comentários pra entender o fluxo? Se tiver aninhamento profundo, condição complexa ou número mágico, resolva com guard clause/nome/constante (ver Legibilidade do corpo) antes de considerar pronto.
8. Não delete o rascunho depois — ele fica como registro.
