## 2. Expressões e comandos

Agora que vimos como construir um programa completo, vamos aprender o universo do que podemos fazer dentro de uma função C, executando através de diferentes tipos de comandos.

### 2.1. Operadores

Um operador é algo que podemos usar em expressões aritméticas, como um sinal de mais '+' ou um teste de igualdade '=='. Os operadores da linguagem C parecerão familiares, pois o designer da Python, Guido van Rossum, utilizou como referência esses operadores; mas existem algumas diferenças significativas — e o VisuAlg, por sua vez, fica em um meio-termo, mais próximo do Python nesse ponto.

> C operator precedence
>
> `++ --` (postfix)  
> `+ - !` (unário)  
> `* / %`  
> `+ -` (binário)  
> `< > <= >=`  
> `== !=`  
> `&&`  
> `||`  
> `= += -= *= /= %=`

> Python operator precedence
> ``  
> `+ -` (unary)  
> `* / % //`  
> `+ -` (binary)  
> `< > <= >= == !=`  
> `not`  
> `and`  
> `or`

> VisuAlg operator precedence
>
> `+ -` (unário), `nao`  
> `^` (potência)  
> `* / div mod`  
> `+ -` (binário)  
> `< > <= >= = <>`  
> `e`  
> `ou`

Algumas distinções importantes:

* C não possui um operador de exponenciação como o operador `**` do Python. Para exponenciação em C, você deverá usar uma função de biblioteca denominada `pow ()`. Por exemplo, `pow (1.1, 2.0)` calcula 1.1². O VisuAlg, por outro lado, tem um operador de potência próprio, o `^` — nesse ponto ele é mais parecido com Python do que com C: `1.1 ^ 2` calcula a mesma coisa.

* C usa símbolos em vez de palavras para as operações booleanas AND (`&&`), OR (`||`) e NOT (`!`). O VisuAlg, assim como Python, usa palavras: `e`, `ou` e `nao`.

* O nível de precedência de NOT (o operador `!`) É muito alto em C. Isso quase nunca é desejado, então você acaba precisando de parênteses na maioria das vezes quer for usar o operador `!`.

* C define a atribuição como um operador, enquanto o Python define a atribuição como um comando. O valor do operador de atribuição é o valor atribuído. Uma consequência do design de C é que uma atribuição pode legalmente ser parte de outra declaração.

```c
while ((a = getchar()) != EOF)
```

Aqui, nós atribuímos o valor retornado por `getchar ()` à variável `a`, e então testamos se o valor atribuído a` a` corresponde à constante `EOF`, que é usada para decidir se deve repetir o loop novamente . Muitas pessoas afirmam que esse estilo de programação é extraordinariamente ruim; outros acham muito conveniente evitar. O Python, é claro, foi projetado de modo que uma atribuição deva ocorrer como sua própria instrução separada, portanto, as atribuições dentro de uma condição de um comando `while` é ilegal no Python. **O VisuAlg segue a mesma regra do Python aqui**: `<-` é um comando, não um operador, então você também não consegue escrever algo como `enquanto ((a <- leia()) <> -1) faca` em VisuAlg.

* Os operadores `++` e `--` são para incrementar e decrementar uma variável. Assim, a declaração ` i ++ ` é uma forma mais curta da declaração ` i = i + 1` (ou` i + = 1` ). **Nem Python nem VisuAlg têm esses operadores.** Em Python você escreve `i += 1`; em VisuAlg, nem `+=` existe — o único jeito é por extenso: `i <- i + 1`.

* O operador de divisão `/` realiza uma divisão inteira se ambos operandos forem do tipo `int`; isto é, qualquer resto é ignorado com tal divisão. Assim, em C, a expressão ` 13 / 5` é avaliada como 2, enquanto `13 / 5.0` é 2.6: A primeira tem valores inteiros como operandos de ambos lados, enquanto a segunda tem um número de ponto flutuante à direita.

    > Por outro lado, com versões mais recentes do Python (3.0 e posterior), o operador de barra única sempre faz a divisão de ponto flutuante. Com versões mais antigas do Python, o operador single-slash funcionava como o C, mas isso muitas vezes levava a bugs - em parte porque o tipo associado a uma variável não é fixo como está em um programa em C.

    O VisuAlg resolve essa ambiguidade tendo dois operadores diferentes desde o início: `/` sempre faz divisão real (como o `/` do Python 3), e `div` faz divisão inteira (como o `//` do Python). O resto da divisão é `%` em C e Python, e `mod` em VisuAlg.

| | C | VisuAlg | Python |
|---|---|---|---|
| divisão sempre real | `/` (se algum operando for `float`/`double`) | `/` | `/` |
| divisão inteira | `/` (se ambos operandos forem `int`) | `div` | `//` |
| resto da divisão | `%` | `mod` | `%` |
| potência | `pow(x, y)` | `x ^ y` | `x ** y` |
| e / ou / não lógico | `&& \|\| !` | `e` `ou` `nao` | `and` `or` `not` |
| incremento | `i++` | *(não existe)* `i <- i + 1` | *(não existe)* `i += 1` |

!!! question "Exercício de mapeamento 2.1"
    Em VisuAlg:

    ```
    media <- (n1 + n2 + n3) / 3
    aprovado <- media >= 6 e faltas <= 15
    ```

    Escreva o trecho de C equivalente, declarando as variáveis que forem necessárias. Preste atenção especial no operador lógico "e": qual símbolo C usa no lugar da palavra `e`?

### 2.2. Tipos básicos

A lista de tipos de C é bastante restrita.

* `int` para um inteiro
* `char` para um único caractere
* `float` para um número de ponto flutuante de precisão simples
* `double` para um número de ponto flutuante de precisão dupla

O tipo `int` é o mais simples. Você também pode criar outros tipos inteiros, usando os nomes de tipo `long` e `short`. Um `long` reserva pelo menos tantos bits quanto um `int`, enquanto um `short` reserva menos bits que um `int` (ou o mesmo número ). A linguagem não garante o número de bits para cada um, mas a maioria dos compiladores atuais usa 32 bits para um `int`, o que permite números de até 2.15 × 10<sup>9</sup>. Isso é suficiente para a maioria dos propósitos, e muitos compiladores também usam 32 bits para um `long`, então as pessoas normalmente usam o `int` em seus programas.

O tipo ` char` também é simples: representa um único caracter, como uma letra ou pontuação. Você pode representar um caracter individual em um programa colocando o caractere entre aspas simples: `digit0 = '0'; ` atribuiria o caracter zero em uma variável `char denominada` `digit0`.

Dos dois tipos de ponto flutuante, `float` e `double`, a maioria dos programadores hoje utilizam quase exclusivamente o tipo `double`. Esses tipos são para números que podem ter um ponto decimal neles, como 2.5, ou para números maiores que um `int` podendo conter valores como 6.02 × 10<sup>23</sup>. Os dois tipos diferem em que um `float` normalmente utiliza apenas 32 bits de armazenamento, enquanto um `double` normalmente utiliza 64 bits. A técnica de armazenamento de 32 bits permite um intervalo de números mais estreito (−3,4 × 10<sup>38</sup> a 3,4 × 10<sup>38</sup>) e - mais problemático - cerca de 7 dígitos significativos. Um `float` não pôde armazenar um número como 281.421.906 (a população dos EUA em 2000, de acordo com o censo), porque requer nove dígitos significativos; teria que armazenar uma aproximação, como 281,421,920. Por outro lado, a técnica de armazenamento de 64 bits permite um intervalo mais amplo de números (-1,7 × 10<sup>308</sup> a 1,7 × 10<sup>308</sup>) e aproximadamente 15 dígitos significativos. Isso é mais adequado para propósitos gerais, e os 32 bits extras de armazenamento raramente valem a pena, então `double` é quase sempre preferível.

VisuAlg simplifica bastante esse ponto: não existe distinção `float`/`double`, só existe `real`. Já para inteiros, é só `inteiro`, sem `long`/`short`.

C não possui um tipo booleano para representar valores true / false. Isto tem implicações importantes para uma declaração como `if `, onde você precisa de um teste para determinar se deve executar o corpo. A abordagem de C é tratar o inteiro 0 como _false_ e todos os outros valores inteiros como _true_. O seguinte seria um programa C legal.

```c
int main() {
    int i = 5;
    if (i) {
        printf("in if\n");
    } else {
        printf("in else\n");
    }
    return 0;
}
```

Este programa compilaria, e imprimiria "in if" quando executado, desde que o valor da expressão `if( i)` seja 5, o que não é 0 e, portanto a condição seria verdadeira.

Os operadores de C que parecem operar sobre valores booleanos (como `==`, `&&`, e `||`), na verdade, operam sobre valores inteiros. Em particular, eles computam 1 para representar _true_ e 0 para representar _false_. Isso significa que você pode legitimamente digitar o seguinte para contar quantos `a`,` b` e `c` são positivos.

```c
pos = (a > 0) + (b > 0) + (c > 0);
```

Essa peculiaridade - que C considera verdadeiros os inteiros diferentes de zero - é geralmente considerado um erro. Quando a linguagem foi desenvolvido as linguagens de máquina raramente tinham suporte direto para valores booleanos, normalmente esperava que você realize esses testes comparando-os com zero. Mas os compiladores melhoraram além do ponto em que estavam quando o C foi inventado, e agora podem facilmente traduzir comparações booleanas com código de máquina eficiente. Além do mais, esse design de C leva a programas confusos, portanto, a maioria dos programadores especialistas em C evitam usar o atalho, preferindo compará-lo explicitamente a zero como uma questão de bom estilo de programação. Mas tal prática não conserta o fato de que essa peculiaridade da linguagem geralmente leva a erros de programação. A maioria das linguagens mais novas optam por um tipo especial associado a valores booleanos. (O Python possui seu próprio tipo booleano, mas também trata 0 como falso para as instruções `if`).

Aqui o VisuAlg está do lado de Python, não de C: existe um tipo `logico` de verdade, com os literais `verdadeiro` e `falso` — não dá para usar um inteiro no lugar de uma condição, como se faz (e como C permite) em `if (i)`.

| C | VisuAlg | Python |
|---|---|---|
| não existe (usa `int`, 0 = falso) | `logico` (`verdadeiro` / `falso`) | `bool` (`True` / `False`) |

### 2.3. Chaves

Várias declarações, como a instrução `if `, incluem um corpo que pode conter várias instruções. Normalmente, o corpo é cercado por chaves ('{' e '}') para indicar sua extensão. Mas quando o corpo possui apenas uma declaração, as chaves são opcionais. Assim, podemos encontrar o máximo de dois números sem chaves, já que o corpo de ambos, ` if ` e `else`, contém apenas uma única instrução.

```c
if (first > second)
    max = first;
else
    max = second;
```

Nem VisuAlg nem Python têm essa flexibilidade: em Python o bloco é sempre a indentação, sem exceção; em VisuAlg, `entao`/`senao` sempre precisam do `fimse` no final, não importa se o corpo tem uma linha ou dez.

```
se (first > second) entao
    max <- first
senao
    max <- second
fimse
```

(Também poderíamos incluir chaves em apenas um dos dois corpos, desde que esse corpo contenha apenas uma declaração.)

Os programadores C usam isso com bastante frequência quando querem que um dos vários testes ` if ` sejam executados. Um exemplo disso é com o código da fórmula quadrática abaixo. Podemos calcular o número de soluções da seguinte forma:

```c
disc = b * b - 4 * a * c;
if (disc < 0) {
    num_sol = 0;
} else {
    if (disc == 0) {
        num_sol = 1;
    } else {
        num_sol = 2;
    }
}
```

Note que a cláusula `else ` aqui contém apenas uma instrução (uma declaração `if … else`), então podemos omitir as chaves ao redor dela. Podemos então escrever da seguinte maneira:

```c
disc = b * b - 4 * a * c;
if (disc < 0) {
    num_sol = 0;
} else
    if (disc == 0) {
        num_sol = 1;
    } else {
        num_sol = 2;
    }
```

Mas essa situação surge com freqüência suficiente para que os programadores C sigam uma regra especial de indentanção nesse caso - uma regra que permite que todos os casos sejam escritos no mesmo nível de recuo.
```c
disc = b * b - 4 * a * c;
if (disc < 0) {
    num_sol = 0;
} else if (disc == 0) {
    num_sol = 1;
} else {
    num_sol = 2;
}
```
Como isso é viável usando as regras de chaves de C, C não inclui o conceito de uma cláusula `elif` que você encontra no Python. Você pode apenas agrupar quantas combinações `else if ` que você quiser.

O VisuAlg **não tem** esse atalho: como todo `se` exige um `fimse` correspondente, encadear várias condições sempre aninha um bloco dentro do outro, cada um com seu próprio `fimse` — não existe uma palavra equivalente a `elif`/`else if` que evite o aninhamento.

```
disc <- b * b - 4 * a * c
se (disc < 0) entao
    num_sol <- 0
senao
    se (disc = 0) entao
        num_sol <- 1
    senao
        num_sol <- 2
    fimse
fimse
```

Além desta situação particular, eu recomendo que você inclua as chaves de qualquer maneira. À medida que você continua trabalhando em um programa, você freqüentemente acha que deseja adicionar instruções adicionais no corpo de um `if `, e ter as chaves lá já lhe poupa o incômodo de adicioná-las mais tarde. E torna mais fácil manter o controle das chaves, já que cada nível de indentação requer uma chave direita de fechamento.

!!! question "Exercício de mapeamento 2.2"
    Em Python:

    ```python
    if idade < 12:
        categoria = "crianca"
    elif idade < 18:
        categoria = "adolescente"
    else:
        categoria = "adulto"
    ```

    1. Primeiro, escreva isso em VisuAlg (repare que você vai precisar de dois `fimse`, já que não existe `senaose`/`elif`).
    2. Depois, escreva a versão em C usando o atalho `else if` mostrado acima.

    ??? tip "Dica, não gabarito"
        A versão em C, graças ao `else if`, fica com a mesma "forma" (mesmo número de blocos, sem aninhar) que a versão em Python — mesmo o C não tendo `elif` de verdade.

### 2.4. Comandos

Vimos quatro tipos de instruções, três das quais se correlacionam de perto com o Python.

1. `int x;`

Nós já discutimos declarações de variáveis em [Seção 1.2](cap01.md#12-declaracoes-variaveis). Eles não têm paralelo no Python — mas têm paralelo direto no bloco `var` do VisuAlg.

2. `x = y + z;` ou `printf("%d", x);`

Você pode ter uma expressão como um comando. Tecnicamente, a expressão poderia ser `x + 3`; mas tal afirmação não tem sentido: pedimos ao computador para adicionar x e 3, mas não pedimos que nada aconteça a ele. Quase sempre, as expressões têm uma de duas formas: Uma forma é um operador que altera o valor de uma variável, como o operador de atribuição (`x = 3;`), o operador de atribuição de adição `+ =` ou o operador de incremento ` ++ `. A outra forma de expressão que você vê como uma instrução é uma chamada de função, como uma instrução que simplesmente chama a função `printf ()`. Em VisuAlg, o equivalente de `x = y + z;` é `x <- y + z`, sem ponto e vírgula.

3. `if (x < 0) { printf("negative"); }`

Você pode ter um comando  `if`, que funciona de forma muito semelhante ao `if` do Python (e ao `se` do VisuAlg, visto na Seção 2.3). A única grande diferença é a sintaxe: Em C, uma condição da declaração if deve ser colocada entre parênteses, não há dois pontos após a condição, e o corpo tem um conjunto de chaves que a encerram.

Como já vimos, C não possui uma cláusula `elif` como no Python; em vez disso, os programadores C usam a regra de chave opcional e escrevem ` else if` .

4. `return 0;`

Você pode ter um comando `return` para sair de uma função com um determinado valor de retorno. Ou para uma função sem valor de retorno (ou seja o tipo de retorno 'void'), você escreveria simplesmente `return;`. Em VisuAlg, isso é `retorne valor`, e só existe dentro de uma `funcao` — um `procedimento` (o equivalente a uma função `void`) não usa `retorne`.

Existem mais três tipos de instruções que se correlacionam de perto com os equivalentes do Python.

5. `while (i >= 0) { i--; }`

O comando `while` funciona de forma idêntico à do Python, embora a sintaxe seja diferente da mesma maneira que a sintaxe `if`.

```c
while (i >= 0) {
    printf("%d\n", i);
    i--;
}
```

Em VisuAlg:

```
enquanto (i >= 0) faca
    escreval(i)
    i <- i - 1
fimenquanto
```

Novamente, a expressão de teste requer um conjunto de parênteses em torno dela, não há cólon e usamos chaves para cercar o corpo do loop.

6. `break;`

Como no Python, o comando `break` sai imediatamente do loop mais interno em que ela é encontrada. Naturalmente, a declaração tem um ponto e vírgula a seguir. O equivalente em VisuAlg é a palavra `interrompa`.

7. `continue;`

Também como em Python, o comando `continue` pula para a parte inferior do loop mais interno no qual ele é encontrado e testa se deve repetir o loop novamente. Tem um ponto e vírgula também. **Atenção**: o VisuAlg **não tem** um comando equivalente a `continue`. Se você está acostumado a usar `continue` em Python, ao passar para C (ou para VisuAlg) esse é um bom momento para reparar que você já conhece uma alternativa sem `continue` — geralmente invertendo a condição e aninhando o resto do corpo do laço dentro de um `if`.

8. `for (i = 0; i < 10; i++) {…`

Embora o Python também tenha um comando `for`, seu propósito e sua sintaxe possuem pouca similaridade com a declaração` for` de C. Em C, a palavra-chave `for` é seguida por um conjunto de parênteses contendo três partes separadas por ponto e vírgula.

```c
for (init; test; update)
```

A intenção do loop `for` de C é permitir passar uma variável através de uma série de números, como a contagem de 0 a 9. A parte antes do primeiro ponto e vírgula (_init_) é executada assim que a instrução` for` é alcançada; é para inicializar a variável que contará. A parte entre os dois pontos e vírgulas (_test_) é avaliada antes de cada iteração para determinar se a iteração deve ser repetida. E a parte que segue o ponto-e-vírgula final (_update_) é avaliada no final de cada iteração para atualizar a variável de contagem para a iteração a seguir.

Nesse ponto, quem já programa em VisuAlg tem uma vantagem sobre quem só programa em Python: o `para` do VisuAlg já é limitado a "andar por uma faixa de valores", exatamente como o `for` mais comum de C — só muda a sintaxe.

Na prática, loops 'for' são usados com mais freqüência para contar n iterações. O idioma padrão para isso é o seguinte.

```c
for (i = 0; i < n; i++) {
    comandos
}
```

Em VisuAlg:

```
para i de 0 ate n-1 faca
    comandos
fimpara
```
Aqui temos uma variável de contador `i` cujo valor começa em 0. Com cada iteração, testamos se ` i` atingiu `n` ou não; e se não tiver, então nós executamos o corpo da declaração `for` e então executamos a atualização` i++ `para que `i` vá para o inteiro seguinte. O resultado é que o corpo é executado para cada valor de `i` de 0 até n - 1. Repare que o `para` do VisuAlg já inclui o limite final (`ate n-1`), então não é preciso (nem existe) uma "condição de parada" separada como em C — mais parecido, nesse ponto, com o `range(n)` do Python.

Mas você também pode usar um laço `for` para outros propósitos. No exemplo a seguir, exibimos as potências de 2 até 512. Observe como a parte de atualização da declaração `for` mudou para" `p *= 2`".

```c
for (p = 1; p <= 512; p *= 2) {
    printf("%d\n", p);
}
```

Esse é um bom exemplo de onde o `for` de C é **mais flexível** do que o `para` de VisuAlg ou o `for` de Python: como C separa init/teste/atualização como três expressões livres, a "atualização" pode ser qualquer coisa (aqui, multiplicar por 2), não apenas somar um passo fixo. Nem o `para ... passo ...` do VisuAlg nem o `range(inicio, fim, passo)` do Python conseguem expressar diretamente um passo multiplicativo — nesse caso a forma mais natural em VisuAlg (ou Python) seria reescrever como um `enquanto`/`while`.

9. `switch (conceito) { case 'A':`…

O comando `switch` não tem equivalente em Python, mas é essencialmente equivalente a uma forma particular de uma declaração` if`… `elif`…` elif`… `else` onde cada um dos testes é para valores diferentes da mesma variável. O VisuAlg tem um comando parecido, o `escolha`.

Um comando `switch` é útil quando você tem vários blocos de código possíveis, um dos quais deve ser executado com base no valor de uma expressão específica. Aqui está uma instância clássica da declaração `switch`:

```c
switch (letter_grade) {
case 'A':
    gpa += 4;
    credits += 1;
    break;
case 'B':
    gpa += 3;
    credits += 1;
    break;
case 'C':
    gpa += 2;
    credits += 1;
    break;
case 'D':
    gpa += 1;
    credits += 1;
    break;
case 'W':
    break;
default:
    credits += 1;
}
```

Dentro dos parênteses após a palavra chave `switch`, temos uma expressão cujo valor deve ser um caractere ou inteiro. O computador avalia essa expressão e desce para uma das palavras-chave `case` com base em seu valor. Se o valor é o caractere _A_, então o primeiro bloco é executado (`gpa + = 4; créditos += 1;`); se for _B_, então o segundo bloco é executado; se não for nenhum dos caracteres (como um _F_), o bloco que segue a palavra-chave `default` é executado.

O comando `break` no final de cada bloco é um detalhe crucial: Se o comando `break` for omitida, o computador continua no bloco seguinte. Em nosso exemplo acima, se omitimos todas as declarações `break`, então uma nota de _A_ levaria o computador a executar não apenas o caso _A_, mas também os casos _B_, _C_, _D_, _W_ e `default`. O resultado seria que o `gpa` aumentaria em 4 + 3 + 2 + 1 = 10, enquanto que `credits` aumentaria em 5. Ocasionalmente você realmente deseja que o computador continue no próximo caso (chamado de "fall-through") , e assim você omite uma declaração `break`; mas na prática você quase sempre quer a declaração `break` no final de cada caso.

Há uma exceção importante em que fall-through é bastante comum: às vezes, você deseja que o mesmo código seja aplicado a dois valores diferentes. Por exemplo, se quiséssemos que nada acontecesse se o grau fosse _P_ ou _W_, então poderíamos incluir ` case 'P': ` logo antes de `case 'W'`, sem nenhum código intermediário.

O `escolha` do VisuAlg equivalente ficaria assim:

```
escolha (letter_grade)
    caso "A"
        gpa <- gpa + 4
        credits <- credits + 1
    caso "B"
        gpa <- gpa + 3
        credits <- credits + 1
    caso "C"
        gpa <- gpa + 2
        credits <- credits + 1
    caso "D"
        gpa <- gpa + 1
        credits <- credits + 1
    caso "W"
    outrocaso
        credits <- credits + 1
fimescolha
```

Repare na diferença mais importante: **o `escolha` do VisuAlg não tem fall-through** — cada `caso` já se comporta como se tivesse um `break` embutido, então não existe `interrompa` dentro de um `caso`, e também não tem como "cair" propositalmente de um caso para o próximo (fora o truque de deixar um `caso` vazio, como fizemos acima com `"W"`). Se você programou em VisuAlg antes, o fall-through de C é provavelmente o maior "gotcha" que você vai encontrar em `switch`.

!!! question "Exercício de mapeamento 2.3"
    Em VisuAlg:

    ```
    para i de 1 ate 10 faca
        se (i mod 2 = 0) entao
            escreval(i)
        fimse
    fimpara
    ```

    Escreva o `for` em C que produz a mesma saída (os números pares de 1 a 10, um por linha). Você pode usar `if` dentro do `for`, exatamente como no VisuAlg — a diferença está só na sintaxe do cabeçalho do laço e no delimitador de bloco.

### 2.5. Arrays

O Python suporta muitos tipos que combinam os tipos atômicos básicos em um grupo:tuples, lists, strings, dictionaries, sets.

O suporte de C é muito mais rudimentar: o tipo composto _só_ é o array, que é semelhante à lista do Python, exceto que um array em C não pode crescer ou encolher - seu tamanho é fixo no momento da criação. Nesse ponto o VisuAlg é parecido com C: um `vetor` também tem tamanho fixo, definido na declaração. Você pode declarar e acessar uma matriz da seguinte maneira.

```c
double pops[50];
pops[0] = 897934;
pops[1] = pops[0] + 11804445;
```

Em VisuAlg:

```
var
    pops: vetor[0..49] de real
inicio
    pops[0] <- 897934
    pops[1] <- pops[0] + 11804445
```

Neste exemplo, criamos um array contendo 50 slots para valores double. Os slots são indexados de 0 a 49.

C não tem suporte para acessar o tamanho de um array depois de criado; isto é, não há nada análogo ao `len (pops) do Python ou` pops.length` do Java. O VisuAlg também não guarda o tamanho do vetor em uma variável para você — mas, diferente de C, ele sabe os limites declarados (`0..49`) e os usa para checar os acessos, como veremos a seguir.

Um ponto importante no que diz respeito aos arrays: O que acontece se você acessar um índice de array fora do array, como acessar `pops [50]` ou `pops [-100]`? Com Python ou Java, isso terminará o programa com uma mensagem amigável apontando para a linha com falha e dizendo que o programa foi além dos limites da matriz. **O VisuAlg se comporta como Python/Java aqui**: acessar `pops[50]` gera um erro de execução avisando que o índice está fora da faixa declarada. C não é tão amigável. Quando você acessa além dos limites de um array, ele o faz cegamente.

Isso pode levar a um comportamento peculiar. Por exemplo, considere o programa a seguir.

```c
int main() {
    int i;
    int vals[5];

    for (i = 0; i <= 5; i++) {
        vals[i] = 0;
    }
    printf("%d\n", i);
    return 0;
}
```

Alguns sistemas (incluindo uma distribuição do Linux que eu uso) colocariam `i` na memória logo após a matriz` vals`; assim, quando `i` atinge 5 e o computador executa" `vals [i] = 0`", ele de fato redefine a memória correspondente a `i` para 0. Como resultado, o loop` for` foi zerado, e o programa passa pelo loop novamente e repetidamente. O programa nunca alcança a chamada de função `printf` e o programa nunca termina.

> Vamos testar ? :)

Em programas mais complicados, a falta de checagem de limites de array pode levar a bugs muito difíceis, onde o valor de uma variável muda misteriosamente em centenas de funções, e você como programador deve determinar onde um índice de array foi acessado fora dos limites. Este é o tipo de bug que leva muito tempo para descobrir e reparar. Se você tentasse escrever a mesma falha em VisuAlg (um `para i de 0 ate 5` sobre um `vetor[0..4]`), o próprio VisuAlg pararia a execução com um erro assim que `i` chegasse a 5 — é uma boa forma de visualizar, em segurança, o que o C faz "por baixo dos panos" sem avisar.

É por isso que você deve considerar as mensagens de erro fornecidas pelo Python (ou Java, ou VisuAlg) como extraordinariamente amigáveis: não apenas informa a causa de um problema, mas também informa exatamente qual linha do programa estava com defeito. Isso economiza muito tempo de depuração.

De vez em quando, você verá uma falha no programa em C, com uma mensagem como "falha de segmentação" ou erro de barramento. "Não será útil incluir qualquer indicação de qual parte do programa é a culpa: tudo que você consegue é aquelas essas duas palavras. Esses erros geralmente significam que o programa tentou acessar um local de memória inválido. Isso pode indicar uma tentativa de acessar um índice de matriz inválido, mas normalmente o índice precisa estar bem fora dos limites para que isso ocorra. (Em geral, isso indica uma tentativa de fazer referência a um ponteiro não inicializado ou a um ponteiro NULL, que discutiremos mais adiante.)

!!! question "Exercício de mapeamento 2.4"
    Em VisuAlg:

    ```
    var
        notas: vetor[0..4] de real
        i: inteiro
        soma: real
    inicio
        soma <- 0
        para i de 0 ate 4 faca
            leia(notas[i])
            soma <- soma + notas[i]
        fimpara
        escreval("Media: ", soma / 5)
    ```

    Escreva a versão em C. Pense em como ler um valor do teclado em C (dica: é a função `scanf`, que ainda não vimos em detalhe neste texto — se quiser, pesquise a sintaxe de `scanf("%lf", &notas[i])` antes de tentar, mas tente primeiro montar o resto do programa ao redor dela).

### 2.6. Comentários

No design original de C, todos os comentários começam com uma barra seguida por um asterisco ("/\*") e terminam com um asterisco seguido por uma barra ("\*/"). O comentário pode abranger várias linhas.

```c
/* gcd - returns the greatest common
 * divisor of its two parameters */
int gcd(int a, int b) {
```

(O asterisco na segunda linha é ignorado pelo compilador. A maioria dos programadores o incluiria, porém, porque parece mais bonito e também porque indica a um leitor humano que o comentário está sendo continuado a partir da linha anterior.)

Embora este comentário em várias linhas tenha sido o único comentário originalmente incluído no C, o C ++ introduziu um comentário de linha única que provou ser tão útil que a maioria dos compiladores C atuais também o suportam. Começa com dois caracteres de barra ("//") e vai para o final da linha.

```c
int gcd(int a, int b) {
  if (b == 0) {
    return a;
  } else {
    // recurse if b != 0
    return gcd(b, a % b);
  }
}
```

O VisuAlg só tem o equivalente ao comentário de linha única de C — também com `//`, indo até o final da linha. Não existe um equivalente direto ao comentário de bloco `/* ... */` para comentar várias linhas de uma vez; em VisuAlg, cada linha do comentário precisa do seu próprio `//`.

```
// gcd - devolve o maximo divisor comum
// entre os dois parametros
funcao gcd(a, b: inteiro): inteiro
```

### 2.7. Lista de exercícios

#### Estruturas condicionais

1. Faça um programa que leia um número do usuário e determine se este número é par ou ímpar.
2. Faça um programa que receba valores inteiros X, Y e Z do usuário e determine se estes valores podem formar os lados de um triângulo. Em caso afirmativo, informe se o triângulo é equilátero, isósceles ou escaleno.
3. Faça um programa que receba um símbolo de operação do usuário (`+`, `-`, `/` ou `*`) e dois números reais, e retorne o resultado da operação sobre esses dois números (dica: o símbolo pode ser lido como um `char`, com `scanf(" %c", &op);` — repare no espaço antes do `%c`, que evita problemas com quebras de linha deixadas no buffer).

#### Estruturas de repetição

4. O número 3025 tem a seguinte característica: 30 + 25 = 55, e 55×55 = 3025. Faça um programa para encontrar todos os números de 4 algarismos com a mesma característica do número 3025.
5. Faça um programa que mostre os 100 primeiros termos da sequência de Fibonacci.
6. Um método para calcular uma raiz quadrada aproximada:
    1. Comece testando um `g` qualquer.
    2. Se `g*g` está suficientemente perto de `x`, então `g` é uma boa aproximação da raiz quadrada de `x` — fim.
    3. Senão, crie um novo `g`, que é a média entre `g` e `x/g` (ou seja, `g = (g + x/g) / 2`).
    4. Com o novo `g`, volte ao passo 2.

    Implemente esse método em C, usando um `while`.
7. Uma outra forma de calcular a raiz quadrada de um número: subtraia dele os ímpares consecutivos a partir de 1, até que o resultado fique menor ou igual a zero. O número de subtrações feitas é a raiz quadrada exata (se o resultado final for exatamente 0) ou aproximada do número. Exemplo, para 16: 16 − 1 = 15, 15 − 3 = 12, 12 − 5 = 7, 7 − 7 = 0 → 4 subtrações, e de fato √16 = 4.
8. Faça um programa que receba dois números do usuário e calcule o MDC (máximo divisor comum) entre eles usando o método de Euclides. O programa deve continuar pedindo pares de números até que o usuário digite 0 e 0.
9. Faça um programa que encontre todos os pares de números amigáveis entre 1 e 10000. Um par de números é amigável quando cada um deles é igual à soma dos divisores do outro (excluindo ele mesmo). Exemplo: 220 e 284 são amigáveis.
10. *(desafio, com uma função nova)* Faça um programa que sorteie um número entre 0 e 500, e pergunte ao usuário, repetidamente, qual é o "número mágico". A cada tentativa, o programa deve dizer se o número secreto é maior ou menor que a tentativa, e contar quantas tentativas foram feitas. Ao acertar, classifique o usuário: 1 a 3 tentativas — muito sortudo; 4 a 6 — sortudo; 7 a 10 — normal; mais que 10 — tente novamente.

    Para sortear um número, você vai precisar de uma função que ainda não vimos no texto: `rand()`, da biblioteca `<stdlib.h>`. `rand() % 501` gera um número pseudoaleatório entre 0 e 500. Se quiser que a sequência sorteada mude a cada execução, pesquise também sobre `srand(time(NULL))` (da biblioteca `<time.h>`), chamada uma única vez no início do `main`.
