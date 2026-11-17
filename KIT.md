# KIT — replicar a equipe de IAs em outro repositório

Como montar o mesmo esquema (várias IAs trabalhando no mesmo repo sem se
atropelar) em qualquer projeto novo.

---

## A ideia em uma frase

**Um branch é a mesa de trabalho comum; o Pull Request é o único caminho
para colocar algo na mesa; uma IA (o Diretor) é a única que mergeia.**

Sem isso, duas IAs escrevem no mesmo arquivo e uma sobrescreve a outra.

---

## Passo a passo

### 1. Crie o repositório e uma branch de trabalho

```bash
# depois de criar o repo vazio no GitHub
git clone https://github.com/<usuario>/<repo>.git
cd <repo>
git checkout -b trabalho        # esta é a "mesa" — pode ter qualquer nome
```

Use sempre a mesma branch como base. No projeto JOGO ela se chama
`arena/462f72bf-jogo`. Aqui vamos chamar de `trabalho`.

### 2. Antes de convidar qualquer IA, commite os 3 arquivos-semente

Isto é obrigatório. IA que abre o repo e não acha os arquivos não faz nada.

```bash
# edite os 3 arquivos abaixo e então:
git add EQUIPE.txt INSTRUCOES.md PROMPT.txt
git commit -m "Semeia equipe: registro, instruções e prompt"
git push origin trabalho
```

O conteúdo de cada um está na seção seguinte.

### 3. Convide as IAs, uma por vez

Para cada IA, mande só isto (o conteúdo do `PROMPT.txt`):

```
Olhe o INSTRUCOES.md no branch trabalho e siga o que está pedindo.
```

Ela lê o registro, escolhe função e personagem livres, e abre PR.

### 4. Você (ou o Diretor) mergeia

```bash
gh pr list                    # ver o que chegou
gh pr view <n>                # ler antes
gh pr merge <n> --merge       # aprovar
```

Só depois do merge as outras IAs enxergam a entrega.

### 5. Repita

Cada IA lê o estado atual (`git fetch origin && git checkout trabalho`),
escreve o arquivo dela, abre PR. Ninguém edita o arquivo de outro.

---

## Os 3 arquivos-semente

### `EQUIPE.txt` — o registro

Lista de quem ocupa o quê. Serve para duas coisas: evitar que duas IAs
peguem a mesma função e mostrar o que ainda está livre.

```
=====================================================
 EQUIPE
=====================================================
  [x] <Função A> ......... <IA 1>
  [ ] <Função B> ......... livre
  [x] <Função C> ......... <IA 2>
  [ ] <Função D> ......... livre

 Reserva de personagens
 ----------------------
  [x] <Personagem 1> ..... <IA 1>
  [ ] <Personagem 2> ..... livre

-----------------------------------------------------
 INSTRUÇÕES PARA AS IAs
-----------------------------------------------------
 1. Faça commit e push na sua branch e abra um Pull Request
    para a branch trabalho.

Escolha apenas função e personagem que estejam como "livre".
Não apague nem altere a escolha da outra IA.
```

### `INSTRUCOES.md` — o contrato

O documento que todas leem. Precisa ter, no mínimo:

```markdown
# FUNÇÕES

## REGRA GERAL — todos leem antes de começar

### Entrega
Cada IA cria UM arquivo com o nome fixo da sua função (ex.: `03-funcao-x.md`).

Estrutura obrigatória:
  1. Minha responsabilidade (e o que NÃO é meu)
  2. Decisões que eu tomo
  3. O que eu entrego
  4. Especificação (o conteúdo real: números, tabelas, regras)
  5. Dependências
  6. Pendências e dúvidas

### Fluxo
  git fetch origin
  git checkout -B trabalho origin/trabalho
  git checkout -b <branch-da-sua-funcao>
  git add <seu-arquivo>
  git commit -m "<Função>: <o que entrou>"
  git push origin <branch-da-sua-funcao>
  gh pr create --base trabalho

### Limites
  - Edite APENAS os seus arquivos.
  - Não mexa no EQUIPE.txt.
  - Um PR = uma entrega.
  - Conflito se resolve com rebase, nunca force push.

### Padrão
  Se algo estiver ambíguo: escolha a opção mais simples, registre na
  seção 2 do seu .md e siga.

## 1. <Função A> — <IA 1>
**Arquivo:** `01-funcao-a.md`
O que faz, o objetivo, o que deve conter no .md.

## 2. <Função B> — <IA 2>
**Arquivo:** `02-funcao-b.md`
...
```

### `PROMPT.txt` — a frase de convite

```
Olhe o INSTRUCOES.md no branch trabalho e siga o que está pedindo.
```

Uma linha. A IA lê o resto sozinha.

---

## Nomes fixos: por que importa

Cada função tem **um arquivo com nome fixo** (`01-...md`, `02-...md`).
Nunca deixe a IA escolher o nome: dois nomes para a mesma coisa viram dois
arquivos e ninguém sabe qual vale.

O prefixo numérico também ordena a leitura quando alguém abre a pasta.

---

## Os tropeços que já aconteceram (e como evitar)

**A IA diz que não acha o arquivo.**
Está no `main` ou num branch antigo. Mande:
```
git fetch origin && git checkout -B trabalho origin/trabalho && ls
```

**A IA responde no branch dela e você não vê.**
Ela commitou mas não abriu PR. Confira com `gh pr list` e `git branch -r`.
PR aberto é o sinal de que terminou — deixe isso explícito nas instruções.

**A IA trabalha em versão velha do board.**
Sempre `git fetch` + checkout da base antes de começar. Sem isso ela lê
instrução desatualizada.

**O checkout local "volta sozinho" para o commit inicial.**
Sessões de IA podem reiniciar o workspace. Antes de escrever qualquer coisa:
`git fetch origin && git reset --hard origin/trabalho`.

**Duas IAs pegam a mesma função.**
Acontece quando as duas leem o registro no mesmo instante. Resolva no merge:
o primeiro PR que chegar fica, o segundo escolhe outra função livre.

---

## Quando não usar isto

- Equipe de 1 IA: é overhead puro, trabalhe direto no branch.
- Tarefas que se sobrepõem muito: se as funções precisam editar o mesmo
  arquivo, o esquema de arquivo-por-função não funciona. Divida por arquivo,
  não por tema.

---

## Ordem que funcionou

1. Registro vazio + instruções + prompt, commitado na base.
2. Cada IA escolhe função e personagem → PR → merge. (Etapa curta, só para
   provar que o canal funciona.)
3. Cada IA escreve o `.md` da função → PR → merge.
4. Só então alguém escreve código, depois de todas as especificações estarem
   aprovadas.

Pular a etapa 2 é o erro mais comum: sem ela você não sabe se as IAs
conseguem, de fato, commitar e abrir PR no seu repo.
