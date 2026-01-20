# MapStruct

O MapStruct é um _code generator_ (gerador de código) que cria automaticamente implementações de _mappers_ (interfaces responsáveis por converter objetos).
Ou seja, você define **o que** deve ser mapeado, e o MapStruct gera o **como**.


👉 **Ele é muito usado para:**
- Converter entidades JPA em DTOs.
- Converter DTOs em entidades (para salvar ou atualizar dados).
- Converter objetos de requisição (Request) em objetos de domínio.
- Evitar `ModelMapper`, que usa reflection (mais lento e menos seguro).

🔗[Dependência Maven do MapStruct](https://mvnrepository.com/artifact/org.mapstruct/mapstruct)




# Anottations:

## `@Mapper`

### 1️⃣ Para que serve a annotation `@Mapper`?

Serve para indicar que uma interface ou classe será responsável por definir **mapeamentos entre objetos**. O MapStruct utiliza essa anotação para gerar automaticamente o código de mapeamento em tempo de compilação

Exemplo básico:
```java
@Mapper
public interface UserMapper {
    UserDTO toDTO(User user);
    User toEntity(UserDTO dto);
}
```

Durante o build, o MapStruct gera algo como:
```java
public class UserMapperImpl implements UserMapper {
    ...
}
```

### 2️⃣ Atributos que `@Mapper` pode receber
#### 🔹 `componentModel`

**Para que serve?**

Define como o mapper será gerenciado pelo framework de injeção de dependência.
No contexto de **Spring Boot**, o mais comum é:
```java
@Mapper(componentModel = "spring")
```

Isso faz com que o MapStruct gere a implementação com:
```java
@Component
```

👉Ou seja: 
- O mapper vira um bean do Spring
- Pode ser injetado com `@Autowired` ou via construtor

Exemplo:
```java
@Mapper(componentModel = "spring")
public interface UserMapper {
    UserDTO toDTO(User user);
}
```

Uso em um service:
```java
@Service
public class UserService {

    private final UserMapper userMapper;

    public UserService(UserMapper userMapper) {
        this.userMapper = userMapper;
    }
}
```
Outros valores possíveis:

- `"default"` → não usa DI (você acessa via `Mappers.getMapper(...)`)
- `"spring"` → Spring (mais usado)
- `"cdi"` → CDI
- `"jsr330"` → `@Named`, `@Inject`


#### 🔹 `uses`

**Para que serve?**  
O atributo `uses` é usado quando **um mapper depende de outro mapper**.

Imagine:

- `User` tem um `Address`
- Você já tem um `AddressMapper`
- Agora o `UserMapper` precisa reutilizar esse mapper

Exemplo:
```java
@Mapper(componentModel = "spring", uses = AddressMapper.class)
public interface UserMapper {

    UserDTO toDTO(User user);
}
```

O que acontece:
- O MapStruct injeta o `AddressMapper` automaticamente
- Sempre que encontrar um `Address` → `AddressDTO`, ele usa o mapper indicado

Exemplo de cenário real:
```java
public class User {
    private String name;
    private Address address;
}
```
```java
public class UserDTO {
    private String name;
    private AddressDTO address;
}
```

Se existir:
```java
@Mapper(componentModel = "spring")
public interface AddressMapper {
    AddressDTO toDTO(Address address);
}
```

Então no `UserMapper`:
```java
@Mapper(componentModel = "spring", uses = AddressMapper.class)
public interface UserMapper {
    UserDTO toDTO(User user);
}
```

❌ Sem o `uses`, você teria que fazer mapeamento manual  
✔ Com o `uses`, o MapStruct resolve automaticamente


Resumo rápido:

|Atributo |	Para que serve |
|---------|----------------|
|`@Mapper`  |	Marca a interface como um mapper do MapStruct |
|`componentModel = "spring"` |	Torna o mapper um bean do Spring |
|`uses = {...}` |	Permite reutilizar outros mappers dentro desse mapper |

## `@Mapping`

### 1️⃣ O que é a anotação `@Mapping`

A anotação `@Mapping` serve para **configurar manualmente como um campo de origem (por exemplo, de um `Entity` ou `DTO`) deve ser mapeado para um campo de destino**.
Ela é usada dentro da interface do Mapper, acima do método que faz a conversão.

Exemplo prático:
```java
@Mapper(componentModel = "spring")
public interface DisdispositivoMapper {

    //Entity -> DispositivoresponseDTO
    @Mapping()
    DispositivoResponseDTO toResponseDTO(Dispositivo dispositivo);

}
```
### 2️⃣ Atributos que a `@Mapping` pode receber
####  `source` e `target`

Esses dois atributos dizem ao MapStruct de **onde vem o valor e para onde ele vai**.

| Atributo	| Significado	| Exemplo |  
|-----------|-------------|---------|
| `source`	| Nome do campo no objeto de origem (source object)	| `"usuario.id"` |   
| `target`	| Nome do campo no objeto de destino (target object) |	`"usuarioId"` |   

👉 Em resumo:

`source` → origem dos dados  
`target` → destino dos dados

**Exemplo prático**

Suponha que você tenha isso:

Entity
```java
public class Dispositivo {
    private Long id;
    private String modelo;
    private Usuario usuario;
}
```
DTO
```java
public class DispositivoResponseDTO {
    private Long id;
    private String modelo;
    private Long usuarioId;
}
```

👉 Aqui, o `usuarioId` não existe diretamente em `Dispositivo`, mas sim dentro de `usuario`.

Então o MapStruct não conseguiria inferir sozinho — e é aí que entra o `@Mapping`:
```java
@Mapper(componentModel = "spring")
public interface DispositivoMapper {

    @Mapping(source = "usuario.id", target = "usuarioId")
    DispositivoResponseDTO toResponseDTO(Dispositivo dispositivo);
}
```

👉 Nesse caso:

`source` = `"usuario.id"` → vem do objeto `Dispositivo` → acessa `getUsuario().getId()`

`target` = `"usuarioId"` → vai pro campo `usuarioId` no `DispositivoResponseDTO`


### 3️⃣ Quando devo usar `@Mapping`

Use quando:

✅ Os nomes dos campos são diferentes  
✅ Há objetos aninhados (como `usuario.id` → `usuarioId`)  
✅ Quer ignorar campos (`@Mapping(target = "id", ignore = true)`)  
✅ Precisa de conversão customizada (`date` → `string`, `enum` → `int`, etc.)  
