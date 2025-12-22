# Leitura de arquivos usando o OpenCSV

Quando trabalhamos com CSV manualmente, enfrentamos problemas como:

- vírgulas dentro de campos (`Alessandro, Melo` → isso quebra o `split(",")`)
- campos entre aspas (`"João da Silva"`)
- campos com quebra de linha interna
- campos vazios
- escapes
- CSV com delimitadores diferentes (ponto e vírgula, tab, pipeline)

**OpenCSV resolve tudo isso automaticamente.**

## Componentes principais da biblioteca:

### 🔹CSVReader

→ O `CSVReader` é a classe principal do OpenCSV usada para **ler arquivos CSV já interpretados**. Permite trabalhar com os **dados brutos**.

**O que ele faz:**

- Lê o arquivo
- Converte bytes para caracteres (através de um `Reader` que você passa)
- Interpreta o **formato CSV padrão**:
    - separador padrão → vírgula (`,`)
    - caractere de aspas padrão → aspas duplas (`””`)
    - escape padrão → barra invertida (`\`)
    - Espaços após o separador NÃO são ignorados automaticamente
    - etc…
- Retorna **linhas já interpretadas e divididas em colunas**, como `String[]` (cada posição é uma coluna)

**Características:**

- **Baixo nível:** você trabalha com **os dados brutos manualmente**.
- Usado “por cima” de um `Reader` (`FileReader` ou `InputStreamReader` + `FileInputStream`) assim como o `BufferedReader`
- Usa um **`CSVParser` padrão internamente**.
- Pode ser configurado através do `CSVReaderBuilder`

Exemplo:

```java
CSVReader reader = new CSVReader(new InputStreamReader(new FileInputStream("arquivoTeste.csv"), StandardCharsets.UTF_8));
String[] linha;
while ((linha = reader.readNext()) != null) {
    System.out.println(Arrays.toString(linha));
}
```

**👉Ou seja:**

**CSVReader = BufferedReader + CSVParser padrão embutido**


### 🔹CSVParser

→ É a classe responsável por **executar o *parsing* da linha**. 

**O que ele faz:**

- É a real responsável por:
    - dividir a linha pelos separadores (vírgula por padrão);
    - tratar aspas, caracteres de escape, campos com vírgulas dentro de aspas, etc.;
    - retorna um **`String[]`** contendo cada coluna.

**Características**

- É usado **internamente** pelo `CSVReader`.
- Pode ser configurado através da classe `CSVParserBuilder`.

Exemplo:

```java
linha:  João,25,"São Paulo"
retorno: ["João", "25", "São Paulo"]
```

Principais métodos

| Método | Descrição |
| --- | --- |
| **`parseLine(String line)`** | Converte uma linha CSV em `String[]` respeitando delimitadores e aspas. |
| **`parseLineMulti(String line)`** | Igual ao anterior, mas permite finalizar campos *multiline* (quebras de linha). |
| **`getSeparator()`** | Retorna qual é o delimitador atual (padrão: vírgula). |
| **`getQuotechar()`** | Retorna o caractere de aspas configurado. |
| **`getEscape()`** | Retorna o caractere de escape usado. |

👉Ou seja:  
O **`CSVParser`** faz automaticamente todo o **tratamento de uma linha CSV** que, em Java puro, você teria que **programar na mão**.

### 🔹CSVParserBuilder

→ Classe auxiliar que permite a criação de um **`CSVParser` configurável**.

**O que ela faz:**

- Permite criar um `CSVParser` configurado, permitindo definir:
    - qual é o delimitador (`,`, `;`, `|` etc.)
    - qual caractere indica aspas (`””`, `’’`)
    - qual caractere é usado para escape (`\`)
    - como tratar campos vazios
    - se valores podem ter múltiplas linhas
    - e outras regras específicas do formato CSV

Como essas configurações podem variar muito dependendo do arquivo, o OpenCSV criou a classe **`CSVParserBuilder`** permitindo configurar tudo isso de forma fluida (estilo *builder*).

**Características:**

- Sempre é passado para um `CSVReaderBuilder`.

Exemplo:

```java
CSVParser parser = new CSVParserBuilder()
        .withSeparator(';') // define qual é o delimitador
        .withIgnoreQuotations(false) //define se deve ignorar aspas ou não
        .withIgnoreLeadingWhiteSpace(true) // define se deve ignorar espaços antes de valores
        .build();
```

Principais métodos:

| Método | Para que serve | Exemplo |
| --- | --- | --- |
| **`withSeparator(char separator)`** | Define o delimitador do CSV (`,`, `;`, etc.) | `withSeparator(;)` |
| **`withQuoteChar(char quoteChar)`** | Define o caractere de aspas usado no CSV | `.withQuoteChar('"')` |
| **`withEscapeChar(char escapeChar)`** | Define o caractere de escape | `.withEscapeChar('\\')` |
| **`withIgnoreLeadingWhiteSpace(boolean ignore)`** | Ignora espaços antes de valores (ex.: `"  valor"`) | `.withIgnoreLeadingWhiteSpace(true)` |
| **`withIgnoreQuotations(boolean ignore)`** | Faz o parser ignorar aspas (trata tudo como texto literal) | `.withIgnoreQuotations(true)` |
| **`withFieldAsNull(CSVReaderNullFieldIndicator indicator)`** | Controla como campos vazios serão mapeados para `null` | `.withFieldAsNull(CSVReaderNullFieldIndicator.EMPTY_SEPARATORS)` |
| **`withStrictQuotes(boolean strict)`** | Só aceita conteúdo dentro de aspas como campo válido | `.withStrictQuotes(true)` |
| **`withMultiLine(boolean multi)`** | Permite campos que contêm quebras de linha | `.withMultiLine(true)` |
| **`build()`** | Cria e retorna um `CSVParser` configurado | `.build()` |

### 🔹CSVReaderBuilder

→ É um *builder* utilizado para criar um `CSVReader` de forma **configurável**.

**O que ela faz:**

- Permite configurar:
    - número de linhas para pular (ex: cabeçalho)
    - separador
    - aspas
    - regras específicas do *parser* (através da classe `CSVParserBuilder`).
    - escape de caracteres

Exemplo:

```java
CSVReader reader = new CSVReaderBuilder(new InputStreamReader(new FileInputStream("arquivoTeste.csv"), StandardCharsets.UTF_8))
        .withSkipLines(1) //define quantas linhas pular.
        .withCSVParser(new CSVParserBuilder() // define como deve ser feito o parsing
            .withSeparator(';')
            .withIgnoreQuotations(false)
            .build()
        ).build();
```

**Principais métodos:**

| Método | Descrição |
| --- | --- |
| **`withSkipLines(int lines)`** | Pula N linhas antes de começar a ler (ex.: pular cabeçalho). |
| **`withCSVParser(CSVParser parser)`** | Define um ***parser* personalizado (delimitador, aspas etc)**. |
| **`withFieldAsNull(CSVReaderNullFieldIndicator indicator)`** | Controla como campos vazios são tratados. |
| **`withKeepCarriageReturn(boolean)`** | Mantém "\r" em campos se desejar. |
| **`build()`** | Cria e retorna um `CSVReader` configurado. |

👉Ou seja:

Você cria um **CSVReader altamente customizado**.

# Mapeando automático com `CsvToBean` e `CsvToBeanBuilder`

### 🔹`CsvToBean`:

→ Classe usada para converter as linhas de um CSV **diretamente em objetos Java (beans)** quando você **não quiser** trabalhar com os dados brutos (`String[]`).

O que ele faz:

- Lê o arquivo
- Interpreta o **formato CSV padrão**.
- Converte cada **linha** em **um objeto do tipo escolhido** (`Pessoa`, `Produto`, etc.)
- Usa **anotações** ou **estratégias de mapeamento** para **associar colunas → campos do objeto**.
- Converte tipos automaticamente (int, double, Date, boolean etc).
- Retorna um um Bean/Objeto (`T`)

Características:

- **Alto nível:** automatiza **mapeamento**, **parsing** e **conversão de dados.**
- Recebe um `Reader` (`FileReader` ou `InputStreamReader` + `FileInputStream`)
- Usa um `CsvParser` padrão internamente.

Pontos a se atentar:

- É recomendado que não seja passado `CSVReader` para o `CsvToBean` pois o *parsing* será feito duas vezes, o que pode acabar gerando erros.
- Não é recomendado usar `new CsvToBean<>()` porque requer configurações manuais e é mais propenso a erros. O `CsvToBeanBuilder` já aplica as configurações necessárias de forma segura.

### 🔹`CsvToBeanBuilder`:

→ Permite criar um `CsvToBean` **configurado**.

O que ela faz:

- Permite que configuremos como deve ocorrer o *parsing* e alguns detalhes do mapeamento:
    - delimitador, aspas, escape, campos vazios, e outras opções de parsing.
    - **qual bean deverá ser criado** (`Pessoa.class`)
    - **estratégia de mapeamento** (por nome ou por posição)

Características:

- Possui a maioria dos métodos do `CSVPaserBuilder`, ou seja, podemos configurar como o *parser* deve funcionar

## Criando o Mapeamento:

Existe 3 formas de **criar o mapeamento** entre **uma linha do CSV e um objeto Java (*bean*)**.

### 1️⃣ Mapeamento por anotações:

→ é a forma mais recomendada

Para essa abordagem temos duas opções: `@CsvBindByName` e `@CsvBindByPosition`.

🔹 **`@CsvBindByName`**:

→ Mapeia pelo **NOME** da coluna do cabeçalho

```java
public class Produto {

    @CsvBindByName(column = "nome") //nome da coluna no arquivo
    private String nome;

    @CsvBindByName(column = "preco")
    private double preco;
}
```

**Quando usar:**

- CSV tem cabeçalho
- Ordem das colunas pode mudar
- Código mais limpo e robusto

**⚠️ATENÇÃO:**

Ao usar a anotação `@CsvBindByName`, **NÃO** pule o cabeçalho do arquivo (`withSkipLines(1)`), já que essa abordagem analisa justamente o cabeçalho do arquivo para realizar o mapeamento.

**🔹 `@CsvBindByPosition`**

→ Mapeia pela **POSIÇÃO** da coluna.

```java
public class Produto {

    @CsvBindByPosition(position = 0)
    private String nome;

    @CsvBindByPosition(position = 1)
    private double preco;
}
```

**Quando usar:**

- CSV sem cabeçalho
- Arquivos muito rígidos de layout fixo

📌 **Por que usar?**

Facilita, é mais claro, mantém o código próximo aos dados e funciona direto com `CsvToBeanBuilder`.

### 2️⃣ **MappingStrategy Manual (forma tradicional/legada)**

→ Você não usa anotações; define explicitamente o mapeamento no código.

**🔹 `ColumnPositionMappingStrategy`**

Mapeia pela POSIÇÃO.

```java
ColumnPositionMappingStrategy<Produto> strategy =
        new ColumnPositionMappingStrategy<>();
strategy.setType(Produto.class);
strategy.setColumnMapping("nome", "preco");
```

**Útil quando:**

- Você não quer/ou não pode usar anotações
- O layout é rígido
- Mapeamento é dinâmico



**🔹 `HeaderColumnNameMappingStrategy`**

Mapeia pelo NOME da coluna.

```java
HeaderColumnNameMappingStrategy<Produto> strategy =
        new HeaderColumnNameMappingStrategy<>();
strategy.setType(Produto.class);
```

Funciona como `@CsvBindByName`, mas sem anotar a classe.



### 3️⃣ **Mapping Customizado (Convertes, Processadores e Validadores)**

Você pode criar conversores próprios para transformar campo → objeto.

✔️ `@CsvCustomBindByName`

```java
public class DataConverter extends AbstractBeanField<LocalDate, String> {
    @Override
    protected LocalDate convert(String value) {
        return LocalDate.parse(value, DateTimeFormatter.ofPattern("dd/MM/yyyy"));
    }
}
```

```java
public class Produto {
    @CsvCustomBindByName(column = "data", converter = DataConverter.class)
    private LocalDate data;
}
```

**Quando usar:**

- Datas com formato custom
- Conversões complexas
- Parsers específicos (ex.: preço com moeda, transforms, validação)
