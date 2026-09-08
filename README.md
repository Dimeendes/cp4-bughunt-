# Checkpoint 4 — Bug Hunt StreamFIAP

> Copie este arquivo para a raiz do seu repositório com o nome **README.md**
> e preencha todas as seções.

## Identificação

**Grupo:** Grupo 21

| Integrante | RM | Turma |
|---|---|---|
| Giovanni de Lela Anjos Costa | RM563066 | 2CCPG|
| Thiago Sobral de Alvarenga | RM562695 | 2CCPG |
| Pedro Miranda Campos Riato | RM562117 | 2CCPG |
| Gabriel Hiro Nakamura | RM562221 | 2CCPG |
| Diego Antonio Silva Mendes | RM565509 | 2CCPG |
| Israel Karacsony de Camargo Nunes | RM563435 | 2CCPG |

| Campo | |
|---|---|
| **Total de bugs corrigidos** | 12 / 12 |
| **Total de ajustes de Clean Code** | 3 / 6 |

---

## Parte 1 — Bugs encontrados

> Uma linha por bug, na ordem em que você os encontrou. Use a numeração dos seus
> commits (`fix: bug01 ...`). Preencha TODAS as colunas — metade da nota está aqui.

| # | Sintoma observado (o que fiz/vi) | Causa raiz (arquivo e linha aproximada) | Correção aplicada | Conceito da disciplina |
|---|---|---|---|---|
| bug01 | O cálculo de preço retorna 9.90 para um documentário, quando a regra diz que deveria ser gratuito. | `Documentario.java` (linha 16). Documentário herda o valor padrão da superclasse devido à ausência do método. | Adicionado override retornando 0.0. | Aula 8/9 — Herança/Polimorfismo (override ausente). |
| bug02 | Usuário com créditos recebe erro ao alugar algo barato, e usuário sem saldo consegue alugar algo caro. | `Usuario.java` (linha 28). A comparação em `temCreditosSuficientes` (preco >= this.creditos) estava invertida. | Invertida a comparação para creditos >= preco. | Lógica condicional / regra de negócio (saldo). |
| bug03 | Uma série sempre custa 9.90 fixo, ignorando o preço correto pelas temporadas. | `Serie.java` (linha 19). O método tem um parâmetro extra (desconto), fazendo overload em vez de override. | Removido parâmetro extra e adicionado @Override. | Aula 7 — Override vs Overload (pergunta 4). |
| bug04 |Busca por ID inexistente retorna 200 OK vazio em vez de 404. |`ConteudoController.java` (linhas 31-40). Bloco try/catch vazio engolia a exceção e retornava null. |Removido try/catch para exceção propagar.] |Aula 11 — Tratamento de exceções. |
| bug05 |O usuário não recebe um id gerado, pois ao salvar um usuário novo, o id retornado vem null. | arquivo  model/Usuario.java, linhas 11-12 .|Adiciona @GeneratedValue(strategy = GenerationType.IDENTITY).|Aula 13 (Spring Boot) - geração automática de ID.|
| bug06 |O construtor de Série não chama super(...), ao cadastrar uma série, ela é salva sem título, categoria, duração, classificação etária e disponibilidade. |arquivo model/Serie.java, linhas 14-15. | adiciona o super(...) com os atributos da classe mãe. |Aula 6 (Herança) - uso obrigatório de super nas classes filhas. |
| bug07 |Busca por categoria manual comparando Strings com == . |arquivo controller/ConteudoController.java, método listarPorCategoria, linha 44.| usa findByCategoria do repository em vez de comparar String com == manualmente" . |Aula 13 (Spring Boot) - Spring Data JPA — Repository. |
| bug08 |Ao alugar um conteúdo com classificação indicativa maior que a idade do usuário, a API responde com erro genérico do servidor (500) e não devolve uma mensagem clara |arquivo exception/GlobalExceptionHandler.java, linha 11 | adiciona o  handler @ExceptionHandler(ClassificacaoIndicativaException.class) no GlobalExceptionHandler | Aula 11 (Tratamento de Exceções ) - exceções checked e unchecked .|
| bug09 |Ao aplicar a promoção em um filme, seu valor era reajustado para 1,2x do original, aumentando em 20% | Arquivo `Filme.java` (linha 25).| Alterada lógica que multiplicava o valor original de 1.2 para 0.8 (reduzindo em 20% o valor, como esperado)| Lógica matemática / regra de negócio (promoções)|
| bug10 |O atributo "nome" estava sem o referenciador "this", fazendo o atributo não ser armazenado corretamente | Arquivo `Usuario.java` (linha 23). | Adicionado referenciador "this." ao atributo nome do construtor de usuário | Aula 02 (Métodos e comportamentos) - Referência a atributos  |
| bug11 | Ao cadastrar uma nova série, são esperados 6 argumentos, mas somente 5 estavam sendo enviados, faltando o argumento "isDisponivel" | Arquivo `ConteudoController.java` (linha 62). | Adicionado o argumento 'serie.isDisponivel()' a instância de serie | Aula 04 (Construtores) - parâmetros de classe|
| bug12 | Ao calcular o preço de aluguel de um novo item, o fato de ser promocionavel era ignorado e seu valor se mantinha como o original | Arquivo `Usuario.java` (linha 44) | Alterado o método calcularPrecoAluguel() para calcularPrecoPromocional(), que verifica se o item possui promoção e aplica o preço promocional quando necessário, mantendo o preço original caso contrário.| Aula 07 (muitas formas polimorfismo de sobrescrita) / Regra de negócio (promoções)|
## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 | Arquivo `Conteudo.java` (linha 16) | Deixava uma variável pública | Alterei de pública para privada |
| clean02 | Arquivo `ConteudoController.java` (linhas 75-88 | Código morto | Removi parte do código que não estava sendo utilizada |
| clean03 | Arquivo `ConteudoController.java` (linhas 53, 61 e 70) | Quebra de encapsulamento | Troquei o acesso direto que a funçao utilizava pelo getter |
| clean04 | | | |
| clean05 | | | |
| clean06 | | | |

---

## Parte 3 — Perguntas de reflexão

> Responda com suas palavras, 5 a 10 linhas cada, **usando o código real do projeto
> como exemplo**. Respostas genéricas de tutorial não pontuam.

### 1. Injeção de dependência (Aula 13)
Os controllers recebem os repositories via `@Autowired` (ex.: `ConteudoController`
usa `ConteudoRepository`). Explique por que o Spring precisa gerenciar esses objetos
em vez de criarmos com `new ConteudoRepository()`. O que exatamente o Spring faz ao
injetar um bean, e por que isso não funcionaria com um `new` comum?

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)
Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
`ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
melhor, e como o `findByCategoria` consegue funcionar sem implementação.

### 3. Exceções checked vs unchecked (Aula 11)
A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
`extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
regra (classificação indicativa) chegar de forma clara ao cliente da API.

### 4. Sobrescrita vs sobrecarga (Aula 7)
Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)
Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

### 6. Abstração e interface (Aulas 8 e 9)
`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

---

## Parte 4 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```

```
