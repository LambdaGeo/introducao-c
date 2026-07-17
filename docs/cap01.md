## 1. Construindo um simples programa

Começaremos com vários princípios gerais, usando para isso programa C completo, mas limitado, até o final da Seção 1.

Ao longo do texto, sempre que fizer sentido, vamos colocar lado a lado o mesmo trecho em **C**, em **Python** e em **VisuAlg** (o pseudocódigo/IDE usado em muitas disciplinas introdutórias no Brasil). A ideia não é decorar C do zero, e sim **mapear** o que você já sabe fazer em Python/VisuAlg para a sintaxe de C.

!!! tip "Como estudar com este material (e com IA)"
    Antes de perguntar para uma IA "como eu escrevo isso em C?", tente primeiro encontrar a resposta você mesmo nas seções abaixo ou testando no compilador. Depois de ter uma solução que funciona, aí sim vale a pena discutir com a IA se existe uma forma mais idiomática (mais "natural") de escrever aquele mesmo código em C. Pular direto para "traduz esse programa pra C" pula justamente a parte que ensina a linguagem.

### 1.1. Compiladores versus interpretadores

Uma grande diferença entre C e Python é simplesmente como você executa programas escritos nas duas linguagens. Com programas em C, você geralmente usa um _compilador_ antes de executar. Por outro lado, com o Python, você normalmente usa um _interpretador_. Um **compilador** gera um arquivo contendo a tradução do programa no código nativo da máquina. O compilador na verdade não executa o programa; em vez disso, você primeiro executa o compilador para criar um executável nativo e, em seguida, executa o executável gerado. Assim, depois de criar um programa em C,

```c
#include <stdio.h>

int main () {
    printf ("Ola mundo!\n");
    return 0;
}
```

executá-lo é um processo de duas etapas.

```
sh:~$ gcc meuprograma.c -o meuprograma
sh:~$ ./meuprograma
Ola mundo!
```

No primeiro comando ("gcc meuprograma.c"), invocamos o compilador, chamado _gcc_. O compilador lê o arquivo meuprograma.c, no qual salvamos nosso código C, e ele gera um novo arquivo chamado meuprograma contendo uma tradução deste código no código binário usado pela máquina. No segundo comando ("./meuprograma"), dizemos ao computador para executar este código binário. Como está executando o programa, o computador não tem idéia de que meuprograma acabou de ser criado a partir de algum programa em C: ele simplesmente executa cegamente o código encontrado no arquivo, da mesma forma que executa cegamente o código encontrado no gcc em resposta ao primeiro comando.

Por outro lado, um **interpretador** lê o programa escrito pelo usuário e o executa diretamente. Isso remove uma etapa do processo de execução, mas um compilador tem a vantagem de gerar um executável que é executado da mesma forma que a maioria dos outros aplicativos da máquina, e usualmente gerando códigos mais rápidos.

Ser compilado tem algumas implicações radicais no design da linguagem. C é projetada para que o compilador possa dizer tudo o que precisa saber para traduzir o programa C sem realmente executar o programa.

> Observação 1: esse texto tem como objetivo ser mais simples. Por exemplo, o processo de execução em C é feito em diversas outras etapas, que inclui a **pré-compilaação** e a **linkedição**.

> Observação 2: alguns interpretadores atuais podem usar um processo de compilação para um código intermediário. Então, o código executado não é exatamente o código escrito pelo programador.

E o VisuAlg, onde entra nisso? O VisuAlg não compila: o próprio ambiente **interpreta** o pseudocódigo linha a linha e mostra o resultado na hora, parecido com o que o Python faz. O mesmo "Olá mundo" em VisuAlg fica assim:

```
algoritmo "OlaMundo"
inicio
    escreval("Ola mundo!")
fimalgoritmo
```

Repare que, apesar de não compilar, o VisuAlg já usa palavras-chave para delimitar onde o algoritmo começa e termina (`inicio` / `fimalgoritmo`) — isso vai ajudar bastante quando chegarmos nas chaves `{ }` de C, na Seção 1.3.

### 1.2. Declarações Variáveis

Entre as informações que compilador da linguagem C requer, um dos exemplos mais notáveis é a **declarações de variáveis**, informando ao compilador sobre a variável antes que a variável seja realmente usada. Isso é típico de muitas linguagens de programação proeminentes, particularmente entre aquelas que devem ser compiladas antes de serem executadas.

> Observação: essas linguagens que requerem a declaração de variáveis, associando cada variável a um tipo, são conhecidas como estaticamente tipadas.

Em C, a declaração da variável define o tipo **da variável**, especificando se é um inteiro (`int`), um número de ponto flutuante (`double`), caracter (`char`), ou algum outro tipo que estudaremos depois. Uma vez que você declara que uma variável é de um tipo particular, você não pode mudar seu tipo: Se a variável `x` é declarada do tipo`double`, e você atribui `x = 3;` , então ` x` irá, na verdade, manter o valor de ponto flutuante 3.0 em vez do inteiro 3. Você pode, se desejar, imaginar que `x` é uma caixa que é capaz apenas de manter valores `double `; se você tentar colocar alguma outra coisa dentro dele (como o `int` valor 3), o compilador o converterá em um `double` para que ele caiba.

Declarar uma variável é bastante simples: você insere o tipo da variável, alguns espaços em branco, o nome da variável e um ponto e vírgula:

```c
double x;
```

Se você já usa VisuAlg, essa exigência não é novidade: VisuAlg **também** é estaticamente tipada, e também exige declarar cada variável antes de usá-la — só que reunidas em um bloco `var`, no início do algoritmo, em vez de uma linha solta como em C:

```
var
    x: real
```

| C | VisuAlg | Python |
|---|---|---|
| `int` | `inteiro` | `int` |
| `double` / `float` | `real` | `float` |
| `char` | `caractere` | `str` (1 caractere) |
| *(não existe tipo string nativo — Seção "Strings")* | `cadeia` | `str` |

Em C, as de variáveis pertencem a função ou bloco em que são declarada.

Se você esquecer de declarar uma variável, o compilador se recusará a compilar um programa no qual uma variável é usada, mas não é declarada. A mensagem de erro indicará a linha dentro do programa, o nome da variável e uma mensagem como "símbolo não declarado" (symbol undeclared). Por exemplo, dado o seguinte programa:

```c
int main () {
    a = 10;
    b = 20;
    c = a + b;
}
```

Uma tentativa de compilação, levaria aos seguintes erros:

```
$ gcc programaerrovariavelnaodeclarada.c
programaerrovariavelnaodeclarada.c:5:5: error: ‘a’ undeclared (first use in this function)
     a = 10;
     ^
programaerrovariavelnaodeclarada.c:5:5: note: each undeclared identifier is reported only once for each function it appears in
programaerrovariavelnaodeclarada.c:6:5: error: ‘b’ undeclared (first use in this function)
     b = 20;
     ^
programaerrovariavelnaodeclarada.c:7:5: error: ‘c’ undeclared (first use in this function)
     c = a + b;
     ^
```

O VisuAlg reclamaria de forma muito parecida se você tentasse usar `a`, `b` e `c` sem declará-los antes no bloco `var` — é exatamente o mesmo tipo de erro que você já deve ter visto lá.

Para um programador Python, parece difícil incluir essas declarações de variáveis em um programa, embora isso seja mais fácil com mais prática. Os programadores de C tendem a sentir que as declarações variáveis valem a menor dor. A maior vantagem é que o compilador irá identificar automaticamente sempre que um nome de variável for digitado incorretamente e apontar diretamente para a linha onde ele está escrito incorretamente. Isso é muito mais conveniente do que executar um programa e descobrir que ele está errado em algum lugar devido ao nome da variável com erros ortográficos.

!!! question "Exercício de mapeamento 1.1"
    Em VisuAlg, você escreveria isto para declarar as variáveis de um pequeno cálculo de média:

    ```
    var
        nota1, nota2, nota3: real
        media: real
    ```

    **Sem pedir a tradução pronta para a IA**: como ficam essas quatro declarações em C? Pense primeiro em qual tipo de C corresponde a `real`, e se dá para declarar várias variáveis do mesmo tipo em uma única linha (como no VisuAlg) ou se você prefere uma linha por variável.

    ??? tip "Dica, não gabarito"
        Releia a tabela de tipos acima. Em C também é possível declarar várias variáveis do mesmo tipo separadas por vírgula em uma única instrução, terminando com `;`.

### 1.3. Espaço em branco

Em Python, os espaços em branco, tabs e quebra de linhas são importantes: você separa suas instruções colocando-as em linhas separadas e indica a extensão de um bloco (como o corpo de uma instrução `while` ou `if`) usando recuo. Esses usos do espaço em branco são idiossincrasias do Python. (Reconhecidamente, FORTRAN e BASIC também usam quebras de linha para instruções separadas, mas nenhuma outra linguagem entre as principais depende de espaços em branco para indicar blocos.)

Como a maioria das linguagens de programação, C não usa espaço em branco, exceto para separar palavras. A maioria das declarações é terminada com um ponto e vírgula `;`, e blocos de instruções são indicados usando um conjunto de chaves, `{` e `}`. Aqui está um fragmento de exemplo de um programa em C, com seu equivalente em Python.

```c
disc = b * b - 4 * a * c;
if (disc < 0) {
    num_sol = 0;
} else {
    t0 = -b / a;
    if (disc == 0) {
        num_sol = 1;
        sol0 = t0 / 2;
    } else {
        num_sol = 2;
        t1 = sqrt(disc) / a;
        sol0 = (t0 + t1) / 2;
        sol1 = (t0 - t1) / 2;
    }
}
```

Equivalente em Python:

```python
disc = b * b - 4 * a * c
if disc < 0:
    num_sol = 0
else:
    t0 = -b / a
    if disc == 0:
        num_sol = 1
        sol0 = t0 / 2
    else:
        num_sol = 2
        t1 = disc ** 0.5 / a
        sol0 = (t0 + t1) / 2
        sol1 = (t0 - t1) / 2
```

Equivalente em VisuAlg — repare que, assim como C, o VisuAlg **também não depende de indentação** para saber onde um bloco começa e termina: em vez de chaves, ele usa as palavras `entao`/`senao`/`fimse`.

```
disc <- b * b - 4 * a * c
se (disc < 0) entao
    num_sol <- 0
senao
    t0 <- -b / a
    se (disc = 0) entao
        num_sol <- 1
        sol0 <- t0 / 2
    senao
        num_sol <- 2
        t1 <- raizq(disc) / a
        sol0 <- (t0 + t1) / 2
        sol1 <- (t0 - t1) / 2
    fimse
fimse
```

Note três diferenças de sintaxe que aparecem toda hora e valem a pena guardar:

| | C | VisuAlg | Python |
|---|---|---|---|
| atribuição | `=` | `<-` | `=` |
| igualdade (comparação) | `==` | `=` | `==` |
| delimitador de bloco | `{ }` | palavra-chave de abertura ... `fimXXX` | indentação |

O programa C à esquerda é como eu escreveria. No entanto, o espaço em branco é insignificante, então o computador ficaria tão feliz se eu tivesse escrito o seguinte.

```c
disc=b*b-4*a*c;if(disc<0){
num_sol=0;}else{t0=-b/a;if(
disc==0){num_sol=1;sol0=t0/2
;}else{num_sol=2;t1=sqrt(disc/a;
sol0=(t0+t1)/2;sol1=(t0-t1)/2;}}
```

Enquanto o computador pode estar tão feliz com isso, nenhum humano sensato preferiria. Assim, qualquer programador competente tende a ser muito cuidadoso com os espaços em branco para indicar a estrutura do programa.

(Existem algumas exceções à regra de ignorar o espaço em branco: É ocasionalmente significativo separar palavras e símbolos. O fragmento `intmain` é diferente do fragmento ` int main` ; da mesma forma, o fragmento `a++ + 1` é diferente do fragmento `a+ + +1`.)

### 1.4. A função `printf ()`

À medida que trabalhamos para escrever programas C úteis, um ingrediente importante é exibir resultados para o usuário ver, o que você realizaria usando `print` em Python ou `escreva`/`escreval` em VisuAlg. Em C, você usa `printf ()`. Esta é, na verdade, uma função, uma das mais úteis na biblioteca padrão da linguagem.

A maneira como os parâmetros para `printf ()` funcionam é um pouco complicada, mas também bastante conveniente: O primeiro parâmetro é uma string especificando o formato do que imprimir, e os seguintes parâmetros indicam os valores a serem impressos. A maneira mais fácil de entender isso é olhar para um exemplo.

```c
printf("# solns: %d\n", num_sol);
```

Esta linha diz para imprimir usando "# solns:%d\n" como a string de formatação. A função `printf ()` passa por esta string de formatação, imprimindo os caracteres "# solns:" antes de chegar ao caractere de porcentagem '%'. O caractere de porcentagem é especial para `printf ()`: Diz para imprimir um valor especificado em um parâmetro subseqüente. Nesse caso, um _d_ minúsculo segue o caractere de porcentagem, indicando para exibir o parâmetro como um `int` no formato decimal. (O _d_ significa decimal.) Então, quando `printf ()` alcança `%d`, ele olha o valor do parâmetro a seguir (vamos imaginar `num_sol` é 2 neste exemplo) e exibe esse valor. Em seguida, continua através da string de formatação, neste caso, exibindo um caractere de quebra de linha. No geral, o usuário vê a seguinte linha de saída:

> \# solns: 2

Esse esquema de "string de formato com marcadores %" é a maior novidade de sintaxe aqui — nem Python (`print`) nem VisuAlg (`escreva`) exigem que você diga o tipo do valor que está imprimindo, eles simplesmente concatenam os argumentos:

```python
print("# solns:", num_sol)
```

```
escreva("# solns: ", num_sol)
```

Como o Python, o C permite incluir caracteres de escape em uma string usando uma barra invertida. A sequência "\n" representa o caractere de quebra de linha. Da mesma forma, "\t" representa o caractere de tabulação, "\" "representa o caractere de aspas duplas e" \\ "representa o caractere de barra invertida. Esses caracteres de escape fazem parte da sintaxe C, não fazem parte da função ` printf () ` (isto é, a string que a função `printf ()` recebe na verdade contém uma nova linha, não uma barra invertida seguida por um _n_. Assim, a natureza da barra invertida é fundamentalmente diferente do caractere percentual, que printf () veria e interpretaria em tempo de execução.)

Vamos ver outro exemplo.

```c
printf("# solns: %d", num_sol);  
printf("solns: %f, %f", sol0, sol1);
```

Vamos supor que `num_sol` contém 2, `sol0` com 4 e `sol1` com 1. Quando o computador alcança essas duas chamadas de função `printf ()`, ele primeiro executa a primeira linha, que exibe "\ # solns: 2 ", E depois o segundo, que mostra " solns: 4000000, 1.000000 ", conforme ilustrado abaixo.

> \# solns: 2solns: 4.000000, 1.000000

Note que `printf ()` exibe apenas o que é dito, sem adicionar espaços extras ou novas linhas; Se quisermos que uma nova linha seja inserida entre as duas saídas, precisaríamos incluir "\n" no final da primeira string de formatação. Em VisuAlg, essa preocupação não existe: `escreva` nunca pula linha sozinho e `escreval` sempre pula — não existe meio-termo controlado por um caractere de escape dentro da string.

A segunda chamada para `printf ()` neste exemplo ilustra como a função pode imprimir múltiplos valores de parâmetro. Na verdade, não há realmente nenhuma razão para não termos combinado as duas chamadas para `printf ()` em uma neste caso.

```c
printf("# solns: %dsolns: %f, %f", num_sol, sol0, sol1);
```

A propósito, a função `printf ()` exibe "4.00000" ao invés de simplesmente "4" porque a string de formato usa "%f", que diz interpretar os parâmetros como números de ponto flutuante. Se você quiser exibir apenas "4", você pode se sentir tentado a usar "%d". Mas isso não funcionaria, porque `printf ()` interpretaria a representação binária usada para um número de ponto flutuante como uma representação binária para um inteiro, e esses tipos são armazenados de maneiras completamente diferentes. No meu computador, substituir cada "%f" por "%d" leva à seguinte saída:

> `# solns: 2solns: 0, 1074790400`

Há uma variedade de caracteres que podem seguir o caractere de porcentagem na string de formatação.

* `%d`, como já vimos, diz para imprimir um valor `int` na forma decimal.
* `%x` diz para imprimir um valor `int` em formato hexadecimal.
* `%f` diz para imprimir um valor `double` em forma de ponto decimal.
* `%e` diz para imprimir um valor `double` em notação científica (por exemplo, 3.000000e8).
* `%c` diz para imprimir um valor `char`.
* `%s` diz para imprimir uma string. Não há nenhum tipo de variável para representar uma string, mas C suporta alguns recursos de string usando matrizes de caracteres. Adiaremos a discussão desses recursos para mais tarde, depois de discutirmos os indicadores, já que as sequências envolvem alguns conceitos mais complexos que ainda não vimos.

Você também pode incluir um número entre o caractere de porcentagem e o descritor de formato como em "%10d", que diz `printf ()` para justificar à direita um número inteiro decimal em dez colunas.

!!! question "Exercício de mapeamento 1.2"
    Em VisuAlg você escreveria:

    ```
    escreval("Media da turma: ", media)
    ```

    E em Python:

    ```python
    print("Media da turma:", media)
    ```

    Escreva o `printf` equivalente em C, assumindo que `media` é uma variável do tipo `double`. Preste atenção em duas coisas que **não existem** em VisuAlg/Python e que você precisa adicionar: (1) o especificador de formato certo para `double`, e (2) o caractere de quebra de linha, se você quiser que apareça (o `printf` não pula linha sozinho, igual ao `escreva` do VisuAlg).

    ??? tip "Dica, não gabarito"
        `double` usa o mesmo especificador que `float`. Reveja a lista de especificadores logo acima se não lembrar qual é.

### 1.5. Funções

Ao contrário do Python, todo o código C deve estar aninhado dentro das funções e as funções usualmente não são aninhadas umas nas outras. Assim, a estrutura geral de um programa em C é tipicamente muito direta: É uma lista de definições de funções, uma após a outra, cada uma contendo uma lista de instruções a serem executadas quando a função é chamada.

Aqui está um exemplo simples de uma definição de função:

```c
double expon(double b, int e) {
    if (e == 0) {
        return 1.0;
    } else {
        return b * expon(b, e - 1);
    }
}
```

Uma função C é definida nomeando o tipo de retorno (`double` aqui, já que a função produz um resultado de ponto flutuante), seguido pelo nome da função (`expon`), seguido por um conjunto de parênteses listando os parâmetros. Cada parâmetro é descrito incluindo o tipo do parâmetro e o nome do parâmetro. A seguir, a lista de parâmetros entre parênteses é um conjunto de chaves, no qual você aninha o corpo da função.

Se você tem uma função que não possui nenhum valor de retorno útil, você usaria 'void` como o tipo de retorno.

Programas possuem uma função especial chamada `main`, cujo tipo de retorno é um inteiro. Esta função é o "ponto de partida" para o programa: O computador essencialmente chama a função `main` do programa quando quer executar o programa. O valor de retorno inteiro é aparentemente sem sentido; sempre retornaremos 0 em vez de nos preocuparmos sobre como o valor de retorno pode ser usado.

> Observação: este resultado é útil ao sistema operacional, pois se um programa retorna 0, o sistema operacional sabe que ele executou sem erros.

Estamos agora em posição de apresentar um programa C completo, juntamente com seu equivalente em Python.

```c
int gcd(int a, int b) {
  if (b == 0) {
    return a;
  } else {
    return gcd(b, a % b);
  }
}

int main() {
  printf("GCD: %d\n",
    gcd(24, 40));
  return 0;
}
```

```python
def gcd(a, b):
  if b == 0:
    return a
  else:
    return gcd(b, a % b)

print("GCD: " + str(gcd(24, 40)))
```

Em VisuAlg, uma "função" (`funcao`) sempre tem um valor de retorno; quando você não precisa de retorno, usa-se `procedimento`. Repare também que `%` (resto da divisão) em C corresponde à palavra `mod` em VisuAlg — assim como `!=` de C corresponde a `<>` em VisuAlg:

```
funcao gcd(a, b: inteiro): inteiro
inicio
    se (b = 0) entao
        retorne a
    senao
        retorne gcd(b, a mod b)
    fimse
fimfuncao

algoritmo "gcd"
inicio
    escreval("GCD: ", gcd(24, 40))
fimalgoritmo
```

Como você pode ver, o programa C consiste em duas definições de função. Em contraste com o programa Python, onde a linha `print` existe fora de qualquer definição de função, o programa C requer que `printf()` esteja na função `main` do programa, já que é aqui que colocamos todo o código de nível superior é concluir quando o programa é executado.)

!!! question "Exercício de mapeamento 1.3"
    Você já sabe escrever, em Python **ou** em VisuAlg, uma função/procedimento que calcula o fatorial de um número `n` (recursivamente ou com um laço, como preferir). Antes de perguntar a uma IA:

    1. Escreva essa função em Python ou VisuAlg (o que for mais natural pra você) — nem que seja no papel.
    2. Usando só o que foi mostrado nesta seção (tipo de retorno, chaves, ponto e vírgula, `return`), tente escrever a versão em C.
    3. Compile e teste. Só depois de ter algo funcionando, pergunte a uma IA se existe uma forma mais idiomática de escrever essa mesma função em C.

    ??? tip "Dica, não gabarito"
        O cabeçalho da função vai ter o formato `int fatorial(int n) { ... }`. Pense em qual é o caso base (quando `n` é 0 ou 1) antes de pensar no caso recursivo.

### 1.6. Lista de exercícios

Estes exercícios usam só o que foi visto até aqui: declaração de variáveis, expressões, `printf` e funções — sem `if`, sem laços. Se você sentir falta de alguma dessas ferramentas em algum exercício, é sinal de que ele foi pensado para o próximo capítulo; tente resolver só com o que já foi apresentado.

1. Faça um programa que receba do usuário um número inteiro representando os segundos transcorridos desde 00:00 de um dado dia, e converta-o para horas, minutos e segundos.
2. Faça um programa que receba 3 valores inteiros do usuário e mostre a sua média (que pode não ser inteira).
3. Faça um programa que receba o raio de um círculo e imprima sua área e sua circunferência (use `3.14159` no lugar de π).
4. Escreva uma função `double celsius_para_fahrenheit(double c)` que converte uma temperatura de Celsius para Fahrenheit (`F = C * 9.0/5.0 + 32`), e um `main` que lê uma temperatura em Celsius e imprime o resultado usando essa função.
5. Escreva uma função `int soma(int a, int b)` e outra `int produto(int a, int b)`. No `main`, leia dois números do usuário e imprima a soma e o produto, chamando as duas funções.
