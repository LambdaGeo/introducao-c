## 5. Structs (registros)

Nos capítulos anteriores, um vetor sempre guardava vários valores **do mesmo tipo** — um vetor de notas, um vetor de nomes. Mas é comum precisar agrupar valores de **tipos diferentes** que descrevem uma mesma coisa: o nome de um aluno (uma string) e suas três notas (três números), por exemplo.

!!! note "Ainda sem ponteiros — mas eles vão aparecer nas placas de aviso"
    Assim como no capítulo de vetores e strings, dá pra aprender bastante sobre structs sem entender ponteiros a fundo. Mas structs são justamente o lugar onde a falta de ponteiros começa a doer de verdade — principalmente quando falamos de passar um struct para uma função. Sempre que isso acontecer, vou marcar com um aviso "⚠️ ponteiros à frente" e explicar só o suficiente para você não se enganar, deixando o mecanismo completo para o próximo capítulo.

### 5.1. Por que agrupar dados diferentes

Imagine o seguinte problema: ler o nome e três notas de vários alunos de uma turma, e depois imprimir os nomes dos alunos com nota acima da média da turma.

Com o que vimos até agora, uma solução possível seria usar vetores separados, um para os nomes e um (ou três) para as notas:

```c
char nomes[30][20];
float nota1[30], nota2[30], nota3[30];
```

Isso funciona, mas os dados estão **espalhados**: nada no código deixa explícito que `nomes[5]`, `nota1[5]`, `nota2[5]` e `nota3[5]` pertencem à mesma pessoa — é só uma convenção que você, programador, precisa lembrar e manter correta em todo lugar que mexe nesses vetores. Se você reordenar um vetor e esquecer de reordenar os outros três junto, os dados "descasam" silenciosamente.

O que queremos é um novo *tipo* que represente "um aluno" — nome e três notas, juntos, como uma coisa só. Em C, esse novo tipo é um `struct`. Em VisuAlg, a ideia equivalente se chama `registro`. E em Python, isso é feito com uma `classe`.

### 5.2. Declarando um struct

```c
struct aluno {
    char nome[20];
    float nota1, nota2, nota3;
};
```

Isso não cria nenhuma variável ainda — é só a **definição do tipo**, dizendo que, de agora em diante, `struct aluno` é um tipo composto por essas quatro informações. Para criar uma variável desse tipo e usar seus campos, fazemos:

```c
struct aluno a1;

strcpy(a1.nome, "Maria");
a1.nota1 = 8.5;
a1.nota2 = 7.0;
a1.nota3 = 9.5;

printf("%s\n", a1.nome);
```

Repare no `.` para acessar um campo (`a1.nota1`) — isso é igual em C, VisuAlg e Python. Repare também que, como `nome` é um vetor de `char` (Seção 4.3), atribuir um valor a ele exige `strcpy`, não `=` — a mesma regra de string que já vimos continua valendo dentro de um struct.

O `registro` equivalente em VisuAlg:

```
tipo
    aluno = registro
        nome: cadeia
        nota1, nota2, nota3: real
    fimregistro

var
    a1: aluno

inicio
    a1.nome <- "Maria"
    a1.nota1 <- 8.5
    a1.nota2 <- 7.0
    a1.nota3 <- 9.5

    escreval(a1.nome)
```

E a classe equivalente em Python:

```python
class Aluno:
    def __init__(self, nome, nota1, nota2, nota3):
        self.nome = nome
        self.nota1 = nota1
        self.nota2 = nota2
        self.nota3 = nota3

a1 = Aluno("Maria", 8.5, 7.0, 9.5)
print(a1.nome)
```

Aqui vale uma ressalva importante: uma `struct` de C e um `registro` de VisuAlg são **só dados** — não existe a ideia de "método" (uma função que pertence ao struct). Uma `classe` em Python, por outro lado, normalmente vem com métodos e um `__init__` para construir o objeto — é uma ferramenta mais rica, pensada para orientação a objetos, não só para agrupar dados. Se você quiser, em Python, algo mais parecido em espírito com um struct/registro puro — só os dados, sem cerimônia de métodos —, existem alternativas mais leves como `dataclass` ou `namedtuple`:

```python
from dataclasses import dataclass

@dataclass
class Aluno:
    nome: str
    nota1: float
    nota2: float
    nota3: float

a1 = Aluno("Maria", 8.5, 7.0, 9.5)
```

| | `struct` (C) | `registro` (VisuAlg) | `class` (Python) |
|---|---|---|---|
| agrupa dados de tipos diferentes | sim | sim | sim |
| tem métodos (funções "do tipo") | não | não | sim (é o normal em Python) |
| acesso a um campo | `a1.nome` | `a1.nome` | `a1.nome` |
| equivalente "só dados", sem métodos | *(já é assim por padrão)* | *(já é assim por padrão)* | `dataclass` / `namedtuple` |

!!! question "Exercício de mapeamento 5.1"
    Em VisuAlg:

    ```
    tipo
        ponto = registro
            x, y: real
        fimregistro

    var
        p1: ponto

    inicio
        p1.x <- 3.0
        p1.y <- 4.0
        escreval("Distancia da origem: ", raizq(p1.x^2 + p1.y^2))
    ```

    Escreva o `struct ponto` em C, declare uma variável `p1`, atribua os mesmos valores e imprima a distância até a origem (lembre-se: em C, potência é `pow()`, não `^`, como vimos na Seção 2.1).

### 5.3. `typedef`: dando um apelido ao tipo

Em C, toda vez que você quer declarar uma variável desse tipo, precisa escrever `struct aluno`, com a palavra `struct` na frente — diferente de VisuAlg e Python, onde basta o nome do tipo (`aluno`, `Aluno`). É comum usar `typedef` para criar um apelido e poder escrever só `Aluno`, como nas outras duas linguagens:

```c
typedef struct {
    char nome[20];
    float nota1, nota2, nota3;
} Aluno;

Aluno a1;
a1.nota1 = 8.5;
```

Isso não muda nada no comportamento — é só uma conveniência de sintaxe para deixar o código mais parecido com o que você já escreve em VisuAlg ou Python.

### 5.4. Vetor de structs

Voltando ao problema do início do capítulo: agora que temos um tipo `Aluno`, o jeito natural de representar uma turma inteira é um **vetor de structs** — juntando o que vimos no Capítulo 4 com o que acabamos de ver aqui.

```c
Aluno turma[30];

turma[0].nome ...      // strcpy(turma[0].nome, "Maria");
turma[0].nota1 = 8.5;

for (int i = 0; i < 30; i++) {
    printf("%s\n", turma[i].nome);
}
```

```
var
    turma: vetor[0..29] de aluno
    i: inteiro
inicio
    turma[0].nome <- "Maria"
    turma[0].nota1 <- 8.5

    para i de 0 ate 29 faca
        escreval(turma[i].nome)
    fimpara
```

```python
turma = []
turma.append(Aluno("Maria", 8.5, 7.0, 9.5))

for aluno in turma:
    print(aluno.nome)
```

Note, mais uma vez, a diferença de filosofia: em C e em VisuAlg você reserva de antemão um vetor de tamanho fixo (30 alunos, e nem um a mais); em Python, a `list` cresce conforme você adiciona alunos com `append`.

!!! question "Exercício de mapeamento 5.2"
    Usando o `struct aluno` da Seção 5.2 (ou a versão com `typedef` da Seção 5.3), declare um vetor de 5 alunos em C, preencha os campos de cada um em um laço (usando `scanf` para nome e notas), e ao final imprima o nome dos alunos cuja média das três notas seja maior que 7. Tente montar isso a partir do que já foi mostrado nas Seções 4.1 e 5.2, antes de perguntar para uma IA.

### 5.5. Passando structs para funções

⚠️ **Ponteiros à frente.** Lembra que, na Seção 4.2, vimos que passar um vetor para uma função em C já deixa a função enxergar (e poder alterar) o vetor original, meio que "de graça"? Com structs, **acontece o oposto**: passar um struct para uma função, do jeito simples, **copia o struct inteiro**. A função recebe uma cópia independente; qualquer alteração feita lá dentro não volta para a variável original.

```c
void aumenta_nota(struct aluno a) {
    a.nota1 += 1.0;   // isso altera só a copia local, dentro da função
}

int main() {
    struct aluno a1;
    a1.nota1 = 8.5;

    aumenta_nota(a1);
    printf("%.1f\n", a1.nota1);   // ainda imprime 8.5 — a original não mudou!
    return 0;
}
```

Isso costuma surpreender quem já se acostumou com o comportamento de vetores no capítulo anterior — e é exatamente aqui que a comparação com VisuAlg e Python se torna mais reveladora:

* Em **VisuAlg**, um `registro` passado como parâmetro segue a mesma regra que já vimos para vetores: por padrão é por valor (copia), e só é passado por referência se o parâmetro for declarado com `var`. Ou seja, o VisuAlg é **consistente** entre vetor e registro — os dois seguem a mesma regra explícita.
* Em **Python**, um objeto (instância de uma `class`, incluindo `dataclass`) passado para uma função **não é copiado** — a função recebe uma referência ao mesmo objeto, e alterar um atributo dentro da função altera o objeto original, igualzinho ao que vimos com listas. Ou seja, Python é consistente entre lista e objeto, mas na direção **oposta** à de C: em Python, tudo (lista ou objeto) é compartilhado por padrão; em C, vetor é compartilhado, mas struct é copiado.

| Passado para uma função... | C | VisuAlg (sem `var`) | Python |
|---|---|---|---|
| vetor / lista | alterações afetam o original | cópia (não afeta o original) | alterações afetam o original |
| struct / registro / objeto | **cópia** (não afeta o original) | cópia (não afeta o original) | alterações afetam o original |

Para fazer uma função em C alterar de verdade o struct da variável que chamou — o mesmo efeito do `var` em VisuAlg, ou o comportamento padrão de um objeto em Python —, é preciso passar o **endereço** do struct, usando o operador `&`, e a função precisa recebê-lo como um **ponteiro**:

```c
void aumenta_nota(struct aluno *a) {
    a->nota1 += 1.0;   // agora sim altera o struct original
}

aumenta_nota(&a1);
```

Não se preocupe em entender `&`, `struct aluno *` ou `->` agora — isso é exatamente o assunto do próximo capítulo. Fica só o registro de que essa ferramenta existe, e de que é a peça que faltava para fazer com struct o que vetor já faz sozinho.

### 5.6. Structs dentro de structs

Um campo de um struct também pode ser, ele mesmo, outro struct — útil para modelar algo que já tem uma estrutura própria, como um endereço dentro dos dados de um aluno.

```c
struct endereco {
    char rua[30];
    int numero;
};

struct aluno {
    char nome[20];
    struct endereco casa;
};

struct aluno a1;
strcpy(a1.nome, "Maria");
strcpy(a1.casa.rua, "Rua das Flores");
a1.casa.numero = 123;
```

```
tipo
    endereco = registro
        rua: cadeia
        numero: inteiro
    fimregistro

    aluno = registro
        nome: cadeia
        casa: endereco
    fimregistro

var
    a1: aluno
inicio
    a1.nome <- "Maria"
    a1.casa.rua <- "Rua das Flores"
    a1.casa.numero <- 123
```

```python
class Endereco:
    def __init__(self, rua, numero):
        self.rua = rua
        self.numero = numero

class Aluno:
    def __init__(self, nome, casa):
        self.nome = nome
        self.casa = casa

a1 = Aluno("Maria", Endereco("Rua das Flores", 123))
```

Os três casos usam a mesma ideia — um valor "de dentro" de outro — e o acesso encadeado com `.` (`a1.casa.numero`) funciona igual nas três linguagens.

!!! note "Um caso especial que fica para depois: structs que se referenciam"
    Existe um tipo de struct que **não dá para representar sem ponteiros de jeito nenhum**: um struct que tem, como campo, um ponteiro para *outro struct do mesmo tipo* — é assim que se constroem listas encadeadas, árvores, e outras estruturas de dados dinâmicas. Isso não é possível com um campo comum (só com ponteiro), e nem VisuAlg nem Python precisam de nada especial para o equivalente (uma lista em Python pode conter referências a outros objetos livremente). Vamos construir isso com calma no próximo capítulo, depois de entender ponteiros de verdade.

!!! question "Exercício de mapeamento 5.3"
    Em Python:

    ```python
    class Data:
        def __init__(self, dia, mes, ano):
            self.dia = dia
            self.mes = mes
            self.ano = ano

    class Livro:
        def __init__(self, titulo, publicacao):
            self.titulo = titulo
            self.publicacao = publicacao

    livro1 = Livro("O Pequeno Principe", Data(6, 4, 1943))
    print(livro1.titulo, "-", livro1.publicacao.ano)
    ```

    Escreva os `struct`s equivalentes em C (`struct data` e `struct livro`, com `livro` tendo um campo do tipo `struct data`), declare e preencha uma variável `livro1` com os mesmos valores, e imprima o título e o ano de publicação.

### 5.7. Lista de exercícios

1. Usando o `struct ponto { float x, y; };` da Seção 5.2, escreva uma função `float distancia(struct ponto a, struct ponto b)` que calcula a distância entre dois pontos (`sqrt(pow(b.x - a.x, 2) + pow(b.y - a.y, 2))`, usando `<math.h>`). Repare que aqui passar os pontos por valor (cópia) é perfeitamente adequado — a função só precisa ler os campos, não alterá-los.
2. Defina um `struct retangulo` com dois campos do tipo `struct ponto` (por exemplo, `canto_inferior_esquerdo` e `canto_superior_direito`). Escreva funções `float area(struct retangulo r)` e `float perimetro(struct retangulo r)`.
3. Defina um `struct data { int dia, mes, ano; };` e escreva uma função `int mais_recente(struct data a, struct data b)` que devolve `1` se `a` é mais recente que `b`, ou `0` caso contrário. Lembre que C não tem um jeito de comparar dois structs inteiros de uma vez com `==` (assim como não tem para strings) — você precisa comparar campo por campo (primeiro o ano, depois o mês, depois o dia).
4. Defina `struct funcionario { char nome[30]; float salario; };` e, usando o que vimos na Seção 5.4, declare um vetor de 10 funcionários. Escreva um programa que leia os dados dos 10 funcionários e imprima: o nome de quem tem o maior salário, e o total da folha de pagamento (soma de todos os salários).
5. Volte ao `struct aluno` da Seção 5.2 (nome + três notas). Escreva uma função `void imprime_aluno(struct aluno a)` que imprime o nome e a média das três notas. Depois, escreva uma função `void aprova(struct aluno *a)` que, se a média do aluno for maior ou igual a 6, altera um novo campo `int aprovado` do struct para `1` (você vai precisar adicionar esse campo ao `struct aluno`). Por que a primeira função recebe `struct aluno a` (por valor) e a segunda precisa de `struct aluno *a` (por ponteiro)? Releia a Seção 5.5 se tiver dúvida.
