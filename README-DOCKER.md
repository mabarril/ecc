# Guia de Execução do Sistema via Docker Compose

Este documento contém as instruções necessárias para iniciar o sistema (Banco de Dados, API Node.js e Frontend Angular/Nginx) utilizando Docker Compose.

## 📌 Pré-requisitos

Certifique-se de ter os seguintes softwares instalados na sua máquina:
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## ⚙️ Configurações Iniciais

1. **Variáveis de Ambiente (`.env`)**
   Na raiz do projeto, existe um arquivo `.env` que contém as credenciais de banco de dados, chaves secretas (JWT) e configurações da API. Ele já está configurado para o ambiente de desenvolvimento local. Caso necessite alterar credenciais (como a senha do root do MySQL), modifique os valores neste arquivo.

2. **Certificados SSL (`./ssl`)**
   A pasta `ssl` contém os arquivos **`cert.pem`** e **`key.pem`**, necessários para que o contêiner `ecc-web` (Nginx) exiba o frontend usando HTTPS. Eles já encontram-se gerados na pasta.

## 🚀 Como Iniciar o Sistema

### 1. Iniciar os Serviços

Para iniciar todos os containers integrados, abra o terminal na raiz do projeto e execute o comando abaixo. A flag `--build` garante que as imagens mais recentes da API e do Frontend sejam criadas com o código atual.

```bash
docker-compose up -d --build
```

Isso fará com que o Docker crie a rede `ecc-network`, inicialize o MySQL, compile as imagens e inicialize a API e o servidor Web Nginx. A flag `-d` executa serviços em modo "detached" (em segundo plano).

### 2. Verificar o Status dos Contêineres

Para verificar se estão rodando corretamente e checar se o banco de dados ficou saudável ("healthy"):

```bash
docker-compose ps
```

Para ver os logs de todos os containers em tempo real (pressione `Ctrl+C` para sair dessa visualização):

```bash
docker-compose logs -f
```

## 🌐 Acessos e Uso

- **Frontend e API**:
  Acesse [https://localhost](https://localhost) no seu navegador. As portas HTTP (80) e HTTPS (443) estão conectadas do host ao contêiner `ecc-web`. *Nota: Se aparecer um aviso de segurança no navegador por ser um certificado local auto-assinado, você pode prosseguir dizendo que conhece os riscos.*

- **Banco de Dados (Acesso externo)**:
  Você pode se conectar ao MySQL através de sua ferramenta favorita (DBeaver, MySQL Workbench, etc), usando:
  - **Host**: `localhost`
  - **Porta**: `3306`
  - **Database**: `eccbsb` (ou conecte global via `root` usando a senha em `MYSQL_ROOT_PASSWORD` que está no `.env`)
  - **Usuário e senha**: `ecc_user` / (veja *MYSQL_PASSWORD* no arquivo `.env`)
  
  ⚠️ **Atenção (Erro no DBeaver)**: 
  Se você encontrar o erro `"Public Key Retrieval is not allowed"` ao tentar conectar no MySQL pelo DBeaver, será necessário habilitar essa permissão:
  1. Vá até as configurações da conexão no DBeaver.
  2. Clique na aba **"Driver properties"** e encontre a propriedade **`allowPublicKeyRetrieval`**.
  3. Mude o valor de **`FALSE`** para **`TRUE`**.
  4. Salve e teste a conexão novamente!


## 🛑 Gerenciamento Comum

Para **deslogar e desligar** os contêineres e sua rede:

```bash
docker-compose down
```

Para **reiniciar** os serviços:

```bash
docker-compose restart
```

## 🧹 Como Limpar o Banco de Dados (Reset de Dados)

Os dados do SQL ficam guardados usando o docker volume `ecc_mysql_data` para resistirem entre reinicializações.
Se desejar limpar o ambiente inteiro e deletar todo o esquema do seu banco permanentemente, rodar o down seguido da tagulação de deleção de volume:

```bash
docker-compose down -v
```
> **Aviso:** Da próxima vez que rodar `docker-compose up -d --build`, o MySQL estará vazio de novo e você recriará um BD virgem em cima dele.
