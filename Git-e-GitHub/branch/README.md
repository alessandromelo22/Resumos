# Branches (Ramificações):

## 🔹O que é?

**Uma branch deve representar uma unidade de trabalho.**

Exemplos:

- Uma funcionalidade
- Uma correção de bug
- Uma melhoria específica

> **Normalmente uma branch é criada para “resolver” uma issue.**
> 

## 🔹Como criar uma branch?

Comando git:

```java
git branch <nome-da-nova-branch>
```

→ nesse comado você apenas cria a nova branch, mas continua na sua atual.

## 🔹Trocar de branch

Para trocar de branch basta usar o comando:

```java
git checkout <nome-da-nova-branch>
```

## 🔹Criar e trocar para nova branch

Para criar e já trocar para a nova branch, basta usar o comando:

```java
git checkout -b <nome-da-nova-branch>
```

## 🔹Apagar uma branch

```java
git branch -d nome_do_branch
```

## 🔹Como trabalhar na mesma branch em maquinas diferentes?

Imagine o seguinte cenário:

- No **Desktop**, você criou a branch **`feature/pagina-de-contato`**
- Desenvolveu a funcionalidade e fez alguns commits
- Precisou trocar de máquina (ex.: Notebook) e quer continuar exatamente do ponto onde parou

Para isso, o fluxo correto é o seguinte.

---

### 1️⃣ Enviar a branch para o repositório remoto (GitHub)

Na **máquina onde a branch foi criada** (Desktop), após realizar os commits, você precisa **publicar a branch no repositório remoto**:

```bash
git push -u origin feature/pagina-de-contato
```

O `-u` cria o vínculo entre a branch local e a branch remota, facilitando futuros `git pull` e `git push`.

---

### 2️⃣ Atualizar as referências no outro computador

Na **outra máquina** (Notebook), primeiro entre no diretório do projeto e atualize as referências do repositório remoto:

```bash
git fetch origin
```

Esse comando **não altera código**, apenas informa ao Git quais branches existem no remoto.

---

### 3️⃣ Criar a branch local a partir da branch remota

Agora sim, você deve **criar a branch local baseada na branch remota**, e não uma branch vazia:

```bash
git checkout -b feature/pagina-de-contato origin/feature/pagina-de-contato
```

Com isso:

- A branch local é criada
- Ela já vem com todo o histórico e commits
- Ela fica automaticamente vinculada à branch remota

Pronto. Você já pode continuar o desenvolvimento normalmente.

---

### ❌ Por que `git pull` NÃO é o comando certo aqui?

Porque:

- `git pull` **só funciona sobre a branch atual**
- Ele **não cria branch nova**
- Ele só atualiza **o que você já tem localmente**

Se a branch **não existe localmente**, o `pull` não tem onde aplicar as alterações.

---

## 🔹Checklist pós-merge de Pull Request (fluxo padrão)

### 1️⃣ Merge realizado no GitHub

- Pull Request aprovado e mergeado na `main` (ou `develop`)
- Opcional: branch da feature deletada no GitHub

Até aqui, **somente o repositório remoto foi alterado**.

---

Em CADA máquina (Desktop, Notebook, etc.):

### 2️⃣ Ir para a branch principal

```bash
git checkout main
```

---

### 3️⃣ Atualizar a branch principal local

```bash
git pull
```

Agora sua `main` local contém o código que veio do merge.

---

### 4️⃣ Apagar a branch da feature localmente

```bash
git branch -d feature/pagina-de-contato
```

Esse comando:

- Só funciona se a branch **já foi mergeada**
- Evita apagar trabalho não integrado por acidente

---

### 5️⃣ Limpar referências remotas (opcional, mas recomendado)

Se você deletou a branch no GitHub:

```bash
git fetch --prune
```

Isso remove referências locais a branches remotas que não existem mais.
