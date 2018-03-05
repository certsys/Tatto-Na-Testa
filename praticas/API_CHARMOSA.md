# API

![API](../images/api.png)

## 1. API design

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
## 2. API security
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
## 3. API documentation
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
