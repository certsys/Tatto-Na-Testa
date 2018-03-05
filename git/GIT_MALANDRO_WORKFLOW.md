# Git Workflow Malandro 
Nós como bons samaritano usamos o [Feature-branch-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow) com [Interactive Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing) e alguns elementos do [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow). Os principais passos são os seguintes:

* Para um novo projeto :heart_eyes:, inicialize um repositório git no diretório do projeto. 
   ```sh
   cd <project directory>
   git init
   ```

* Crie uma nova feature/brusquetta branch.
    ```sh
    git checkout -b <brusquetta>
    ```
* Faça aquele [commit campeão](COMMIT_CAMPEAO.md).
    ```sh
    git add
    git commit -a
    ```
    _Por que?:_
    > `git commit -a` vai abrir um editor que permite separar o assunto do corpo.

* Sincronize com o remoto para obter as alterações que você perdeu :flushed:
    ```sh
    git checkout develop
    git pull
    ```
    
    _Por que?:_
    > Isso lhe dará a chance de lidar com conflitos em sua maquina enquanto faz o rebase, ao invés de pegar conflitos ao fazer o Pull Request.
    
* Atualize a sua feature branch com as últimas alterações de develop utilizando o rebase interativo.
    ```sh
    git checkout <brusquetta>
    git rebase -i --autosquash develop
    ```
    
    _Por que?:_
    > Você pode usar --autosquash para transformar todos seus commits, em um único commit. Ninguém quer muitos commits para uma única feature branch. [leia mais pequeno gafanhoto](https://robots.thoughtbot.com/autosquashing-git-commits)
    
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
    
    
* Faça um [Pull Request Amigão](PULL_REQUEST_AMIGAO.md).
* Pull request serão aceitados, mergeados e fechado pelo reviewer.
* Remova a sua feature branch.

  ```sh
  git branch -d <brusquetta>
  ```
  para remover todas as branches que não estão mais no remoto
  ```sh
  git fetch -p && for branch in `git branch -vv | grep ': gone]' | awk '{print $1}'`; do git branch -D $branch; done
  ```