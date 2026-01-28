# Stream API

## O que são Streams?

Um **Stream** **é uma abstração para processar sequências de dados (de coleções, arrays, I/O) de forma funcional, permitindo encadear operações como filtrar (filter), transformar (map) e reduzir (reduce) de maneira declarativa, eficiente e concisa,
sem armazenar os elementos, executando-os sob demanda em um pipeline de funções**.

> [!IMPORTANT]
> Não é uma estrutura de dados, mas sim um fluxo de operações aplicadas sequencialmente aos dados da fonte. 

## **Principais Conceitos:**

1. **Fonte de Dados:**
   - De onde os dados vêm (Listas, Arrays, I/O, Stream.of()).
2. **Pipeline:**
   - Uma cadeia de operações encadeadas.
3. **Operações Intermediárias:**
   - Retornam outro Stream e são "preguiçosas" (lazy), como `filter()`, `map()`, `sorted()`, `distinct()`.
4. **Operações Terminais:**
   - Consomem o Stream e produzem um resultado final ou efeito, como `collect()`, `forEach()`, `reduce()`, `sum()`, `count()`.

Exemplo:

```java
List<Integer> numeros = Arrays.asList(1, 2, 3, 4, 5, 6);
List<Integer> paresDobrados = numeros.stream() // 1. Fonte
                                     .filter(n -> n % 2 == 0) // 2. Intermediária: filtra pares
                                     .map(n -> n * 2)         // 3. Intermediária: dobra
                                     .collect(Collectors.toList()); // 4. Terminal: coleta
// paresDobrados será [4, 8, 12]

```
