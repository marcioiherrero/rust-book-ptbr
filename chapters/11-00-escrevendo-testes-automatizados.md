---
title: "Escrevendo testes automatizados"
chapter_code: 11-00
slug: escrevendo-testes-automatizados
---

# Escrevendo Testes Automatizados

No ensaio de 1972 “The Humble Programmer”, Edsger W. Dijkstra disse que “program testing can be a very effective way to show the presence of bugs, but it is hopelessly inadequate for showing their absence.” Isso não significa que não devamos tentar testar o máximo possível!

_Correção_ em nossos programas é o grau em que nosso código faz o que pretendemos que faça. O Rust foi projetado com alto grau de preocupação com a correção dos programas, mas correção é complexa e não é fácil provar. O sistema de tipos do Rust assume grande parte dessa carga, mas o sistema de tipos não captura tudo. Por isso, o Rust inclui suporte para escrever testes automatizados de software.

Digamos que escrevemos uma função `add_two` que adiciona 2 a qualquer número passado a ela. A assinatura desta função aceita um inteiro como parâmetro e retorna um inteiro como resultado. Quando implementamos e compilamos essa função, o Rust faz toda a verificação de tipos e borrow checking que você aprendeu até agora para garantir que, por exemplo, não estamos passando um valor `String` ou uma referência inválida para esta função. Mas o Rust _não pode_ verificar que esta função fará precisamente o que pretendemos, que é retornar o parâmetro mais 2 em vez de, digamos, o parâmetro mais 10 ou o parâmetro menos 50! É aí que entram os testes.

Podemos escrever testes que afirmam, por exemplo, que quando passamos `3` à função `add_two`, o valor retornado é `5`. Podemos executar esses testes sempre que fizermos alterações no código para garantir que qualquer comportamento correto existente não mudou.

Testar é uma habilidade complexa: embora não possamos cobrir em um capítulo todos os detalhes sobre como escrever bons testes, neste capítulo discutiremos a mecânica das facilidades de teste do Rust. Falaremos sobre as anotações e macros disponíveis ao escrever seus testes, o comportamento padrão e as opções fornecidas para executar seus testes, e como organizar testes em testes unitários e testes de integração.
