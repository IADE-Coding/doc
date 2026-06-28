# Linguagem C

- [Linguagem C](#linguagem-c)
  - [TODO](#todo)
  - [Notação](#notação)
  - [Versões](#versões)
  - [Regras fundamentais](#regras-fundamentais)
  - [Processo de compilação](#processo-de-compilação)
  - [Tipos de ficheiros](#tipos-de-ficheiros)
  - [Pré-processador](#pré-processador)
    - [Incluir código - `#include`](#incluir-código---include)
    - [Macros - `#define`](#macros---define)
  - [Definição de ficheiros cabeçalho](#definição-de-ficheiros-cabeçalho)
  - [Tipos de dados](#tipos-de-dados)
  - [Funções](#funções)
  - [Tipos de variáveis](#tipos-de-variáveis)
  - [Cast](#cast)
  - [Memória](#memória)
  - [Retorno da função `main`](#retorno-da-função-main)

## TODO

- [x] Versões
- [x] Etapas de compilação
- [ ] Parâmetros de `main`
- [x] Variáveis
- [ ] Vírgula flutuante
- [ ] Funções
- [ ] Apontadores
- [ ] Gestão de memória
  - [ ] Stack & heap
- [ ] Bibliotecas
  - [ ] Estática
  - [ ] Partilhada
  - [ ] Flags GCC (-L -I)
  - [ ] -Wall -Wextra -c99
  - [ ] Electric fence

## Notação

Os exemplos apresentados neste documento estão completos e, para cada um, são indicadas as instruções de compilação e execução. As instruções de linha de comandos iniciam com o símbolo `$`. Cada ficheiro de exemplo inicia com um comentário de linha indicando o nome do ficheiro.

## Versões

A linguagem foi desenvolvida por Dennis Ritchie que, no início da década de 1970 esteve envolvido na construção do sistema operativo UNIX. Em 1972 apresentou a primeira versão da linguagem, e em 1978 foi publicado o livro *The C Programming Language*, por Brian Kernighan e Dennis Ritchie, que se tornou efetivamente na sua especificação informal. As próximas versões da linguagem passaram a ser *standards* internacionais.

- ANSI C, que corresponde à segunda edição da obra *The C Programming Language*;
- C99, introduz suporte mais mais alguns tipos, como `bool` ou `complex`, permite comentários de linha com `//`, e melhora a robustez da linguagens em vários aspetos gerais e especializados;
- C11, clarifica aspetos não definidos nas versões anteriores, e introduz várias alterações especializadas;
- C17, apenas corrige imprecisões do C11.

Mais informação sobre versões da linguagem em <https://en.wikipedia.org/wiki/C_%28programming_language%29#History>.

## Regras fundamentais

Com exceção de instruções de pre-processador, as linhas de código escritas na linguagem C terminam com um ponto e vírgula.

É possível declarar variáveis, funções, e novos tipos de dados. Cada declaração necessita de um identificador. Este identificador deve começar por uma letra (maiúscula ou minúscula) ou *underscore* (i.e., `_`), e pode conter letras (maiúsculas ou minúsculas), *undercores*, e números.

O mesmo identificador não pode ser utilizado duas vezes no mesmo ficheiros, nem pode pertencer à seguinte lista de palavras reservadas (em C99):

```C
_Bool, _Complex, _Imaginary, auto, break, case, char, const, continue, default, do, double, else, enum, extern, float, for, goto, if, inline, int, long, register, restrict, return, short, signed, sizeof, static, struct, switch, typedef, union, unsigned, void, volatile, while
```

Um *objeto* C corresponde a código compilado. Não confundir com Programação Orientada a Objetos. O C não é orientado a objetos.

Um *programa* C corresponde a código compilado onde existe uma função com o nome `main`.

Um programa pode ser construído com base em apenas um ficheiro de código, ou recorrendo a vários ficheiros, onde apenas num deles existe a função `main`, sendo os restantes *objetos* do qual o programa depende.

## Processo de compilação

O código C passa por um conjunto de processos até ser traduzido em instruções máquina:

1. **Pre-processador**
   1. As instruções de pre-processador. i.e., iniciadas por `#`, como `#include`, são traduzidas para código C;
   2. O resultado é um código fonte modificado.
2. **Compilador**
   1. São gerados *objetos* C, que são artefactos compilados, prontos a serem integrados num programa;
   2. O resultado são um ou mais ficheiros `.o`.
3. ***Linker***
   1. Os vários *objetos* são *ligados* num único executável, que vai incluir todo o código máquina necessário para executar a aplicação;
   2. O resultado é um executável.

## Tipos de ficheiros

Existem dois tipos de ficheiros em C:

- Cabeçalho, `.h`: inclui expressões de pre-processador, declaração de tipos de dados, e declaração de funções;
- Implementação, `.c`: inclui expressões de pre-processador, declaração de tipos de dados, declarações de funções, e implementação de funções.

A intenção dos cabeçalhos é permitir a utilização do código escrito num ficheiro por outro. O cabeçalho permite declarar apenas as funções e tipos de dados que pretendemos partilhar com utilizadores do nosso código. Mais informação sobre utilização de ficheiros em <https://en.wikipedia.org/wiki/Include_directive#C/C++>.

## Pré-processador

As instruções de pré-processador são importantes para, nomeadamente, definir relações entre vários ficheiros de código, declarar expressões simples ou constantes.

### Incluir código - `#include`

A instrução `#include` permite sinalizar a cópia de um ficheiro `.h` para o ficheiro atual. Desta forma declaramos que pretendemos utilizar as funções declaradas no cabeçalho indicado.

Na etapa do *linker*, a implementação destas funções deve estar disponível num dos vários objetos envolvidos na construção do executável.

### Macros - `#define`

É possível declarar expressões de código simples (i.e., *macros*) sem definir funções. Por exemplo, o máximo entre dois números pode ser declarado com a uma função

```C
// max_func.c
#include <stdio.h>

int max(const int a, const int b) {
  return a > b ? a : b;
}

int main() {
  printf("%d\n", max(4, 2));
  return 0;
}
```

```bash
$ gcc max_func.c -o max_func
$ ./max_func
4
```

Ou pode ser declarado numa macro de pré-processador, que mais tarde será traduzida para código C durante a compilação. A macro pode ser utilizada diretamente no código de funções.

```C
// max_macro.c
#include <stdio.h>

#define MAX(a, b) ((a) > (b) ? (a) : (b))

int main() {
  printf("%d\n", MAX(4, 2));
  return 0;
}
```

```bash
$ gcc max_macro.c -o max_macro
$ ./max_macro
4
```

Mais informação sobre macros em <https://gcc.gnu.org/onlinedocs/cpp/Macros.html>.

A instrução `#define` foi o mecanismo disponível para definir constantes até à introdução do operador `const` na normal C90. No exemplo seguinte, `PI_1` e `PI_2` representam duas estratégias possíveis para a definição de constantes, embora a utilização de `const` seja a recomendada.

```C
// const_strategies.c
#include <stdio.h>

#define PI_1 3.1415926

const double PI_2=3.1415926;

int main() {
  printf("%f\n", PI_1);
  printf("%f\n", PI_2);
  return 0;
}
```

```bash
$ gcc const_strategies.c -o const_strategies
$ ./const_strategies
3.141593
3.141593
```

## Definição de ficheiros cabeçalho

Um cabeçalho pode ser incluído por vários ficheiros `.c` num mesmo projeto, o que representa um potencial problema de compilação. O ficheiro só deve ser incluído uma vez.

Para assegurar a inclusão exclusiva, a construção de ficheiros `.h` pode ser guardada recorrendo ao pré-processador.

```C
#ifndef NOME_UNICO
#define NOME_UNICO

// código do cabeçalho

#endif
```

A construção vai permitir declarar uma variável `NOME_UNICO`, que só pode ser definida uma vez, efetivamente garantindo que o código entre os `#ifndef` e `#endif` só é executado uma vez.

## Tipos de dados

A linguagem permite declarar cinco tipos de dados fundamentais: `char`, `short`, `int`, `float`, e `double`. Permite ainda os seguintes modificadores: `signed`, `unsigned`, `short`, e `long`. As combinações entre tipos e modificadores estão detalhadas em <https://en.wikipedia.org/wiki/C_data_types#Basic_types>.

O standard C99 introduziu o tipo `bool` (disponível em `stdbool.h`) para definir variáveis booleanas, e o modificador `complex` (em `complex.h`), aplicável a `double`, `float`, para definir números complexos.

Cada tipo tem um tamanho em bytes disponível para representar valores, e o seu domínio depende do modificador utilizado.

A representação de valores numéricos varia de acordo com o tipo de dados utilizados.

Valores inteiros podem declarados positivos (e.g., `int i = 42;`) ou negativos (e.g., `int i = -3;`).

Valores reais  podem ser declarados com precisão simples (`float`), i.e., `float f = 10.11F;`,  ou dupla (`double`), e.g., `double d = 42e-4;`.

Carateres (`char`) são representados entre plicas, e.g., `char c = 'A';`;

Existem operadores aritméticos, `+ - * / %`, de afetação, `=`, incremento e decremento, `++ --`, e lógicos, `> < == <= >= ! != && !!`.

Para determinar o tamanho, em bytes, de uma variável podemos utilizar a função `size_t sizeof(var)`.

```C
// sizes.c
#include <stdio.h>

int main() {
    printf("char: %ld\n", sizeof(char));
    printf("short: %ld\n", sizeof(short));
    printf("int: %ld\n", sizeof(int));
    printf("float: %ld\n", sizeof(float));
    printf("double: %ld\n", sizeof(double));
    printf("long: %ld\n", sizeof(long));
    printf("long long: %ld\n", sizeof(long long));
    printf("void *: %ld\n", sizeof(void *));
}
```

Em GNU Linux 64 bits, CPU Intel(R) Core(TM) i7-8565U CPU @ 1.80GHz, gcc (Ubuntu 11.2.0-7ubuntu2) 11.2.0, os resultados são

```text
$ gcc sizes.c -o sizes
$ ./sizes
char: 1
short: 2
int: 4
float: 4
double: 8
long: 8
long long: 8
void *: 8
```

`void *` indica o tamanho de qualquer apontador.

Mais informação sobre a função `size_of` em <https://en.wikipedia.org/wiki/Sizeof>.

## Funções

Uma função é uma construção da linguagem que permite definir um *contrato* de execução de um conjunto de linhas de código.

Este contrato tem o seguinte formato

```C
tipo_retorno identificador(tipo_1 parâmetro_1, ..., tipo_n parâmetro_n) {
  bloco_da_função
}
```

O contrato implica que, se o utilizador da função *invocar* o seu identificador, e indicar um valor para cada parâmetro declarado, a função executa o seu bloco, dentro do qual os parâmetros existem como variáveis cujo valor inicial foi o indicado pelo utilizador, e retorna um valor com o tipo indicado.

O bloco da função é um conjunto arbitrário de linhas de código, cuja única restrição é a necessidade de surgir uma linha de retorno (`return`) com o tipo de dados indicado. Se o retorno for declarado com `void`, a função não tem que indicar retorno.

O operador de retorno funciona com `return valor` onde `valor` é um valor ou identificador (de variável ou função) com o tipo de retorno da função.

## Tipos de variáveis

É possível declarar variáveis locais, globais, estáticas e externas.

Uma variável local é declarada numa função, e vai existir a partir desse momento até ao final do bloco onde foi declarada. Uma variável declarada numa função não existe fora dela.

Uma variável global é declarada num ficheiro de código, fora de uma função, e pode ser utilizada por qualquer função no ficheiro. Por exemplo,

```C
// var_global.c
#include <stdio.h>

int n = 42;

int main() {
    printf("%d\n", n);
    return 0;
}
```

```bash
$ gcc var_global.c -o var_global
$ ./var_global
42
```

Uma variável estática é declarada numa função, e mantém o seu valor atual entre invocações sucessivas da função. Estas variáveis são declaradas com o modificador `static`. Por exemplo,

```C
// var_static.c
#include <stdio.h>

void func() {
    static int n = 0;
    n++;
    printf("%d\n", n);
}

int main() {
    for(int i = 0; i < 3; i++) {
        func();
    }
    return 0;
}
```

```bash
$ gcc var_static.c -o var_static
$ ./var_static
1
2
3
```

Uma variável externa é uma variável global de outro ficheiro. Estas variáveis são declaradas com o modificador `extern`. Por exemplo,

```C
// lib.h
#ifndef LIB_H
#define LIB_H
extern int n;
#endif
```

```C
// lib.c
#include "lib.h"

int n = 42;
```

```C
// main.c
#include <stdio.h>
#include "lib.h"

int main() {
    printf("%d\n", n);
    return 0;
}
```

```bash
$ gcc main.c lib.c -o main
$ ./main
42
```

## Cast

A conversão explícita de um valor de um tipo para outro é suportada pelo mecanismo de *type casting*.

O novo tipo é indicado entre parêntesis antes do identificador da variável com o valor atual.

```C
(tipo) expressão
```

A *expressão* pode ser qualquer construção que, depois de avaliada, tenha um valor.

Por exemplo, considerando a variável `float f = 42.5F;`, para converter o tipo para inteiro e guardar numa nova variável fazemos `int i = (int)f;`. Esta conversão perde informação relativa à parte decimal do `float`.

Outro exemplo seria converter o tipo de uma soma, como `int i = (int)(1.2 + 1.3);`.

## Memória


## Retorno da função `main`

Em `C`, um programa retorna um valor inteiro. Na realidade, esta é uma expetativa do sistema operativo, que interpreta este valor como sucesso ou insucesso da execução. Geralmente, escrevemos

```C
int main() {
    return 0;
}
```

Tal indica que o programa termina com valor 0, o que *geralmente* representa *sucesso*.

Na realidade, o sistema operativo é que determina qual é o código de sucesso. Se a intenção for promover a portabilidade da aplicação, é mais seguro utilizar a constante `EXIT_SUCCESS` definida em `stdlib.h` para indicar este retorno, porque o seu seu valor é determinado pelo sistema operativo.

```C
#include <stdlib.h>

int main() {
    return EXIT_SUCCESS;
}
```
