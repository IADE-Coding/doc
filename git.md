# GIT

O Git é um sistema de versões distribuído. O objetivo dos sistemas de versões é oferecer uma linha temporal. Essa linha permite regressar a um ponto anterior do projeto, explorar alternativas, e auditar alterações. Ao ser distribuído, o git funciona como uma rede de repositórios independentes que partilham as várias alterações do projeto entre si. Cada colaborador tem um repositório local, geralmente dentro de um diretório chamado `.git`, no diretório geral do projeto. Além dos repositórios locais dos colaboradores, é comum existir um repositório remoto, mantido por um serviço online, e.g., GitHub (<https://github.com/>).

Os sistemas de versões suportam a noção de ramificação temporal do projeto, i.e., permitem criar várias versões concorrentes do projeto, onde é possível acrescentar trabalho sem alterar as outras versões automaticamente, permitindo isolar trabalho entre colaboradores, explorar ideias novas sem receio de perder versões estáveis do projeto, e organizar a evolução do projeto. Cada ramificação do projeto tem o nome de *branch*. A tabela seguinte apresenta uma listagem dos comandos mais comuns do git.

| Comando             | Descrição                                                                                           |
| ------------------- | --------------------------------------------------------------------------------------------------- |
| `clone`             | Copia um repositório para o diretório atual. O repositório pode estar numa máquina local ou remota. |
| `init`              | Inicializar o repositório no diretório indicado.                                                    |
| `add`               | Incluir ficheiros no próximo commit.                                                                |
| `status`            | Mostrar situação atual.                                                                             |
| `commit`            | Gravar alterações.                                                                                  |
| `commit -m`         | Indicar mensagem de commit.                                                                         |
| `commit -a`         | Incluir ficheiros alterados no commit.                                                              |
| `commit -am`        | Incluir ficheiros alterados e indicar mensagem de commit.                                           |
| `log`               | Mostrar informação sobre commits anteriores.                                                        |
| `remote`            | Operações relacionadas com repositórios remotos (GitHub).                                           |
| `push`              | Enviar estado atual para repositório remoto.                                                        |
| `pull`              | Receber estado do repositório remoto.                                                               |
| `reset`             | Cancelar inclusões para o próximo commit.                                                           |
| `reset --hard HEAD` | Regressar ao estado do último commit.                                                               |
| `branch`            | Operações relacionadas com branches do repositório.                                                 |
| `checkout`          | Navegar entre momentos diferentes na história do projeto, e alternar entre branches.                |
| `merge`             | Inclui alterações num branch.                                                                       |

O git não tem opinião sobre como utilizar branches. No limite, é perfeitamente possível levar um projeto até ao fim sem separar o desenvolvimento em branches, mas é desaconselhado.

Por defeito, o git criar um branch `main` para o projeto. O nome `main` é uma convenção, e a maioria dos projetos tem este branch. A restante organização é responsabilidade da gestão do projeto.

## Metodologia

Idealmente existirão dois branches fixos num projeto: `main`[^master_main] e `develop`.

[^master_main]: Tradicionalmente, o nome do *branch*  principal é `master`, mas recentemente tem-se considerado que esse termo tem conotações negativas. A proposta mais comum é utiliar `main`.

O trabalho deve ser desenvolvido num terceiro branch, que isola a funcionalidade que está a ser acrescentada. Após terminado e testado o trabalho, este terceiro branch deve ser incluído em `develop`. Todas as funcionalidades a implementar devem convergir para `develop`. Só quando todas estiverem implementadas é que `develop` converge para `main`. Na prática, `main` representa sempre uma versão funcional do projeto, e `develop` uma versão em desenvolvimento.

A tabela seguinte ilustra este modelo de organização.

| `main`                              | `develop`                            | `funcA`                    | `funcB`                    |
| ----------------------------------- | ------------------------------------ | -------------------------- | -------------------------- |
| `git init .`                        |                                      |                            |                            |
| `git checkout -b develop`           |                                      |                            |                            |
|                                     | `git checkout -b funcA`              |                            |                            |
|                                     |                                      | **Desenvolver e testar**   |                            |
|                                     |                                      | `git commit -am "A.1`"     |                            |
|                                     |                                      | `...`                      |                            |
|                                     |                                      | `git commit -am "A.N`"     |                            |
|                                     |                                      | `git commit -am "Done A."` |                            |
|                                     |                                      | `git checkout develop`     |                            |
|                                     | `git merge funcA`                    |                            |                            |
|                                     | `git checkout -b funcB`              |                            |                            |
|                                     |                                      |                            | **Desenvolver e testar**   |
|                                     |                                      |                            | `git commit -am "B.1"`     |
|                                     |                                      |                            | `...`                      |
|                                     |                                      |                            | `git commit -am "B.N"`     |
|                                     |                                      |                            | `git commit -am "Done B."` |
|                                     |                                      |                            | `git checkout develop`     |
|                                     | `git merge funcA`                    |                            |                            |
|                                     | **Sem mais tarefas para a versão 1** |                            |                            |
|                                     | **Testar novamente**                 |                            |                            |
|                                     | **Algum trabalho final**             |                            |                            |
|                                     | `git commit -am "V 1.0"`             |                            |                            |
|                                     | `git checkout main`                  |                            |                            |
| `git merge develop`                 |                                      |                            |                            |
| **Publicar o projeto**              |                                      |                            |                            |
| `git checkout develop`              |                                      |                            |                            |
| **Continuar para a próxima versão** |                                      |                            |                            |

Quando vários colaboradores remotos contribuem para o projeto, torna-se necessário incluir um mecanismo de sincronização[^Colab].

[^Colab]: Ver secção sobre [Estratégias de colaboração](#estratégias-de-colaboração).

## Resolução de conflitos

A operação `merge` tem potencial para provocar conflitos no projeto. Um conflito representa um ponto num ficheiro de onde o git não consegue determinar que versão dos ficheiros é correta. O git faz o melhor possível para resolver conflitos, mas pode ser necessário editar os ficheiros onde a resolução automática não é aplicável.

## Repositórios remotos

Os repositórios remotos são geralmente representados localmente com o nome `origin`. Cada colaborador terá de configurar o repositório remoto do projeto. Na prática pode até ser mais que um, dado que não existe limite para o número de repositórios remotos que podem ser mantidos, e cada um terá um nome diferente. O método mais comum é manter um, e esse ter o nome `origin`.

## Estratégias de colaboração

Existem duas práticas comuns para suporte à colaboração com repositórios git: um projeto remoto partilhado por vários colaboradores; vários projetos remotos, periodicamente sincronizados.

Se existir apenas um repositório remoto, cada colaborador regista o repositório como origin, e faz `checkout push/pull` à medida em que projeto evolui. É uma abordagem mais simples, visto que existe apenas um repositório remoto para configurar, partilhado por todos. Tem a desvantagem de não existir critério quanto a alterações do repositório remoto, o que torna provável ocorrerem conflitos entre código alterado simultaneamente entre colaboradores.

O segundo método determina que cada colaborador tenha o seu repositório remoto. A ideia é existir um repositório remoto inicial do gestor do projeto que todos os colaboradores copiam para si. Esta operação de cópia tem o nome técnico de *fork*[^GitHub]. Após o *fork*, os repositórios ficam iguais, cabendo aos colaboradores organizar a hierarquia, se existir. Note-se que cada repositório git é autocontido (i.e., tem toda a informação sobre o projeto), mesmo estando relacionado com outros de alguma forma. É uma boa prática sinalizar o repositório remoto do gestor como upstream do repositório local.

[^GitHub]: Ver secção sobre [GitHub](#github).

A segunda alternativa, com vários repositórios remotos é utilizada com maior frequência, porque permite evoluir vários aspetos do projeto sem risco de contaminação da área de trabalho de cada colaborador.

A tabela seguinte apresenta um exemplo de colaboração entre três colaboradores. O colaborador A é o gestor do projeto, e o repositório remoto dele (repoA) representa o ponto principal de sincronização.

| Colaborador A                     | Colaborador B                   | Colaborador C                   |
| --------------------------------- | ------------------------------- | ------------------------------- |
| `git init .`                      |                                 |                                 |
| `git checkout -b develop`         |                                 |                                 |
| `git add .`                       |                                 |                                 |
| `git commit -m "Initial commit."` |                                 |                                 |
| `git remote add origin repoA`     |                                 |                                 |
| `git push origin develop`         |                                 |                                 |
|                                   | **Fork no GitHub**              | **Fork no GitHub**              |
|                                   | `git clone repoB`               | `git clone repoC`               |
|                                   | `git remote add upstream repoA` | `git remote add upstream repoA` |
|                                   | `git checkout develop`          | `git checkout develop`          |
|                                   | `git checkout -b funcA`         |                                 |
|                                   | `...`                           |                                 |
|                                   | `git commit -am "FuncA done."`  |                                 |
|                                   | `git push origin funcA`         |                                 |
|                                   | **Pull Request no GitHub**      |                                 |
| **Revisão do Pull Request de B**  |                                 |                                 |
| **Merge do Pull Request**         |                                 |                                 |
|                                   | `git checkout develop`          | `git fetch upstream`            |
|                                   | `git fetch upstream`            | `git merge upstream/develop`    |
|                                   | `git merge upstream/develop`    |                                 |

Os colaboradores B e C contribuem com trabalho, e vão atualizando a sua versão do projeto com base no estado do repositório do gestor. Localmente, cada colaborador organiza o seu trabalho de acordo com a metodologia apresentada na Secção 4.1. O branch criado para a funcionalidade implementada deve ser enviado para o repositório remoto (e.g., GitHub), e o pedido de inclusão (Pull Request, no GitHub), deve ser feito para esse branch. É responsabilidade do gestor do projeto fazer merge entre o branch com a funcionalidade e o develop.

## GitHub

O GitHub é um serviço que permite hospedar remotamente repositórios git. Localmente, o repositório remoto fica representado como `origin`. Dentro de `origin` existirão vários branches, tantos quantos forem sendo enviados (`push`) para o GitHub.

Para colaborar com um projeto é necessário criar uma cópia para a conta pessoal. Esta operação tem o nome de *fork*, e cria toda a estrutura necessária para editar uma versão pessoal do projeto.

O GitHub mantém uma relação entre o repositório original e a nova cópia, de forma a permitir a colaboração. Localmente, essa relação é suportada com a sinalização do repositório original como upstream (`git remote add upstream url_original`).

Periodicamente, o colaborador deve atualizar a sua cópia com as novidades de `upstream`, de forma a minimizar a probabilidade de ter de resolver conflitos. A Figura 6 ilustra esse procedimento com comportamento do Colaborador C.

Existem ferramentas que permitem aumentar a produtividade com o GitHub. Além do GitHub Desktop (<https://desktop.github.com/>), que simplifica a interação mais comum através de uma aplicação gráfica, o Hub (<https://hub.github.com/>) e a GitHub CLI (<https://cli.github.com/>) permitem resolver grande parte da interação diretamente da linha de comandos.
