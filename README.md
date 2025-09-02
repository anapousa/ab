# Roteiro sobre configuração e uso de um pipeline CI/CD

Este repositório apresenta um roteiro prático para configurar e utilizar um pipeline de **Integração Contínua e Delivery Contínuo (CI/CD)**. O objetivo é simular práticas reais de DevOps no contexto de desenvolvimento web, utilizando o GitHub Actions.

O GitHub Actions permite automatizar fluxos de trabalho (workflows) como testes, builds e deploys. Neste tutorial, aplicaremos esses princípios em um cenário semelhante ao de um ambiente de produção.

**Contexto**:  Você faz parte da equipe de desenvolvimento web de uma ONG dedicada ao cuidado animal, responsável pelo sistema Adote Fácil, uma plataforma online para adoção de animais. A equipe é distribuída geograficamente e composta por profissionais especializados em diferentes áreas do projeto web, cada um responsável por módulos distintos do sistema. Diante disso, você identificou que a adoção de metodologias de integração e delivery contínuo (CI/CD) neste projeto trará diversas melhorias, como:
- Automatizar tarefas manuais, como a análise e o merge de Pull Requests, liberando os colaboradores para outras atividades de maior valor;
- Elevar a qualidade e o padrão do código produzido;
- Estimular a criação e atualização constante da documentação técnica;
- Facilitar a coleta rápida e eficiente de feedbacks dos usuários;
- E outras vantagens relevantes para o ciclo de desenvolvimento.

Com base nesses benefícios, a equipe elaborou um guia detalhado para a instalação de um ambiente focado na implementação dessas práticas de CI/CD, visando otimizar o fluxo de trabalho e garantir entregas mais ágeis e seguras.

## Tarefa #1: Configurar o GitHub Actions
#### Passo 1

Crie um token de acesso pessoal no GitHub (I) e faça um Fork (II) do projeto **adote-facil**. 

**(I)** Para criar um token, clique no ícone do seu perfil, localizado no canto superior direito da tela, e selecione **Settings** no menu.

![image](https://github.com/user-attachments/assets/6b849a9d-2737-4cc4-815c-8085d29c4f60)

Role a tela para baixo e clique em **Developer settings**, no canto inferior esquerdo.

![image](https://github.com/user-attachments/assets/822c224b-509a-45a6-8db5-3df3fe67c4d2)

No menu que aparecer, clique em **Personal access tokens** e depois em **Tokens classic**. 

![image](https://github.com/user-attachments/assets/c3524f42-d63e-4350-ac5d-f620293f47b7)

<img width="544" height="238" alt="image" src="https://github.com/user-attachments/assets/a79cff4e-0be7-4db5-aacf-93a96fb09122" />

No canto superior direito, clique no botão **Generate new token** e escolha **Generate new token (classic)**.

<img width="292" height="56" alt="image" src="https://github.com/user-attachments/assets/9240ab02-f8dd-4e99-bed6-0100a9f4e63f" />

Dê um nome para ele e marque as opções `repo` e `workflow` para gerar o Token. Gere o token mínimo (7 dias) apenas para este experimento. 

<img width="1536" height="742" alt="image" src="https://github.com/user-attachments/assets/aa6ffbe9-b707-45dc-bdb3-0ae52f2913bb" />

Por fim, role a página até o final e clique em **Generate token**.

![image](https://github.com/user-attachments/assets/60c0d608-07a3-4aa8-b7df-54eb792b4e1e)

Como este é apenas um experimento, você pode copiar e salvar o token em um bloco de notas. Ele será necessário quando o GitHub solicitar sua senha em operações protegidas, como clonar, fazer push ou pull.

#

**(II)** Acesse o repositório [adote-facil](https://github.com/ArthurEnrique15/adote-facil) e clique no botão **Fork**, localizado no canto superior direito da página. 

<img width="1489" height="1065" alt="image" src="https://github.com/user-attachments/assets/b1572da8-59ad-4f9c-87c5-d0469e0da548" />

Isso irá criar uma cópia do projeto na sua conta, permitindo que você trabalhe nele livremente.

#### Passo 2

Clone o seu próprio repositório, criado a partir do fork, para a sua máquina local. Para isso, use o comando abaixo, substituindo `<USER>` pelo **seu nome de usuário do GitHub**:

```bash
git clone https://github.com/<USER>/adote-facil.git
```

Em seguida, dentro do diretório do repositório que você clonou, crie o arquivo `.github/workflows/experimento-ci-cd.yml` com o conteúdo fornecido abaixo. Para isso, primeiro crie os diretórios `.github` e, dentro dele, o diretório `workflows`, e então salve o arquivo `experimento-ci-cd.yml` nessa última pasta. 

A seguir, mostramos como criar as pastas e o arquivo tanto no Windows quanto no Linux. O conteúdo que deve ser inserido no arquivo **experimento-ci-cd.yml** está logo abaixo.

**Linux**: você pode usar os comandos `mkdir` para criar os diretórios, `touch` para criar o arquivo, e outros comandos como `cd`, `ls` e `mv` para navegar e manipular os arquivos. Alternativamente, pode usar a interface gráfica (GUI) do seu sistema para criar as pastas e o arquivo.

```bash
# Entre no diretório do repositório clonado
cd nome-do-seu-repositorio

# Crie o diretório .github e o subdiretório workflows (com -p para criar pais se não existirem)
mkdir -p .github/workflows

# Navegue até o diretório workflows
cd .github/workflows

# Crie o arquivo experimento-ci-cd.yml vazio
touch experimento-ci-cd.yml

# Agora, você pode abrir esse arquivo no editor de texto de sua preferência, por exemplo:
nano experimento-ci-cd.yml
# ou
vim experimento-ci-cd.yml

# Cole o conteúdo do pipeline CI/CD dentro desse arquivo e salve
```

**Windows:**

Acesse o diretório do repositório que foi clonado após o fork e crie, dentro dele, a pasta `.github` e, em seguida, a subpasta `workflows`.

No VSCode ou na sua IDE de preferência, clique com o botão direito sobre o diretório `workflow` e selecione a opção New File. Então, crie o arquivo experimento-ci-cd.yml

<img width="675" height="197" alt="image" src="https://github.com/user-attachments/assets/c142a1bf-d2eb-431f-9952-33b6c2a8297d" />

#

**Arquivo YML**
```yml
name: experimento-ci-cd

# Evento que aciona o workflow
on:
  pull_request:
    branches:
      - main

# Definição dos jobs (tarefas) que serão executadas
jobs:

  # Primeiro job: Executar testes unitários
  unit-test:
    runs-on: ubuntu-latest # Define o sistema operacional usado no runner (Ubuntu na versão mais recente)
    steps:
      - name: Checkout do código
        uses: actions/checkout@v4 # Faz o download do repositório no runner

      - name: Instalar dependências do backend
        run: |
          cd backend
          npm install

      - name: Executar testes unitários com Jest
        run: |
          cd backend
          npm test -- --coverage # Executa os testes e gera relatório de cobertura

  # Segundo job: Build (construção) das imagens Docker
  build:
    needs: unit-test # Esse job só será executado após o job 'unit-test' ser concluído com sucesso
    runs-on: ubuntu-latest
    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Configurar Docker Buildx
        uses: docker/setup-buildx-action@v2 # Habilita a ferramenta Buildx do Docker

      - name: Configurar Docker QEMU
        uses: docker/setup-qemu-action@v2 # Permite builds multiplataforma usando emulação (útil em CI)

      - name: Build das imagens Docker
        run: docker compose build # Executa o build das imagens definidas no docker-compose.yml

  # Terceiro job: Subir os containers temporariamente para testes básicos de integração
  up-containers:
    needs: build # Esse job depende do job 'build'
    runs-on: ubuntu-latest

    # Definição de variáveis de ambiente necessárias para o backend e banco
    env:
      POSTGRES_DB: adote_facil
      POSTGRES_HOST: adote-facil-postgres
      POSTGRES_USER: ${{ secrets.POSTGRES_USER }}
      POSTGRES_PASSWORD: ${{ secrets.POSTGRES_PASSWORD }}
      POSTGRES_PORT: 5432
      POSTGRES_CONTAINER_PORT: 6500

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Criar arquivo .env
        working-directory: ./backend
        run: |
          echo "POSTGRES_DB=${{ env.POSTGRES_DB }}" > .env
          echo "POSTGRES_HOST=${{ env.POSTGRES_HOST }}" >> .env
          echo "POSTGRES_USER=${{ env.POSTGRES_USER }}" >> .env
          echo "POSTGRES_PASSWORD=${{ env.POSTGRES_PASSWORD }}" >> .env
          echo "POSTGRES_PORT=${{ env.POSTGRES_PORT }}" >> .env
          echo "POSTGRES_CONTAINER_PORT=${{ env.POSTGRES_CONTAINER_PORT }}" >> .env

      - name: Subir containers com Docker Compose
        working-directory: ./backend
        run: |
          docker compose up -d
          sleep 10 # Aguarda alguns segundos para garantir que os serviços subam
          docker compose down

  # Quarto job: Criação do release e entrega do artefato do projeto
  delivery:
    needs: build # Esse job depende do job 'build'
    runs-on: ubuntu-latest
    permissions:
      contents: write # Permissão necessária para criar release

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Gerar arquivo ZIP do projeto completo
        run: zip -r adote-facil-projeto.zip . -x '*.git*' '*.github*' 'node_modules/*'
        # Compacta o projeto, ignorando arquivos desnecessários

      - name: Criar release com artefato
        uses: ncipollo/release-action@v1.12.0
        with:
          artifacts: "adote-facil-projeto.zip"
          tag: "v1.0.0"
          name: "Release v1.0.0"
          body: "Release automática do projeto Adote Fácil via GitHub Actions"
```

Esse arquivo configura o GitHub Actions (GHA) para ser executado sempre que um evento do tipo `pull_request` for direcionado para a branch principal (`main`) do repositório. O workflow está dividido em três jobs, que representam as etapas da automação:

- **test**: executa os testes do sistema; 
- **build**: realiza o processo de compilação do projeto;
- **delivery**: simula uma entrega (deploy) da aplicação.

#### Passo 3

Acesse o diretório do repositório clonado (use o comando `cd` no terminal para entrar na pasta `.../adote-facil/`) e, em seguida, adicione, registre e envie as alterações para o GitHub com os seguintes comandos:

```bash
git add --all
git commit -m "Configurando o GitHub Actions"
git push origin main
```

## Tarefa #2: Configurar GitHub Secrets

Em muitos projetos, é comum utilizar variáveis sensíveis — como senhas, tokens e chaves de acesso — que não devem ser incluídas diretamente no código por questões de segurança. Para lidar com isso, o GitHub Actions oferece o recurso chamado *Secrets*, que permite armazenar variáveis de ambiente de forma criptografada e segura.

A seguir, vamos configurar os Secrets para armazenar as informações de conexão com o banco de dados:

#### Passo 1

Acesse o seu repositório adote-facil no GitHub, clique na aba Settings (configurações) na parte superior do repositório. Em seguida, no menu lateral, vá em **Secrets and variables > Actions** e clique no botão **New repository secret** para adicionar um novo segredo.

<img width="1571" height="96" alt="image" src="https://github.com/user-attachments/assets/a4fd36c0-a0d4-4398-8cc0-2bbd7eb07b0e" />

<img width="296" height="224" alt="image" src="https://github.com/user-attachments/assets/74b8152e-d3e0-4b3f-afb4-ba40e4f4b07b" />

#### Passo 2

Crie dois segredos (secrets) com os seguintes valores:

1. **POSTGRES_USER**
   - **Name**: `POSTGRES_USER`
   - **Secret**: `Postgres`

Em seguida, clique em Add secret.

<img width="484" height="420" alt="image" src="https://github.com/user-attachments/assets/48290ff9-7b73-4ac4-9e0e-396245a66ab9" />

2. **POSTGRES_PASSWORD**
   - **Name**: `POSTGRES_PASSWORD`
   - **Secret**: `postgres`

Em seguida, clique em Add secret.

<img width="484" height="420" alt="image" src="https://github.com/user-attachments/assets/40643f19-afae-4cad-8f36-e1446b1a15f5" />

Esses valores simulam um cenário de acesso ao banco de dados. Eles serão utilizados automaticamente no workflow `.github/workflows/experimento-ci-cd.yml`.

## Tarefa #3: Criando um Pull Request (PR) com bug

Este caso simula um cenário em que o código possui um erro (bug), o que faz com que o pipeline de CI/CD falhe. Como resultado, o GitHub bloqueia automaticamente o *merge* na branch `main`, impedindo que código com falhas seja integrado à versão principal do projeto.

#### Passo 1

Vamos simular que o teste da função responsável por atualizar o e-mail do usuário está falhando — ou seja, está retornando um valor diferente do esperado.

Em sua máquina local, abra o projeto forkado no VSCode ou na sua IDE de preferência, localize o arquivo `/backend/src/services/user/update-user.spec.ts` (conforme mostrado na imagem abaixo) e faça as seguintes alterações:

- Comente a linha original do teste (a linha 92 do arquivo), que contém o comando expect(...).

- Logo abaixo, insira a seguinte linha de teste que utiliza um e-mail incorreto:
```diff
expect(result).toEqual(Success.create({ ...updatedUser, email: 'email-errado@mail.com' }))
````

<img width="1345" height="200" alt="image" src="https://github.com/user-attachments/assets/38696814-16fb-41fd-8106-af5866cc73f6" />

#### Passo 2

Após fazer as modificações no código, crie uma nova branch, faça o commit das alterações e envie para o repositório remoto com os seguintes comandos:

```bash
git checkout -b bug
git add --all
git commit -m "Alterando o arquivo update-user-spec.ts"
git push origin bug
```

#### Passo 3

Em seguida, crie um Pull Request (PR) com suas modificações. Para isso, acesse no navegador a seguinte URL, substituindo `<USER>` pelo seu nome de usuário no GitHub: `https://github.com/<USER>/adote-facil/compare/main...bug`

Nessa página, revise as alterações feitas. Após a verificação, clique no botão Create pull request. Na janela que abrir, adicione uma breve descrição do PR e confirme a criação clicando novamente em **Create pull request**.

Assim que o PR for criado, o pipeline configurado no arquivo `experimento-ci-cd.yml` será automaticamente acionado pelo GitHub Actions. Como inserimos um bug, os testes irão falhar — essa falha será exibida na página de execução do pipeline. Você pode acompanhar o status do processo acessando a aba Actions do seu repositório.

Em resumo, o servidor de CI/CD detectou automaticamente um problema no código enviado, impedindo sua integração ao branch principal do projeto.

## Tarefa #4: Criando um Pull Request (PR) com a correção

Vamos restaurar o arquivo ao seu estado original. Para isso, descomente a linha 92 e remova a linha 93. Assim, ao criar um novo PR, os testes serão executados com sucesso, sem apresentar falhas.

Após fazer essas alterações, salve o arquivo e crie uma nova branch para corrigir o bug. Em seguida, execute os comandos abaixo para adicionar, commitar e enviar as mudanças para o repositório remoto:

```bash
git checkout -b fixture
git add --all
git commit -m "Consertando a função Test"
git push origin fixture
```
Caso solicitado, informe seu nome de usuário e o token que criamos na *Tarefa 1* para autenticação.

Em seguida, crie novamente um novo Pull Request (PR) com a correção. Para isso, acesse no navegador a seguinte URL, substituindo <USER> pelo seu nome de usuário no GitHub: `https://github.com/<USER>/adote-facil/compare/main...fixture`

Nessa página, revise as alterações realizadas. Em seguida, clique no botão Create pull request, no canto superior direito da tela. Na janela que abrir, insira uma breve descrição do PR e confirme a criação clicando novamente no botão **Create pull request**, no canto inferior direito.

Você pode acompanhar o andamento do pipeline acessando a aba **Actions** do repositório e selecionando o nome do PR em execução.

Após a criação do PR, o GitHub Actions iniciará automaticamente o pipeline. Esse pipeline será responsável por executar os testes, realizar o build, criar a release e gerar um arquivo .zip do projeto. Ao final do processo, o arquivo estará disponível para download. Para acessá-lo, basta clicar em Releases, localizado na barra lateral direita do repositório.

<img width="1600" height="656" alt="image" src="https://github.com/user-attachments/assets/505721d3-67f8-49da-b88d-82ee3d1e53e1" />

# FIM
