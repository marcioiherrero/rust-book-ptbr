---
title: "Variáveis e mutabilidade"
chapter_code: 03-01
slug: variaveis-e-mutabilidade
---

# Variáveis e mutabilidade

Como mencionado na seção [Armazenando valores com variáveis](/livro/cap02-00-programando-um-jogo-de-adivinhacao#armazenando-valores-com-variáveis), por padrão, as variáveis em Rust são imutáveis. Esse é um dos vários "empurrõezinhos" que o Rust dá para incentivar você a escrever código que aproveite melhor a segurança e a facilidade de concorrência que a linguagem oferece. Ainda assim, você tem a opção de tornar suas variáveis mutáveis. Vamos explorar como e por que o Rust incentiva o uso da imutabilidade e por que, às vezes, você pode querer abrir mão disso.

Quando uma variável é imutável, depois que um valor é associado a ela, esse valor não pode ser alterado. Para ilustrar isso, crie um novo projeto chamado `variables` dentro do diretório de projetos usando o comando: `cargo new variables`

Em seguida, dentro do diretório `variables`, abra o arquivo `src/main.rs` e substitua o código pelo seguinte (que ainda **não vai compilar**):

Arquivo: src/main.rs (Este código não compila!)
```rust
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

Salve o arquivo e execute o programa com `cargo run`. Você deverá receber uma mensagem de erro relacionada à imutabilidade, parecida com esta:

```bash
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     println!("The value of x is: {x}");
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
  |
help: consider making this binding mutable
  |
2 |     let mut x = 5;
  |         +++

For more information about this error, try `rustc --explain E0384`.
error: could not compile `variables` (bin "variables") due to 1 previous error

```

Este exemplo mostra como o compilador ajuda você a encontrar erros nos seus programas.
Erros de compilação podem ser frustrantes, mas, na prática, eles só querem dizer
que seu programa ainda não está fazendo com segurança o que você quer que ele faça;
eles _não_ significam que você seja um mau programador! Mesmo Rustaceans experientes
continuam recebendo erros do compilador.

Você recebeu a mensagem de erro `` cannot assign twice to immutable variable `x` ``
porque tentou atribuir um segundo valor à variável imutável `x`.

É importante receber erros em tempo de compilação quando tentamos alterar um valor
marcado como imutável, porque exatamente esse tipo de situação pode levar a bugs.
Se uma parte do código opera com a suposição de que um valor nunca vai mudar e outra
parte do código muda esse valor, existe a possibilidade de a primeira parte não se
comportar como foi projetada. A causa desse tipo de bug pode ser difícil de encontrar
depois, especialmente quando o segundo trecho de código muda o valor apenas _às vezes_.
O compilador Rust garante que, quando você afirma que um valor não vai mudar, ele
realmente não vai mudar, então você não precisa ficar controlando isso por conta própria.
Com isso, seu código fica mais fácil de raciocinar.

Mas a mutabilidade pode ser muito útil e também pode tornar o código mais conveniente
de escrever. Embora as variáveis sejam imutáveis por padrão, você pode torná-las mutáveis
adicionando `mut` antes do nome da variável, como fez no [Capítulo 2](/livro/cap02-00-programando-um-jogo-de-adivinhacao#armazenando-valores-com-variáveis). Adicionar `mut` também comunica sua
intenção para quem vier a ler o código no futuro, indicando que outras partes do código
vão mudar o valor dessa variável.

Por exemplo, vamos alterar _src/main.rs_ para o seguinte:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

Quando executamos o programa agora, obtemos isto:

```console
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.30s
     Running `target/debug/variables`
The value of x is: 5
The value of x is: 6
```

Podemos alterar o valor associado a `x` de `5` para `6` quando `mut` é usado.
No fim das contas, decidir usar mutabilidade ou não depende de você e do que
considera mais claro naquela situação específica.

<!-- Old headings. Do not remove or links may break. -->
<a id="constants"></a>

### Declarando constantes

Assim como variáveis imutáveis, _constantes_ são valores associados a um nome
e que não podem ser alterados, mas existem algumas diferenças entre constantes
e variáveis.

Primeiro, não é permitido usar `mut` com constantes. Constantes não são apenas
imutáveis por padrão: elas são sempre imutáveis. Você declara constantes usando
a palavra-chave `const`, em vez de `let`, e o tipo do valor _deve_ ser anotado.
Vamos cobrir tipos e anotações de tipo na próxima seção,
[“Tipos de dados”](/livro/cap03-02-tipos-de-dados#tipos-de-dados), então não se preocupe com os detalhes
agora. Por enquanto, basta saber que é obrigatório sempre anotar o tipo.

Constantes podem ser declaradas em qualquer escopo, inclusive no escopo global,
o que as torna úteis para valores que muitas partes do código precisam conhecer.

A última diferença é que constantes só podem ser definidas com uma expressão
constante, e não com o resultado de um valor que só poderia ser calculado em
tempo de execução.

Aqui está um exemplo de declaração de constante:

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

O nome da constante é `THREE_HOURS_IN_SECONDS`, e seu valor é definido como o
resultado de multiplicar 60 (o número de segundos em um minuto) por 60 (o número
de minutos em uma hora) por 3 (o número de horas que queremos contar neste programa).
A convenção de nomenclatura do Rust para constantes é usar tudo em maiúsculas, com
underscores entre as palavras. O compilador consegue avaliar um conjunto limitado
de operações em tempo de compilação, o que nos permite escrever esse valor de um
jeito mais fácil de entender e verificar, em vez de definir a constante diretamente
como 10.800. Consulte a [seção sobre avaliação de constantes na Rust
Reference](https://doc.rust-lang.org/reference/const_eval.html) para mais informações sobre quais operações podem ser usadas
ao declarar constantes.

Constantes são válidas durante todo o tempo de execução do programa, dentro do escopo
em que foram declaradas. Essa característica torna constantes úteis para valores do
domínio da sua aplicação que várias partes do programa talvez precisem conhecer, como
o número máximo de pontos que qualquer jogador pode ganhar em um jogo, ou a velocidade
da luz.

Dar nomes de constantes para valores fixos usados ao longo do programa ajuda a transmitir
o significado desse valor para futuras pessoas mantenedoras do código. Também ajuda ter
um único lugar no código para alterar, caso esse valor fixo precise ser atualizado no
futuro.

### Sombreamento

Como você viu no tutorial do jogo de adivinhação no [Capítulo 2](/livro/cap02-00-programando-um-jogo-de-adivinhacao#comparando-o-palpite-com-o-numero-secreto), você pode declarar uma
nova variável com o mesmo nome de uma variável anterior. Rustaceans dizem que a primeira
variável foi _sombreada_ (_shadowed_) pela segunda, o que significa que a segunda variável
é a que o compilador enxerga quando você usa aquele nome. Na prática, a segunda variável
“encobre” a primeira, de modo que todos os usos daquele nome passam a se referir a ela
até que ela também seja sombreada ou que o escopo termine. Podemos sombrear uma variável
reutilizando o mesmo nome e repetindo o uso da palavra-chave `let`, como a seguir:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");
}
```

Esse programa primeiro associa `x` ao valor `5`. Depois, cria uma nova variável `x`
ao repetir `let x =`, pegando o valor original e somando `1`, de modo que o valor de
`x` passa a ser `6`. Em seguida, dentro de um escopo interno criado com chaves, a terceira
instrução `let` também sombreia `x` e cria uma nova variável, multiplicando o valor anterior
por `2` para dar a `x` o valor `12`. Quando esse escopo termina, o sombreamento interno acaba
e `x` volta a ser `6`. Quando executamos esse programa, ele mostra o seguinte:

```console
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6
```

Sombreamento é diferente de marcar uma variável com `mut`, porque receberemos um erro
em tempo de compilação se, por acidente, tentarmos reatribuir essa variável sem usar a
palavra-chave `let`. Ao usar `let`, podemos fazer algumas transformações em um valor,
mas manter a variável imutável depois que essas transformações terminarem.

A outra diferença entre `mut` e sombreamento é que, como estamos efetivamente criando
uma nova variável quando usamos `let` novamente, podemos mudar o tipo do valor e ainda
reutilizar o mesmo nome. Por exemplo, imagine que nosso programa peça para a pessoa
usuária indicar quantos espaços ela quer entre um texto digitando caracteres de espaço;
depois queremos armazenar essa entrada como um número:

```rust
let spaces = "   ";
let spaces = spaces.len();
```

A primeira variável `spaces` é do tipo string, e a segunda variável `spaces` é do tipo
número. Assim, o sombreamento evita que precisemos inventar nomes diferentes, como
`spaces_str` e `spaces_num`; em vez disso, podemos reutilizar o nome mais simples `spaces`.
No entanto, se tentarmos usar `mut` para isso, como mostrado aqui, teremos um erro em
tempo de compilação:

```rust,ignore,does_not_compile
let mut spaces = "   ";
spaces = spaces.len();
```

O erro diz que não temos permissão para mudar o tipo de uma variável:

```console
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0308]: mismatched types
 --> src/main.rs:3:14
  |
2 |     let mut spaces = "   ";
  |                      ----- expected due to this value
3 |     spaces = spaces.len();
  |              ^^^^^^^^^^^^ expected `&str`, found `usize`

For more information about this error, try `rustc --explain E0308`.
error: could not compile `variables` (bin "variables") due to 1 previous error
```

Agora que exploramos como variáveis funcionam, vamos olhar para mais tipos de dados
que elas podem ter.