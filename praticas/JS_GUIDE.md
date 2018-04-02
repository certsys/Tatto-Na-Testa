# Guidelines para Projetos JS &middot;

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
- [Licença](#licensing)

<a name="documentation"></a>
 ## 1. Documentação
 
 ![Documentação](../images/documentation.png)
 
 * Use este [template](./readme/README.md) para o `README.md`, Sinta-se livre para adicionar mais coisas :wink:.
 * Para projetos com mais de um repositório, forneça links para eles em seus respectivos arquivos `README.md`.
 * Mantenha `README.md` atualizado à medida que um projeto evolui.
 * Comente seu código. Tente tornar o mais claro possível o que você pretende com cada seção principal.
 * Se houver uma discussão aberta sobre github ou stackoverflow sobre o código ou abordagem que você está usando, inclua o link em seu comentário. 
 * Não use comentários como uma desculpa para um código ruim. Siga o clean code.
 * Não use clean code como uma desculpa para não comentar.
 * Mantenha os comentários relevantes à medida que seu código evolui.
 
 <a name="environments"></a>
 ## 2. Environments
 
 ![Environments](../images/laptop.png)
 
 * Defina os ambientes de `development`, `test` e `production` se necessário.
 
     _Por que?:_
     > Diferente dados, tokens, APIs, portas e etc... podem/devem ser diferentes em cada ambiente. Você pode querer um ambiente de "desenvolvimento" isolado que chama um mock de API que retorna dados previsíveis, tornando os testes automatizados e manuais muito mais fáceis. Ou você pode querer ativar o Google Analytics somente em `production` e assim por diante. [leia mais...](https://stackoverflow.com/questions/8332333/node-js-setting-up-environment-specific-configs-to-be-used-with-everyauth)
 
 
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
### 2.1 Ambientes de dev consistentes:
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
    > Para criar um ambiente consistente em todo o fluxo de trabalho. Sem muita necessidade de mexer com dependências ou configurações. [leia mais...](https://hackernoon.com/how-to-dockerize-a-node-js-application-4fbab45a0c19)

* Use módulos locais em vez de usar módulos instalados globalmente.

    _Por que?:_
    > Permite que você compartilhe suas ferramentas com seu colega em vez de esperar que elas as tenham globalmente em seus sistemas.

<a name="consistent-dependencies"></a>
### 2.2 Dependencias consistentes:

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
## 3. Dependencias

![Github](../images/modules.png)

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
## 4. Testando
![Testing](../images/testing.png)
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
## 5. Estrutura e Nomenclatura
![Structure and Naming](../images/folder-tree.png)
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

* Tente ao máximo nomear os arquivos, componentes e diretórios em **INGLÊS**.

    _Por quê?_
    > Isso mantém um padrão entre devs. Caso a gente precise mostrar nossos códigos pra uma pessoa do Sudão do Sul, não vamos ter trabalho de explicar o que `TabelaComPesquisa` significa. Só cuidado com plurais pra não apanhar do Hansen em público.

* `CheckBox/index.js` deve ter o componente` CheckBox`, como poderia `CheckBox.js`, mas **não**` CheckBox/CheckBox.js` ou `checkbox/CheckBox.js` que são redundantes.

* Idealmente, o nome do diretório deve corresponder ao nome da exportação padrão de `index.js`.

    _Por que?:_
    > Então você pode esperar o import do componente ou módulo você simplesmente importando sua pasta pai.

<a name="code-style"></a>
## 6. Code style

![Code style](../images/code-style.png)

<a name="code-style-check"></a>
### 6.1 Alguns code style guidelines

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
### 6.2 Forçando padrão de code style

* Use um arquivo de [.editorconfig](http://editorconfig.org/) file que ajuda os desenvolvedores a definir e manter estilos de codificação consistentes entre diferentes editores e IDEs no projeto.

    _Por que?:_
    > O projeto EditorConfig consiste em um formato de arquivo para definir estilos de codificação e uma coleção de plugins de editor de texto que permitem aos editores ler o formato do arquivo e aderir aos estilos definidos. Os arquivos EditorConfig são facilmente legíveis e eles funcionam bem com sistemas de controle de versão.

* Seu editor o avise sobre erros de estilo de código. Use [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier) e [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier) com sua configuração de ESLint. [leia mais...](https://github.com/prettier/eslint-config-prettier#installation)

* Considere utilizar Git hooks.

    _Por que?:_
    > Git hooks aumentam a produtividade do abiguinho do lado. Faça mudanças, commit e push para ambientes de homologação ou produção sem o medo de quebrar o build. [leia mais...](http://githooks.com/)

<a name="logging"></a>
## 7. Logging

![Logging](../images/logging.png)

* Evite logs de console no client side em produção

    _Por que?:_
    > Mesmo que seu processo de compilação possa (deve) se livrar deles, certifique-se de que seu verificador de estilo de código avisa você sobre os logs de console restantes.

* Produza log de produção legível. Idealmente use bibliotecas de log para serem usadas no modo de produção (como [winston](https://github.com/winstonjs/winston) ou
[node-bunyan](https://github.com/trentm/node-bunyan)).

<a name="licensing"></a>
## 8. Licenciamento
![Licensing](../images/licensing.png)

Certifique-se de usar recursos que você tenha os direitos de usar. Se você usa bibliotecas, lembre-se de procurar o MIT, o Apache ou o BSD, mas se você os modifica, examine os detalhes da licença. Imagens e vídeos protegidos por direitos autorais podem causar problemas legais.
