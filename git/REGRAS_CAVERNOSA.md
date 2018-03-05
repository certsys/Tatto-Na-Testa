# Regras pra dar tudo certo
Existem algumas regras para utilizar o git da maneira que o cumpade Washington sentiria orgulho. Sempre devemos manter em mente:
* Trabalhe em uma feature branch.
    
    _Por que?:_
    >Porque assim, todo o trabalho é feito de forma isolada em um branch dedicado, em vez do branch principal. Ele permite que você envie vários pull requests sem confusão. Você pode iterar sem poluir a master com código potencialmente instável e inacabado. [leia mais...](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)
* Crie a branch a partir de `develop`
    
    _Por que?:_
    >Desta forma, você pode certificar-se de que o código na master quase sempre será construído sem problemas e pode ser usado para efetuar releases de forma automatizada (isso pode ser um exagero para alguns projetos).

* Nunca faça o push para `develop` ou `master`. Faça um [Pull Request Amigão](PULL_REQUEST_AMIGAO.md).
    

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

* Use [esse `.gitignore`](./.gitignore)
    
    _Por que?:_
    > Já existe uma lista de arquivos que não devem ser enviados com o código para o repo remoto. Além disso, exclui a configuração de pastas e arquivos para editores mais usados, bem como pastas de dependência mais comuns.

* Proteja a branch de `develop` e `master` com todas as suas forças de um bom marinheiro bebado
  
  ![Git](../images/protect-branches.jpg)
    
    _Por que?:_
    > Isso protege suas branches com "código pronto" de receber mudanças inesperadas e irreversíveis. leia mais... [Github](https://help.github.com/articles/about-protected-branches/)
    