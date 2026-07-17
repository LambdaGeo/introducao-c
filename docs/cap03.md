## 3. Bibliotecas

Tendo discutido as funções internas, agora nos voltamos para discutir questões relacionadas a funções e separar um programa em vários arquivos.

!!! note "Um capítulo sem muito equivalente direto"
    Diferente dos capítulos anteriores, os três assuntos aqui (protótipos, arquivos de cabeçalho e diretivas de pré-processador) não têm um mapeamento direto em VisuAlg, porque o VisuAlg não tem essas ideias: todo algoritmo VisuAlg vive em um único arquivo, sem um passo de pré-processamento separado. Em Python o mais próximo é o sistema de módulos (`import`), mas mesmo assim os mecanismos são bem diferentes por baixo dos panos. Por isso, aqui vamos focar em **explicar o "porquê"** de cada mecanismo existir em C — que é justamente o tipo de coisa que não dá pra simplesmente "traduzir" de uma linguagem que você já conhece, porque a necessidade em si é nova.

### 3.1. Protótipos de função

Em C, uma função deve ser declarada acima do local onde você a usa. No programa C de [Figura 2], definimos a função `gcd ()` primeiro, depois a função `main ()`. Isto é significativo: Se nós trocamos as funções `gcd ()` e `main ()`, o compilador iria reclamar em `main() `que a função `gcd ()` não foi declarada. Isso porque C assume que um compilador lê um programa de cima para baixo: No momento em que chega ao `main ()`, ele não foi informado sobre uma função `gcd ()`, e por isso ele acredita que a função não exista.

Nem Python nem VisuAlg têm essa exigência: em ambos, o comum é definir todas as funções antes de usá-las de qualquer forma (é uma boa prática), mas nenhum dos dois erros se você, por exemplo, definir duas funções que chamam uma à outra (recursão mútua) — Python resolve isso em tempo de execução, quando a chamada de fato acontece, não em tempo de leitura do arquivo.

Isso gera um problema, especialmente em programas maiores que abrangem vários arquivos, em que as funções de um arquivo precisarão chamar funções em outro. Para contornar isso, C fornece a noção de um **protótipo de função**, onde escrevemos o cabeçalho da função mas omitimos a definição do corpo.

Por exemplo, digamos que queremos quebrar nosso programa C em dois arquivos: O primeiro arquivo, math.c, conterá a função `gcd ()` e o segundo arquivo, main.c, conterá o `main () `função. O problema com isso é que, na compilação main.c, o compilador não saberá sobre a função `gcd ()` que está tentando chamar.

Uma solução é incluir um protótipo de função em main.c.

```c
int gcd(int a, int b);

int main() {
    printf("GCD: %d\n", gcd(24, 40));
    return 0;
}

```

A linha `int gcd ...` é o protótipo da função. Você pode ver que começa da mesma forma que uma definição de função, mas nós simplesmente colocamos um ponto-e-vírgula onde o corpo da função normalmente seria. Ao fazer isso, estamos declarando que a função será eventualmente definida, mas ainda não a definimos. O compilador aceita isso e obedientemente compila o programa sem reclamações.

### 3.2. Arquivos de cabeçalho (Header files)

Programas maiores que abrangem vários arquivos freqüentemente contêm muitas funções que são usadas muitas vezes em muitos arquivos diferentes. Seria doloroso repetir cada protótipo de função em todos os arquivos que usam a função. Então, criamos um arquivo - chamado de **arquivo de cabeçalho** - que contém cada protótipo escrito apenas uma vez (e possivelmente algumas informações compartilhadas adicionais), e então podemos nos referir a esse arquivo de cabeçalho em cada arquivo de origem que deseja os protótipos. O arquivo de protótipos é chamado de arquivo de cabeçalho, pois contém as "heads" ou "cabeças" de várias funções. Convencionalmente, os arquivos de cabeçalho usam o prefixo `.h`, em vez do prefixo `.c` usado para os arquivos fonte C.

Por exemplo, podemos colocar o protótipo da nossa função `gcd ()` em um arquivo de cabeçalho chamado math.h.

```c
int gcd(int a, int b);
```

Podemos usar um tipo especial de linha começando com `#include` para incorporar este arquivo de cabeçalho no topo do main.c.

```c
#include <stdio.h>
#include "math.h"

int main() {
    printf("GCD: %d\n",
        gcd(24, 40));
    return 0;
}
```

Este exemplo em particular não é muito convincente, mas imagine uma biblioteca que consiste em dezenas de funções, que são usadas em dezenas de arquivos: De repente, a economia de tempo de ter apenas um único protótipo para cada função em um arquivo de cabeçalho começa a fazer sentido.

A linha `#include` é um exemplo de uma diretiva para o pré-processador do C, através da qual o compilador C envia cada programa antes de compilá-lo. Um programa pode conter comandos (**diretivas**) informando ao pré-processador para manipular o texto do programa que o compilador realmente processa. A diretiva `#include` diz ao pré-processador para substituir a linha`#include` pelo conteúdo do arquivo especificado.

Você notará que colocamos `stdio.h` entre colchetes, enquanto `math.h` está entre aspas duplas. Os colchetes angulares são para arquivos de cabeçalho padrão - arquivos mais ou menos incorporados ao sistema C. As aspas são para arquivos de cabeçalho personalizados que podem ser encontrados no mesmo diretório dos arquivos fonte.

Se você já usou `import` em Python, a *motivação* de `#include` vai soar familiar (reaproveitar código escrito em outro arquivo) — mas o *mecanismo* é bem diferente, e vale a pena não confundir os dois:

| | `#include` de C | `import` de Python |
|---|---|---|
| O que faz | Copia e cola o texto do arquivo, literalmente, antes de compilar | Carrega o módulo já compilado/executado uma vez, e expõe seus nomes |
| Quando roda | Em uma etapa de pré-processamento, antes do compilador ver o código | Em tempo de execução, na primeira vez que o `import` é alcançado |
| O que é incluído | Normalmente só os protótipos (`.h`) — o código de verdade (`.c`) é compilado e "linkado" à parte | O módulo inteiro, incluindo o código executável das funções |

### 3.3. Constantes

Outra diretiva de pré-processador particularmente útil é a diretiva `#define`. Ela diz ao pré-processador para substituir todas as ocorrências futuras de alguma palavra por outra.

```c
#define PI 3.14159
```

Neste fragmento, dissemos ao pré-processador que, para o resto do programa, ele deveria substituir cada ocorrência de "PI" por "3.14159". Suponha que mais tarde no programa tenha a seguinte linha:

```c
printf("area: %f\n", PI * r * r);
```
Vendo isso, o pré-processador iria traduzi-lo no seguinte texto para o compilador C processar:

```c
printf("area: %f\n", 3.14159 * r * r);
```
> vamos testar ? :)

Essa substituição acontece nos bastidores, para que o programador não veja a substituição.

VisuAlg tem uma ideia de constante nomeada (`const PI = 3.14159`) e Python também tem a convenção de variáveis em maiúsculas para indicar "isso é uma constante" (`PI = 3.14159`) — mas em nenhum dos dois casos existe uma etapa de pré-processamento textual por trás: é só uma variável comum, guardada na memória como qualquer outra, que por convenção (ou, no caso do VisuAlg, por declaração explícita com `const`) não deveria ser reatribuída. O `#define` de C é mais poderoso (e mais perigoso) que isso, porque ele nem chega a existir como variável — é apenas texto substituído antes da compilação, como mostra o próximo exemplo.

A diretiva `# define` não está restrita a definir constantes como essa, no entanto. Por usar apenas substituição textual, a diretiva pode ser usada (e abusada) de outras maneiras. Por exemplo, pode-se incluir o seguinte.

```c
#define forever while(1)
```
Posteriormente, você poderia usar `forever` como se fosse uma construção de loop, e o pré-processador a substituiria por" `while(1)` ".

```c
forever {
     printf("hello world\n");
}
```
Os programadores especialistas em C consideram esse estilo muito ruim, já que ele leva rapidamente a programas ilegíveis. Nem Python nem VisuAlg têm como fazer algo parecido — e esse exemplo é justamente uma boa demonstração de por que a maioria das linguagens mais novas optou por não ter um pré-processador textual tão livre quanto o de C.

!!! question "Exercício de mapeamento 3.1"
    Este exercício é um pouco diferente dos anteriores, porque não existe uma linha de VisuAlg ou Python para "traduzir" — o objetivo é justamente notar a ausência.

    Imagine que você tem, em Python, dois arquivos: `matematica.py` (com uma função `mdc(a, b)`) e `main.py`, que faz `from matematica import mdc` e usa a função. Sem escrever código C ainda, responda: por que, em C, para fazer a mesma separação em dois arquivos (`math.c` e `main.c`), você **precisa** de um arquivo `math.h` com o protótipo de `mdc`, e não basta simplesmente compilar os dois arquivos `.c` juntos? (Dica: pense em quando o compilador de C lê `main.c` — ele já leu `math.c` nesse momento?)

Estas são as noções básicas de escrever programas em C, dando-lhe o suficiente para poder escrever programas razoavelmente úteis. Mas, para ser um programador proficiente em C, você precisaria saber sobre ponteiros - um tópico que adiaremos em outro momento.

### 3.4. Lista de exercícios

1. Você tem uma função `int quadrado(int x)` que calcula o quadrado de um número, usada dentro de um `main` que lê um número do usuário e imprime o seu quadrado. Separe esse programa em três arquivos: `quadrado.h` (com o protótipo), `quadrado.c` (com a definição da função) e `main.c` (com o `main`, incluindo `quadrado.h`). Escreva também o comando `gcc` necessário para compilar os dois arquivos `.c` juntos, gerando um único executável.
2. Use `#define` para criar duas constantes, `VERDADEIRO` como `1` e `FALSO` como `0`, e escreva um pequeno programa que declare uma variável `int encontrou` e a use dentro de um `if`, comparando com `VERDADEIRO`. Depois, imagine (sem rodar) qual seria o texto exato que o pré-processador entrega ao compilador, com `VERDADEIRO`/`FALSO` já substituídos.
3. Reescreva o Exercício 8 da lista do Capítulo 2 (números amigáveis) definindo, com `#define`, um `LIMITE` para o intervalo de busca (por exemplo, `#define LIMITE 10000`), e use `LIMITE` no lugar do número mágico espalhado pelo código.
4. *(desafio)* A diretiva `#define` também aceita "parâmetros", nesse formato:

    ```c
    #define QUADRADO(x) ((x) * (x))
    ```

    Isso não é uma função de verdade — continua sendo substituição de texto, só que agora com uma "lacuna" preenchida pelo que você passar no lugar de `x`. Escreva um pequeno programa que use `QUADRADO(5)` dentro de um `printf`. Depois, tente prever (e depois teste) o que acontece com `QUADRADO(2 + 3)` — o resultado é 25, como você esperaria de uma função, ou é outra coisa? Os parênteses extras ao redor de `(x)` na definição da macro são exatamente o que evita (ou não evita) esse problema; tente entender por quê comparando com e sem eles.
