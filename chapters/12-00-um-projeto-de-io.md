---
title: "Um projeto de I/O"
chapter_code: 12-00
slug: um-projeto-de-io
---

# Um Projeto de I/O: Construindo um Programa de Linha de Comando

Este capítulo é uma recapitulação de muitas habilidades que você aprendeu até agora e uma exploração de alguns recursos adicionais da biblioteca padrão. Construiremos uma ferramenta de linha de comando que interage com entrada e saída de arquivos e da linha de comando para praticar alguns dos conceitos de Rust que você já domina.

A velocidade, a segurança, a saída em um único binário e o suporte multiplataforma do Rust tornam a linguagem ideal para criar ferramentas de linha de comando. Para nosso projeto, faremos nossa própria versão da clássica ferramenta de busca em linha de comando `grep` (**g**lobally search a **r**egular **e**xpression and **p**rint). No caso de uso mais simples, `grep` busca em um arquivo especificado uma string especificada. Para isso, `grep` recebe como argumentos um caminho de arquivo e uma string. Em seguida, lê o arquivo, encontra linhas que contêm o argumento string e imprime essas linhas.

Ao longo do caminho, mostraremos como fazer nossa ferramenta de linha de comando usar recursos de terminal que muitas outras ferramentas de linha de comando usam. Leremos o valor de uma variável de ambiente para permitir que o usuário configure o comportamento da nossa ferramenta. Também imprimiremos mensagens de erro no fluxo de console de erro padrão (`stderr`) em vez da saída padrão (`stdout`), para que, por exemplo, o usuário possa redirecionar a saída bem-sucedida para um arquivo enquanto ainda vê mensagens de erro na tela.

Um membro da comunidade Rust, Andrew Gallant, já criou uma versão completa e muito rápida de `grep`, chamada `ripgrep`. Em comparação, nossa versão será bem simples, mas este capítulo lhe dará parte do conhecimento de fundo necessário para entender um projeto do mundo real como `ripgrep`.

Nosso projeto `grep` combinará vários conceitos que você aprendeu até agora:

- Organizar código (Capítulo 7)
- Usar vetores e strings (Capítulo 8)
- Lidar com erros (Capítulo 9)
- Usar traits e lifetimes quando apropriado (Capítulo 10)
- Escrever testes (Capítulo 11)

Também introduziremos brevemente closures, iterators e trait objects, que o Capítulo 13 e o Capítulo 18 cobrirão em detalhes.
