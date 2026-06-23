---
title: "Erros irrecuperáveis com `panic!`"
chapter_code: 09-01
slug: erros-irrecuperaveis-com-panic
challenge_day: 11
reading_minutes: 7
---

# Erros irrecuperáveis com `panic!`

Às vezes, coisas ruins acontecem no seu código e não há nada que você possa fazer a respeito. Nesses casos, Rust possui a macro `panic!`. Na prática, há duas maneiras de causar um pânico: executando uma ação que faz o código entrar em pânico (como acessar um array além do seu limite) ou chamando a macro `panic!` explicitamente. Em ambos os casos, causamos um pânico no programa. Por padrão, esses pânicos imprimem uma mensagem de falha, desfazem, limpam a stack e encerram o programa. Por meio de uma variável de ambiente, você também pode fazer com que o Rust exiba a pilha de chamadas quando um pânico ocorre, facilitando rastrear a origem do problema.

> ### Desfazendo a Pilha ou Abortando em Resposta a um Pânico
>
> Por padrão, quando um pânico ocorre, o programa começa a desfazer a pilha (unwinding), o que significa que o Rust percorre a pilha de volta e limpa os dados de cada função que encontra. No entanto, percorrer e limpar tudo dá muito trabalho. Por isso, o Rust permite que você escolha a alternativa de abortar imediatamente, o que encerra o programa sem realizar a limpeza.
>
> A memória que o programa estava usando precisará então ser limpa pelo sistema operacional. Se no seu projeto você precisar que o binário resultante seja o menor possível, você pode trocar o unwinding pelo abort em caso de pânico, adicionando `panic = 'abort'` nas seções `[profile]` apropriadas do seu arquivo _Cargo.toml_. Por exemplo, se quiser abortar em caso de pânico no modo release, adicione o seguinte:
>
>
> ```toml
> [profile.release]
> panic = 'abort'
> ```

Vamos tentar chamar `panic!` em um programa simples:

**Arquivo: src/main.rs (Este código entra em pânico!)**

```rust
fn main() {
    panic!("crash and burn");
}
```

Ao executar o programa, você verá algo assim:

```console
$ cargo run
   Compiling panic v0.1.0 (file:///projects/panic)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.25s
     Running `target/debug/panic`

thread 'main' panicked at src/main.rs:2:5:
crash and burn
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

A chamada a `panic!` causa a mensagem de erro contida nas duas últimas linhas. A primeira linha mostra nossa mensagem de pânico e o local no código-fonte onde o pânico ocorreu: _src/main.rs:2:5_ indica que é a segunda linha, quinto caractere do nosso arquivo _src/main.rs_.

Nesse caso, a linha indicada faz parte do nosso código e, se formos até ela, veremos a chamada da macro `panic!`. Em outros casos, a chamada a `panic!` pode estar no código que o nosso código chama, e o nome do arquivo e o número da linha relatados na mensagem de erro serão de código de outra pessoa, onde a macro `panic!` é chamada, e não da linha do nosso código que eventualmente levou à chamada de `panic!`.

Podemos usar o backtrace das funções de onde veio a chamada a panic! para descobrir a parte do nosso código que está causando o problema. Para entender como usar um backtrace de `panic!`, vejamos outro exemplo e observemos como é quando a chamada a `panic!` vem de uma biblioteca por causa de um bug no nosso código, em vez de vir diretamente do nosso código chamando a macro. A Listagem 9-1 tem um código que tenta acessar um índice em um vetor além do intervalo de índices válidos.

**Arquivo: src/main.rs (Este código entra em pânico!)**

```rust
fn main() {
    let v = vec![1, 2, 3];

    v[99];
}
```

<a id="listagem-9-1"></a>

[Listagem 9-1](#listagem-9-1): Tentativa de acessar um elemento além do fim de um vetor, o que causará uma chamada a `panic!`

Aqui, estamos tentando acessar o 100º elemento do nosso vetor (que está no índice 99, pois a indexação começa em zero), mas o vetor tem apenas três elementos. Nessa situação, o Rust entrará em pânico. O uso de `[]` deveria retornar um elemento, mas se você passar um índice inválido, não há nenhum elemento que o Rust pudesse retornar que fosse correto.

Em C, tentar ler além do fim de uma estrutura de dados é um comportamento indefinido. Você pode obter o que estiver na localização de memória que corresponderia àquele elemento na estrutura de dados, mesmo que essa memória não pertença a essa estrutura. Isso é chamado de _buffer overread_ e pode levar a vulnerabilidades de segurança se um atacante conseguir manipular o índice de forma a ler dados que não deveria acessar, armazenados após a estrutura de dados.

Para proteger seu programa contra esse tipo de vulnerabilidade, se você tentar ler um elemento em um índice que não existe, o Rust interromperá a execução e se recusará a continuar. Vamos experimentar e ver:

```console
$ cargo run
   Compiling panic v0.1.0 (file:///projects/panic)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.27s
     Running `target/debug/panic`

thread 'main' panicked at src/main.rs:4:6:
index out of bounds: the len is 3 but the index is 99
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Esse erro aponta para a linha 4 do nosso _main.rs_, onde tentamos acessar o índice 99 do vetor `v`.

A linha com `note:` nos informa que podemos definir a variável de ambiente `RUST_BACKTRACE` para obter um backtrace do que exatamente aconteceu para causar o erro. Um _backtrace_ é uma lista de todas as funções que foram chamadas até aquele ponto. Os backtraces em Rust funcionam como em outras linguagens: a chave para lê-lo é começar pelo topo e ler até encontrar arquivos que você escreveu. Esse é o ponto onde o problema se originou. As linhas acima desse ponto são código que o seu código chamou; as linhas abaixo são código que chamou o seu código. Essas linhas antes e depois podem incluir código do núcleo do Rust, código da biblioteca padrão ou crates que você está usando. Vamos tentar obter um backtrace definindo a variável de ambiente `RUST_BACKTRACE` com qualquer valor diferente de `0`. A Listagem 9-2 mostra uma saída semelhante ao que você verá.

<a id="listagem-9-2"></a>

[Listagem 9-2](#listagem-9-2): O backtrace gerado por uma chamada a `panic!` exibido quando a variável de ambiente `RUST_BACKTRACE` está definida

```console
$ RUST_BACKTRACE=1 cargo run
thread 'main' panicked at src/main.rs:4:6:
index out of bounds: the len is 3 but the index is 99
stack backtrace:
   0: rust_begin_unwind
             at /rustc/4d91de4e48198da2e33413efdcd9cd2cc0c46688/library/std/src/panicking.rs:692:5
   1: core::panicking::panic_fmt
             at /rustc/4d91de4e48198da2e33413efdcd9cd2cc0c46688/library/core/src/panicking.rs:75:14
   2: core::panicking::panic_bounds_check
             at /rustc/4d91de4e48198da2e33413efdcd9cd2cc0c46688/library/core/src/panicking.rs:273:5
   3: <usize as core::slice::index::SliceIndex<[T]>>::index
             at file:///home/.rustup/toolchains/1.85/lib/rustlib/src/rust/library/core/src/slice/index.rs:274:10
   4: core::slice::index::<impl core::ops::index::Index<I> for [T]>::index
             at file:///home/.rustup/toolchains/1.85/lib/rustlib/src/rust/library/core/src/slice/index.rs:16:9
   5: <alloc::vec::Vec<T,A> as core::ops::index::Index<I>>::index
             at file:///home/.rustup/toolchains/1.85/lib/rustlib/src/rust/library/alloc/src/vec/mod.rs:3361:9
   6: panic::main
             at ./src/main.rs:4:6
   7: core::ops::function::FnOnce::call_once
             at file:///home/.rustup/toolchains/1.85/lib/rustlib/src/rust/library/core/src/ops/function.rs:250:5
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```
São muitas informações! A saída exata que você vê pode ser diferente dependendo do seu sistema operacional e da versão do Rust. Para obter backtraces com essas informações, os símbolos de depuração devem estar habilitados. Os símbolos de depuração são habilitados por padrão ao usar `cargo build` ou `cargo run` sem a flag `--release`, como fizemos aqui.

Na saída da Listagem 9-2, a linha 6 do backtrace aponta para a linha no nosso projeto que está causando o problema: linha 4 de _src/main.rs_. Se não quisermos que nosso programa entre em pânico, devemos começar nossa investigação pelo local apontado pela primeira linha que menciona um arquivo que escrevemos. Na Listagem 9-1, onde deliberadamente escrevemos um código que entraria em pânico, a maneira de corrigir o pânico é não solicitar um elemento além do intervalo dos índices do vetor. Quando seu código entrar em pânico no futuro, você precisará descobrir que ação o código está executando com quais valores para causar o pânico e o que o código deveria fazer em vez disso.

Voltaremos a `panic!` e quando devemos e não devemos usá-los para tratar condições de erros na seção [Usar panic! ou não usar panic!](/livro/cap09-03-panic-ou-nao-panic), mais adiante neste capítulo. A seguir veremos como se recuperar de um erro usando `Result`.
