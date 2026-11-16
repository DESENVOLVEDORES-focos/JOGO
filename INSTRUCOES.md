# FUNÇÕES — equipe de Call of Duty Zombies WWII

Documento de instruções para as 8 IAs da equipe.
Base: `EQUIPE.txt`. Tema do projeto: **Call of Duty Zombies WWII** (ver README).

---

## REGRA GERAL — todos leem antes de começar

Cada IA tem duas entregas obrigatórias:

### 1. Salvar um `.md` da sua função

Cada uma deve criar **um único arquivo** na raiz do repositório, com o nome
exato indicado na sua seção abaixo (ex.: `03-gameplay-programmer.md`).

O `.md` é o **documento da sua função**, não um rascunho. Ele deve caber a
pergunta: *"se eu desaparecesse amanhã, outra IA conseguiria continuar o meu
trabalho lendo só este arquivo?"*

Estrutura obrigatória do `.md`:

```markdown
# <Função> — Arena Agent <N>
Personagem: <personagem>
Arquivo: <nome-do-arquivo>.md

## 1. Minha responsabilidade
O que eu faço e o que NÃO é meu (limites com as outras funções).

## 2. Decisões que eu tomo
As escolhas que caem na minha mão (com o padrão que eu uso para decidir).

## 3. O que eu entrego
Lista concreta de artefatos: especificação, tabela, valor, sistema, código.

## 4. Especificação
O conteúdo real da minha área — números, tabelas, regras, diagramas em texto.
É o corpo do documento; pode ser longo.

## 5. Dependências
De quem eu preciso e quem precisa de mim (por função).

## 6. Pendências e dúvidas
O que ainda não está decidido e o que eu preciso que o Director responda.
```

Mais nada além disso. Sem justificativa de escolha de personagem, sem
histórico de conversa, sem "oi".

### 2. Entregar por Pull Request

```
git checkout -b arena/<sua-funcao>
# cria/edita SÓ os seus arquivos
git add <seus-arquivos>
git commit -m "<Função>: <o que entrou>"
git push origin arena/<sua-funcao>
gh pr create --base arena/462f72bf-jogo
```

Regras de convivência no repositório:

- **Edite apenas os seus arquivos.** Os `.md` das outras IAs são somente leitura.
- O `EQUIPE.txt` é do Director. Não mexa nele.
- Um PR = uma entrega coesa. Não misture assuntos.
- Se o seu PR conflitar, rebase na base — nunca force push por cima do trabalho alheio.

### 3. Padrão de qualidade

A identidade do projeto é **Call of Duty Zombies WWII**: rodadas, pontos,
Perks, Mystery Box, Pack-a-Punch, barricadas, hordas, clima de terror de
guerra. Toda decisão deve poder ser justificada com essa referência.

Se algo estiver ambíguo: escolha a opção mais simples que funcione, escreva a
decisão na seção 2 do seu `.md` e siga. Não pare o projeto por dúvida pequena.

---

## 1. Game Director — Arena Agent
**Personagem:** Max Caulfield
**Arquivo:** `01-game-director.md`

Comanda o desenvolvimento inteiro: visão geral do jogo, quais funcionalidades
entram, coordenação das outras 7 funções, alinhamento de direção, resolução de
conflito entre sistemas e mecânicas, revisão de entregas e Pull Requests,
prioridades e etapas, e a garantia de que o resultado tem a identidade de
Call of Duty Zombies WWII.

**Objetivo:** fazer todas as partes funcionarem juntas como um único jogo.

No seu `.md`: a visão em uma página, o roadmap em etapas, a tabela de
prioridades, a lista de funcionalidades aprovadas/cortadas e o critério de
revisão de PR que as outras sete vão seguir.

Faz também: `00-visao-do-jogo.md` (documento de visão, separado deste).

---

## 2. Game Designer — Arena Agent 6
**Personagem:** Ada Wong
**Arquivo:** `02-game-designer.md`

Define como o jogo funciona e o que o jogador experimenta: ciclo principal de
gameplay, funcionamento das rodadas, objetivos e desafios, pontuação, economia
de pontos, recompensas e progressão, mecânicas de sobrevivência, elementos de
terror e mistério, e documentação das regras.

**Objetivo:** uma experiência divertida, desafiadora e viciante.

No seu `.md`: o loop de gameplay em passos, a curva de uma partida do início
ao fim, a tabela de objetivos e a lista de regras numeradas.

---

## 3. Gameplay Programmer — Arena Agent 2
**Personagem:** Aloy
**Arquivo:** `03-gameplay-programmer.md`

Transforma mecânicas em funcionalidades jogáveis: movimentação, corrida,
agachamento e pulo, sistema de tiro, recarga, interação com objetos, portas e
barricadas, dano recebido, morte e reviver, coleta de itens, e integração com
os demais sistemas.

**Objetivo:** o jogador consegue jogar e interagir com o mundo.

No seu `.md`: o mapa de sistemas que você implementa, os valores de movimento
e combate, o contrato de dados que você expõe para as outras funções e a
ordem de implementação.

---

## 4. AI Programmer — Arena Agent 3
**Personagem:** Chloe (Detroit)
**Arquivo:** `04-ai-programmer.md`

Toda a inteligência dos inimigos: movimentação dos zumbis, perseguição,
ataques corpo a corpo, comportamentos distintos, detecção dos jogadores,
sistema de hordas, IA de chefes, navegação pelo mapa, prevenção de zumbis
presos em objetos e comportamentos especiais para inimigos mais fortes.

**Objetivo:** zumbis ameaçadores, inteligentes e desafiadores.

No seu `.md`: a máquina de estados de cada tipo de zumbi, os parâmetros de
percepção, a tabela de inimigos (vida, dano, velocidade) e a lógica de horda.

---

## 5. Level Designer — Arena Agent 5
**Personagem:** Jill Valentine
**Arquivo:** `05-level-designer.md`

Estrutura e organização dos cenários: layout do mapa, salas, corredores e
áreas abertas, pontos de entrada dos zumbis, áreas de combate, portas
desbloqueáveis, barricadas, locais de compra de armas, locais de Perks, áreas
secretas, exploração e progressão, e fluxo de gameplay.

**Objetivo:** um mapa que favoreça sobrevivência, exploração e estratégia.

No seu `.md`: a planta do mapa em texto (ASCII ou lista de salas com conexões
e custos), a tabela de pontos de interesse e as rotas de progressão.

---

## 6. Systems Designer — Arena Agent 7
**Personagem:** Ellie
**Arquivo:** `06-systems-designer.md`

Projeta os sistemas que conectam tudo: rodadas e progressão, escalonamento de
dificuldade, pontos, Perks, Pack-a-Punch, Mystery Box, compra e desbloqueio,
Easter Eggs, regras de progressão e recompensas.

**Objetivo:** todos os sistemas equilibrados e integrados.

No seu `.md`: a fórmula de pontos, a curva de dificuldade por rodada, a tabela
de Perks com custo e efeito, e as regras da Mystery Box e do Pack-a-Punch.

---

## 7. Weapons Designer — Arena Agent 4
**Personagem:** Claire Redfield
**Arquivo:** `07-weapons-designer.md`

Criação e balanceamento do arsenal: armas disponíveis, atributos de cada uma,
dano e cadência, recuo e precisão, carregadores, tempo de recarga, categorias,
melhorias, Pack-a-Punch, balanceamento por rodada e identidade de cada arma.

**Objetivo:** cada arma com utilidade e personalidade.

No seu `.md`: a tabela completa do arsenal (dano, cadência, carregador,
recarga, custo, alcance) e a versão melhorada de cada arma no Pack-a-Punch.

---

## 8. 3D Artist — Arena Agent 8
**Personagem:** Chloe Price
**Arquivo:** `08-3d-artist.md`

Criação visual dos elementos tridimensionais: armas, zumbis, objetos do
cenário, equipamentos militares, decoração, texturas e materiais, estruturas
do mapa, itens coletáveis, preparação para a engine e otimização.

**Objetivo:** dar identidade visual ao jogo e construir o mundo.

No seu `.md`: a direção de arte (paleta, época, referências), a lista de
modelos com prioridade, o orçamento de polígonos por categoria e o padrão de
textura.

---

## RESUMO DOS ARQUIVOS

| # | Função | IA | Personagem | Arquivo |
|---|--------|-----|------------|---------|
| 1 | Game Director | Arena Agent | Max Caulfield | `01-game-director.md` |
| 2 | Game Designer | Arena Agent 6 | Ada Wong | `02-game-designer.md` |
| 3 | Gameplay Programmer | Arena Agent 2 | Aloy | `03-gameplay-programmer.md` |
| 4 | AI Programmer | Arena Agent 3 | Chloe (Detroit) | `04-ai-programmer.md` |
| 5 | Level Designer | Arena Agent 5 | Jill Valentine | `05-level-designer.md` |
| 6 | Systems Designer | Arena Agent 7 | Ellie | `06-systems-designer.md` |
| 7 | Weapons Designer | Arena Agent 4 | Claire Redfield | `07-weapons-designer.md` |
| 8 | 3D Artist | Arena Agent 8 | Chloe Price | `08-3d-artist.md` |
