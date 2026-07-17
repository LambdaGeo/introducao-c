## 4. Vetores e Strings

Na [Seção 2.5](cap02.md#25-arrays) já demos uma primeira olhada em vetores. Neste capítulo vamos aprofundar em vetores e, principalmente, em como C representa **strings** — que, como você vai ver, não são um tipo à parte em C, e sim um caso particular de vetor.

!!! note "Ainda sem ponteiros"
    Dá pra explicar bastante coisa sobre vetores e strings em C sem falar de ponteiros — e é isso que vamos fazer aqui, focando nas semelhanças e diferenças com o `vetor`/`cadeia` do VisuAlg e a `list`/`str` do Python. Só que algumas coisas em C (por que passar um vetor para uma função já altera o original? por que `==` não compara o conteúdo de duas strings?) só fazem sentido de verdade quando você entende ponteiros. Por enquanto vamos tratar esses comportamentos como **fatos observáveis** — "é assim que funciona" — e explicar o "porquê" no capítulo de ponteiros, quando vamos voltar a estes mesmos tópicos.

### 4.1. Vetores: inicializando e percorrendo

Como já vimos, um vetor em C tem tamanho fixo, definido na hora da declaração — nesse ponto ele é igual ao `vetor` do VisuAlg, e diferente da `list` do Python, que cresce e encolhe livremente.

Uma diferença que ainda não tínhamos visto: em C (e em Python) você pode declarar um vetor já **inicializando** todos os seus valores de uma vez, com uma lista entre chaves. O VisuAlg não tem essa opção — lá, você sempre inicializa posição por posição.

```c
int v[5] = {10, 20, 30, 40, 50};
```

```python
v = [10, 20, 30, 40, 50]
```

```
var
    v: vetor[0..4] de inteiro
inicio
    v[0] <- 10
    v[1] <- 20
    v[2] <- 30
    v[3] <- 40
    v[4] <- 50
```

Se você inicializa um vetor em C com menos valores do que o tamanho declarado, o C completa o resto com zero — é a única forma de "zerar" um vetor inteiro de forma compacta, sem um laço:

```c
int contadores[100] = {0};   // as 100 posições começam em 0
```

Percorrer um vetor com um laço é basicamente igual nas três linguagens — só muda a sintaxe do `for`/`para`, que já vimos no capítulo anterior:

```c
int soma = 0;
for (int i = 0; i < 5; i++) {
    soma += v[i];
}
```

```python
soma = 0
for i in range(5):
    soma += v[i]
# ou, mais "pythonico": soma = sum(v)
```

```
soma <- 0
para i de 0 ate 4 faca
    soma <- soma + v[i]
fimpara
```

Repare no comentário na versão Python: para tarefas comuns como somar todos os elementos, Python tem funções prontas (`sum`, `max`, `min`, `sorted`, ...) que operam sobre a lista inteira de uma vez. Nem C nem VisuAlg têm esse tipo de função embutida para vetores — você sempre escreve o laço manualmente.

| Operação | C | VisuAlg | Python |
|---|---|---|---|
| tamanho fixo, definido na declaração | sim | sim | não (`list` cresce/encolhe) |
| inicializar com valores literais | `{10, 20, 30}` | não existe — só posição por posição | `[10, 20, 30]` |
| adicionar elemento no fim | não existe | não existe | `v.append(x)` |
| somar/máx/mín "prontos" | não existe (laço manual) | não existe (laço manual) | `sum(v)`, `max(v)`, `min(v)` |
| índice do primeiro elemento | `0` | `0` (ou o que você declarar, ex. `1..N`) | `0` |

!!! question "Exercício de mapeamento 4.1"
    Em VisuAlg:

    ```
    var
        notas: vetor[0..4] de real
        i: inteiro
        maior: real
    inicio
        maior <- notas[0]
        para i de 1 ate 4 faca
            se (notas[i] > maior) entao
                maior <- notas[i]
            fimse
        fimpara
        escreval("Maior nota: ", maior)
    ```

    Escreva o equivalente em C, considerando que o vetor `notas` já vem preenchido com 5 valores (por exemplo, inicializado como `{7.5, 9.0, 6.0, 8.5, 10.0}`).

### 4.2. Passando vetores para funções

Aqui vai um comportamento importante para observar, mesmo sem entender ainda o mecanismo por trás: em C, quando você passa um vetor como parâmetro para uma função e a função modifica os elementos do vetor, **essa modificação é vista de volta na função que chamou** — diferente do que acontece com um `int` ou um `double` passados normalmente.

```c
void dobra(int v[], int tamanho) {
    for (int i = 0; i < tamanho; i++) {
        v[i] = v[i] * 2;
    }
}

int main() {
    int numeros[3] = {1, 2, 3};
    dobra(numeros, 3);
    printf("%d %d %d\n", numeros[0], numeros[1], numeros[2]);  // 2 4 6
    return 0;
}
```

Se isso te lembrou o comportamento de listas em Python, você não está enganado:

```python
def dobra(v):
    for i in range(len(v)):
        v[i] = v[i] * 2

numeros = [1, 2, 3]
dobra(numeros)
print(numeros)  # [2, 4, 6]
```

Em Python, uma função que recebe uma `list` e modifica seus elementos **também** altera a lista original — porque listas são um tipo mutável, e o que é passado para a função é uma referência à mesma lista, não uma cópia. Em C, algo parecido acontece com vetores (o motivo exato — que envolve ponteiros — fica para mais adiante).

Já em VisuAlg, o comportamento **depende de como você declara o parâmetro**: por padrão, um parâmetro é passado por valor (a função recebe uma cópia, e mudanças não voltam para quem chamou); para que a função possa alterar o vetor original, é preciso declarar o parâmetro com a palavra `var`:

```
procedimento dobra(var v: vetor[0..2] de inteiro)
var
    i: inteiro
inicio
    para i de 0 ate 2 faca
        v[i] <- v[i] * 2
    fimpara
fimprocedimento
```

Ou seja: em C esse comportamento de "referência" vem de graça para vetores, sem precisar escrever nada de especial; em VisuAlg você precisa pedir isso explicitamente com `var`. Guarde essa observação — ela vai fazer todo o sentido quando falarmos de ponteiros.

### 4.3. Strings em C: vetores de `char`

Aqui está a maior diferença entre C e as duas linguagens que você já conhece: **C não tem um tipo string**. O que existe é uma convenção: uma string é um vetor de `char` que termina com um caractere especial, `'\0'` (chamado de caractere nulo), marcando onde o texto acaba.

```c
char nome[20] = "Sergio";
```

Essa linha cria um vetor de 20 posições e preenche as 7 primeiras com `'S'`, `'e'`, `'r'`, `'g'`, `'i'`, `'o'`, `'\0'` — o `\0` é adicionado automaticamente pelo compilador porque usamos uma string literal (`"Sergio"`) para inicializar. As outras 13 posições ficam com lixo de memória, sem valor definido, mas não importa: qualquer função que trabalha com essa string vai parar de ler assim que encontrar o `'\0'`, então o "lixo" depois dele nunca é usado.

Repare que o tamanho do vetor (aqui, 20) **precisa ser maior** do que o texto que você pretende guardar — sobrando pelo menos 1 posição para o `\0`. Isso não tem paralelo direto em Python ou VisuAlg, onde uma string simplesmente cresce e encolhe conforme o conteúdo, sem você se preocupar em reservar espaço.

| | C (`char[]`) | VisuAlg (`cadeia`) | Python (`str`) |
|---|---|---|---|
| é um tipo de verdade, ou é "só um vetor"? | é um vetor de `char` (convenção) | tipo próprio | tipo próprio |
| precisa reservar tamanho manualmente | sim | não | não |
| marcador de fim de string visível | `'\0'` | não existe (a linguagem cuida disso) | não existe (a linguagem cuida disso) |
| pode ser modificada depois de criada | sim (é um vetor comum) | sim | **não** — `str` é imutável em Python |

Ler uma string do teclado usa `scanf` com o especificador `%s` — e, diferente de outras variáveis, você **não** usa `&` antes do nome do vetor (mais um comportamento para anotar e entender de verdade só no capítulo de ponteiros):

```c
char nome[20];
scanf("%s", nome);
printf("Ola, %s!\n", nome);
```

```python
nome = input("Nome: ")
print("Ola,", nome + "!")
```

```
var
    nome: cadeia
inicio
    leia(nome)
    escreval("Ola, ", nome, "!")
fimalgoritmo
```

!!! warning "scanf(\"%s\", ...) é perigoso"
    O `scanf("%s", nome)` não sabe que `nome` só tem 20 posições — se o usuário digitar um texto maior que isso, o `scanf` vai escrever além do fim do vetor, exatamente o tipo de comportamento sem checagem de limites que vimos na [Seção 2.5](cap02.md#25-arrays). Existem formas mais seguras de ler uma linha em C (como `fgets`), mas isso fica para mais adiante.

### 4.4. Funções de string (`<string.h>`)

Como uma string em C é só um vetor de `char`, os operadores comuns não fazem o que você esperaria vindo de Python ou VisuAlg. Em particular:

!!! warning "O maior gotcha para quem vem de Python/VisuAlg"
    Em C, **você não pode usar `==` para comparar o conteúdo de duas strings**, e **não pode usar `=` para copiar uma string em outra**. Isso compila, mas não faz o que parece fazer — `==` compara *onde* os vetores estão na memória, não o que tem dentro deles. Para comparar ou copiar o conteúdo de verdade, C oferece funções prontas na biblioteca `<string.h>`.

```c
#include <string.h>

char a[20] = "casa";
char b[20] = "casa";

if (a == b) {          // ERRADO: quase sempre dá falso, mesmo com o mesmo texto!
    printf("iguais\n");
}

if (strcmp(a, b) == 0) {  // CERTO: strcmp devolve 0 quando as strings são iguais
    printf("iguais\n");
}
```

Em Python, `a == b` já compara o conteúdo das duas strings — é exatamente o que parece que deveria fazer. Em VisuAlg, `a = b` faz a mesma coisa. É só em C que você precisa de uma função à parte para isso.

| O que você quer fazer | C | VisuAlg | Python |
|---|---|---|---|
| tamanho da string | `strlen(s)` | `compr(s)` | `len(s)` |
| copiar o conteúdo | `strcpy(destino, origem)` | `destino <- origem` | `destino = origem` |
| concatenar (juntar) | `strcat(destino, origem)` | `destino <- destino + origem` | `destino += origem` |
| comparar conteúdo | `strcmp(a, b) == 0` | `a = b` | `a == b` |
| pedaço da string (substring) | *(precisa laço manual, ou funções de `<string.h>` mais avançadas)* | `copia(s, inicio, tamanho)` | `s[inicio:fim]` |

```c
char nome[30] = "Sergio";
char sobrenome[20] = " Costa";

strcat(nome, sobrenome);          // nome agora contém "Sergio Costa"
printf("%s tem %lu letras\n", nome, strlen(nome));
```

```python
nome = "Sergio"
sobrenome = " Costa"
nome += sobrenome                  # nome agora contém "Sergio Costa"
print(nome, "tem", len(nome), "letras")
```

```
var
    nome: cadeia
    sobrenome: cadeia
inicio
    nome <- "Sergio"
    sobrenome <- " Costa"
    nome <- nome + sobrenome
    escreval(nome, " tem ", compr(nome), " letras")
fimalgoritmo
```

Note que `strcat(nome, sobrenome)` exige que o vetor `nome` já tenha espaço sobrando para caber o resultado da concatenação — se `nome` tivesse sido declarado com tamanho exato para "Sergio" (7 posições, contando o `\0`), o `strcat` escreveria além do fim do vetor. Mais um lugar onde C confia inteiramente em você para reservar espaço suficiente.

!!! question "Exercício de mapeamento 4.2"
    Em Python:

    ```python
    usuario = input("Digite seu usuario: ")
    senha = input("Digite sua senha: ")

    if usuario == "admin" and senha == "1234":
        print("Acesso permitido")
    else:
        print("Acesso negado")
    ```

    Escreva o equivalente em C. Lembre-se: (1) strings em C precisam de um vetor de `char` com tamanho reservado, e (2) comparar strings exige `strcmp`, não `==`. Se quiser, primeiro escreva a versão em VisuAlg como passo intermediário.

    ??? tip "Dica, não gabarito"
        `strcmp(usuario, "admin") == 0` é a forma de checar se `usuario` é igual a `"admin"`. Para combinar as duas condições, você vai precisar do operador lógico "e" de C — reveja a [Seção 2.1](cap02.md#21-operadores) se não lembrar qual é.

### 4.5. Vetores de strings

Assim como você pode ter um vetor de `int` ou de `double`, também é possível ter um vetor onde cada posição é uma string — útil, por exemplo, para guardar uma lista de nomes. Em C isso é declarado como uma matriz de `char` (um vetor de vetores de `char`): a primeira dimensão é "quantas strings", a segunda é "tamanho máximo de cada string".

```c
char nomes[3][20] = {"Ana", "Bruno", "Carla"};

for (int i = 0; i < 3; i++) {
    printf("%s\n", nomes[i]);
}
```

```python
nomes = ["Ana", "Bruno", "Carla"]

for nome in nomes:
    print(nome)
```

```
var
    nomes: vetor[0..2] de cadeia
    i: inteiro
inicio
    nomes[0] <- "Ana"
    nomes[1] <- "Bruno"
    nomes[2] <- "Carla"
    para i de 0 ate 2 faca
        escreval(nomes[i])
    fimpara
fimalgoritmo
```

Repare como, de novo, o Python é o único dos três que consegue inicializar a lista inteira com uma linha só (`["Ana", "Bruno", "Carla"]`) e nem precisa se preocupar com quantas letras cada nome tem — o VisuAlg pelo menos deixa você inicializar cada posição em uma linha; o C exige que você reserve, por adiantado, um tamanho máximo (`20`, no exemplo) para o maior nome que algum dia vai caber ali.

!!! question "Exercício de mapeamento 4.3"
    Em VisuAlg:

    ```
    var
        cores: vetor[0..3] de cadeia
        i: inteiro
    inicio
        cores[0] <- "vermelho"
        cores[1] <- "verde"
        cores[2] <- "azul"
        cores[3] <- "amarelo"
        para i de 0 ate 3 faca
            se (compr(cores[i]) > 5) entao
                escreval(cores[i])
            fimse
        fimpara
    ```

    Esse trecho imprime as cores cujo nome tem mais de 5 letras. Escreva a versão em C (dica: qual das funções da tabela da Seção 4.4 substitui `compr`?).

### 4.6. Lista de exercícios

#### Vetores

1. Faça um programa que leia N valores (N informado pelo usuário, até um máximo de 100), guarde-os em um vetor, e imprima: (a) o maior valor; (b) a média dos valores; (c) os valores em ordem crescente; (d) o subconjunto de valores primos contidos no vetor.
2. Faça um programa que preencha um vetor de inteiros com 10 posições e imprima: o maior elemento, o menor elemento, a soma de todos os elementos, e a média aritmética entre eles.
3. Faça um programa que, dadas duas matrizes A e B de dimensão 5×5 (`int a[5][5];` declara uma matriz 5×5 de inteiros; acessa-se com `a[i][j]`), calcule e imprima: (a) a soma das duas matrizes; (b) a soma dos elementos da diagonal principal de cada matriz; (c) o produto das duas matrizes.
4. Em um tabuleiro de xadrez 8×8, cada posição guarda um código: `0` = ausência de peça, `1` = peão, `2` = cavalo, `3` = torre, `4` = bispo, `5` = rei, `6` = rainha. Leia um tabuleiro 8×8 (64 valores) para uma matriz `int tabuleiro[8][8]` e calcule: (a) a soma entre a quantidade de peões e de bispos; (b) a quantidade de posições sem peça alguma.
5. A tabela abaixo dá a distância, em km, entre 5 cidades:

    | | Cidade 1 | Cidade 2 | Cidade 3 | Cidade 4 | Cidade 5 |
    |---|---|---|---|---|---|
    | **Cidade 1** | 0 | 12 | 10 | 5 | 15 |
    | **Cidade 2** | 12 | 0 | 10 | 28 | 20 |
    | **Cidade 3** | 10 | 10 | 0 | 18 | 25 |
    | **Cidade 4** | 5 | 28 | 18 | 0 | 22 |
    | **Cidade 5** | 15 | 20 | 25 | 22 | 0 |

    Guarde essa tabela em uma matriz `int dist[5][5]` já inicializada no código (uma matriz literal, como vimos na Seção 4.1) e escreva um programa que: (a) leia pares de códigos de cidade (1 a 5) e informe a distância entre elas, repetindo até que o usuário digite 0 e 0; (b) dado um percurso informado como uma sequência de códigos de cidade (por exemplo, `1 2 3 2 5 1 4`), imprima a distância total percorrida, somando a distância entre cada cidade e a próxima da sequência.
6. Um cinema com capacidade para 20 lugares está sempre lotado. Em uma sessão, cada um dos 20 espectadores respondeu um questionário com sua idade e sua opinião sobre o filme (uma string: `"otimo"`, `"bom"`, `"regular"`, `"ruim"` ou `"pessimo"`). Leia esses 20 pares (idade, opinião) e calcule: (a) a quantidade de respostas `"otimo"`; (b) a diferença percentual entre respostas `"bom"` e `"regular"`; (c) a média de idade das pessoas que responderam `"ruim"`; (d) a maior idade entre quem respondeu `"pessimo"`. Use `strcmp` para comparar as opiniões — lembre-se de que `==` não funciona para strings, como vimos na Seção 4.4.
7. Considere o programa abaixo, que lê as notas de uma turma e calcula estatísticas:

    ```c
    #include <stdio.h>

    int main(void) {
        int qtAlunos;
        printf("Quantidade de alunos: ");
        scanf("%d", &qtAlunos);

        float notas[100];
        for (int i = 0; i < qtAlunos; i++) {
            printf("Nota (%d): ", i);
            scanf("%f", &notas[i]);
        }

        float soma = 0;
        for (int i = 0; i < qtAlunos; i++) {
            soma = soma + notas[i];
        }
        float media = soma / qtAlunos;

        float maior = notas[0];
        for (int i = 1; i < qtAlunos; i++) {
            if (notas[i] > maior) maior = notas[i];
        }

        float menor = notas[0];
        for (int i = 1; i < qtAlunos; i++) {
            if (notas[i] < menor) menor = notas[i];
        }

        float limiar;
        printf("Nota limiar: ");
        scanf("%f", &limiar);
        int conta = 0;
        for (int i = 0; i < qtAlunos; i++) {
            if (notas[i] > limiar) conta++;
        }

        printf("Media: %f\n", media);
        printf("Maior nota: %f\n", maior);
        printf("Menor nota: %f\n", menor);
        printf("Foram %d maiores que %f\n", conta, limiar);
        return 0;
    }
    ```

    Reescreva esse programa criando as seguintes funções (lembre-se da Seção 4.2: um vetor passado como parâmetro já permite que a função o acesse/altere):

    - `int le_notas(float notas[], int max)` — lê as notas do usuário e devolve quantas foram lidas.
    - `float media_notas(float notas[], int n)` — devolve a média.
    - `float maior_nota(float notas[], int n)` — devolve o maior valor.
    - `float menor_nota(float notas[], int n)` — devolve o menor valor.
    - `int conta_acima(float notas[], int n, float limiar)` — devolve quantos valores estão acima do limiar.

#### Strings

8. Faça um programa que leia uma palavra (sem espaços) e conte quantas vogais ela tem.
9. Faça um programa que leia uma palavra (sem espaços) e diga se ela é um palíndromo — isto é, se lida de trás para frente é igual à original (ex.: `"arara"`, `"osso"`).
10. Faça um programa que leia uma palavra e a imprima invertida (ex.: entrada `"programa"`, saída `"amargorp"`).
11. Faça um programa que leia um vetor de 5 palavras (`char nomes[5][20]`, como na Seção 4.5) e as imprima em ordem alfabética. (Dica: `strcmp(a, b)` devolve um valor negativo se `a` vem antes de `b` em ordem alfabética, positivo se vem depois, e 0 se são iguais — dá para usar isso no lugar de `<`/`>` dentro de um algoritmo de ordenação como o que você já deve conhecer de Python/VisuAlg.)
