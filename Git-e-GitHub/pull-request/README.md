# Pull Requests:

## O que é?

Uma **Pull Request** é um **pedido formal para integrar código de uma branch em outra**.

Em termos simples, ela diz:

> “Eu terminei um conjunto de mudanças e gostaria que elas fossem revisadas e incorporadas ao código principal.”
> 

Normalmente:

- **de:** uma branch de `feature` ou `fix`
- **para:** `main` ou `develop`

> [!IMPORTANT]
> Vale lembrar que enquanto a Pull Request estiver **aberta**, você pode continuar fazendo **novos commits na mesma branch**. E depois de satisfeito ai sim realizar o *merge* entre as branches

---

## 💡Como pensar corretamente: unidade de trabalho

A unidade de trabalho **não é o commit**, é a **funcionalidade / correção / melhoria**.

- **Issue** define *o que*
- **Branch** isola *o trabalho*
- **Commits** registram *o progresso*
- **Pull Request** entrega *o resultado*

### Regra de ouro (memorize isso)

> Issue → Branch → Commits → Pull Request → Merge

❌Nunca:

- Issue → Pull Request direto
- Trabalhar direto na `main`
- Misturar várias funcionalidades na mesma branch

---

## 🔗Como linkar uma pull request a uma issue?

Na descrição da Pull request, basta utilizar alguma dessas **palavras reservadas do GitHub** pra essa ocasião:

```markdown
Closes #numero da issue
Fixes #numero da issue
Resolves #numero da issue
```

Usando algumas desses e outras palavras reservadas do GitHub, você **_linka_ a Pull request a issue informada pelo numero**, e ao realizar o merge dessa Pull request, a issue *linkada* será **fechada automaticamente**.

Exemplo de Pull request com issues linkadas:

```markdown
Adiciona implementação de testes nas camadas de Service da entidade Usuario.

Resolves #34
```

Caso utilize apenas o `#numero da issue` ou uma `palavra qualquer #numero da issue`  dentro do pull request, também será linkado porém a **issue NÃO será fechada quando for realizado o merge**.

```markdown
Referencia #10
Conserta #7
#9
```

