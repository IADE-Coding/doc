# Make

O programa `make` foi criado para facilitar a repetição de tarefas de compilação (e qualquer outro tipo de tarefas).

É frequente ser necessário compilar bibliotecas (i.e., ficheiros `.o`, ou *objetos* C), e os executáveis que as utilizam.

O programa segue um conjunto de regras descritas num ficheiro com o nome `Makefile`.

Considerando os seguintes ficheiros

```C
// lib.c
#ifndef LIB_H
#define LIB_H
int calculate(const int a, const int b);
#endif

// lib.c
#include "lib.h"

int calculate(const int a, const int b) {
    return a + b;
}

// main.c
#include <stdio.h>
#include "lib.h"

int main() {
    printf("%d\n", calculate(2, 2));
    return 0;
}
```

Para compilar o programa é necessário compilar o objeto `lib.o` em primeiro lugar

```bash
gcc -c -Wall -Wextra lib.c -o lib.o
```

Por último, compilamos o executável

```bash
gcc -Wall -Wextra main.c lib.o -o main
```

O mesmo procedimento pode ser descrito no seguinte ficheiro `Makefile`

```Makefile
main: main.c lib.o
	gcc -Wall -Wextra main.c lib.o -o main

lib.o: lib.c
	gcc -c -Wall -Wextra lib.c -o lib.o
```

E é suficiente executar

```bash
make main
```

As operações executadas são

```bash
gcc -c -Wall -Wextra lib.c -o lib.o
gcc -Wall -Wextra main.c lib.o -o main
```

O `make` seguir a seguinte ordem de operações:

1. Verifica se existe um ficheiro chamado `main`
   1. Se existir, verifica a sua data de alteração;
      1. Se a data de alteração for anterior à data de alteração de alguma das suas dependências, verifica as dependências e executa a operação.
   2. Se não existir, verifica as dependências e executa a operação.

Ou seja, dado que o ficheiro `main` não existe, é necessário executar a operação do *target* `main`. Para essa execução ocorrer é necessário satisfazer as suas dependências de objetos, i.e., verificar se existem os ficheiros `main.c` e `lib.o`. O ficheiro `main.c` existe, e essa dependência fica satisfeita. Após verificar que o ficheiro `lib.o` não existe, o programa vai verificar se existe um *target* com esse nome e, se existir, executa-o.

Como `main` é o primeiro target do ficheiro `Makefile`, podemos executar apenas `make`. O programa executa o primeiro *target* automaticamente.

A execução real de operações é a seguinte:

```bash
gcc -Wall -Wextra -c lib.c
gcc -Wall -Wextra -o main main.c lib.o
```

Um *target* de `Makefile` tem a seguinte estrutura

```Makefile
regra: objeto1 objeto2 ... objetoN
	operaçãoA
	operaçãoB
	...
	operaçãoX
```

Antes de cada operação é necessário introduzir um tab (`\t`), e não espaços.

Uma dependência não tem que ser necessariamente um ficheiro. Pode ser apenas um *target* com um conjunto de operações relevantes para o projeto. Por exemplo, é conveniente acrescentar um *target* que elimine executáveis e objetos, deixando apenas o código no diretório.

```Makefile
main: main.c lib.o
	gcc -Wall -Wextra main.c lib.o -o main

lib.o: lib.c
	gcc -c -Wall -Wextra lib.c -o lib.o

clean:
	rm -f *.o main
```

Para executar o novo *target* fazemos `make clean`.

Um `Makefile` também permite definição de variáveis. Essas variáveis são acedidas recorrendo à notação `$(VAR)`.

O nome das variáveis é arbitrário, mas existem algumas *implícitas* como `CFLAGS`, com flags de compilação, `LDLIBS` com as bibliotecas de sistema a *linkar* ao programa (e.g., `-lm` para a biblioteca de funções matemáticas declaradas em `math.h`), e `LDFLAGS` com os caminhos para bibliotecas fora das localização padrão. A utilização destas variáveis implícitas permite simplificar a escrita de `Makefiles` mais tarde. Mais sobre o significado de variáveis implícitas em <https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html>.

```Makefile
CFLAGS=-Wall -Wextra
CC=gcc

main: main.c lib.o
	$(CC) $(CFLAGS) main.c lib.o -o main

lib.o: lib.c
	$(CC) $(CFLAGS) lib.c -o lib.o

clean:
	rm -f *.o main
```

Por exemplo, no *target* `main`, a operação `$(CC) $(CFLAGS) main.c lib.o -o main` é executada como `gcc -Wall -Wextra -o main main.c lib.o`.

O `make` também disponibiliza um conjunto de variáveis automáticas para utilizar nos ficheiros `Makefile`. Por exemplo,

```Makefile
CFLAGS=-Wall -Wextra
CC=gcc

main: main.c lib.o
	$(CC) $(CFLAGS) -o $@ $^

lib.o: lib.c
	$(CC) $(CFLAGS) -c $^

clean:
	rm -f *.o main
```

Onde `$@` representa o nome do *target*, e `$^` representa as dependências. Por exemplo, no *target* `main`, a operação `$(CC) $(CFLAGS) -o $@ $^` é executada como `gcc -Wall -Wextra -o main main.c lib.o`.

Mais informação sobre variáveis automáticas em <https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html#Automatic-Variables>.

Estão ainda disponíveis *targets* automáticos. Tratam-se de regras inscritas no `make`, que permitem simplificar a escrita de ficheiros `Makefile`, omitindo alguns *targets*. Por exemplo, um *target* `nome.o` assume que se trata de uma *objeto* `C`, e tem uma regra de compilação implícita na forma `$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $*.c`.

Ou seja, podemos omitir o *target* `lib.o`.

```Makefile
CFLAGS=-Wall -Wextra
CC=gcc

main: main.c lib.o
	$(CC) $(CFLAGS) -o $@ $^

clean:
	rm -f *.o main
```

Outra regra implícita possível é a compilação de programas com código fonte `C`. Podemos simplificar o *target* `main` retirando `main.c` da lista de dependências.

```Makefile
CFLAGS=-Wall -Wextra
CC=gcc

main: lib.o

clean:
	rm -f *.o main
```

Podemos também tirar partido da declaração de variáveis e indicar o objetivo do projeto (i.e., o executável), e os *objetos* dos quais depende.

```Makefile
TARGET=main
OBJECTS=lib.o
CFLAGS=-Wall -Wextra
CC=gcc

$(TARGET): $(OBJECTS)

clean:
	rm -f $(OBJECTS) $(TARGET)
```

Onde criamos uma variável `TARGET` com o nome do executável final, e uma lista de dependências na variável `OBJECTS` (estes nomes são arbitrários, mas têm utilização comum). Se o programa utilizar mais *objetos* C no futuro, é suficiente acrescentar à lista na variável `OBJECTS`.