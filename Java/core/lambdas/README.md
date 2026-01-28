# Lambdas λ

## 🔹O que é uma expressão lambda?

Uma lambda é uma **implementação inline de uma interface funcional** (uma interface que possui **apenas um método abstrato**).

Exemplo simples:

```java
(int a, int b) -> a + b
```

👉O exemplo representa uma função que recebe dois inteiros e retorna a soma.

## 🔹Interface funcional

Para usar lambdas, é obrigatório que exista uma **interface funcional**.

Como foi dito, uma Interface funcional é uma Interface que possui apenas **um método abstrato** (um método para ser implementado), conhecido também como conceito **SAM** (**Single Abstract Method**)

Exemplo:

```java
@FunctionalInterface
interface Soma {
    int calcular(int a, int b);
}
```

👉A anotação `@FunctionalInterface` não é obrigatória, mas ajuda o compilador a garantir que a interface continue sendo funcional.

## 🔹Para que servem as lambdas?

### 1. Reduzir código boilerplate

Antes (uso de classes anônimas):

```java
Soma s = new Soma() {
    @Override
    public int calcular(int a, int b) {
        return a + b;
    }
};

System.out.println(s.calcular(2, 3)); // 5
```

Com Lambda:

```java
Soma s = (a, b) -> a + b;
System.out.println(s.calcular(2, 3)); // 5
```

### 2. Trabalhar com coleções de forma funcional (Streams)

```java
List<Integer> numeros = List.of(1,2,3,4,5);

numeros.stream()
       .filter(n -> n %2 ==0)
       .map(n -> n *10)
       .forEach(System.out::println);
```

👉 Aqui, lambdas definem **o que fazer**, não **como iterar**.

### Algumas formas de estruturar funções Lambdas:

```java
() -> expressão

parâmetro -> expressão

(parâmetro) -> expressão

(parâmetro1, parâmetro2) -> expressão

() -> {
	//bloco
	return valor;
}
```
## 🔹Interfaces funcionais comuns no Java

O pacote `java.util.function` fornece várias interfaces prontas. Normalmente são vistas em métodos da Interface `Stream`, ou em alguns métodos da classe `Optional<T>`:

#### `Supplier<T> (”Fornecedor”):`

```java
@FunctionalInterface
public interface Supplier<T> {

    /**
     * Gets a result.
     *
     * @return a result
     */
    T get();
```

→ “A função não recebe argumentos, serve apenas para entregar (retornar) um objeto”.

#### `Consumer<T> (”Consumidor”):`

```java
@FunctionalInterface
public interface Consumer<T> {

    /**
     * Performs this operation on the given argument.
     *
     * @param t the input argument
     */
    void accept(T t);
```

→ “A função recebe um argumento, realiza a operação mas não retorna nada”.

#### `BiConsumer<T, U>:`

```java
@FunctionalInterface
public interface BiConsumer<T, U> {

    /**
     * Performs this operation on the given arguments.
     *
     * @param t the first input argument
     * @param u the second input argument
     */
    void accept(T t, U u);
```

→ “A função recebe dois argumentos, realiza a operação mas não retorna nada”.

#### `Predicate<T>`

```java
@FunctionalInterface
public interface Predicate<T> {

    /**
     * Evaluates this predicate on the given argument.
     *
     * @param t the input argument
     * @return {@code true} if the input argument matches the predicate,
     * otherwise {@code false}
     */
    boolean test(T t);
```

→ “A função recebe um argumento, realiza a operação e retorna um boolean sobre a operação”.

→ Muito útil para comparações

#### `BiPredicate<T, U>`

```java
@FunctionalInterface
public interface BiPredicate<T, U> {

    /**
     * Evaluates this predicate on the given arguments.
     *
     * @param t the first input argument
     * @param u the second input argument
     * @return {@code true} if the input arguments match the predicate,
     * otherwise {@code false}
     */
    boolean test(T t, U u);

```

→ “A função recebe dois argumentos, realiza a operação e retorna um boolean sobre a operação”.

→ Muito útil para comparações

#### `Function<T,R>`

```java
@FunctionalInterface
public interface Function<T, R> {

    /**
     * Applies this function to the given argument.
     *
     * @param t the function argument
     * @return the function result
     */
    R apply(T t);

```

→ “Recebe um argumento, realiza a operação e retornar um objeto, podendo ser o mesmo objeto apenas modificado ou outro objeto de tipo diferente. ”

#### `BiFunction<T, U, R>`

```java
@FunctionalInterface
public interface BiFunction<T, U, R> {

    /**
     * Applies this function to the given arguments.
     *
     * @param t the first function argument
     * @param u the second function argument
     * @return the function result
     */
    R apply(T t, U u);
```

→ “Recebe dois argumentos, realiza a operação e retornar um objeto, podendo ser o mesmo objeto apenas modificado ou outro objeto de tipo diferente. ”
