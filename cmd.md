# Linha de comandos do Windows

## Command Prompt

A linha de comandos do Windows é acedida através do programa `cmd.exe`.

Da linha de comandos é possível executar programas, e gerir diretórios e ficheiros. A tabela seguinte apresenta alguns operadores mais comuns disponíveis.

| Comando    | Descrição                           |
| ---------- | ----------------------------------- |
| `dir`      | Mostrar o conteúdo de um diretório. |
| `dir /A`   | Incluir diretórios ocultos.         |
| `cd nome`       | Navegar para um diretório.          |
| `cd .`     | Fica no mesmo diretório.            |
| `cd ..`    | Navega para o diretório anterior.   |
| `mkdir nome`    | Criar um diretório.                 |
| `rmdir nome`    | Remover um diretório.               |
| `rmdir /S nome` | Remover um diretório com ficheiros. |
| `cls`      | Limpar o ecrã.                      |
| `type nome`     | Mostrar o conteúdo de um ficheiro.  |

Além do conteúdo variável de cada diretório, estes contêm ainda dois diretórios especiais: o ponto (`.`), que é uma referência para si próprio; e os dois pontos (`..`), que é uma referência para o diretório anterior.

A maioria dos comandos aceita modificadores e/ou argumentos. Os modificadores são indicados com uma barra para a direita (`/`), e o argumento mais frequente é o nome de um diretório ou ficheiro, e.g., `mkdir nome_do_projeto`.

## Acrescentar executáveis à variável de ambiente `PATH`

A linha de comandos executa programas que estejam acessíveis através de uma variável de ambiente chamada `Path`. Esta variável representa uma listagem de diretórios no computador onde os executáveis estão disponíveis. No Windows 10, para acrescentar (ou remover) diretórios da Path é necessário o seguinte:

1. Windows + Pause (abre a janela de propriedades do sistema);
2. Link "Change settings";
3. Tab superior "Advanced", e Botão "Environment Variables...";
4. Editar a Path em "User variables" ou "System variables". 
