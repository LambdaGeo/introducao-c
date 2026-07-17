# C para Programadores Python e VisuAlg

Adaptado e ampliado a partir de ["C for Python Programmers"](https://www.cs.toronto.edu/~patitsas/cs190/c_for_python.html), de Carl Burch (Hendrix College, 2011), com modificações de Elizabeth Patitsas (University of Toronto, 2012) — obra original em [toves.org/books/cpy/](http://www.toves.org/books/cpy/). Esta versão traduz o conteúdo para português, adiciona comparações com **VisuAlg** ao longo de todo o texto, exercícios de mapeamento Python/VisuAlg → C, listas de exercícios por capítulo, e capítulos novos que não existiam na obra original (Structs, Ponteiros e Alocação Dinâmica, Vetores e Strings).

!!! info "Licença"
    "C for Python Programmers" está licenciado sob [Creative Commons Atribuição-CompartilhaIgual 3.0 Estados Unidos](http://creativecommons.org/licenses/by-sa/3.0/us/). Como esta é uma obra derivada, ela é distribuída sob a mesma licença — veja o arquivo `LICENSE` no repositório.

Nos anos 70, nos Laboratórios Bell, Ken Thompson projetou a linguagem de programação C para ajudar no desenvolvimento do sistema operacional UNIX. Através de uma variedade de eventos históricos, poucos intencionais, o UNIX cresceu de um pequeno desvio de pesquisa para um popular sistema operacional de força industrial. E junto com o sucesso do UNIX veio o C, já que o sistema operacional foi projetado para que os programas C pudessem acessar todos os seus recursos. À medida que mais programadores adquiriram experiência com o C, eles começaram a usá-lo também em outras plataformas, de modo que se tornou uma das linguagens primárias para o desenvolvimento de software no final dos anos 80.

Enquanto C não possua o amplo domínio que já teve, sua influência foi tão grande que muitas outras linguagens foram projetadas para parecer, incluindo C++, C#, Objective-C, Java, JavaScript, PHP e Perl. Saber C é em si uma coisa boa — é um excelente ponto de partida para se relacionar mais diretamente com o que um computador faz. Mas aprender C também é um bom ponto de partida para se familiarizar com todos esses outros idiomas.

> Este documento é dirigido para pessoas que já aprenderam a programar em Python e/ou VisuAlg e que desejam aprender C. A influência de C em Python é considerável, nas palavras do inventor do Python, Guido van Rossum ("Uma Introdução ao Python para Programadores UNIX/C", 1993). Portanto, já saber programar em Python — ou em VisuAlg — é um bom primeiro passo para aprender C.

## Como usar este material

A proposta **não é** aprender C do zero, como se fosse a sua primeira linguagem. É **mapear** o que você já sabe fazer em Python ou VisuAlg para a sintaxe de C, construção por construção: você já sabe escrever um `if`? Ótimo — a pergunta que interessa agora é só "como eu escrevo esse mesmo `if` em C?".

Por isso, ao longo do texto:

- Todo trecho de código em C vem acompanhado do equivalente em Python e/ou em VisuAlg, para você comparar lado a lado.
- Aparecem caixas de **"Exercício de mapeamento"**, com um trecho pronto em Python ou VisuAlg, pedindo a versão em C daquele trecho específico.
- Ao final de cada capítulo há uma **Lista de exercícios**, com problemas de programação mais abertos (no estilo do que se vê numa disciplina), para praticar o conteúdo do capítulo por inteiro, não só um trecho isolado.

E sobre usar IA (Claude, ChatGPT ou qualquer outra) enquanto estuda:

!!! tip "Regra de ouro"
    Nunca peça direto para a IA "traduzir" um programa inteiro para C. Primeiro tente você mesmo resolver o mapeamento, elemento por elemento, com a ajuda do texto. Só depois de ter uma solução que compila e funciona é que vale a pena perguntar à IA se existe uma forma **mais natural/idiomática** de escrever aquele mesmo código em C — essa segunda conversa é ótima para aprender; a primeira ("faz pra mim") pula justamente a parte que ensina.

## Conteúdo

* [1. Construindo um simples programa](cap01.md)
* [2. Expressões e comandos](cap02.md)
* [3. Bibliotecas](cap03.md)
* [4. Vetores e Strings](cap04.md)
* [5. Structs (registros)](cap05.md)
* [6. Ponteiros e Alocação Dinâmica](cap06.md)
* [7. Compilação Separada e Bibliotecas Externas](cap07.md)

## Testando online

Você não precisa instalar nada para testar os exemplos deste material:

* [https://www.onlinegdb.com/online_c_compiler](https://www.onlinegdb.com/online_c_compiler) — compilador de C online.
* [https://www.mycompiler.io/pt/new/c](https://www.mycompiler.io/pt/new/c) — outra opção de compilador de C online.
