# Compilar C com `gcc`

- [Compilar C com `gcc`](#compilar-c-com-gcc)
  - [TODO](#todo)
  - [Notação](#notação)
  - [GCC](#gcc)
    - [Instalação em GNU Linux](#instalação-em-gnu-linux)
    - [Instalação em MacOS](#instalação-em-macos)
    - [Instalação em Windows](#instalação-em-windows)
    - [Exemplo](#exemplo)
      - [Organização de projeto](#organização-de-projeto)
      - [Código inicial](#código-inicial)
      - [Compilação](#compilação)

## TODO

- [ ] GCC
  - [x] Objetos C
  - [ ] Flags de compilação
- [ ] Make
  - [x] Makefile
  - [ ] CMake
- [ ] GDB
- [ ] Código Máquina
  - [ ] objdump
  - [ ] gdb
  - [ ] godbolt
- [ ] Valgrind

## Notação

Os exemplos apresentados neste documento estão completos e, para cada um, são indicadas as instruções de compilação e execução. As instruções de linha de comandos iniciam com o símbolo `$`. Cada ficheiro de exemplo inicia com um comentário de linha indicando o nome do ficheiro.

## GCC

`GCC` significa *GNU Compiler Collection* e é, na realidade, uma coleção de compiladores. O projeto pode ser acompanhado em <https://gcc.gnu.org/>.

O compilador `C` do `GCC` depende de algumas caraterísticas do sistema operativo. Nomeadamente, este deve seguir a norma POSIX (*Portable Operating System Interface*), que define um conjunto de *standards* que regulam as expetativas sobre os sistemas. Essas expetativas incluem:

- Linha de comandos (*shell*) com determinadas caraterísticas;
- Chamadas de sistema padronizadas.

Podemos considerar, para efeitos de compatibilidade com o GCC, que GNU Linux e MacOS, sendo derivados do UNIX, são POSIX (na realidade, destes dois tipos de sistemas, apenas MacOS foi certificado POSIX). Windows precisa da instalação de subsistema compatível com POSIX. 

A certificação [POSIX](http://get.posixcertified.ieee.org/) e [UNIX](https://www.opengroup.org/certifications/unix) são processos complexos, que envolvem várias etapas.

### Instalação em GNU Linux

De acordo com a distribuição, existirão ferramentas que apoiam a instalação de software. Por exemplo, [Ubuntu](https://ubuntu.com/) (variante de [Debian](https://www.debian.org/)), vem com a aplicação `apt` instalada. Numa linha de comandos, é possível instalar todo o ambiente de desenvolvimento com

```bash
$ apt install build-essential
```

Como qualquer sistema UNIX, devem existir vários utilizadores, com níveis de permissão diferentes. Geralmente, o utilizador atual não tem permissões de administrador.

O utilizador administrador tem o nome de `root`, e não é boa ideia utilizar diretamente. Este utilizador pode apagar qualquer ficheiro no sistema, o que pode tornar o sistema inoperacional.

No entanto, a instrução de instalação necessita de permissões de administrador, pelo que pode ser executada com

```bash
$ sudo apt install build-essential
```

O programa `sudo` permite executar outros programas como se fosse `root`.

### Instalação em MacOS

MacOS tem o compilador instalado desde origem. Não é necessário executar mais operações.

No entanto, o sistema não vem com o *debugger* `gdb` instalado. Para o instalar, a estratégia mais simples é recorrer ao [HomeBrew](https://brew.sh/), um sistema de gestão de instalação de software para MacOS. Com o sistema instalado, executamos, num terminal

```bash
$ brew install gdb
```

### Instalação em Windows

Windows necessita de um subsistema POSIX. As alternativas são (entre outras):

- *Windows Subsystem for Linux* (WSL), disponível como *feature* opcional em Windows 10 e 11.
- [MingW](https://www.mingw-w64.org/), que pode ser instalado diretamente, ou através da instalação do IDE [CodeBlocks](https://www.codeblocks.org/).

### Exemplo

#### Organização de projeto

Para desenvolver um projeto em `C` é importante manter alguma organização. Existem diversas estratégias, mas vamos considerar que o código se encontra no diretório `C:\Users\user\dev\project` em Windows, ou `/home/user/dev/project` em Linuc/MacOS.

Em Windows, a separação entre diretórios e ficheiros é representada com uma barra para a esquerda (`\`), em GNU linux/MacOS utiliza-se uma barra para a direita (`/`).

Focando no projeto, este deverá ter a seguinte estrutura inicial

```Text
dev/project
    .
    ..
    main.c
```

Alguns aspetos relevantes:

- A notação é a utilizada em Linux, i.e., as barras estão para a esquerda;
- Existe dois diretórios especiais, representados com pontos:
  - O diretório `.` representa o diretório atual, i.e., é um atalho para `dev/project/`
  - O diretório `..` representa o diretório anterior, i.e., é um atalho para `dev/`
- O ficheiro `main.c` é um ficheiro de código `C`, sinalizado pela extensão `.c`

#### Código inicial

O código de `main.c` é

```C
// main.c
#include <stdio.h>

int main() {
    printf("Hello, world!\n");
    return 0;
}
```

```bash
$ gcc main.c
$ ./a.out
Hello, World!
```

Aqui temos um programa que apenas mostra a frase `Hello, world!` no terminal. O caráter `\n` representa uma nova linha. A função `printf` permite transportar os carateres até à linha de comandos, e está disponível em `stdio.h`.

A instrução `#include` é uma operação de pre-processador, que é equivalente a copiar o conteúdo de `stdio.h` para o ficheiro atual.

Geralmente, um ficheiro `.h` contém *declarações* de funções, constantes, e macros. A implementação das funções é feito num ficheiro `.c`. Esta separação é importante para podermos controlar que construções podem ser partilhadas com outros ficheiros (já que a instrução `#include` simula uma cópia do conteúdo dos ficheiros). Ao declarar essas funções, ficamos obrigados a, eventualmente, disponibilizar o seu código.

A estratégia mais comum passa por dar o mesmo nome aos dois ficheiros, variando apenas a extensão. Assim sabemos que as funções declaradas no `.h` são partilháveis, e têm o seu código disponível no `.c`, e as que só existem no `.c` (ou seja, não estão declaradas no `.h`) não estão acessíveis a terceiros.

Em relação à estratégia seguido no `stdio.h`, sabemos que parte do esforço de implementação do compilador passa por escrever o código das funções nele declaradas. Na realidade, esse código está espalhado por vários ficheiros `.c`, e não ficou concentrado num único `stdio.h`.

#### Compilação

Para compilar o programa fazemos

```bash
gcc main.c
```

Vamos obter o resultado compilado num ficheiro `a.out` (em Windows será `a.exe`, mas a extensão não é particularmente relevante em sistemas UNIX). A estrutura do projeto passa a ser

```Text
dev/project
    .
    ..
    main.c
    a.out
```

Para executar, fazemos

```bash
./a.out
```

A instrução indica que pretendemos a execução do programa `a.out` no diretório `.`, i.e., no diretório atual.

Para obtermos um executável com um nome mais interessante fazemos

```bash
gcc main.c -o main
```

Ficamos com a seguinte estrutura:

```Text
dev/project
    .
    ..
    main.c
    main
```

Para executar seguimos a estratégia anterior, fazemos

```bash
./main
```

