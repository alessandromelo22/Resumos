# MVC (Mode-View-Controller)


O **MVC (Model–View–Controller)** é um padrão de arquitetura de software que separa a aplicação em três camadas principais — **Model**, **View** e **Controller** — com o objetivo de melhorar organização, manutenção, testabilidade e reaproveitamento de código. 
Sua principal ideia é **separar a lógica de negócios da interface com o usuário**, reduzindo acoplamento e aumentando a flexibilidade.


## 🎲Model (modelo)

É a camada responsável por **representar os dados e regras de negócio** da aplicação.

Funções principais:

- Gerenciar dados, estados e lógica de negócio.
- Validar informações recebidas.
- Comunicar-se com banco de dados, APIs ou serviços externos.
- Notificar o Controller (ou a View, em algumas variações) sobre mudanças de estado.

O Model **não tem conhecimento da interface do usuário** — ele apenas processa informação.


## 🎨**View (Visão)**

É a camada responsável pela **interface com o usuário**.

Funções principais:

- Exibir dados fornecidos pelo Model (geralmente passando pelo Controller).
- Coletar interações do usuário (cliques, digitação, seleções etc.).
- Não deve conter regras de negócio; apenas lida com apresentação.

A View depende do Controller para entender o que deve ser exibido e como reagir às ações do usuário.


## 🔗**Controller (Controlador)**

Age como o **intermediário** entre a View e o Model.

Funções principais:

- Receber ações da View (eventos: clique, submit etc.).
- Interpretar essas ações e decidir o que fazer.
- Solicitar mudanças ao Model.
- Atualizar a View de acordo com o novo estado dos dados.

O Controller é o “cérebro” que coordena o fluxo entre interface e regras de negócio.


## ⚙**Como o MVC Funciona (Fluxo de Comunicação)**

1️⃣**Usuário interage com a View** 
- Ex.: clicar em um botão, enviar um formulário.
    
2️⃣**A View encaminha o evento ao Controller**
- O Controller é responsável por interpretar a intenção da ação.
    
3️⃣**O Controller aciona o Model**  
- Pode solicitar consultas, atualizações, validações e outras regras de negócio.
    
4️⃣**O Model processa a lógica e retorna resultados**
- Pode alterar dados internos ou buscar informações.
    
5️⃣**O Controller atualiza a View**
- A interface é atualizada para refletir o novo estado do Model.
    
Esse fluxo garante que **View e Model permaneçam desacoplados**, se comunicando apenas por meio do Controller.


## ✅**Benefícios**

✔ Separação clara de responsabilidades  
✔ Código mais organizado e reutilizável  
✔ Facilidade de manutenção e testes  
✔ Interface pode mudar sem afetar regras de negócio
