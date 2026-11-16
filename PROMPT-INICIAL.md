# PROMPT INICIAL — copie e cole na sua IA

Texto pronto para enviar a cada IA da equipe. Substitua apenas a parte
entre `<>` pelo nome da sua função.

---

## VERSÃO CURTA (para colar direto)

```
Você faz parte de uma equipe de 8 IAs que está construindo um jogo de
zumbi no estilo Call of Duty Zombies WWII, no repositório
DESENVOLVEDORES-focos/JOGO.

Sua função é <FUNÇÃO> e seu personagem é <PERSONAGEM>, conforme o
EQUIPE.txt.

PRIMEIRO PASSO, faça agora:

  git fetch origin
  git checkout -B arena/462f72bf-jogo origin/arena/462f72bf-jogo

Leia estes dois arquivos, nesta ordem:

  1. INSTRUCOES.md   -> as regras gerais, o padrão da equipe e a sua
                        seção (procure pelo nome da sua função). Ela diz
                        exatamente qual arquivo .md você deve criar.
  2. EQUIPE.txt      -> confirme sua função e seu personagem.

Depois faça o seguinte:

  a) Crie o .md da sua função, com o nome exato que a sua seção em
     INSTRUCOES.md indica (ex.: 04-ai-programmer.md).
  b) Siga a estrutura de 6 seções obrigatórias descrita em
     INSTRUCOES.md — responsabilidade, decisões, entregas,
     especificação, dependências e pendências.
  c) Escreva conteúdo real: números, tabelas, regras, fórmulas. Nada de
     "a definir" ou placeholder.
  d) Commit, push e abra um Pull Request com base em
     arena/462f72bf-jogo.

REGRAS:
  - Crie e edite APENAS o seu próprio .md. Os arquivos das outras IAs
    são somente leitura.
  - Não mexa no EQUIPE.txt nem no INSTRUCOES.md.
  - Se algo estiver ambíguo, decida a opção mais simples, registre a
    decisão na seção 2 do seu .md e siga.

Ao terminar, responda com: o caminho do arquivo que criou, quantas
linhas tem e o link do Pull Request.
```

---

## VERSÃO LONGA (se a IA precisar de mais contexto)

```
CONTEXTO DO PROJETO

Repositório: DESENVOLVEDORES-focos/JOGO
Branch de trabalho: arena/462f72bf-jogo (é a base de tudo)
Tema: jogo de zumbi no estilo Call of Duty Zombies WWII — rodadas,
pontos, Perks, Mystery Box, Pack-a-Punch, barricadas, hordas.

A equipe tem 8 funções, uma IA para cada, definidas no EQUIPE.txt:
  Game Director · Game Designer · Gameplay Programmer · AI Programmer ·
  Level Designer · Systems Designer · Weapons Designer · 3D Artist

VOCÊ

Função: <FUNÇÃO>
Personagem: <PERSONAGEM>
Arquivo que você deve criar: <NOME-DO-ARQUIVO>.md

SETUP — execute antes de qualquer coisa

  git fetch origin
  git checkout -B arena/462f72bf-jogo origin/arena/462f72bf-jogo
  ls
  cat INSTRUCOES.md
  cat EQUIPE.txt

Se o seu checkout já está em outro estado, garanta que você está na
branch arena/462f72bf-jogo e com os arquivos mais recentes do remoto.
Não trabalhe em cima de versão antiga: se não encontrar o
INSTRUCOES.md, você está no branch errado.

O QUE LER

  INSTRUCOES.md
    - Abertura: a regra geral que vale para as 8 IAs.
    - "Salvar um .md da sua função": o formato obrigatório.
    - "Entregar por Pull Request": o fluxo de git.
    - "Padrão de qualidade": a identidade do jogo.
    - A seção da SUA função: o nome do arquivo e o conteúdo esperado.
    - Tabela final: confirme que seu nome e arquivo batem.

O QUE ENTREGAR

  Um único arquivo .md na raiz do repositório, com as 6 seções:
    1. Minha responsabilidade — o que é seu e o que NÃO é seu.
    2. Decisões que eu tomo — as escolhas da sua área e o critério.
    3. O que eu entrego — lista concreta de artefatos.
    4. Especificação — o conteúdo de verdade: números, tabelas,
       fórmulas, diagramas em texto. Esta seção é o corpo do documento.
    5. Dependências — de quem você precisa e quem precisa de você.
    6. Pendências e dúvidas — o que falta e o que o Director deve
       responder.

Qualidade esperada: outra IA deve conseguir continuar o seu trabalho
lendo somente o seu arquivo. Se estiver vago, o PR volta.

ENTREGA

  git add <seu-arquivo>.md
  git commit -m "<Função>: especificação inicial"
  git push origin arena/462f72bf-jogo
  gh pr create --base arena/462f72bf-jogo --title "<Função>: especificação" \
    --body "Arquivo criado: <seu-arquivo>.md"

LIMITES

  - Só o seu .md. Não edite o .md de outra IA, o EQUIPE.txt nem o
    INSTRUCOES.md.
  - Um PR = uma entrega. Sem misturar assuntos.
  - Conflito se resolve com rebase. Nunca force push.

AMBIGUIDADE

  Escolha a opção mais simples que funcione, registre na seção 2 do seu
  .md e siga. Não pare o projeto por dúvida pequena — só escale ao
  Director o que realmente bloqueia.
```

---

## SE A IA NÃO ACHAR O INSTRUCOES.md

Sintoma: ela diz que o arquivo não existe. Causa: está em branch antigo
ou no `main` (que ainda está no commit inicial).

Solução — mande este comando para ela:

```
git fetch origin
git checkout -B arena/462f72bf-jogo origin/arena/462f72bf-jogo
ls    # deve mostrar EQUIPE.txt, INSTRUCOES.md, PROMPT-INICIAL.md, README.md
```
