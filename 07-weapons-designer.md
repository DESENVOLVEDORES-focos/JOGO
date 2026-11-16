# Weapons Designer — Arena Agent 4
Personagem: Claire Redfield
Arquivo: 07-weapons-designer.md

## 1. Minha responsabilidade

Eu defino **o arsenal inteiro**: quais armas existem, o que cada uma faz, quanto
custa, quanto pesa na mão do jogador e como cada uma muda quando entra no
Pack-a-Punch.

**É meu:**

- Lista fechada de armas (wall buys, Mystery Box, granadas, corpo a corpo, Wonder Weapon).
- Todos os atributos numéricos de cada arma: dano, cadência, carregador, reserva,
  recarga, precisão, recuo, alcance efetivo, tempo de ADS, tempo de troca.
- Dano por zona do corpo (cabeça, tronco, membro) e multiplicadores.
- Comportamento balístico: penetração em horda, queda de dano por distância.
- Curva de dano versus vida de zumbi por rodada (em conjunto com o Systems Designer).
- Versão Pack-a-Punch de cada arma: o que muda, nome novo e custo.
- Custo em pontos de cada arma na parede e o que sai da Mystery Box.
- Orçamento de munição por rodada: quantos tiros o jogador tem direito.

**NÃO é meu:**

- Implementar o sistema de tiro, hitscan, recarga, animação de recuo, troca de arma.
  → **Gameplay Programmer** (ele consome a minha tabela como contrato de dados).
- Fórmula de pontos, custo dos Perks, regras da Mystery Box e do Pack-a-Punch como
  *sistema* (preço, local, número de caixas, sorteio).
  → **Systems Designer**.
- Escala de vida/dano/velocidade dos zumbis.
  → **AI Programmer** (eu uso os valores dele, não os altero).
- Onde ficam os wall buys no mapa, corredores de tiro, distâncias reais na planta.
  → **Level Designer**.
- Modelo, textura, som, muzzle flash, animação de recarga.
  → **3D Artist** (e quem cuidar de áudio).
- Se a arma entra ou é cortada do escopo, e o balanceamento final entre funções.
  → **Game Director** (eu proponho, ele aprova).
- Curva de dificuldade, economia de pontos e progressão de rodadas como experiência.
  → **Game Designer** + **Systems Designer**.

Regra de fronteira: **eu escrevo números e regras, não código.** Se o Gameplay
Programmer precisar inventar um valor que está na minha tabela, o erro é meu.

## 2. Decisões que eu tomo

Meus critérios, na ordem em que eu aplico:

1. **Toda arma tem um custo.** Nada é estritamente melhor que tudo. Se uma arma
   domina em todos os cenários, ela perde algo: munição cara, recarga longa, peso
   (velocidade de movimento) ou alcance.
2. **Teto de dano, não teto de tempo.** O jogador mata zumbi com **dano por tiro ×
   cadência × munição**, e a arma que falha é a que falha em **uma** dessas três.
3. **A parede é a espinha dorsal.** O jogador deve conseguir chegar à rodada 15 com
   armas de parede. Mystery Box e Pack-a-Punch são aceleradores, não requisitos.
4. **Cada arma tem identidade em uma frase.** Se eu não consigo resumir a arma em
   uma frase de vantagem e uma de fraqueza, ela não entra no arsenal.
5. **CoD Zombies WWII é o padrão.** Rodadas, pontos, Pack-a-Punch, caixa — os
   números são calibrados para uma partida de 4 jogadores que começa com pistola e
   termina com arsenal melhorado por volta da rodada 20.
6. **Dano cresce mais devagar que a vida do zumbi.** Por isso o jogador é empurrado
   para upgrades; Pack-a-Punch e Wonder Weapon existem para fechar essa lacuna.
7. **Empate se resolve pelo mais simples.** Em dúvida entre duas armas parecidas,
   corta uma e diferencia a outra. Arsenal enxuto é mais legível.
8. **Nada de arma que mate sem o jogador mirar.** Sem auto-aim, sem explosivo de
   área que limpe horda sozinho fora da Wonder Weapon.

Decisões que já tomei neste documento (para não ficar em aberto):

- **Calibre de referência:** dano expresso em "ponto de dano" (PD), vida de zumbi
  em PD. 1 tiro de pistola = 1 PD base.
- **Sem recuo vertical acumulativo em armas automáticas de parede** — recuo é por
  tiro, com recuperação total. Arma de caixa pode ter padrão de recuo próprio.
- **Munição de parede é comprada por carregador**, não por bala.
- **Corpo a corpo não é arma primária**, é recurso de emergência com dano fixo por
  rodada (ver 4.9).
- **Pack-a-Punch não muda o tipo de arma**, só os números e o nome. A exceção é a
  Wonder Weapon, que ganha um efeito novo.
- **Nenhuma arma tem dano explosivo de área fora de granadas e Wonder Weapon.**

## 3. O que eu entrego

1. `07-weapons-designer.md` (este arquivo): a especificação completa do arsenal.
2. **Tabela mestre do arsenal** — 11 armas de fogo + corpo a corpo + 3 granadas,
   com os 9 atributos de cada (seção 4.1).
3. **Tabela de custos** de wall buy por arma e por munição (seção 4.2).
4. **Tabela de dano por zona** e multiplicadores (seção 4.3).
5. **Curva de dano × vida de zumbi por rodada**, com a rodada de quebra de cada
   arma (seção 4.4).
6. **Tabela Pack-a-Punch completa**: nome, atributos novos, custo (seção 4.5).
7. **Tabela de granadas e corpo a corpo** (seções 4.9 e 4.10).
8. **Regras de munição e economia de tiro por rodada** (seção 4.6).
9. **Contrato de dados** em JSON para o Gameplay Programmer implementar direto
   (seção 4.7).
10. **Lista de armas cortadas** do escopo inicial, com motivo (seção 4.11).

## 4. Especificação

### 4.0 Unidades e convenções

| Termo | Significado |
|---|---|
| **PD** | Ponto de Dano. 1 PD = 1 tiro de Pistola M1911 no tronco. |
| **RPM** | Cadência em tiros por minuto. |
| **Cadência efetiva** | RPM ÷ 60 = tiros por segundo (usado nas contas). |
| **Carregador** | Balas por carregador. |
| **Reserva** | Balas totais disponíveis **sem** comprar munição (carregador + coldre). |
| **Recarga** | Segundos do início ao fim da recarga completa. |
| **Recarga tática** | Recarga com munição ainda no carregador (−25% de tempo). |
| **Precisão** | Dispersão em graus a 10 m (menor = melhor). |
| **Recuo** | Deslocamento vertical da mira por tiro, em graus, com recuperação total. |
| **Alcance efetivo** | Distância em que a arma mantém 100% do dano. |
| **Queda** | % de dano perdido por cada 5 m além do alcance efetivo. |
| **TTK** | Tempo para matar um zumbi de vida padrão da rodada. |

Premissas fixas (não são minhas, mas eu dependo delas): o mapa tem corredores de
**8 a 20 m** e áreas abertas de até **35 m**. Um jogador a pé cobre ~4,5 m/s andando.

### 4.1 Tabela mestre do arsenal

Coluna **Fonte**: `P` = wall buy, `C` = Mystery Box.

| # | Arma | Cat. | Fonte | Dano (PD) | RPM | Carregador | Reserva | Recarga (s) | Precisão (°) | Recuo (°) | Alcance (m) | Queda |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | M1911 | Pistola | P (inicial) | 40 | 350 | 8 | 80 | 1,9 | 2,0 | 0,6 | 15 | 15% |
| 2 | Kar98k | Rifle de ferrolho | P | 320 | 52 | 5 | 50 | 3,2 | 0,8 | 4,0 | 40 | 5% |
| 3 | M1 Garand | Rifle semiauto | P | 170 | 300 | 8 | 64 | 2,4 | 1,2 | 1,6 | 30 | 8% |
| 4 | Thompson M1928 | SMG | P | 45 | 780 | 20 | 200 | 2,3 | 2,6 | 0,7 | 14 | 20% |
| 5 | MP40 | SMG | P | 50 | 620 | 32 | 224 | 2,6 | 2,4 | 0,8 | 16 | 18% |
| 6 | BAR M1918 | Metralhadora leve | P | 110 | 480 | 20 | 160 | 3,0 | 1,8 | 1,5 | 26 | 12% |
| 7 | Browning M1919 | Metralhadora | P | 70 | 620 | 50 | 250 | 5,5 | 3,4 | 1,0 | 22 | 14% |
| 8 | Trench Gun | Escopeta | P | 26 × 8 bagos = 208 | 68 | 6 | 42 | 3,4 | 9,0 (cone) | 3,2 | 9 | 40% |
| 9 | MG42 | Metralhadora pesada | C | 95 | 900 | 75 | 300 | 5,8 | 3,8 | 1,2 | 24 | 12% |
| 10 | PPSH-41 | SMG | C | 55 | 1000 | 71 | 284 | 3,0 | 3,2 | 0,9 | 15 | 20% |
| 11 | Panzerschreck | Antitanque | C | 900 (explosão) | 25 | 1 | 6 | 4,5 | 1,0 | 6,0 | 30 | 0% |
| 12 | Ray Gun (MK1) | Wonder Weapon | C | 600 + 200 (splash 2 m) | 200 | 10 | 40 | 3,0 | 1,4 | 1,0 | 25 | 0% |

Notas dos números:

- **M1911** é a arma de nascença: 8 tiros, 80 de reserva. Mata um zumbi na rodada 1
  com 3 tiros no tronco (120 PD > 100 de vida). Na rodada 6 ela já pede 5 tiros —
  é o relógio que empurra o jogador para a parede.
- **Kar98k** é o "abre horda": 320 PD mata um zumbi com um tiro no tronco até a
  rodada 13, e **penetra 3 zumbis** em fila (ver 4.3), com dano caindo 30% por corpo.
- **M1 Garand** é a arma de precisão acessível: semiauto, 8 tiros, recarga com
  clipe que cai no chão (o clipe vazio é um detalhe de identidade da WWII).
- **Thompson** é a SMG de entrada: cadência alta, dano baixo, recarga média. Boa
  até a rodada 10; o **MP40** troca cadência por dano e carregador maior (32), com
  recarga pior — é o par "horda × sustentação".
- **BAR** é a automática de dano médio a longa distância; **Browning** é a de
  sustentação: 50 no carregador, mas recarga de 5,5 s e precisão ruim. Escolha real.
- **Trench Gun** é a arma do corredor: letal a menos de 9 m, inútil a 15 m.
- **MG42** é a arma de caixa que sustenta a rodada 20+: 900 RPM e 75 no carregador,
  punida pela recarga de 5,8 s e pela mobilidade baixa (-12% de velocidade).
- **Panzerschreck** é a resposta a horda agrupada: 1 tiro por vez, 6 de reserva, e
  **dano em aliados** se o jogador for imprudente (60% do dano de fogo amigo).
- **Ray Gun** é a Wonder Weapon desta spec: limpa a horda, mas tem munição contada
  e só sai da caixa.

### 4.2 Custos e economia de compra

| Arma | Custo na parede (pontos) | Custo do carregador | Custo da reserva cheia |
|---|---|---|---|
| M1911 | 100 | 60 | 200 |
| Kar98k | 500 | 100 | 300 |
| M1 Garand | 600 | 130 | 350 |
| Thompson M1928 | 1 200 | 200 | 500 |
| MP40 | 1 000 | 180 | 450 |
| BAR M1918 | 1 800 | 280 | 650 |
| Browning M1919 | 3 000 | 400 | 900 |
| Trench Gun | 1 500 | 220 | 550 |
| Corpo a corpo (faca) | — | — | melhora na parede por 3 000 |

Regras de compra:

1. Só se compra munição de arma que o jogador **tem** na mão ou no coldre.
2. Munição de parede repõe até 2 carregadores por compra; não enche a reserva de uma vez.
3. **Arma de caixa não vende munição na parede** — só a própria caixa (quando o
   jogador a re-sorteia) ou o Pack-a-Punch (que recompõe a reserva ao melhorar).
4. Preço é fixo por rodada; **não escala**. A economia escala pelo lado da renda
   (pontos por zumbi), que é do Systems Designer.
5. Teto de pontos para compra: nenhum. Se o jogador tem 20 000 pontos, ele gasta.

### 4.3 Dano por zona do corpo

| Zona | Multiplicador |
|---|---|
| Cabeça | × 2,0 (× 3,5 em rifle de ferrolho e Ray Gun) |
| Tronco | × 1,0 |
| Membro (braço/perna) | × 0,6 |
| Explosão (centro) | × 1,0 do valor de explosão |
| Explosão (borda, raio +50%) | × 0,5 |

**Penetração** (fila de zumbis):

| Arma | Zumbis atravessados | Dano no 2º / 3º |
|---|---|---|
| Kar98k | 3 | 70% / 40% |
| M1 Garand | 2 | 65% |
| Browning, MG42 | 2 | 60% |
| Todas as outras | 1 | — |

**Corpo a corpo** nunca tem multiplicador de zona; é dano fixo.

### 4.4 Curva de dano × vida do zumbi por rodada

Vida do zumbi padrão (referência da AI Programmer, eu **não** altero aqui):

`Vida(rodada) = 100 × 1,10^(rodada − 1)` → R1 = 100, R5 = 146, R10 = 236, R15 = 380, R20 = 612, R25 = 986.

Tiros para matar no tronco (`N = ceil(Vida / Dano)`):

| Arma | R1 | R5 | R10 | R15 | R20 | Rodada em que passa de 8 tiros |
|---|---|---|---|---|---|---|
| M1911 (40) | 3 | 4 | 6 | 10 | 16 | **R14** |
| Thompson (45) | 3 | 4 | 6 | 9 | 14 | **R15** |
| MP40 (50) | 2 | 3 | 5 | 8 | 13 | **R16** |
| M1 Garand (170) | 1 | 1 | 2 | 3 | 4 | R29 |
| Kar98k (320) | 1 | 1 | 1 | 2 | 2 | R36 |
| Trench Gun (208 no tronco) | 1 | 1 | 2 | 2 | 3 | R31 |
| BAR (110) | 1 | 2 | 3 | 4 | 6 | **R24** |
| Browning (70) | 2 | 3 | 4 | 6 | 9 | **R20** |
| MG42 (95) | 2 | 2 | 3 | 4 | 7 | **R23** |
| Ray Gun (600) | 1 | 1 | 1 | 1 | 2 | R42 |

**Leitura da tabela (é o coração da minha entrega):**

- A **rodada 14–16 é o "vale" do jogo**: as armas de parede de entrada (pistola e
  SMGs) passam de 8 tiros por zumbi. É aqui que o jogador precisa de caixa,
  Pack-a-Punch ou de um Perk de dano. Esse vale é intencional.
- A **automação sustenta até ~R20–R24** (Browning quebra na R20, MG42 na R23, BAR
  na R24); a partir daí só Pack-a-Punch e Wonder Weapon mantêm o ritmo.
- Nenhuma arma de parede "vira inútil" — ela só fica cara em tempo. Isso preserva
  a economia do jogo.
- Zumbi especial (mais forte) usa a mesma curva com vida × 4 e é tratado pela
  AI Programmer; eu garanto que o Arsenal tem resposta para ele (ver 4.5).

### 4.5 Pack-a-Punch

Custo: **5 000 pontos** (definido pelo Systems Designer; eu calibro os efeitos).
Regra: uma arma por uso, o jogador escolhe qual. Ao melhorar, **a reserva é
recomposta ao máximo**.

| Arma | Nome melhorado | Dano | RPM | Carregador | Reserva | Recarga | Efeito novo |
|---|---|---|---|---|---|---|---|
| M1911 | **M1911-A1 "Vingança"** | 90 | 420 | 12 | 144 | 1,6 | Granada de concussão na recarga tática (1×/rodada) |
| Kar98k | **Kar98k "Sentinela"** | 700 | 60 | 8 | 80 | 2,8 | Penetra 5 zumbis |
| M1 Garand | **M1 "Testamento"** | 320 | 340 | 12 | 96 | 2,0 | Headshot causa explosão de 120 PD em 1,5 m |
| Thompson | **Thompson "Ceifadeira"** | 80 | 900 | 30 | 300 | 2,0 | +25% de dano em zumbi a menos de 8 m |
| MP40 | **MP40 "Correio"** | 85 | 700 | 45 | 315 | 2,2 | Recarga tática não consome munição da reserva |
| BAR | **BAR "Muralha"** | 190 | 520 | 30 | 240 | 2,6 | Penetra 3; -10% de recuo |
| Browning | **Browning "Sermão"** | 130 | 700 | 75 | 375 | 4,2 | Carregador não recarrega se ficar com 1 bala |
| Trench Gun | **Trench "Juízo"** | 44 × 8 = 352 | 78 | 8 | 56 | 2,9 | Bagos incendiários: 20 PD/s por 3 s |
| MG42 | **MG42 "Tempestade"** | 170 | 950 | 100 | 400 | 4,8 | 30% dos tiros causam supressão (zumbi para 0,6 s) |
| PPSH-41 | **PPSH "Enxame"** | 95 | 1050 | 100 | 400 | 2,6 | Recarga 40% mais rápida com carregador cheio |
| Panzerschreck | **Panzerschreck "Caim"** | 3200 | 30 | 1 | 10 | 4,0 | Raio de explosão +50% |
| Ray Gun | **Ray Gun MK2 "Aurora"** | 950 + 300 splash | 240 | 14 | 56 | 2,6 | Splash causa 40% de dano extra em cadeia (raio 2,5 m) |

Regras do Pack-a-Punch:

1. Arma melhorada **não volta** ao estado original nesta partida.
2. Nome e efeito são fixos — não é aleatório.
3. O efeito novo sempre **reforça a identidade** da arma (a Kar98k penetra mais,
   a Browning sustenta mais), nunca transforma a arma em outra categoria.
4. Arma melhorada **sempre compra munição** no Pack-a-Punch por 1 500 pontos.

### 4.6 Regras de munição e economia de tiro

1. **Orçamento de tiro por rodada.** Assumindo 6 zumbis na rodada 1, crescendo
   +2 por rodada: na rodada 10 são ~24 zumbis. Com MP40 (5 tiros/zumbi) são 120
   tiros; o jogador carrega 256. Dois carregadores de folga por rodada é a meta.
   *(Premissa de contagem de zumbis: provisória — o número oficial é do Game
   Designer; se ele mudar, eu recalibro esta regra.)*
2. **A munição é o recurso, não a vida.** O jogador deve comprar munição entre
   rodadas, não no meio do tiroteio.
3. **Reserva nunca é infinita.** Toda compra tem custo crescente em oportunidade
   (pontos que iriam para porta, Perk ou caixa).
4. **Recarregar cancelável** por corrida/troca, com a munição do carregador antiga
   perdida. Isso pune recarregar no pior momento e é intencional.
5. **Não há auto-recarga.** A arma vazia só volta a atirar após recarga completa.
6. **Granada é limitada a 3 por jogador**, recompradas por 500 pontos na parede.
7. **Arma de caixa tem 30% de chance de vir com munição reduzida** (design de tensão:
   o jogador pode pegar uma MG42 com 40 balas).

### 4.7 Contrato de dados (para o Gameplay Programmer)

Formato sugerido: um único arquivo `weapons.json` na raiz. Eu entrego o conteúdo,
ele implementa o carregamento. Campos obrigatórios por arma:

```json
{
  "id": "mp40",
  "nome": "MP40",
  "categoria": "smg",
  "fonte": "parede",
  "custo": 1000,
  "custo_municao": 180,
  "dano": 50,
  "dano_cabeca_multiplicador": 2.0,
  "dano_membro_multiplicador": 0.6,
  "rpm": 620,
  "carregador": 32,
  "reserva": 224,
  "recarga_s": 2.6,
  "recarga_tatica_s": 1.95,
  "precisao_graus": 2.4,
  "recuo_graus": 0.8,
  "alcance_m": 16,
  "queda_por_5m": 0.18,
  "penetracao": 1,
  "modo": "auto",
  "mobilidade_multiplicador": 1.0,
  "pack_a_punch": {
    "nome": "MP40 Correio",
    "dano": 85,
    "rpm": 700,
    "carregador": 45,
    "reserva": 315,
    "recarga_s": 2.2,
    "efeito": "recarga_tatica_gratis"
  }
}
```

Regras do contrato:

- **Nenhum valor numérico de arma pode estar hardcoded no código.** Tudo vem daqui.
- `id` é estável e em `snake_case`; o nome exibido é `nome`.
- `modo` aceita `semi`, `auto`, `ferrolho`, `pump`, `lancamento`.
- Se o Gameplay Programmer precisar de um campo que não está aqui, ele me pede;
  eu adiciono e versiono o arquivo. Ninguém inventa valor no código.

### 4.8 Ordem de implementação (para o Gameplay Programmer)

1. M1911 + M1 Garand (hitscan básico, semi) — valida o pipeline.
2. Kar98k (ferrolho, penetração, zoom de mira).
3. Thompson e MP40 (auto, recuo, recarga).
4. Trench Gun (cone de bagos).
5. Compra na parede (arma e munição) + troca de arma.
6. Browning e BAR (automáticas pesadas, mobilidade).
7. Pack-a-Punch: aplicar modificador sobre a arma equipada.
8. Caixa: MG42, PPSH, Panzerschreck.
9. Ray Gun (projétil, splash, cadeia no MK2).

### 4.9 Granadas

| Granada | Dano | Raio | Quantidade | Custo | Regra |
|---|---|---|---|---|---|
| Stielhandgranate | 250 (centro) | 3,5 m | 3 | 500 | Cozinha 2 s; cozinhar até o fim machuca o jogador |
| Granada de fumaça | 0 | 4 m | 2 | 300 | Bloqueia a detecção da IA por 4 s (a AI Programmer implementa) |
| Granada de concussão | 60 | 3 m | 2 | 400 | Zumbi atordoado 2 s |

### 4.10 Corpo a corpo

| Nível | Dano | Alcance | Golpes/s | Custo | Observação |
|---|---|---|---|---|---|
| Faca padrão | 90 | 1,5 m | 1,0 | — | Recarga de golpe de 0,9 s |
| Faca melhorada (parede) | 250 | 1,7 m | 1,2 | 3 000 | Substitui a padrão permanentemente |
| Golpe com a arma na mão | 60 | 1,4 m | 1,0 | — | Alternativa quando se está recarregando |

Regra: no máximo **um** golpe corpo a corpo por segundo. Corpo a corpo não é
alternativa ao tiro em rodada alta — é fuga.

### 4.11 Armas cortadas do escopo inicial

| Arma cogitada | Motivo do corte |
|---|---|
| FG42 | Redundante com a BAR: mesmo papel, números parecidos. Arsenal enxuto. |
| STG-44 | Sobreposição com M1 Garand automática; vira ruído de escolha. |
| Springfield (sniper) | Redundante com Kar98k; ferrolho duplicado confunde a leitura. |
| Luger | Redundante com M1911 como pistola inicial. |
| Lança-chamas | Dano contínuo quebra a economia de munição (era do Systems + AI). Fica para DLC. |
| Baioneta | Corpo a corpo já tem 3 níveis; a baioneta criava um quarto sistema sem ganho. |
| Rifle antitanque PTRS | O Panzerschreck já cobre o papel de anti-horda. |

Critério: **cortei tudo que não tinha identidade própria em uma frase.**

## 5. Dependências

| Função | De quem eu preciso | Quem precisa de mim |
|---|---|---|
| **Game Director** | Aprovação do escopo do arsenal, cortes e prioridade de implementação. | Minha tabela fechada e a lista de cortes para o roadmap. |
| **Game Designer** | Curva de uma partida, quantidade de zumbis por rodada, ritmo de tensão. | O arsenal como ferramenta da experiência (o que o jogador pode fazer em cada fase). |
| **Gameplay Programmer** | Que ele leia o `weapons.json` e não invente valores; me avise se um campo faltar. | O contrato de dados (4.7) e a ordem de implementação (4.8). |
| **AI Programmer** | Vida, dano, velocidade e número de zumbis por rodada/tipo. | Os números de dano para ele calibrar a vida relativa (ex.: especial = ×4 vida). |
| **Level Designer** | Distâncias reais de corredor e área aberta na planta; onde os wall buys podem ficar. | O alcance efetivo de cada arma, para ele posicionar as armas no lugar certo do mapa. |
| **Systems Designer** | Custo do Pack-a-Punch (5 000), regras da Mystery Box, economia de pontos. | A tabela Pack-a-Punch (4.5), custos de parede (4.2) e a curva de quebra por rodada (4.4). |
| **3D Artist** | Modelo, textura e animação de recarga coerentes com os nomes (ex.: clipe do Garand). | A lista de armas com prioridade visual e o tamanho/massa de cada uma. |

## 6. Pendências e dúvidas

Coisas que eu preciso que o **Game Director** responda:

1. **Confirma o arsenal de 12 armas?** (8 de parede + 4 de caixa). Se ele quiser
   menos para o escopo inicial, eu corto pela ordem da seção 4.11.
2. **A Ray Gun é a única Wonder Weapon do jogo** ou haverá uma segunda por volta da
   rodada 20? Isso muda a curva da rodada 25+.
3. **O escopo tem 1 mapa só?** Se houver mais de um, preciso que o Level Designer me
   dê as distâncias de cada um antes de calibrar alcance.

Coisas que eu preciso que o **Systems Designer** responda:

4. **O Pack-a-Punch custa 5 000 e melhora uma arma por uso — confirma?**
5. **A Mystery Box custa 950 por sorteio?** Eu usei "quando o jogador a re-sorteia"
   na regra 4.2, preciso do número fechado.
6. **Existe Perk que aumenta dano de arma?** Se sim, o multiplicador entra **antes**
   do meu cálculo de tiros para matar e a tabela 4.4 precisa de uma coluna nova.

Coisas que eu preciso que o **AI Programmer** responda:

7. Os **tipos de zumbi** e o multiplicador de vida de cada um (especial, chefe).
   Eu assumi especial = ×4 vida e chefe = ×15, mas não é meu número.

Coisas que eu preciso que o **Level Designer** responda:

8. Qual a maior distância de tiro limpo do mapa? Eu calibrei o Kar98k para 40 m,
   mas se o mapa não passa de 25 m, eu troco o rifle de ferrolho por outra arma.

Em aberto, decisão minha pendente (menor):

9. A Trench Gun tem 26 PD × 8 bagos, mas o **bago** que erra não faz nada. Se o
   Gameplay Programmer achar pesado simular 8 bagos, eu reduzo para 4 bagos de
   52 PD — mesma matemática, metade do custo de simulação.
10. A recarga tática de 25% mais rápida é uma **regra global** ou só de algumas
    armas? Hoje apliquei global; posso restringir se conflitar com a animação.
