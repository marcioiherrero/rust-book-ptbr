---
title: "Edições"
chapter_code: 22-05
slug: edicoes
---

# Apêndice E: Edições

No Capítulo 1, você viu que `cargo new` adiciona um pouco de metadados ao seu arquivo _Cargo.toml_ sobre uma edição. Este apêndice explica o que isso significa!

A linguagem Rust e o compilador têm um ciclo de lançamento de seis semanas, o que significa que os usuários recebem um fluxo constante de novos recursos. Outras linguagens de programação lançam mudanças maiores com menos frequência; o Rust lança atualizações menores com mais frequência. Com o tempo, todas essas pequenas mudanças se acumulam. Mas de um lançamento para outro, pode ser difícil olhar para trás e dizer: “Uau, entre Rust 1.10 e Rust 1.31, o Rust mudou muito!”

A cada três anos ou so, a equipe do Rust produz uma nova _edição_ do Rust. Cada edição reúne os recursos que foram incorporados em um pacote claro com documentação e ferramentas totalmente atualizadas. Novas edições são lançadas como parte do processo habitual de lançamento de seis semanas.

As edições servem a propósitos diferentes para pessoas diferentes:

- Para usuários ativos de Rust, uma nova edição reúne mudanças incrementais em um pacote fácil de entender.
- Para não usuários, uma nova edição sinaliza que avanços importantes foram incorporados, o que pode fazer valer a pena dar outra olhada no Rust.
- Para quem desenvolve o Rust, uma nova edição fornece um ponto de convergência para o projeto como um todo.

No momento em que este texto foi escrito, quatro edições do Rust estão disponíveis: Rust 2015, Rust 2018, Rust 2021 e Rust 2024. Este livro foi escrito usando idiomas da edição Rust 2024.

A chave `edition` em _Cargo.toml_ indica qual edição o compilador deve usar para o seu código. Se a chave não existir, o Rust usa `2015` como valor da edição por motivos de compatibilidade retroativa.

Cada projeto pode optar por uma edição diferente da edição padrão 2015. As edições podem conter mudanças incompatíveis, como incluir uma nova palavra-chave que conflita com identificadores no código. No entanto, a menos que você opte por essas mudanças, seu código continuará compilando mesmo quando você atualizar a versão do compilador Rust que usa.

Todas as versões do compilador Rust suportam qualquer edição que existia antes do lançamento daquele compilador, e elas podem vincular crates de qualquer edição suportada. Mudanças de edição afetam apenas a forma como o compilador analisa o código inicialmente. Portanto, se você estiver usando Rust 2015 e uma de suas dependências usar Rust 2018, seu projeto compilará e poderá usar essa dependência. A situação oposta, em que seu projeto usa Rust 2018 e uma dependência usa Rust 2015, também funciona.

Para deixar claro: a maioria dos recursos estará disponível em todas as edições. Desenvolvedores que usam qualquer edição do Rust continuarão vendo melhorias conforme novos lançamentos estáveis são feitos. No entanto, em alguns casos, principalmente quando novas palavras-chave são adicionadas, alguns recursos novos podem estar disponíveis apenas em edições posteriores. Você precisará mudar de edição se quiser aproveitar esses recursos.

Para mais detalhes, consulte [_The Rust Edition Guide_](https://doc.rust-lang.org/stable/edition-guide). Este é um livro completo que enumera as diferenças entre edições e explica como atualizar automaticamente seu código para uma nova edição via `cargo fix`.
