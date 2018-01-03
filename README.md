# Guidelines para nossos projetos &middot; [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

> Iniciar um novo projeto, é um momento lindo e maravilhoso, manter o projeto e garantir que seja mantido toda a qualidade, estrutura e afins é muito phoda.
Aqui segue a lista de luzes para vossos caminhos, escrito e utilizado (ou tetando...) aqui na [Certsys](http://certsys.com.br).
Se você deseja compartilhar melhores práticas suas, ou acha que alguma dessas ideias de maconheiro deva ser removida, guarde pra você esse desejo! Brinks :smiley:, [sinta-se livre para mandar um pull request lindão](http://makeapullrequest.com).

- [Git](#git)
    - [Algumas regras do Git](#some-git-rules)
    - [Git workflow](#git-workflow)
    - [Escrevendo boas mensagens de commit](#writing-good-commit-messages)
- [Documentação](#documentation)
- [Ambientes](#environments)
    - [Ambiente de dev consistente](#consistent-dev-environments)
    - [Dependencias consistentes](#consistent-dependencies)
- [Dependencias](#dependencies)
- [Teste](#testing)
- [Estrutura e Nomenclatura](#structure-and-naming)
- [Code style](#code-style)
    - [Melhores práticas de Code Styling](#code-style-check)
    - [Reforçando padrão de Code Styling](#enforcing-code-style-standards)
- [Logging](#logging)
- [API](#api)
    - [Design da API](#api-design)
    - [Segurança da API](#api-security)
    - [Documentação da API](#api-documentation)
- [Licença](#licensing)

<a name="git"></a>
## 1. Git
![Git](./images/branching.png)
<a name="some-git-rules"></a>

### 1.1 Some Git rules
Existem algumas regras para utilizar o git, que sempre devemos manter em mente:
* Trabalhe em uma feature branch.
    
    _Por que?:_
    >Porque assim, todo o trabalho é feito de forma isolada em um branch dedicado, em vez do branch principal. Ele permite que você envie vários pull requests sem confusão. Você pode iterar sem poluir a master com código potencialmente instável e inacabado. [leia mais...](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)
* Crie a branch a partir de `develop`
    
    _Por que?:_
    >Desta forma, você pode certificar-se de que o código na master quase sempre será construído sem problemas e pode ser usado para efetuar releases de forma automatizada (isso pode ser um exagero para alguns projetos).

* Nunca faça o push para `develop` ou `master`. Faça um Pull Request.
    
    _Por que?:_
    > Ele notifica os membros da equipe de que eles completaram um recurso. Também permite uma fácil revisão por pares do código e dedica fórum para discutir o recurso proposto.

* Mantenha atualizado localmente a branch de `develop` e faça rebase interativo antes de enviar a feature branch e fazer um Pull Request.

    _Por que?:_
    > O Rebase vai fazer o merge na branch local e aplicar os commits que você fez localmente no topo do histórico sem criar um commit de merge (assumindo que não houve conflitos). Resultando em um bom e limpo histórico [leia mais ...](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

* Resolva conflitos potenciais enquanto acontece o rebase e antes de fazer o Pull Request.
* Exclua a branch localmente e no remoto.
    
    _Por que?:_
    > Para não estragar sua lista de branches com branches mortas. Feature branches só devem existir enquanto está havendo desenvolvimento.

* Antes de fazer um Pull Request, Tenha certeza que a sua feature branch builda com sucesso e passa todos os testes (incluindo o code style lint).
    
    _Por que?:_
    > Você está prestes a adicionar seu código a uma branch estável. Se os testes na sua feature branch falharem, existe uma grande chance que a sua branch de destinação falhe também. De forma adicional, você precisa aplicar a verificação do code style do projeto antes de fazer um Pull Request. Adiciona legibilidade ao código e reduz a chance de commits para arrumar e formatar o código que foi enviado com formatação errada, commitando somente funcionalidades com valor.

* Use [esse](./.gitignore) `.gitignore`.
    
    _Por que?:_
    > Já existe uma lista de arquivos que não devem ser enviados com o código para o repo remoto. Além disso, exclui a configuração de pastas e arquivos para editores mais usados, bem como pastas de dependência mais comuns.

* Proteja a branch de `develop` e `master`.
  
    _Por que?:_
    > Isso protege suas branches com "código pronto" de receber mudanças inesperadas e irreversíveis. leia mais... [Github](https://help.github.com/articles/about-protected-branches/)
    
<a name="git-workflow"></a>
### 1.2 Git workflow
Por causa da maioria dos pontos levantados anteriormente, nós usamos o [Feature-branch-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow) com [Interactive Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) e alguns elementos do [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow) (nomenclatura e a existencia da branch de develop). Os principais passos são os seguintes:

* Para um novo projeto, inicialize um repositório git no diretório do projeto. __Para subsequentes features/changes, este passo deve ser ignorado__.
   ```sh
   cd <project directory>
   git init
   ```

* Crie uma nova feature/bug-fix branch.
    ```sh
    git checkout -b <branchname>
    ```
* Efetue as mudanças.
    ```sh
    git add
    git commit -a
    ```
    _Por que?:_
    > `git commit -a` vai começar um editor que permite separar o assunto do corpo. Leia mais sobre na *seção 1.3*.

* Sincronize com o remoto para obter as alterações que você perdeu.
    ```sh
    git checkout develop
    git pull
    ```
    
    _Por que?:_
    > Isso lhe dará a chance de lidar com conflitos em sua maquina enquanto faz o rebase, ao invés de pegar conflitos ao fazer o Pull Request.
    
* Atualize a sua feature branch com as últimas alterações de develop utilizando o rebase interativo.
    ```sh
    git checkout <branchname>
    git rebase -i --autosquash develop
    ```
    
    _Por que?:_
    > Você pode usar --autosquash para transformar todos seus commits, em um único commit. Ninguém quer muitos commits para uma única feature branch. [read more...](https://robots.thoughtbot.com/autosquashing-git-commits)
    
* Se você não tiver conflito, ignore este passo. Se você tem conflitos no código, [resolva eles](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/)  e continue o rebase.
    ```sh
    git add <file1> <file2> ...
    git rebase --continue
    ```
* Faça o Push da branch. Rebase vai reescrever o histórico do git, dessa forma, você vai precisar utilizar o `-f` para forçar as mudanças no repo remoto. Se alguém estiver trabalhando na branch, utilize o menos destrutivo `--force-with-lease`.
    ```sh
    git push -f
    ```
    
    _Por que?:_
    > Quando você faz um rebase, você está mudando o histórico da sua feature branch. Como resultado, o Git vai rejeitar o  `git push` padrão. Dessa forma, você vai precisar utilizar o -f ou o --force flag. [leia mais...](https://developer.atlassian.com/blog/2015/04/force-with-lease/)
    
    
* Faça um Pull Request.
* Pull request serão aceitados, mergeados e fechado pelo reviewer.
* Remova a sua feature branch.

  ```sh
  git branch -d <branchname>
  ```
  para remover todas as branches que não estão mais no remoto
  ```sh
  git fetch -p && for branch in `git branch -vv | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```

<a name="writing-good-commit-messages"></a>
### 1.3 Escrevendo boas mensagens de commit 

Ter uma boa orientação para a criação de mensagem de commits e aderir a ele faz com que trabalhar com o Git e colaborar com outros seja muito mais fácil. Aqui estão algumas regras ([source](https://chris.beams.io/posts/git-commit/#seven-rules)):

 * Separe o assunto do corpo com uma nova linha entre os dois.

    _Por que?:_
    > Git é inteligente o suficiente para distinguir a primeira linha de sua mensagem de commit como seu resumo. Na verdade, se você tentar git shortlog, em vez de git log, você verá uma longa lista de mensagens de commit, consistindo no id do commit e somente no resumo.

 * Limite a linha de assunto para 50 caracteres e envolva o corpo a 72 caracteres.

    _Por que?_
    > Os commits devem ser tão pequenos e focados o quanto possível, não é o lugar para escrever muito. [leia mais...](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)

 * Capitalize a linha de assunto.
 * Use o [modo imperativo](https://pt.wikipedia.org/wiki/Modo_imperativo) na linha de assunto.

    _Por que?:_
    > Em vez de escrever mensagens que dizem o que um commiter fez, é melhor considerar essas mensagens como as instruções para o que será feito após o commit ser aplicado no repositório. [leia mais...](https://news.ycombinator.com/item?id=2079612)


 * Use o corpo para explicar **o que** e **porque** ao invés de **como**.
 
 * Siga o modelo do [Karma Commit Message](http://karma-runner.github.io/2.0/dev/git-commit-msg.html)
 
 <a name="documentation"></a>
 ## 2. Documentação
 
 ![Documentação](./images/documentation.png)
 
 * Use este [template](./README.sample.md) para o `README.md`, Sinta-se livre para adicionar mais coisas :wink:.
 * Para projetos com mais de um repositório, forneça links para eles em seus respectivos arquivos `README.md`.
 * Mantenha `README.md` atualizado à medida que um projeto evolui.
 * Comente seu código. Tente tornar o mais claro possível o que você pretende com cada seção principal.
 * Se houver uma discussão aberta sobre github ou stackoverflow sobre o código ou abordagem que você está usando, inclua o link em seu comentário. 
 * Não use comentários como uma desculpa para um código ruim. Siga o clean code.
 * Não use clean code como uma desculpa para não comentar.
 * Mantenha os comentários relevantes à medida que seu código evolui.
 
 <a name="environments"></a>
 ## 3. Environments
 
 ![Environments](./images/laptop.png)
 
 * Defina os ambientes de `development`, `test` e `production` se necessário.
 
     _Por que?:_
     > Diferente dados, tokens, APIs, portas e etc... podem ser diferentes em cada ambiente. Você pode querer um ambiente de "desenvolvimento" isolado que chama um mock de API que retorna dados previsíveis, tornando os testes automatizados e manuais muito mais fáceis. Ou você pode querer ativar o Google Analytics somente em `production` e assim por diante. [leia mais...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)
 
 
 * Carregue suas configurações específicas de deployment de variáveis de ambiente e nunca as adicione à base de código como constantes, [olhe este exemplo](./config.sample.js).
 
     _Por que?:_
     > Você tem tokens, senhas e outras informações valiosas lá. Sua configuração deve ser separada corretamente do aplicativo, como se a base do código pudesse ser tornada pública a qualquer momento.
 
     _Como?:_
     >
     `.env` arquivos para armazenar suas variáveis e adicioná-las a `.gitignore` para serem excluídas. É possível commitar um arquivo de `.env.example` que serve como um guia para desenvolvedores. Para a produção, você ainda deve definir suas variáveis de ambiente da maneira padrão.
     [leia mais](https://medium.com/@rafaelvidaurre/managing-environment-variables-in-node-js-2cb45a55195f)
 
 * É recomendável validar variáveis de ambiente antes de inicializar o app.  [Veja esse exemplo](./configWithTest.sample.js) usando `joi` para validar os valores.
     
     _Por que?:_
     > Pode salvar horas de outras pessoas, somente para descobrir que o erro, era uma simples falha por algum variável de ambiente que estava faltando.

<a name="consistent-dev-environments"></a>
### 3.1 Ambientes de dev consistentes:
* Set a versão do node em `engines` no `package.json`.
    
    _Por que?:_
    > Isso permite que outros conheçam a versão do node em que o projeto funciona. [leia mais...](https://docs.npmjs.com/files/package.json#engines)

* Adicionalmente, use o `nvm` e crie um arquivo `.nvmrc`  na raiz do seu projeto. Não esqueça de mencionar isso na documentação.

    _Por que?:_
    > Qualquer um que use `nvm` pode simplesmente usar` nvm use` para mudar para a versão correta do node. [leia mais...](https://github.com/creationix/nvm)

* É uma boa idéia configurar um script `preinstall` que verifica as versões do node e npm.

    _Por que?:_
    > Algumas dependências podem falhar quando instaladas por versões mais recentes de npm.
    
* Use sempre o Docker.

    _Por que?:_
    > Pode dar-lhe um ambiente consistente em todo o fluxo de trabalho. Sem muita necessidade de mexer com dependências ou configurações. [leia mais...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

* Use módulos locais em vez de usar módulos instalados globalmente.

    _Por que?:_
    > Permite que você compartilhe suas ferramentas com seu colega em vez de esperar que elas as tenham globalmente em seus sistemas.

<a name="consistent-dependencies"></a>
### 3.2 Dependencias consistentes:

* Certifique-se de que os membros da sua equipe tenham exatamente as mesmas dependências que você.

    _Por que?:_
    > Porque você deseja que o código se comporte como esperado e idêntico em qualquer máquina de desenvolvimento [leia mais...](https://medium.com/@kentcdodds/why-semver-ranges-are-literally-the-worst-817cdcb09277)

    _Como?:_
    > Use o `package-lock.json` com o `npm@5` ou maior

    _Eu não tenho npm@5:_
    > Alternativamente, você pode usar `Yarn` e certifique-se de mencioná-lo no` README.md`. Seu arquivo de lock e `package.json` devem ter as mesmas versões após cada atualização de dependência. [leia mais...](https://yarnpkg.com/en/)

    _Eu não gosto do `Yarn` :triumph::_
    > Que pena :fu: :ok_hand:. Para versões anteriores de `npm`, use` -save --save-exact` ao instalar uma nova dependência e crie o `npm-shrinkwrap.json` antes de publicar. [leia mais...](https://docs.npmjs.com/files/package-locks)

<a name="dependencies"></a>
## 4. Dependencias

![Github](./images/modules.png)

* Acompanhe os pacotes disponíveis no momento: e.g., `npm ls --depth=0`. [leia mais...](https://docs.npmjs.com/cli/ls)
* Veja se algum dos seus pacotes se tornou inutilizado ou irrelevante: `depcheck`. [leia mais...](https://www.npmjs.com/package/depcheck)
    
    _Por que?:_
    > Você pode incluir uma biblioteca não utilizada em seu código e aumentar o tamanho do pacote de produção. Encontre essas dependências não utilizadas e se livre delas.

* Antes de usar uma dependência, verifique suas estatísticas de download para ver se é bastante usado pela comunidade: `npm-stat`. [leia mais...](https://npm-stat.com/)
    
    _Por que?:_
    > Mais uso significa principalmente mais contribuidores, o que geralmente significa melhor manutenção, e todos esses resultados resultaram em descobertas rápidas de bugs e correções rapidamente desenvolvidas.

* Antes de usar uma dependência, verifique se ele possui uma boa e madura versão de liberação com um grande número de mantenedores.: e.g., `npm view async`. [leia mais...](https://docs.npmjs.com/cli/view)

    _Por que?:_
    > Ter um monte de contribuidores não será tão eficaz se os mantenedores não efetuarem correções ou patches de correção com rapidez suficiente.

* Se uma dependência menos conhecida for necessária, discuta com a equipe antes de usá-la.
* Certifique-se sempre de que seu app funciona com a versão mais recente de suas dependências sem quebrar: `npm outdated`. [leia mais...](https://docs.npmjs.com/cli/outdated)

    _Por que?:_
    > As atualizações de dependência às vezes podem quebrar o seu código. Verifique sempre as notas da nova versão quando as atualizações aparecerem. Atualize suas dependências um a um, o que facilita a solução de problemas se algo der errado. Use uma ferramenta legal, como o [npm-check-updates](https://github.com/tjunnone/npm-check-updates).

* Verifique se o pacote tem vulnerabilidades conhecidas de segurança com o, e.g., [Snyk](https://snyk.io/test?utm_source=risingstack_blog).


<a name="testing"></a>
## 5. Testando
![Testing](./images/testing.png)
* Tenha um ambiente de `test` se necessário.

    _Por que?:_
    > Embora às vezes o teste de end-to-end no ambiente de `production` possa parecer suficiente, existem algumas exceções: um exemplo é que você não pode querer habilitar informações analíticas em um modo de "produção" e poluir o painel de alguém com dados de teste. O outro exemplo é que sua API pode ter limites de taxa no ambiente de `production` 'e bloqueia suas chamadas de teste após uma certa quantidade de solicitações.

* Coloque seus arquivos de teste dentro dos módulos que estão sendo testados, usando a convenção de nomenclatura como `* .test.js` ou` * .spec.js`, como `moduleName.spec.js`.

    _Por que?:_
    > Você não deseja peregrinar por várias pastas no projeto para achar os teste unitários. [leia mais...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)
    

* Coloque seus arquivos de teste adicionais em uma pasta de teste separada para evitar confusão.

    _Por que?:_
    > Alguns arquivos de teste não se relacionam particularmente com nenhuma implementação específica. Você deve colocar esses arquivos em uma pasta que provavelmente será encontrada por outros desenvolvedores: `__test__`. Este nome: `__test__` também é padrão agora e é reconhecido pela maioria dos frameworks de teste no JavaScript.

* Escreva o código testável, evite os efeitos colaterais, extraie os efeitos colaterais, escreva funções puras

    _Por que?:_
    > Você quer testar uma lógica de negócio como unidades separadas. Você precisa "minimizar o impacto da aleatoriedade e dos processos não deterministas na confiabilidade do seu código". [leia mais...](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)
    
    > Uma função pura é uma função que sempre retorna a mesma saída para a mesma entrada. Por outro lado, uma função impura é aquela que pode ter efeitos colaterais ou depende de condições externas para produzir um valor. Isso torna menos previsível. [leia mais...](https://hackernoon.com/structure-your-javascript-code-for-testability-9bc93d9c72dc)

* Execute testes localmente antes de fazer qualquer pull requests para `develop`.

    _Por que?:_
    > Você não quer ser aquele que causou bugs em produção. Execute seus testes após o `rebase` e antes de fazer o push da sua feature branch para um repositório remoto.

* Documentar seus testes, incluindo instruções na seção relevante do seu arquivo `README.md`.

    _Por que?:_
    > É uma nota útil que você deixa para trás para outros desenvolvedores ou especialistas da DevOps ou QA ou qualquer pessoa que tenha sorte o suficiente para trabalhar em seu código.

<a name="structure-and-naming"></a>
## 6. Estrutura e Nomenclatura
![Structure and Naming](./images/folder-tree.png)
* Organize seus arquivos em torno dos recursos do produto / páginas / componentes, e não papéis. Além disso, coloque seus arquivos de teste ao lado de sua implementação.


    **Ruim**

    ```
    .
    ├── controllers
    |   ├── product.js
    |   └── user.js
    ├── models
    |   ├── product.js
    |   └── user.js
    ```

    **Bom**

    ```
    .
    ├── product
    |   ├── index.js
    |   ├── product.js
    |   └── product.test.js
    ├── user
    |   ├── index.js
    |   ├── user.js
    |   └── user.test.js
    ```

    _Por que?:_
    > Em vez de uma longa lista de arquivos, você criará pequenos módulos que encapsulam um componente, incluindo seu teste e assim por diante. É muito mais fácil navegar e as coisas podem ser encontradas de relance.

* Coloque seus arquivos de teste adicionais em uma pasta de teste separada para evitar confusão.

    _Por que?:_
    > É um economizador de tempo para outros desenvolvedores ou especialistas da DevOps em sua equipe.

* Use uma pasta `./config` e não faça arquivos de configuração diferentes para diferentes ambientes.

    _Por que?:_
    >Quando você cria um arquivo de configuração para diferentes propósitos (banco de dados, API e assim por diante); colocá-los em uma pasta com um nome muito reconhecível, como "config", faz sentido. Lembre-se de não fazer arquivos de configuração diferentes para diferentes ambientes. Ele não escala de forma limpa, à medida que mais implementações do aplicativo são criadas, novos nomes de ambiente são necessários. Os valores a serem usados em arquivos de configuração devem ser fornecidos por variáveis de ambiente. [leia mais...](https://medium.com/@fedorHK/no-config-b3f1171eecd5)
    

* Coloque seus scripts em uma pasta `./scripts`. Isso inclui scripts `bash` e` node`.

    _Por que?:_
    >É muito provável que você possa terminar com mais de um script, criação de produção, construção de desenvolvimento, alimentadores de banco de dados, sincronização de banco de dados e assim por diante.
    

* Coloque o resultado da compilação em uma pasta `./build`. Adicione `build/` a `.gitignore`.

    _Por que?:_
    >Nomeie do jeito que você achar melhor, `dist` também é bom. 

* Use `PascalCase '' camelCase` para nomes de arquivos e nomes de diretórios. Use `PascalCase` apenas para componentes.

* `CheckBox/index.js` deve ter o componente` CheckBox`, como poderia `CheckBox.js`, mas **não**` CheckBox/CheckBox.js` ou `checkbox/CheckBox.js` que são redundantes.

* Idealmente, o nome do diretório deve corresponder ao nome da exportação padrão de `index.js`.

    _Por que?:_
    > Então você pode esperar o import do componente ou módulo você simplesmente importando sua pasta pai.

<a name="code-style"></a>
## 7. Code style

![Code style](./images/code-style.png)

<a name="code-style-check"></a>
### 7.1 Alguns code style guidelines

* Use o stage-2 ou maior para novos projetos. 

    _Por que?:_
    > Usamos transpilers para utilizar as vantagens da nova sintaxe.

* Inclua verificação de estilo de código no processo de build.

* Use [ESLint - Pluggable JavaScript linter](http://eslint.org/) para forçar code style.

    _Por que?:_
    > Nós simplesmente preferimos `eslint`. Possui mais regras suportadas, a capacidade de configurar as regras e a capacidade de adicionar regras personalizadas.

* Nós usamos o [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript) para JavaScript, [leia mais](https://www.gitbook.com/book/duk/airbnb-javascript-guidelines/details).

* Nós usamos o [Flow type style check rules for ESLint](https://github.com/gajus/eslint-plugin-flowtype) quando usando [FlowType](https://flow.org/).

    _Por que?:_
    > Flow apresenta algumas sintaxes que também precisam seguir determinado estilo de código e ser verificadas.

* Use `.eslintignore` para excluir arquivos ou pastas da verificação de estilo de código.

    _Por que?:_
    > Você não precisa poluir seu código com comentários `eslint-disable` sempre que precisar excluir alguns arquivos da verificação de estilo.

* Dependendo do tamanho da tarefa use `// TODO:` comentários ou abra um ticket.

    _Por que?:_
    > Então, você pode lembrar a si mesmo e aos outros sobre uma pequena tarefa (como refatorar uma função ou atualizar um comentário). Para tarefas maiores, use `// TODO (# 3456)` que é aplicada por uma regra de lint e o número é um ticket aberto.

* Faça com que seus nomes sejão facilmente pesquisados com distinções significativas, evitem nomes encurtados. Para funções, use nomes longos e descritivos. Um nome de função deve ser um verbo ou uma frase verbal, e precisa comunicar sua intenção.

    _Por que?:_
    > Isso torna mais natural ler o código-fonte.

* Organize suas funções em um arquivo de acordo com a regra de step-down. As funções de higher level devem estar nos níveis superiores e as low levels em niveis inferiores abaixo.

    _Por que?:_
    > Isso torna mais natural ler o código-fonte.

<a name="enforcing-code-style-standards"></a>
### 7.2 Forçando padrão de code style

* Use um arquivo de [.editorconfig](http://editorconfig.org/) file que ajuda os desenvolvedores a definir e manter estilos de codificação consistentes entre diferentes editores e IDEs no projeto.

    _Por que?:_
    > O projeto EditorConfig consiste em um formato de arquivo para definir estilos de codificação e uma coleção de plugins de editor de texto que permitem aos editores ler o formato do arquivo e aderir aos estilos definidos. Os arquivos EditorConfig são facilmente legíveis e eles funcionam bem com sistemas de controle de versão.

* Seu editor o avise sobre erros de estilo de código. Use [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier) e [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) com sua configuração de ESLint. [leia mais...](https://github.com/prettier/eslint-config-prettier#installation)

* Considere utilizar Git hooks.

    _Por que?:_
    > Git hooks aumenta a produtividade de um desenvolvedor. Faça mudanças, commit e push para ambientes de homologação ou produção sem o medo de quebrar o build. [leia mais...](http://githooks.com/)

<a name="logging"></a>
## 8. Logging

![Logging](./images/logging.png)

* Evite logs de console no client side em produção

    _Por que?:_
    > Mesmo que seu processo de compilação possa (deve) se livrar deles, certifique-se de que seu verificador de estilo de código avisa você sobre os logs de console restantes.

* Produza log de produção legível. Idealmente use bibliotecas de log para serem usadas no modo de produção (como [winston](https://github.com/winstonjs/winston) ou
[node-bunyan](https://github.com/trentm/node-bunyan)).

<a name="api"></a>
## 9. API
<a name="api-design"></a>

![API](./images/api.png)

### 9.1 API design

_Por que?:_
> Porque tentamos impor o desenvolvimento de interfaces RESTful de forma segura, quais membros e clientes da equipe podem consumir de forma simples e consistente.

_Por que?:_
> A falta de consistência e simplicidade pode aumentar massivamente os custos de integração e manutenção. É por isso que o "design da API" está incluído neste documento.


* Na maior parte, seguimos o design orientado a recursos. Possui três fatores principais: recursos, coleta e URLs.
    * Um recurso tem dados, é aninhado e existem métodos que operam contra ele.
    * AUm grupo de recursos é chamado de coleção.
    * A URL identifica a localização online do recurso ou coleção.
    
    _Por que?:_
    > Este é um design muito conhecido para os desenvolvedores (seus principais consumidores da API). Além da legibilidade e facilidade de uso, isso nos permite escrever bibliotecas genéricas e conectores sem saber o que é a API.

* use kebab-case para URLs.
* use camelCase para parâmetros na query string ou resource fields.
* use kebab-case no plural para nomes de recursos nas URLs.

* Sempre use um substantivo plural para nomear uma url apontando para uma coleção: `/users`.

    _Por que?:_
    > Basicamente, ele lê melhor e mantém as URLs consistentes. [leia mais...](https://apigee.com/about/blog/technology/restful-api-design-plural-nouns-and-concrete-names)

* No código-fonte converta plurais para variáveis e propriedades com um sufixo de lista.

    _Por que?_:
    > Plural é bom na URL, mas no código-fonte, é muito sutil e propenso a erros.

* Sempre use um conceito singular que comece com uma coleção e termine com um identificador:

    ```
    /students/245743
    /airports/kjfk
    ```
* Evite URLs como esta: 
    ```
    GET /blogs/:blogId/posts/:postId/summary
    ```

    _Por que?:_
    > Isso não está apontando para um recurso, mas para uma propriedade. Você pode passar a propriedade como um parâmetro para cortar sua resposta.

* Mantenha os verbos fora das URLs do seu recurso.

    _Por que?:_
    > Porque se você usar um verbo para cada operação de recurso, você terá uma enorme lista de URLs e nenhum padrão consistente que ficará dificil de aprender. Além disso, usamos verbos para outra coisa.

* Use verbos para não-recursos. Nesse caso, sua API não retorna nenhum recurso. Em vez disso, você executa uma operação e retorna o resultado. Essas **não são** CRUD (criar, recuperar, atualizar e excluir) operações:

    ```
    /translate?text=Hallo
    ```

    _Por que?:_
    > Porque, para o CRUD, usamos métodos HTTP em URLs de `resource` ou `collection`. Os verbos de que estávamos falando são, na verdade, `Controllers`. Você geralmente não desenvolve muitos desses. [leia mais...](https://byrondover.github.io/post/restful-api-guidelines/#controller)

* O corpo do pedido ou o tipo de resposta são JSON, então siga os nomes de propriedade `camelCase` para` JSON` para manter a consistência.
    
    _Por que?:_
    > Esta é uma diretriz de projeto para JavaScript, onde a linguagem de programação para gerar e analisar JSON é assumida como JavaScript.

* Mesmo que um recurso seja um conceito singular que seja semelhante a uma instância de objeto ou registro de banco de dados, você não deve usar o `table_name` para um nome de recurso e `column_name` para propriedade do recurso.

    _Por que?:_
    > Porque sua intenção é expor recursos, não os detalhes do esquema do banco de dados.

* Novamente, use somente substantivos em sua URL ao nomear seus recursos e não tente explicar suas funcionalidades.

    _Por que?:_
    > Use somente substantivos nos URLs do recurso, evite endpoints como `/ addNewUser` ou` / updateUser`.

* Explique as funcionalidades CRUD usando métodos HTTP:

    _Como:_
    > `GET`: Para recuperar uma representação de um recurso.
    
    > `POST`: Para criar novos recursos e sub-recursos.
   
    > `PUT`: Para atualizar os recursos existentes.
    
    > `PATCH`: Para atualizar os recursos existentes. Apenas atualiza os campos que foram fornecidos, deixando os outros sozinhos.
    
    > `DELETE`:	Para excluir recursos existentes.


* Para recursos aninhados, use a relação entre eles na URL. Por exemplo, usando `id` para relacionar um empregado com uma empresa.

    _Por que?:_
    > Esta é uma maneira natural de tornar os recursos exploráveis.

    _Como:_

    > `GET      /schools/2/students	` , deve obter a lista de todos os alunos da escola 2.

    > `GET      /schools/2/students/31`	, deve obter os detalhes do aluno 31, que pertence à escola 2.

    > `DELETE   /schools/2/students/31`	, deve excluir o estudante 31, que pertence à escola 2.

    > `PUT      /schools/2/students/31`	, deve atualizar a informação do aluno 31.

    > `POST     /schools` , deve criar uma nova escola e devolver os detalhes da nova escola criada.

* Use um número ordinal simples para uma versão com um prefixo `v` (v1, v2). Mova-o todo o caminho para a esquerda no URL para que ele tenha o escopo mais alto:
    ```
    http://api.domain.com/v1/schools/3/students	
    ```

    _Por que?:_
    > Quando suas APIs são públicas para terceiros, atualizar as APIs com alguma mudança pode quebrar os produtos ou serviços existentes usando suas APIs. Usar versões em seu URL pode impedir que isso aconteça. [leia mais...](https://apigee.com/about/blog/technology/restful-api-design-tips-versioning)



* As mensagens de resposta devem ser auto-descritivas. Uma boa resposta de mensagem de erro pode parecer com algo assim:
    ```json
    {
        "code": 1234,
        "message" : "Algo ruím aconteceu",
        "description" : "Mais detalhes"
    }
    ```
    ou para erros de validação:
    ```json
    {
        "code" : 2314,
        "message" : "Falha na Validação",
        "errors" : [
            {
                "code" : 1233,
                "field" : "email",
                "message" : "Email invalido"
            },
            {
                "code" : 1234,
                "field" : "password",
                "message" : "Nenhuma senha fornecida"
            }
          ]
    }
    ```

    _Por que?:_
    > os desenvolvedores dependem de erros bem projetados nos momentos críticos quando estão solucionando problemas e resolvendo problemas depois que os apps que eles criaram usando suas APIs estão nas mãos de seus usuários.


    _Note: Mantenha as mensagens de exceção de segurança tão genéricas quanto possível. Por exemplo, em vez de dizer 'senha incorreta', você pode responder de novo dizendo 'nome de usuário ou senha inválidos' para que não informemos o usuário de forma inconsciente que esse nome de usuário estava realmente correto e apenas a senha estava incorreta._

* Use apenas esses 8 códigos de status para enviar com você resposta para descrever se **tudo funcionou**,
  O **aplicativo cliente fez algo errado** ou A API **fez algo errado**.
    
    _Que são:_
    > `200 OK` A resposta representa o sucesso dos pedidos `GET`, `PUT` ou `POST`.

    > `201 Created` para quando uma nova instância for criada. Criando uma nova instância, usando o método `POST` retorna o código de status `201`.

    > `304 Not Modified` resposta é minimizar a transferência de informações quando o destinatário já possui representações em cache.

    > `400 Bad Request` para quando o pedido não foi processado, pois o servidor não conseguiu entender o que o cliente solicita.

    > `401 Unauthorized` para quando o pedido não possui credenciais válidas e deve re-solicitar com as credenciais necessárias.

    > `403 Forbidden` significa que o servidor entendeu o pedido, mas se recusa a autorizá-lo.

    > `404 Not Found` indica que o recurso solicitado não foi encontrado.

    > `500 Internal Server Error` indica que o pedido é válido, mas o servidor não conseguiu cumpri-lo devido a alguma condição inesperada.

    _Por que?:_
    > A maioria dos provedores de API usa um pequeno subconjunto de códigos de status HTTP. Por exemplo, a API do Google GData usa apenas 10 códigos de status, o Netflix usa 9 e Digg, apenas 8. Naturalmente, essas respostas contêm um corpo com informações adicionais. Existem mais de 70 códigos de status HTTP. No entanto, a maioria dos desenvolvedores não tem todos os 70 memorizados. [leia mais...](https://apigee.com/about/blog/technology/restful-api-design-what-about-errors)


* Forneça um número total de recursos na sua resposta.
* Aceite os parâmetros `limit` e `offset`.

* A quantidade de dados que o recurso expõe também deve ser levada em consideração. O consumidor da API nem sempre precisa da representação completa de um recurso. Use um parâmetro de consulta de campos que contenha uma lista separada por vírgulas de campos para incluir:
    ```
    GET /student?fields=id,name,age,class
    ```
* Paginação, filtragem e classificação não precisam ser suportados desde o início para todos os recursos. Documentar os recursos que oferecem filtragem e classificação.

<a name="api-security"></a>
### 9.2 API security
Estas são algumas boas práticas básicas de segurança:

* Não use a autenticação básica, a menos que por uma conexão segura (HTTPS). Os tokens de autenticação não devem ser transmitidos no URL: `GET /users/123?token=asdf....`

    _Por que?:_
    > Como o Token ou o ID do usuário e a senha são passados pela rede como texto encoded (é baseado em base64, mas base64 é uma codificação reversível), o esquema básico de autenticação não é seguro. [leia mais...](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)

* Os tokens devem ser transmitidos usando o cabeçalho da Autorização em todos os pedidos: `Authorization: Bearer xxxxxx, Extra yyyyy`.

* Rejeitar quaisquer solicitações não-TLS para não responder a qualquer solicitação HTTP e evitar qualquer troca de dados insegura. Responda a solicitações HTTP por `403 Forbidden`.

* Considere usar Limitação de Taxa.

    _Por que?:_
    > Para proteger suas APIs de ameaças de bot que chamam sua API de milhares de vezes por hora. Você deve considerar implementar o limite de taxas no início.

* Definir cabeçalhos HTTP adequadamente pode ajudar a bloquear e proteger seu app. [leia mais...](https://github.com/helmetjs/helmet)

* Sua API deve converter os dados recebidos em sua forma canônica ou rejeitá-los. Retorne 400 Bad Request com detalhes sobre quaisquer erros de dados incorretos ou ausentes.

* Todos os dados trocados com a API REST devem ser validados pela API.

* Serialize seu JSON.

    _Por que?:_
    > Uma preocupação importante com JSON, é a execução de código remoto JavaScript no navegador ... ou, se você estiver usando node.js, no servidor. É vital que você use um serializador JSON apropriado para codificar dados fornecidos pelo usuário corretamente para impedir a execução de código malicioso.

* Valide o tipo de conteúdo e use principalmente `application/*json` (Content-Type header).
    
    _Por que?:_
    > Por exemplo, aceitar o tipo de mime `application/x-www-form-urlencoded` permite que o invasor crie um formulário e desencadeie uma solicitação POST simples. O servidor nunca deve assumir o Content-Type. A falta do cabeçalho Content-Type ou de um cabeçalho Content-Type inesperado deve resultar no servidor rejeitando o conteúdo com uma resposta `4XX`.

* Verifique o API Security Checklist Project. [leia mais...](https://github.com/shieldfy/API-Security-Checklist)

<a name="api-documentation"></a>
### 9.3 API documentation
* Preencha a seção `Referência da API 'no [modelo README.md](./README.sample.md) para API.
* Descreva os métodos de autenticação da API com um exemplo de código.
* Explicando a Estrutura de URL (somente caminho, sem URL de raiz), incluindo O tipo de solicitação (Método).

Para cada endpoint, explique:
* Parâmetros de URL, se existirem Parâmetros de URL, especifique-os de acordo com o nome mencionado na seção de URL:

    ```
    Required: id=[integer]
    Optional: photo_id=[alphanumeric]
    ```

* Se o tipo de solicitação for POST, forneça exemplos úteis. As regras de Params de URL aplicam-se aqui também. Separe a seção em Opcional e Obrigatório.

* Resposta de sucesso, qual deve ser o código de status e existem dados de retorno? Isso é útil quando as pessoas precisam saber o que suas devoluções de chamada devem esperar:

    ```
    Code: 200
    Content: { id : 12 }
    ```

* Resposta de erro, a maioria dos endpoints tem várias formas de falhar. Do acesso não autorizado a parâmetros errados etc. Todos esses devem ser listados aqui. Pode parecer repetitivo, mas ajuda a evitar que as suposições sejam feitas. Por exemplo
    ```json
    {
        "code": 403,
        "message" : "Authentication failed",
        "description" : "Invalid username or password"
    }   
    ```


* Use ferramentas de design da API, há muitas ferramentas de código aberto para uma boa documentação, como [API Blueprint](https://apiblueprint.org/) e [Swagger](https://swagger.io/).

<a name="licensing"></a>
## 10. Licenciamento
![Licensing](./images/licensing.png)

Certifique-se de usar recursos que você tenha os direitos de usar. Se você usa bibliotecas, lembre-se de procurar o MIT, o Apache ou o BSD, mas se você os modifica, examine os detalhes da licença. Imagens e vídeos protegidos por direitos autorais podem causar problemas legais.
