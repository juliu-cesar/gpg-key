# Padrões e técnicas com Git e GitHub
<div align="center">

<img src="https://github.com/juliu-cesar/padroes-e-tecnicas-git-e-github/assets/121033909/34bd2813-9f52-46db-8db0-500c180d1cad.jpg" height="400"/>

</div>

## Git Flow

O Git Flow é um modelo de branching para o Git que facilita a colaboração em equipes e o gerenciamento de lançamentos. Suas principais funcionalidades são o **Fluxo de Branches Padrão** onde definimos as branches padrão para diferentes tipos de trabalho, a **Integração Simplificada** que facilita a integração de código ao controlar as transições entre diferentes fases de desenvolvimento e por fim a **Release e Hotfix Handling** que gerencia o lançamento de versões estáveis e permite correções rápidas com hotfixes.

### Branches Principais:

- **Master/Main Branch**: É a branch principal onde estão as versões estáveis do seu projeto. Ela reflete o estado de produção e é onde as releases são feitas.

- **Develop Branch**: É a branch de desenvolvimento principal. Todas as novas features são mescladas aqui quando estão prontas. Essa branch deve ser idêntica ou refletir o estado da branch principal (geralmente main ou master).

## Funcionamento do Git Flow e seus comandos:

**Features**: Novas funcionalidades são desenvolvidas em branches separadas a partir da develop. Uma vez concluídas, são mescladas de volta à develop.

**Release**: Quando o código na develop está pronto para uma nova versão, é criada uma branch de release. Aqui, são feitos ajustes finais e preparativos para o lançamento. Ao finalizar a release, ela é mesclada tanto na main quanto na develop, atualizando ambas.

**Hotfixes**: Se surgirem problemas críticos na produção, é criada uma branch de hotfix a partir da main. Isso permite corrigir o problema sem interferir no desenvolvimento contínuo na develop. Após a correção, o hotfix é mesclado na main e também na develop, mantendo ambas atualizadas.

- `git flow feature start nome_da_feature`: Cria uma nova feature baseada na branch develop, permitindo o desenvolvimento isolado de uma funcionalidade.

- `git flow feature finish nome_da_feature`: Finaliza uma feature, mesclando-a de volta para a branch de desenvolvimento e removendo a branch da feature.

- `git flow release start nome_da_release`: Inicia uma nova release, criando uma branch para preparar o código para um lançamento.

- `git flow release finish nome_da_release`: Finaliza uma release, mesclando as alterações tanto na branch principal quanto na branch de desenvolvimento. Cria uma tag para a release.

- `git flow hotfix start nome_do_hotfix`: Inicia um hotfix para corrigir problemas críticos na produção, criando uma nova branch para as correções.

- `git flow hotfix finish nome_do_hotfix`: Finaliza um hotfix, mesclando as alterações tanto na branch principal quanto na de desenvolvimento. Cria uma tag para o hotfix.

## Exemplo de um fluxo de trabalho utilizando o Git Flow

```bash
# Instalação
sudo apt-get install git-flow

# Iniciar o repositório e o Git Flow
git init
git flow init

# Criar uma nova feature
git flow feature start paginaPrincipal
# Finalizar a feature e mesclar com a branch develop
git flow feature finish paginaPrincipal

# Criar uma nova release
git flow release start 0.1.0

# Criar uma nova feature
git flow feature start paginaDeContato
# Finalizar a feature e mesclar com a branch develop
git flow feature finish paginaDeContato

# Finalizar a release
git flow release finish 0.1.0

# Criar um hotfix
git flow hotfix start erroTituloPrincipal
# Realizar correções e finalizar o hotfix
git flow hotfix finish erroTituloPrincipal
```

No exemplo acima começamos criando uma nova funcionalidade, e logo em seguida iniciamos uma branch para lançar a release. Durante esse processo da release criamos uma nova funcionalidade, e ao finaliza-la o git flow ira efetuar o merge para a branch develop, porem a release não ira possui essa nova funcionalidade, uma vez que ela foi baseada na versão anterior da develop. Ao finalizar a branch release, o git flow ira criar um merge tanto na branch principal como na develop, pois caso seja efetuado alguma alteração de ultima hora na release, ela precisa ser também inserida na develop, e além disso ira criar uma nova tag para cada release. Por fim criamos uma hotfix para algum erro critico que precisava ser consertado. Ao finalizar o hotfix o git flow assim como para a release, ira efetuar o merge tanto para a *main* como para a develop, e também ira criar uma nova tag.

## Segurança com GPG e assinatura de commits

As chaves GPG (GNU Privacy Guard) são usadas para criptografar e assinar digitalmente dados, garantindo autenticidade, integridade e confidencialidade nas comunicações digitais. Elas são usadas principalmente para autenticação, criptografia, verificação de integridade, entre outros. Vejamos como criar uma chave gpg.

Primeiro verifique se o **GPG**(GNU Privacy Guard) ja esta instalado no Linux, caso não, basta rodar o comando `sudo apt-get install gnupg `. Apos isso podemos criar uma chave GNP, que sera do tipo **RSA**. Abaixo temos o comando para criar a chave e as opções selecionadas para cada pergunta:

```bash
gpg --full-generate-key

# select what kind of key you want:
# (1) RSA and RSA (default)

# RSA keys may be between 1024 and 4096 bits long.
# 4096

# specify how long the key should be valid.
# 1y

# GnuPG needs to construct a user ID to identify your key.
# Real name: name
# Email address: email
# Comment:

# Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit?
# o

# Finally, enter a password
```

É possível listar as chaves com o seguinte comando:

```bash
gpg --list-secret-keys --keyid-form LONG

# sec   rsa4096/---------------- 2023-11-16 [SC] [expires: 2024-11-15]
```

Na linha do `sec` temos o ID da chave após o *rsa4096/*, onde acima esta os traços (----). Ela é composta por letras e números e com ela é que iremos gerar o endereço da **public key**. Vejamos abaixo este comando:

```bash
gpg --armor --export ----------------

# -----BEGIN PGP PUBLIC KEY BLOCK-----
# address too big
# -----END PGP PUBLIC KEY BLOCK-----
```

Ele ira gerar um endereço bastante grande, e sera preciso copiar deste a primeiro linha com o `-----BEGIN PGP` até a ultima com o `KEY BLOCK-----`, vamos colar no GitHub, indo em `settings`, `SSH and GPG Keys` e `new GPG keys`. Com isso, teremos a chave cadastrada, mas é preciso configurar o **git** para que ele assina-le os commits.

- `git config --global user.signinkey ----------------` : configura a chave que o git ira utilizar caso ele assina-le os commits.

- `git config --global commit.gpgsign true` : configura para assinar automaticamente todos os commits efetuados, porem caso seja necessário assinar apenas os commits do repositório atual, não adicionar o *--global*.

- `git config --global tag.gpgSign true` : assim como para a opção anterior, configura para assinar todas as tags criadas.

- `git commit -S -m "..."` : para caso não seja configurado a assinatura automática de todos os commits, podemos utilizar a opção **-S** para assinar apenas commits específicos.


Também é preciso exportar uma variável para o gpg funcionar, o recomendado é ja exporta-la do arquivo do shell como `.bash`, `.bash_profile`, `.zshrc`, etc.

```bash
export GPG_TTY=$(tty)
```

Ao efetuar o primeiro commit com a assinatura, sera exigido a assinatura que foi colocada quando criamos a chave gpg. Ja para os commits seguintes não sera necessário (pode ser necessário digitar novamente para seções de terminais diferentes), e caso ele peça outras vezes, provavelmente o agente do GPG não esta iniciando corretamente. Mais a frente veremos como resolver esse problema.

Para verificar se o commit realmente foi assinado, podemos rodar o seguinte comando:

```bash
git log --show-signature -1
```

Ao enviar este commit para o GitHub, podemos clicar no hash do commit para verificar as modificações feitas, e ao lado do nome do usuário que fez o commit temos a opção **Verified**, indicando que a chave esta correta com a configurada no GitHub.

### Editando a chave GPG

Caso seja necessário adicionar outro email a esta mesma chave, podemos utilizar o seguintes comandos:

```bash
gpg --edit-key ----------------
```

Passando o id da chave ele ira abrir um terminal do gpg onde podemos digitar alguns comandos, que serão:

```bash
# Inicia a configuração para adicionar um novo email
adduid

# Real name: nome
# Email address: email
# Comment:

# Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit?
# o

save
```

Assim como quando foi criado a chave, esse processo é muito parecido, e ao final dele teremos adicionado o novo email com autorização maxima. Para mudar a autorizações do novo email é possível utilizar o seguintes comandos:

```bash
# Seleciona o segundo id
uid 2

# Configura a confiança neste novo email
trust

# 1 = I don't know or won't say
# 2 = I do NOT trust
# 3 = I trust marginally
# 4 = I trust fully
# 5 = I trust ultimately
# m = back to the main menu

# Your decision?
5
```

### Erro agente do gpg não inicia corretamente

Caso o agente do GPG não esteja iniciando corretamente, voce pode tentar a seguinte configuração:

- Dentro da pasta raiz do GPG (`.gnupg`) crie um arquivo chamado `gpg.conf`. Para facilitar podemos abrir com o vim o arquivo, pois ele ira criar esse arquivo caso ele não exista.

```bash
vim ~/.gnupg/gpg.conf
```

- Dentro do arquivo adicione a seguinte frase:

```conf
use-agent
```

- Por fim inicie o agente.

```bash
gpgconf --launch gpg-agent
```

Com isso o agente esta configurado e ira iniciar automaticamente.

## PRs e code Review

O GitHub possui diversas configurações para segurança e padronização do repositório, neste capitulo vamos criar e modificar algumas delas. Como por exemplo não *commitar* as mudanças diretamente na branch principal ou na develop, trabalhar sempre com pull requests, entre outros.

### Selecionando a branch principal de trabalho

Começaremos selecionando a branch `develop` como a principal, pois a branch *main* sera utilizada para representar o código que esta pronto e rodando, enquanto a develop sera a que vamos utilizar no dia a dia. Até o dado momento esta configuração no GitHub pode ser feita da seguinte forma:

- Dentro do repositório vamos em `Settings` e na linha `Default branch` vamos clicar no botão `switch to another branch`, e então selecionar a branch develop.

### Proteção de branches

No GitHub temos as regras de proteção de branches que permitem impor restrições e políticas para controlar o que pode ser feito em branches específicas de um repositório. Isso é útil para garantir a integridade do código, evitar modificações não autorizadas e facilitar um fluxo de trabalho seguro para os colaboradores. Porem é preciso notar que as regras mudam de acordo com o tipo de repositório, onde o tipo particular possui algumas regras e o repositório que for associado a uma organização possui outras, e abaixo vamos explorar este segundo tipo, por ser o mais comum quando trabalhado em uma empresa. Vejamos algumas regras que podemos aplicar:

- **Restrições de Push** : Permite controlar quem pode fazer push para a branch protegida. Pode ser configurada para permitir apenas colaboradores, equipes específicas ou até mesmo bloquear push de todos, exceto administradores.

- **Code Review** : Exige que alterações na branch protegida passem por revisões de código antes de serem mescladas. Pode exigir aprovações de um número mínimo de revisores antes da mesclagem.

- **Status dos Testes** : Garante que alterações na branch protegida passem em verificações automatizadas (por exemplo, testes automatizados, integração contínua) antes de serem mescladas. Pode exigir que os status dos testes automatizados sejam bem-sucedidos antes da mesclagem.

- **Exigir commits assinados** : Define que somente serão aceitos commits que forem assinados, como por exemplo as chaves GPG.

- **Exigir Rebase ou Merge** : Define se as branches devem ser mescladas através de rebase ou merge. Pode ser configurado para evitar merges de branches atrás da branch protegida.

- **Restrições de Exclusão ou Deleção** : Previne a exclusão ou deleção direta da branch protegida ou de branches que correspondem a padrões especificados.

- **Regras de Ramificação (Branch Protection Rules)** : Permite definir padrões de nomes de branches para aplicar regras a múltiplas branches. Por exemplo, `feature/*` pode ser usada para aplicar uma regra a todas as branches que começam com "feature/".

No momento vamos aplicar apenas as seguintes regras, a `Include administrators` e a `Restrict who can push to matching branches`, onde na segunda opção não iremos adicionar nenhum colaborador ou organização, dessa forma apenas os administradores poderão efetuar o commit para as branchs principais. Vamos aplicar essas regras para as branchs main e develop, logo o processo abaixo precisa ser feito duas vezes. 

- Na seção `Settings` do repositório vamos na opção `Branches` e `Add branch protection rule`. Ao abrir as regras vamos digitar o nome da branch que queremos proteger e selecionar as regras desejadas.

### Criando Pull Requests

Trabalhar com pull requests é extremamente recomendado pois permitem revisões de código, feedback de equipe e colaboradores, proporciona um histórico claro das alterações propostas, ajuda na identificação de erros e melhorias. Além disso ao trabalhar em equipe os PRs ajudam a gerenciar o fluxo de trabalho de desenvolvimento.

O GitHub possui varias ferramentas para trabalhar com pull requests, bastando efetuar o push de uma branch nova que ele exibira na pagina inicial do repositório um botão para criar uma nova PR com o ultimo commit enviado, ou também indo na aba de Pull Requests e criando uma nova. Porem como este processo é bastante simples e intuitivo, não vamos abordar como criar um PR neste documento.

### Template para Pull Requests

Ao criar uma no PR nos deparamos com o campo de texto para descrever o motivo daquela PR. Para agilizar e tornar esse processo mais organizado, podemos adicionar um template a ser seguido. Esse templete é inserido no campo de texto sempre que é criado uma nova PR, bastando seguir o template.

Dentro da pasta raiz do projeto vamos criar uma pasta chamada `.github` e dentro dela um arquivo markdown chamado `PULL_REQUEST_TEMPLATE`, e dentro desse arquivo vamos colocar nosso template, vejamos um exemplo:

```markdown
# Description

Please include a summary of the change and which issue is fixed. Please also include relevant motivation and context. List any dependencies that are required for this change.

Fixes # (issue)

## Type of change

Please delete options that are not relevant.

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] This change requires a documentation update

# How Has This Been Tested?

Please describe the tests that you ran to verify your changes. Provide instructions so we can reproduce. Please also list any relevant details for your test configuration

- [ ] Test A
- [ ] Test B

**Test Configuration**:
* Firmware version:
* Hardware:
* Toolchain:
* SDK:

# Checklist:

- [ ] My code follows the style guidelines of this project
- [ ] I have performed a self-review of my own code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes
- [ ] Any dependent changes have been merged and published in downstream modules
```

Lembrando que isso varia para cada caso, mas com apenas essa mudança temos um template para os PRs.

## CodeOwners

O code owners é uma ferramenta do GitHub utilizada para especificar automaticamente quem serão os responsáveis por seções especificas de códigos no repositório, auxiliando a gerenciar os colaboradores que faram as revisões. Podendo ser especificada uma extensão, uma pasta ou um arquivo. Por exemplo, digamos que o colaborador `João` seja o especialista de javascript do time, logo seria interessante que todo arquivo de extensão `.js` fosse revisado por ele.

Para adicionar essa funcionalidade vamos adicionar um arquivo chamado `CODEOWNERS` na pasta `.github` na raiz do projeto. Dentro deles teremos o seguinte:

```
*.js @João
./src @Lucas
package.json @João @Time-A
```

Como visto acima, podemos também selecionar mais de um colaborador e times para ser o revisor padrão para um arquivo, uma pasta ou para todas os arquivos de certa extensão.

## Semantic Versioning (SemVer)

O versionamento semântico é um sistema de numeração de versão que traz mais significado ao número da versão do projeto. A estrutura é composta por tres números `x.y.z`, onde cada um representa um significado especifico:

- **X (Major)** : o primeiro traz a versão principal e é incrementado quando são feitas mudanças **incompatíveis** na API do software. Isso significa que houve alterações que podem quebrar a compatibilidade com versões anteriores. Por exemplo, adicionar ou remover funcionalidades que alteram a maneira como o software funciona.

- **Y (Minor)** : o número da versão secundária é incrementado quando são adicionadas **novas funcionalidades** de maneira retrocompatível. Isso indica que foram adicionadas funcionalidades, mas o software ainda é compatível com versões anteriores. Por exemplo, adicionar novos recursos sem alterar a funcionalidade existente.

- **Z (Patch)** : o ultimo número indica quando são feitas correções de bugs ou pequenas melhorias, mantendo a compatibilidade total com versões anteriores. Geralmente, isso inclui correções de bugs, melhorias de desempenho ou pequenas atualizações que não alteram a funcionalidade existente.

Um detalhe importante sobre o *Major* é que geralmente ela começa da versão `0`, que significa que o software ainda esta em produção e pode sofrer mudanças que quebrem o código mesmo que não seja lançada uma nova versão Major, sendo de responsabilidade do usuário compreender o risco de usar uma aplicação na versão 0. Porem quando for lançada a versão `1.0.0`, os desenvolvedores que serão responsáveis por evitar quebra de código para as próximas versões. Isso significa que muito provavelmente uma versão `1.2.2` sera compatível com uma `1.4.7`, porem pode não ser compatível com uma versão `2.2.0`.

Outra parte importante é que podemos adicionar outras informações apos a versão, apenas colocando um traço `-` apos os números, como por exemplo `1.0.0-alpha`, onde ela é mais atual porem menos estável do que a versão `1.0.0`.

## Conventional Commits

O Conventional Commits é uma convenção para padronizar as mensagens de commits, melhorando a compreensão do histórico do repositório. Essa convenção define um conjunto de termos e um formato específico para as mensagens de commit, vejamos um exemplo:

```text
feat(api): create new route

Adds a new route in the client controller. It is possible to add, update and delete

Refs: #123
```

No commit acima temos a mensagem principal na primeira linha, onde passamos o tipo da modificação, que no caso é `feat`, o escopo `api` e a mensagem. No meio temos o corpo do commit, onde temos uma mensagem mais descritiva, e por fim uma referencia a uma *issue*. Lembrando que fora o tipo e a mensagem principal, o resto é opcional, porem ainda sim muito recomendado. 

Existem varias formas de montar um commit com este padrão, mas para mais detalhes consultar o site oficial [Convencional Commit](https://www.conventionalcommits.org/en/v1.0.0/).

### CommitLint

O CommitLint é uma biblioteca Node que verifica se os commits criados estão no padrão do conventional commits. Uma das vantagens é sua instalação e uso é bastante simples, e uma desvantagem é a necessidade de iniciar um projeto node. Segue o link para o guia [CommitLint](https://commitlint.js.org/#/).

### Commitizen

Esta é outra opção de biblioteca para se trabalhar com conventional commits, mas diferente da opção anterior, ela não apenas verifica se esta no padrão, como também auxilia na construção do commit. Uma vantagem é que ela roda no próprio CLI, e uma desvantagem é a necessidade de iniciar um projeto node. Segue o link para o guia [Commitizen](https://github.com/commitizen/cz-cli).
