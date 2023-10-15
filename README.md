# Actions

GitHub Actions reusáveis do GELOS.

## deploy.yml

Suponha um projeto do GELOS que é implantado nos nossos servidores. Esse
projeto provavelmente é um flake input no repositório de infra (nossas
configurações de NixOS).

Ao fazer mudanças nesse projeto, você provavelmente vai querer um deploy
automático na infra, isso envolve bumpar o `flake.lock` da infra e commitar essa mudança.

Essa action é feita para ajudar com isso. Chame ela onde fizer sentido (e.g.
pushes na main, tags, etc), dessa forma:

```yml
on:
  push:
    branches: [main]
jobs:
  deploy:
    uses: gelos-icmc/actions/.github/workflows/deploy.yml@main
    secrets: inherit
```

Ela irá ativar a action
[bump-lock.yml](https://github.com/gelos-icmc/infra/actions/workflows/bump-lock.yml),
que irá bumpar a versão do projeto e criar um commit, que por sua vez gera um
novo
[deployment](https://github.com/gelos-icmc/infra/actions/workflows/deploy.yml)
da configuração.

Assume-se que, no flake da infra, os inputs de projetos do GELOS se chamam
`gelos-{nome-do-repo}`. Exemplo: o repositório
[`site`](https://github.com/gelos-icmc/site) é nomeado `gelos-site`.

## fix-commit.yml

Essa action foi criada para o caso de uso comum que envolve:
- Autenticar um github app
- Checkout no repositório (em nome do app)
- Rodar algum comando (bump, fix, etc.)
- Commitar e pushar o resultado (em nome do app)

Note que essa action não funciona em forks que não tenham o secret do bot disponível.

Um exemplo de uso:
```yml
on:
  push:
  pull_request:
jobs:
  fix-nbsp:
    uses: gelos-icmc/actions/.github/workflows/fix-commit.yml@main
    secrets: inherit
    with:
      commit-message: Corrigir non-breaking spaces
      command: nix run .#fix-nbsp
```
