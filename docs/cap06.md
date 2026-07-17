## 6. Ponteiros e Alocação Dinâmica

Chegou a hora de fechar as pontas soltas dos dois capítulos anteriores. Vimos que passar um vetor para uma função em C já altera o original "de graça" (Seção 4.2), que passar um struct **não** faz isso (Seção 5.5), e que `scanf` sempre pede `&variavel` sem explicar por quê. Tudo isso tem a mesma explicação: **ponteiros**.

Este é também o primeiro capítulo em que Python e VisuAlg não têm muito o que comparar diretamente — e por um bom motivo, que vale a pena entender antes de começar.

!!! note "Ponteiros implícitos em linguagens de alto nível (Python, Java, etc.)"
    Em linguagens como Python e Java, **toda variável que guarda um objeto, lista ou estrutura complexa é, por baixo dos panos, um ponteiro** (chamado de *referência*). A grande diferença para o C é que essas linguagens escondem a representação numérica e a sintaxe desse ponteiro de você: você não usa os operadores `*` ou `&` e **não pode fazer aritmética de ponteiros** (como somar números a um ponteiro para avançar posições na memória física).
    
    Para visualizar que variáveis em Python são de fato ponteiros apontando para a mesma área de memória, observe este exemplo:
    
    ```python
    l1 = [10, 20]
    l2 = l1
    l2[0] = 99
    
    print(l1)  # Imprime [99, 20]!
    ```
    
    Por que alterar `l2` modificou `l1`? Porque ao fazer `l2 = l1`, você copiou o *endereço* que estava guardado em `l1` para `l2`. Ambas as variáveis passaram a apontar para a mesma região da memória onde os dados físicos da lista `[10, 20]` estão armazenados.
    
    Esse conceito explica também erros clássicos em outras linguagens. O famoso `NullPointerException` em Java, ou o erro `AttributeError: 'NoneType' object has no attribute...` em Python ocorrem exatamente quando tentamos interagir com uma variável que **não aponta para nenhum endereço de memória válido** (uma referência nula, ou `None`). Em C, esse é o equivalente exato de tentar desreferenciar um ponteiro que aponta para `NULL` (ponteiro nulo).
    
    O VisuAlg segue o caminho oposto: ele não expõe endereços e faz cópias de registros por padrão. O C está no meio, dando a você controle direto e explícito sobre esses ponteiros para ler e escrever em qualquer posição da memória.

### 6.1. Endereços de memória e o operador `&`

Toda variável, quando o programa está rodando, ocupa um endereço na memória do computador — um número que identifica *onde* aquele valor está guardado. Em C, você pode descobrir esse endereço com o operador `&` ("endereço de"):

```c
int x = 10;
printf("%p\n", &x);   // imprime algo como 0x7ffee3a1b4ac
```

Isso talvez já pareça familiar: toda vez que você escreveu `scanf("%d", &x);` nos exemplos deste texto, você já estava usando `&`, sem uma explicação completa do porquê. Agora ela existe: `scanf` recebe como parâmetro o **endereço** da variável `x`, e não o valor de `x`, porque `scanf` precisa saber *onde* escrever o valor que acabou de ler do teclado. Se `scanf` recebesse só o valor de `x` (que nem existe ainda, antes da leitura), não haveria como ele "devolver" o número lido para dentro da sua variável.

### 6.2. Ponteiros: guardando um endereço em uma variável

Um **ponteiro** é uma variável cujo valor é um endereço de memória — geralmente o endereço de uma outra variável. Declara-se um ponteiro com um `*` antes do nome:

```c
int x = 10;
int *p;     // p é um ponteiro para int
p = &x;     // p agora guarda o endereço de x
```

A partir daqui, `p` "aponta para" `x`. Para acessar ou modificar o valor de `x` **através de** `p`, usa-se o mesmo `*`, agora com outro papel: "desreferenciar" — ir até o endereço guardado e pegar (ou alterar) o valor que está lá.

```c
printf("%d\n", *p);   // imprime 10 (o valor de x, acessado via p)
*p = 20;               // altera x para 20, através de p!
printf("%d\n", x);     // imprime 20
```

O `*` em C tem, portanto, dois usos diferentes que costumam confundir no início:

| Onde aparece | O que significa |
|---|---|
| `int *p;` (numa declaração) | "`p` é um ponteiro para `int`" |
| `*p` (fora de uma declaração) | "o valor que está no endereço guardado em `p`" |

!!! question "Exercício de mapeamento 6.1"
    Sem se preocupar com VisuAlg ou Python desta vez (não há uma tradução direta): escreva um programinha em C que declare um `int idade = 30;`, um ponteiro `int *p` apontando para `idade`, e que:

    1. imprima o valor de `idade` acessando diretamente a variável;
    2. imprima o mesmo valor acessando através de `p` (com `*p`);
    3. altere `idade` para 31 através de `p`;
    4. imprima `idade` de novo, diretamente, para confirmar que mudou.

### 6.3. Ponteiros explicam o comportamento dos vetores

Lembra da Seção 4.2? Vimos que passar um vetor para uma função já permite que a função altere o vetor original, sem precisar de nada especial — diferente de um `int` comum. Agora dá para explicar o porquê: **o nome de um vetor, usado em uma expressão, na prática "decai" para um ponteiro para a sua primeira posição.**

Isso quer dizer que estas duas declarações de função são, na prática, a mesma coisa:

```c
void dobra(int v[], int tamanho) { ... }
void dobra(int *v, int tamanho)  { ... }
```

Quando você chama `dobra(numeros, 3)`, o que é efetivamente passado para a função é o **endereço** da primeira posição de `numeros` — um ponteiro. Como a função recebe esse endereço (e não uma cópia de todo o vetor), ela consegue alterar diretamente as posições do vetor original através dele. É exatamente o mesmo princípio da Seção 6.2, só que o `&` já vem "embutido" automaticamente quando você usa o nome de um vetor.

Isso também explica um detalhe da Seção 4.3 que ficou em aberto: por que `scanf("%s", nome)` **não** usa `&` antes de `nome`, diferente de `scanf("%d", &x)`. É porque `nome` já é um vetor — o nome dele já decai para um ponteiro sozinho, então colocar `&` na frente seria redundante (e daria um tipo errado).

Uma consequência interessante disso é o cálculo de acessos em memória. O nome do vetor `v` representa, na realidade, o **endereço base** do vetor na memória (ou seja, o endereço do primeiro elemento, `&v[0]`). 

Se quisermos acessar, por exemplo, o **quarto elemento** do vetor (índice 3):
* A forma comum com colchetes é `v[3]`.
* Usando aritmética de ponteiros, fazemos `*(v + 3)` (somamos 3 ao endereço base e desreferenciamos o resultado).

Por baixo dos panos, o compilador faz a conta: `endereço_base + (3 * tamanho_do_tipo_em_bytes)`. O resultado dessa soma é o endereço exato do quarto elemento.

```c
int v[5] = {10, 20, 30, 40, 50};
printf("%d\n", v[3]);       // 40 (quarto elemento)
printf("%d\n", *(v + 3));   // 40 -- exatamente o mesmo elemento!
```

Você não *precisa* escrever `*(v + i)` no dia a dia — a sintaxe de colchetes `v[i]` é muito mais legível e é a que vamos continuar usando. Mas entender que os colchetes são apenas um atalho para aritmética de ponteiros ajuda a perceber por que um vetor em C é tão simples: ele não guarda metadados sobre seu tamanho ou limites, apenas o endereço base inicial.

!!! important "Vetores são como ponteiros imutáveis"
    Uma diferença crucial para fixar: você pode pensar no nome de um vetor como um **ponteiro imutável**. Isso significa que, uma vez criado, o endereço para o qual ele aponta é fixo e não pode ser alterado. Já um ponteiro comum é uma variável mutável, que pode ser reatribuída para apontar para qualquer lugar.
    
    ```c
    int v[5] = {10, 20, 30, 40, 50};
    int *p;
    
    p = v;     // Válido! p agora aponta para o início do vetor (v[0])
    p = p + 1; // Válido! p agora aponta para o segundo elemento (v[1])
    
    // v = p;  // ERRO DE COMPILAÇÃO! v é imutável, você não pode redefinir o seu endereço base.
    ```

### 6.4. Ponteiros explicam o comportamento dos structs

E a Seção 5.5? Vimos que, ao contrário de vetores, um struct passado normalmente para uma função é **copiado**, e alterações dentro da função não afetam o original. A solução que ficou pendurada era passar um **ponteiro para o struct**:

```c
void aumenta_nota(struct aluno *a) {
    (*a).nota1 += 1.0;
}

aumenta_nota(&a1);
```

Aqui, `a` é um ponteiro para `struct aluno`; `&a1` calcula o endereço de `a1` e o passa para a função, do mesmo jeito que fizemos com `int` na Seção 6.2. 

Dentro da função, para chegar até a variável interna do struct a partir de seu ponteiro, precisamos primeiro desreferenciar o ponteiro `a` (chegando ao struct físico) e, em seguida, acessar o campo `nota1`. Fazemos isso escrevendo `(*a).nota1`.

!!! note "Por que os parênteses em (*a).nota1 são obrigatórios?"
    Em C, o operador de acesso a membros `.` tem precedência maior do que o operador de desreferência `*`. Se você escrevesse `*a.nota1`, o compilador interpretaria como `*(a.nota1)` (tentar acessar o campo `nota1` do ponteiro `a` e depois desreferenciar). Como `a` é um ponteiro e não possui campos diretamente, isso causaria um erro de compilação. Os parênteses garantem que a desreferência ocorra primeiro.

Escrever `(*a).nota1` é incômodo, repetitivo e visualmente poluído. Por isso, a linguagem C oferece o operador **`->`**, que serve como um atalho direto para desreferenciar o ponteiro e acessar o campo. 

Na computação, atalhos como este, que simplificam a sintaxe para facilitar a leitura e escrita sem alterar a funcionalidade subjacente do código, são chamados de **açúcar sintático** (syntactic sugar):

```c
void aumenta_nota(struct aluno *a) {
    a->nota1 += 1.0;   // Açúcar sintático! Equivalente a (*a).nota1 += 1.0
}
```

| Você tem... | Para acessar um campo, usa |
|---|---|
| um `struct aluno` comum (`a1`) | `a1.nota1` |
| um ponteiro para struct (`struct aluno *a`) | `a->nota1` (ou, por extenso, `(*a).nota1`) |

!!! question "Exercício de mapeamento 6.2"
    Usando o `struct ponto { float x, y; };` do Exercício 5.1, escreva uma função `void move(struct ponto *p, float dx, float dy)` que soma `dx` a `p->x` e `dy` a `p->y`, alterando o ponto original. Teste chamando `move(&p1, 1.0, 2.0)` e imprimindo `p1.x` e `p1.y` antes e depois.

### 6.5. Por que precisamos de alocação dinâmica

Todo vetor que vimos até agora tem um tamanho fixo, escrito diretamente no código: `int v[50];`. Isso é uma limitação real: e se você só souber, em tempo de execução (depois que o usuário digitou alguma coisa), quantas posições vai precisar?

Em Python, isso nunca é um problema — uma `list` cresce sozinha conforme você faz `append`. Em VisuAlg, a limitação é a **mesma** que em C: o tamanho de um `vetor` também precisa ser conhecido no momento em que ele é declarado. Ou seja, aqui C e VisuAlg estão do mesmo lado, os dois mais rígidos que Python — a diferença é que C oferece uma ferramenta para contornar isso quando necessário (a um custo: você mesmo passa a ser responsável por gerenciar essa memória), enquanto o VisuAlg simplesmente não te dá essa opção.

A ferramenta chama-se **alocação dinâmica**: pedir, durante a execução do programa, um bloco de memória do tamanho que você precisar naquele momento.

### 6.6. Onde a memória reside: Pilha (Stack) vs. Monte (Heap)

Para entender a alocação dinâmica, precisamos saber que o sistema operacional divide a memória RAM que entrega ao nosso programa em regiões com propósitos diferentes. Duas delas são as mais importantes para nós:

* **A Pilha (Stack)**:
  - É onde vivem as variáveis locais comuns (como `int x;` ou `float notas[10];`) declaradas dentro de funções.
  - O gerenciamento é **automático**: quando uma função inicia, o compilador empilha suas variáveis na memória. Quando a função termina (`return`), todo esse espaço é liberado imediatamente.
  - O acesso é extremamente rápido, mas o tamanho total da pilha é limitado (geralmente poucos megabytes). Tentar criar um vetor gigante na pilha pode causar um erro grave chamado *Stack Overflow*.
* **O Monte (Heap)**:
  - É um grande espaço de memória compartilhada disponível para o programa inteiro (quase toda a RAM disponível na máquina).
  - O gerenciamento é **manual**: você precisa pedir memória explicitamente nessa área e, mais importante, você deve devolvê-la manualmente ao sistema.
  - Os dados criados no Heap continuam existindo mesmo após a função que os alocou ter terminado. É por isso que podemos alocar um vetor dentro de uma função e retornar seu ponteiro para o programa principal sem que ele seja destruído.

Isso nos dá um acesso e controle muito maior sobre a memória física em C, mas também traz a responsabilidade de gerenciar esse espaço manualmente, sem depender de um coletor de lixo automático.

### 6.7. `malloc`, `sizeof` e `free`

A função `malloc` (da biblioteca `<stdlib.h>`) reserva um bloco de bytes na memória (na área chamada *heap*) e devolve um ponteiro para o início desse bloco.

```c
#include <stdlib.h>

int *p;
p = (int*) malloc(sizeof(int));
*p = 42;
printf("%d\n", *p);
```

`sizeof(int)` calcula quantos bytes um `int` ocupa nesta máquina (geralmente 4) — usamos isso em vez de escrever `4` na mão porque o tamanho exato pode variar entre sistemas, e `sizeof` sempre acerta. O `(int*)` na frente do `malloc` converte o resultado (que é genérico) para um ponteiro de `int`; não é obrigatório em C, mas é uma boa prática e deixa claro para quem lê o código qual é o tipo esperado.

O uso mais comum de `malloc`, porém, é para vetores de tamanho decidido em tempo de execução — resolvendo exatamente o problema da Seção 6.5:

```c
int n;
printf("Quantos numeros? ");
scanf("%d", &n);

int *v = (int*) malloc(n * sizeof(int));

for (int i = 0; i < n; i++) {
    v[i] = i * i;
}
for (int i = 0; i < n; i++) {
    printf("%d\n", v[i]);
}
```

Repare que, uma vez alocado, `v` se comporta exatamente como um vetor comum — inclusive usando colchetes normalmente (`v[i]`), graças à equivalência que vimos na Seção 6.3.

Só que memória alocada com `malloc` **não é liberada sozinha** quando você termina de usar — diferente de Python e VisuAlg, que gerenciam isso para você. É sua responsabilidade avisar explicitamente que aquele bloco não é mais necessário, com `free`:

```c
free(v);
```

Se você esquecer de chamar `free`, a memória continua reservada até o programa terminar — isso se chama **vazamento de memória** (*memory leak*). Em um programa pequeno que roda e termina rápido isso quase não importa, mas em um programa que roda por muito tempo (um servidor, por exemplo) alocando repetidamente sem nunca liberar, o consumo de memória só cresce até travar o sistema.

| | C | VisuAlg | Python |
|---|---|---|---|
| pede memória para um vetor de tamanho `n`, decidido em tempo de execução | `malloc(n * sizeof(tipo))` | não é possível (tamanho precisa ser fixo na declaração) | `[0] * n`, ou simplesmente `append` conforme precisa |
| libera memória quando não usa mais | você chama `free()` manualmente | não se aplica | automático (*garbage collector*) |

!!! warning "Todo `malloc` merece um `free`"
    Uma boa disciplina, desde já: cada `malloc` que você escrever deveria vir acompanhado de um `free` correspondente, em algum lugar do código, no ponto em que aquela memória deixa de ser necessária.

### 6.8. Ponteiro nulo e verificação de erros

A memória do computador não é infinita. Se o sistema não conseguir atender a um pedido de `malloc` (memória livre insuficiente), a função devolve um ponteiro nulo, representado pela constante `NULL`, em vez de um endereço válido. Programas robustos checam isso:

```c
int *v = (int*) malloc(n * sizeof(int));
if (v == NULL) {
    printf("Erro: memoria insuficiente\n");
    return 1;
}
```

`NULL` representa "este ponteiro não aponta para lugar nenhum". Tentar desreferenciar um ponteiro `NULL` (fazer `*v` quando `v` é `NULL`) é um erro grave em tempo de execução — tipicamente o "falha de segmentação" que já mencionamos na Seção 2.5.

Não existe um conceito exatamente igual em VisuAlg, já que ele não expõe ponteiros. Em Python, o mais próximo é `None` — um valor especial que representa "nenhum objeto", e que também precisa de cuidado (usar um atributo de algo que é `None` também dá erro, o `AttributeError`). A ideia de "isso pode não apontar para nada, confira antes de usar" é parecida nos dois casos, mesmo com mecanismos bem diferentes por baixo.

### 6.9. `calloc` e `realloc`

Duas variações de `malloc` que valem a pena conhecer:

**`calloc`** aloca memória para um vetor de elementos, como `malloc`, mas já inicializa tudo com zero, e recebe os parâmetros separados (quantidade e tamanho de cada um), em vez de você multiplicar na mão:

```c
int *v = (int*) calloc(n, sizeof(int));   // n posições, todas comecando em 0
```

**`realloc`** faz um bloco já alocado crescer (ou diminuir), preservando o conteúdo que já existia — útil quando você percebe, no meio do programa, que vai precisar de mais espaço do que pediu inicialmente:

```c
int *v = (int*) malloc(10 * sizeof(int));
// ... usa v[0..9] ...

v = (int*) realloc(v, 20 * sizeof(int));   // agora v tem espaço para 20 posições
// os primeiros 10 valores continuam lá; as novas 10 posições tem lixo de memória
```

É basicamente assim que uma `list` de Python consegue crescer com `append` sem você perceber: por baixo dos panos, o interpretador Python está fazendo algo parecido com `realloc` (com uma estratégia mais esperta para não precisar realocar a cada `append`), só que escondido de você.

### 6.10. Um primeiro contato com listas encadeadas

Na Seção 5.6, ficou um aviso pendente: um struct pode ter, como campo, um ponteiro para *outro struct do mesmo tipo* — e é assim que se constrói uma **lista encadeada**, a primeira estrutura de dados dinâmica que estamos vendo neste texto.

```c
struct no {
    int valor;
    struct no *proximo;
};
```

Cada `no` guarda um valor e o endereço do próximo `no` da lista — ou `NULL`, se for o último. Para montar uma lista com os valores 10, 20 e 30, alocamos cada nó dinamicamente com `malloc` e vamos encadeando:

```c
struct no *cabeca = NULL;

struct no *n1 = (struct no*) malloc(sizeof(struct no));
n1->valor = 10;
n1->proximo = NULL;
cabeca = n1;

struct no *n2 = (struct no*) malloc(sizeof(struct no));
n2->valor = 20;
n2->proximo = NULL;
n1->proximo = n2;

struct no *n3 = (struct no*) malloc(sizeof(struct no));
n3->valor = 30;
n3->proximo = NULL;
n2->proximo = n3;
```

Para percorrer a lista do início ao fim, usamos um ponteiro auxiliar que vai "andando" de nó em nó, seguindo `->proximo`, até chegar em `NULL`:

```c
struct no *atual = cabeca;
while (atual != NULL) {
    printf("%d\n", atual->valor);
    atual = atual->proximo;
}
```

E, seguindo a disciplina da Seção 6.7, cada nó alocado com `malloc` também precisa ser liberado com `free` quando a lista não for mais usada:

```c
struct no *atual = cabeca;
while (atual != NULL) {
    struct no *proximo = atual->proximo;  // guarda antes de liberar!
    free(atual);
    atual = proximo;
}
```

Repare por que precisamos guardar `atual->proximo` numa variável **antes** de dar `free(atual)`: depois do `free`, ler `atual->proximo` seria acessar memória que você acabou de devolver ao sistema — um erro clássico.

Não existe um "vetor de tamanho fixo" nem um "registro" que façam isso em VisuAlg — essa é uma estrutura que só existe porque C tem ponteiros e alocação dinâmica. Em Python, o efeito equivalente (uma sequência de itens ligados uns aos outros) sai de graça com uma `list` comum, ou, se você quisesse reproduzir a mesma ideia de "nós" explicitamente, com objetos que se referenciam:

```python
class No:
    def __init__(self, valor):
        self.valor = valor
        self.proximo = None

n1 = No(10)
n2 = No(20)
n3 = No(30)
n1.proximo = n2
n2.proximo = n3
cabeca = n1

atual = cabeca
while atual is not None:
    print(atual.valor)
    atual = atual.proximo
```

Note como o código Python e o código C, aqui, ficam **estruturalmente parecidos** — a diferença é que em Python `No(10)` já aloca o objeto automaticamente (sem `malloc`) e nunca precisa de `free`; a lógica de percorrer a lista com um ponteiro/referência "andando" (`atual = atual.proximo` / `atual = atual->proximo`) é essencialmente a mesma ideia nas duas linguagens.

!!! question "Exercício de mapeamento 6.3"
    Não existe VisuAlg ou Python "pronto" para traduzir desta vez — o exercício é o caminho inverso. Usando o código Python da lista encadeada acima como roteiro conceitual (não para traduzir literalmente, e sim como guia de "o que a lista precisa fazer"), escreva uma função em C:

    ```c
    struct no* insere_no_inicio(struct no *cabeca, int valor);
    ```

    que aloca um novo nó com `malloc`, guarda `valor` nele, faz o `proximo` do novo nó apontar para a antiga `cabeca`, e devolve o novo nó como a nova cabeça da lista. Teste chamando essa função três vezes seguidas para inserir 30, depois 20, depois 10, e percorra a lista resultante imprimindo cada valor (deve sair 10, 20, 30, nessa ordem).

    ??? tip "Dica, não gabarito"
        O corpo da função é bem parecido com o trecho que criou `n1` manualmente acima, só que usando o parâmetro `valor` no lugar do número fixo, e `cabeca` (o parâmetro recebido) no lugar de `NULL`/do nó anterior.

### 6.11. Lista de exercícios

#### Conceituais

1. Um ponteiro é: (a) o endereço de uma variável; (b) uma variável que armazena endereços; (c) o valor de uma variável; (d) um indicador da próxima variável acessada. Escolha a alternativa correta e explique por que as outras estão erradas.
2. Qual é o significado do operador `*` em cada um dos seguintes casos?
    - `int *p;`
    - `printf("%d", *p);`
    - `*p = x * 5;`
    - `printf("%d", *(p + 1));`
3. Considere:

    ```c
    int i = 5, *p;
    p = &i;
    printf("%p - %d - %d\n", p, *p + 2, 3 * (*p));
    ```

    Supondo que `i` ocupa o endereço `4094` na memória, qual é a saída deste programa?
4. Considere:

    ```c
    int iVar = 15;
    int jVar, *pPont, *qPont;
    pPont = &iVar;
    jVar = 2 * (*pPont);
    qPont = pPont + 2;
    ```

    Supondo que os endereços de `iVar`, `jVar`, `pPont` e `qPont` sejam, respectivamente, `1080`, `1084`, `1088` e `1096`: (a) depois da linha 3, qual é o valor de `pPont`? (b) depois da linha 4, qual é o valor de `jVar`? (c) depois da linha 5, qual é o valor de `qPont` — e por que ele não é simplesmente `1080 + 2`?
5. Considere:

    ```c
    int *pti;
    int i = 10;
    pti = &i;
    ```

    Qual das afirmativas abaixo é **falsa**? (a) `pti` armazena o endereço de `i`; (b) `*pti` é igual a `10`; (c) ao se executar `*pti = 20;`, `i` passa a ter o valor 20; (d) ao se alterar o valor de `i`, `*pti` também muda; (e) `pti` é igual a `10`.
6. Considere:

    ```c
    float f;
    float *pf;
    pf = &f;
    scanf("%f", pf);
    ```

    O que acontece nessa última linha? (a) efetuamos a leitura de `f` normalmente; (b) não efetuamos a leitura de `f`; (c) há um erro de sintaxe; (d) deveríamos estar usando `&pf` no `scanf`. Justifique com o que vimos na Seção 6.3.
7. *(mais difícil — ponteiro para ponteiro)* Um ponteiro pode apontar para outro ponteiro — declarado com dois asteriscos, como em `int **q;` ("`q` é um ponteiro para um ponteiro para `int`"). Considere:

    ```c
    int x, *p, **q;
    p = &x;
    q = &p;
    x = 10;
    printf("%d\n", &q);   // isto tem um erro -- qual?
    ```

    O programa deveria imprimir `10`, mas tem um erro. Encontre-o e corrija-o (dica: quantas vezes você precisa "desreferenciar" `q` para chegar até o valor de `x`?).

#### Programação

8. Escreva uma função `void troca(int *a, int *b)` que troca os valores de duas variáveis `int` através de ponteiros. Teste no `main` com duas variáveis, imprimindo os valores antes e depois de chamar `troca`.
9. Faça um programa que leia um valor `n` do usuário, aloque dinamicamente (com `malloc`) um vetor de `n` inteiros, leia os `n` valores, calcule e imprima a soma e a média, e por fim libere a memória com `free`.
10. Refaça o Exercício 1 da lista do Capítulo 4 (vetor lido do usuário, com N decidido em tempo de execução) usando `malloc` no lugar de um vetor de tamanho fixo — agora sem precisar de um "tamanho máximo" definido de antemão no código.

#### Listas encadeadas

Use o `struct no` da Seção 6.10 (`struct no { int valor; struct no *proximo; };`) e a função `insere_no_inicio` do Exercício 6.3 como ponto de partida.

11. Escreva uma função `int soma_lista(struct no *cabeca)` que percorre a lista e devolve a soma de todos os valores.
12. Escreva uma função `int conta_nos(struct no *cabeca)` que devolve quantos nós tem a lista.
13. Escreva uma função `int pertence(struct no *cabeca, int valor)` que devolve `1` se `valor` está em algum nó da lista, ou `0` caso contrário.
14. Escreva uma função `void libera_lista(struct no *cabeca)` que libera (com `free`) todos os nós da lista, um por um — igual ao trecho de código mostrado no fim da Seção 6.10, mas como uma função reutilizável.
