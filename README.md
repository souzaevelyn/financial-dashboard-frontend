# Financial Dashboard
Sistema completo de dashboard financeiro para gerenciamento de cotações de moedas e conversões de BRL para USD, desenvolvido com arquitetura de microservícios.

## 📋 Descrição

O Financial Dashboard é uma aplicação web composta por três módulos principais que se comunicam através do protocolo REST:

- **Frontend**: Interface do usuário desenvolvida com HTML, CSS, JavaScript e Bootstrap
- **Backend API**: API REST desenvolvida com Python e Flask
- **Database**: Banco de dados SQLite para persistência de dados

O sistema permite que usuários se cadastrem, façam login, realizem conversões de moedas, salvem cotações e consultem dados históricos através da integração com o Yahoo Finance.

## 🏗️ Arquitetura

```
┌─────────────┐     HTTP     ┌─────────────┐     REST     ┌─────────────┐
│   Frontend  │ ◄──────────► │   Backend   │ ◄──────────► │  Database   │
│  (HTML/JS)  │              │   (Flask)   │              │  (SQLite)   │
└─────────────┘              └─────────────┘              └─────────────┘
                                      │
                                      │ External API
                                      ▼
                              ┌─────────────┐
                              │Yahoo Finance│
                              └─────────────┘
```

Para mais detalhes sobre a arquitetura, consulte o arquivo [ARCHITECTURE.md](ARCHITECTURE.md).

## ✨ Funcionalidades

### Autenticação
- Registro de novos usuários
- Login seguro com hash de senhas
- Gerenciamento de perfil

### Dashboard Financeiro
- Conversão em tempo real de BRL para USD
- Salvamento de conversões realizadas
- Histórico de conversões

### Cotações de Moedas
- Salvamento de cotações de diferentes pares de moedas
- Integração com Yahoo Finance para dados históricos
- Gerenciamento de cotações salvas

### Integração Externa
- Consulta a dados históricos do Yahoo Finance
- Suporte a múltiplos pares de moedas (USDBRL, EURBRL, GBPBRL, JPYBRL)

## 🚀 Tecnologias Utilizadas

### Frontend
- HTML5
- CSS3
- JavaScript (ES6+)
- Bootstrap 5.3.2 (via CDN)
- Fetch API (chamadas HTTP nativas)

### Backend
- Python 3.11
- Flask 3.0.0
- Flask-SQLAlchemy 3.1.1
- Flask-CORS 4.0.0
- Flasgger 0.9.7.1 (Swagger)

### Database
- SQLite 3
- SQL Schema com índices otimizados

### DevOps
- Docker
- Docker Compose
- Nginx

## 📦 Instalação

### Escolha do Método de Instalação

O projeto oferece dois métodos de instalação, cada um com suas vantagens:

| Método | Vantagens | Desvantagens | Indicado Para |
|--------|-----------|--------------|---------------|
| **Docker** | Ambiente isolado, reprodutível, fácil deploy, pronto para produção | Requer Docker instalado, overhead de recursos | Produção, ambientes de teste, desenvolvedores que preferem containers |
| **Local** | Performance nativa, debugging mais simples, modificações em tempo real | Dependências do sistema, menos reprodutível | Desenvolvimento ativo, aprendizado, testes rápidos |

**Recomendação**: Use Docker para produção e ambientes de teste. Use instalação local para desenvolvimento ativo.

### Instalação com Docker (Recomendado)

A instalação com Docker é o método recomendado pois garante que o ambiente seja reproduzível e isolado, evitando conflitos de dependências e versões.

#### Passo 1: Verificar Pré-requisitos

Certifique-se de que você tem o Docker e Docker Compose instalados:

```bash
# Verificar versão do Docker
docker --version
# Saída esperada: Docker version 20.x.x ou superior

# Verificar versão do Docker Compose
docker-compose --version
# Saída esperada: Docker Compose version v2.x.x ou superior
```

Se não tiver o Docker instalado, siga as instruções oficiais:
- **Windows**: [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop)
- **macOS**: [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop)
- **Linux**: [Docker Engine](https://docs.docker.com/engine/install/)

> **⚠️ Pré-requisito no Windows — WSL2**
>
> No Windows, o Docker Desktop precisa do **WSL2 (Windows Subsystem for Linux)** para rodar containers Linux. Se o comando `docker-compose up --build` retornar o erro `failed to connect to the docker API at npipe:////./pipe/dockerDesktopLinuxEngine`, siga estes passos:
>
> 1. Abra o PowerShell **como Administrador** (botão direito → "Executar como administrador")
> 2. Execute: `wsl --install`
> 3. **Reinicie o computador** (obrigatório — o WSL2 só é ativado após o reboot)
> 4. Abra o Docker Desktop, aguarde ele iniciar completamente (ícone na bandeja ficará verde/estável)
> 5. Execute `docker-compose up --build` novamente
>
> **Alternativa sem Docker**: a aplicação funciona normalmente sem containers — basta rodar o backend localmente (`python app.py` na pasta `backend/`) e abrir o `frontend/public/index.html` no navegador. Veja a seção [Instalação Local](#instalação-local-desenvolvimento).

#### Passo 2: Clonar o Repositório

Clone o repositório e navegue até o diretório do projeto:

```bash
# Clonar o repositório
git clone <url-do-repositorio>

# Entrar no diretório do projeto
cd "C:\Users\evely\financial-dashboard"
```

#### Passo 3: Entender a Estrutura Docker

O projeto utiliza três containers Docker independentes:

1. **financial-dashboard-db**: Container responsável pelo banco de dados SQLite
   - Imagem base: Python 3.11-slim
   - Volume: Persistência do banco de dados
   - Porta: Sem exposição externa (acesso interno)

2. **financial-dashboard-backend**: Container da API REST
   - Imagem base: Python 3.11-slim
   - Dependências: Flask, SQLAlchemy, etc.
   - Porta: 5000 (API e documentação Swagger)
   - Dependência: Container database

3. **financial-dashboard-frontend**: Container da interface web estática
   - Imagem base: Nginx Alpine
   - Serve o arquivo `public/index.html` (HTML/CSS/JS) diretamente pelo Nginx
   - Porta: 80 (interface web)
   - Dependência: Container backend

#### Passo 4: Construir e Iniciar os Containers

Execute o seguinte comando para construir as imagens e iniciar todos os containers:

```bash
# Construir e iniciar todos os containers (modo foreground)
docker-compose up --build
```

**O que este comando faz:**
- `--build`: Força a reconstrução das imagens Docker
- `up`: Cria e inicia os containers definidos no docker-compose.yml
- Sem `-d`: Mostra os logs em tempo real no terminal

**Saída esperada:**
```
[+] Building 45.2s (12/12) FINISHED
 => => naming to financial-dashboard-frontend
 => => naming to financial-dashboard-backend
 => => naming to financial-dashboard-db
[+] Running 4/4
 ✔ Network financial-network         Created
 ✔ Container financial-dashboard-db  Started
 ✔ Container financial-dashboard-backend   Started
 ✔ Container financial-dashboard-frontend  Started
```

#### Passo 5: Iniciar em Background (Opcional)

Se preferir rodar os containers em background (sem ocupar o terminal):

```bash
# Iniciar containers em background
docker-compose up -d --build

# Verificar status dos containers
docker-compose ps
```

**Saída esperada do `docker-compose ps`:**
```
NAME                          STATUS          PORTS
financial-dashboard-db        Up (healthy)   -
financial-dashboard-backend   Up (healthy)   0.0.0.0:5000->5000/tcp
financial-dashboard-frontend  Up (healthy)   0.0.0.0:80->80/tcp
```

#### Passo 6: Verificar os Logs

Para acompanhar o que está acontecendo nos containers:

```bash
# Ver logs de todos os containers
docker-compose logs -f

# Ver logs de um container específico
docker-compose logs -f backend
docker-compose logs -f frontend
docker-compose logs -f database
```

#### Passo 7: Acessar a Aplicação

Após os containers estarem rodando, acesse:

- **Frontend (Interface Web)**: file:///C:/Users/evely/financial-dashboard/frontend/public/index.html
  - Interface principal do dashboard financeiro
  - Login, registro, conversor de moedas, etc.

- **Backend API**: http://localhost:5000
  - Exibe informações da API em JSON (`{"name": "Financial Dashboard API", ...}`)
  - Endpoints da API REST (respostas em JSON, não páginas web)

- **Documentação Swagger**: http://localhost:5000/docs
  - Documentação interativa da API
  - Teste de endpoints diretamente no navegador

> **Importante**: O backend é uma API REST, não um site. Ao acessar `http://localhost:5000` no navegador você verá uma resposta JSON, não uma página visual. A interface visual do usuário está no frontend (arquivo `index.html` ou http://localhost com Docker).

> **Nota**: Se os containers não estiverem rodando ou o Docker Desktop não estiver iniciado, você pode acessar o frontend abrindo diretamente o arquivo `frontend/public/index.html` no navegador (basta dar um duplo clique no arquivo). O backend ainda precisará estar rodando localmente (`python app.py` no diretório `backend/`).

#### Passo 8: Testar a Instalação

Faça um teste rápido para verificar se tudo está funcionando:

```bash
# Testar health check da API
curl http://localhost:5000/api/health

# Saída esperada:
# {"status":"healthy","timestamp":"2024-01-01T12:00:00.000000"}
```

#### Comandos Úteis de Gerenciamento

```bash
# Parar todos os containers
docker-compose down

# Parar e remover volumes (limpeza completa)
docker-compose down -v

# Reiniciar containers
docker-compose restart

# Reconstruir apenas um container específico
docker-compose up --build backend

# Entrar em um container (para debugging)
docker-compose exec backend bash
docker-compose exec frontend sh

# Ver uso de recursos
docker stats
```

#### Solução de Problemas Comuns

**Problema: `failed to connect to the docker API at npipe:////./pipe/dockerDesktopLinuxEngine`**
```powershell
# Causa 1: Docker Desktop não está aberto → abra o Docker Desktop e aguarde iniciar
# Causa 2: WSL2 não instalado (Windows) → PowerShell como Admin:
wsl --install
# Depois REINICIE o computador e abra o Docker Desktop novamente
```

**Problema: Porta já em uso**
```bash
# Erro: Bind for 0.0.0.0:80 failed: port is already allocated
# Solução: Altere as portas no docker-compose.yml ou pare o serviço que está usando a porta
```

**Problema: Containers não iniciam**
```bash
# Ver logs detalhados
docker-compose logs

# Reconstruir do zero
docker-compose down
docker-compose up --build --force-recreate
```

**Problema: Permissão negada no Linux**
```bash
# Adicionar seu usuário ao grupo docker
sudo usermod -aG docker $USER
# Fazer logout e login novamente
```

**Problema: Database não persiste**
```bash
# Verificar se o volume está criado
docker volume ls

# O volume financial_db_data deve estar listado
# Se não estiver, recrie com docker-compose up -d
```

#### Entendendo o docker-compose.yml

O arquivo `docker-compose.yml` define:

- **Services**: Os três containers (database, backend, frontend)
- **Networks**: Rede bridge para comunicação entre containers
- **Volumes**: Persistência de dados do banco de dados
- **Health Checks**: Verificação de saúde dos containers
- **Depends_on**: Ordem de inicialização dos containers
- **Environment Variables**: Configurações de ambiente

#### Estrutura de Portas

- **Porta 80**: Frontend (Nginx servindo HTML/JS estático)
- **Porta 5000**: Backend API (Flask)
- **Database**: Sem porta exposta (acesso interno via filesystem)

#### Performance e Recursos

Os containers são configurados para serem leves:
- **Frontend**: ~50MB (Nginx Alpine + arquivos estáticos)
- **Backend**: ~200MB (Python + dependências)
- **Database**: ~100MB (Python + SQLite)

#### Atualização da Aplicação

Quando houver atualizações no código:

```bash
# Parar containers
docker-compose down

# Puxar atualizações (se estiver usando git)
git pull origin main

# Reconstruir e iniciar
docker-compose up --build
```

#### Backup do Banco de Dados

Para fazer backup do banco de dados SQLite:

```bash
# Copiar o arquivo do banco de dados do container
docker cp financial-dashboard-db:/app/financial.db ./backup_financial.db

# Restaurar backup
docker cp ./backup_financial.db financial-dashboard-db:/app/financial.db
```

### Instalação Local (Desenvolvimento)

Esta opção é recomendada para desenvolvimento e testes locais, quando você deseja modificar o código e ver as alterações em tempo real.

> **⚠️ Atenção**: O backend precisa estar rodando em um terminal **dedicado** durante todo o uso. Se ele for interrompido, o frontend exibirá "Erro ao conectar com o servidor". Use sempre uma janela de terminal separada, o script `start-all.bat`, ou o modo debug do VS Code (`F5`).

#### 🚀 Executar no VS Code (Recomendado para desenvolvimento)

O projeto inclui configurações prontas para o VS Code:

1. **Abra a pasta no VS Code**: `File → Open Folder → financial-dashboard`
2. **Instale as extensões recomendadas** (popup automático)
3. **Pressione `F5`** para iniciar o backend com debugger
4. **`Ctrl+Shift+P` → "Tasks: Run Task" → "Abrir Frontend"** para abrir a interface

Ver guia completo em [.vscode/GUIDE.md](.vscode/GUIDE.md)

#### Execução Manual

#### Pré-requisitos Locais

Certifique-se de ter instalado:

- **Python 3.11+**: [Download Python](https://www.python.org/downloads/)
- **Git**: [Download Git](https://git-scm.com/downloads)
- **Navegador web** (Chrome, Firefox, Edge, etc.)

> **Nota**: Node.js não é necessário para execução local, pois o frontend é um arquivo HTML estático. Só é necessário se você quiser usar um servidor web local opcional (ex: `npx http-server`).

#### Passo 1: Configurar o Banco de Dados

O banco de dados SQLite precisa ser inicializado antes de iniciar o backend:

```bash
# Navegar até o diretório do database
cd database

# Executar o script de inicialização
python init_db.py
```

**O que este script faz:**
- Cria o arquivo `financial.db` se não existir
- Executa o schema SQL definido em `schema.sql`
- Cria as tabelas: `user`, `quote`, `conversion`
- Cria índices para performance
- Mostra confirmação das tabelas criadas

**Saída esperada:**
```
Inicializando banco de dados Financial Dashboard...
[OK] Banco de dados inicializado com sucesso: /caminho/financial.db
[OK] Schema executado: /caminho/schema.sql
[OK] Tabelas criadas: ['user', 'sqlite_sequence', 'quote', 'conversion']

Banco de dados pronto para uso!
```

#### Passo 2: Configurar o Backend

O backend é a API REST que processa todas as requisições:

```bash
# Navegar até o diretório do backend
cd backend

# Criar ambiente virtual Python (venv)
python -m venv venv

# Ativar o ambiente virtual
# No Linux/Mac:
source venv/bin/activate
# No Windows:
venv\Scripts\activate

# Instalar as dependências Python
pip install -r requirements.txt
```

**Dependências instaladas:**
- `Flask 3.0.0`: Framework web
- `Flask-CORS 4.0.0`: Suporte a CORS
- `Flask-SQLAlchemy 3.1.1`: ORM para banco de dados
- `flasgger 0.9.7.1`: Documentação Swagger
- `requests 2.31.0`: Cliente HTTP
- `python-dotenv 1.0.0`: Variáveis de ambiente
- `Werkzeug 3.0.1`: Utilitários de segurança

**Iniciar o servidor Flask:**

```bash
# Iniciar a aplicação em modo desenvolvimento
python app.py
```

**Saída esperada:**
```
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in production deployment.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://192.168.x.x:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: xxx-xxx-xxx
```

**Testar o backend:**
Abra outro terminal e execute:
```bash
curl http://localhost:5000/api/health
```

#### Passo 3: Configurar o Frontend

O frontend é uma interface web desenvolvida com HTML, CSS e JavaScript (com Bootstrap via CDN). Não é necessário instalar dependências — basta abrir o arquivo no navegador:

```bash
# Navegar até o diretório do frontend
cd frontend/public
```

Abra o arquivo `index.html` diretamente no navegador:

- **Windows**: Dê um duplo clique no arquivo `frontend\public\index.html` ou execute:
```powershell
start index.html
```

- **Linux/Mac**:
```bash
open index.html        # macOS
xdg-open index.html    # Linux
```

O frontend se conectará automaticamente ao backend em `http://localhost:5000`.

> **Alternativa com servidor web local (opcional)**: Se preferir servir o arquivo via HTTP em vez de abrir diretamente, você pode usar:
> ```bash
> # Com Python (na pasta frontend/public)
> python -m http.server 3000
>
> # Com Node.js (se instalado)
> npx http-server -p 3000
> ```
> E acessar `http://localhost:3000`

#### Passo 4: Acessar a Aplicação

Com o backend rodando:

- **Frontend**: Abra o arquivo `frontend/public/index.html` no navegador (ou `http://localhost:3000` se usar servidor web local)
- **Backend API**: http://localhost:5000
- **Documentação Swagger**: http://localhost:5000/docs

#### Ordem de Inicialização Recomendada

Para evitar erros, siga esta ordem:

1. **Database**: `cd database && python init_db.py` (apenas na primeira vez)
2. **Backend**: `cd backend && source venv/bin/activate && python app.py`
3. **Frontend**: Abra `frontend/public/index.html` no navegador

#### ⚠️ Importante: Mantendo o Backend Rodando

O backend precisa estar rodando **constantemente** em um terminal para o frontend funcionar. Se o processo do Flask for interrompido, o frontend mostrará "Erro ao conectar com o servidor".

**Não execute o backend dentro de uma IDE/assistente de IA em modo background** — o processo pode ser encerrado quando a sessão do assistente termina. Prefira sempre:

- **Terminal dedicado**: Abra uma janela de terminal separada e mantenha `python app.py` rodando nela
- **Script `start-all.bat`**: Dá duplo clique e abre o backend em uma janela própria do Windows + o frontend no navegador automaticamente
- **VS Code**: Use `F5` ou a task "Iniciar Backend API" — o processo fica no terminal integrado do VS Code

Para verificar se o backend está ativo:
```bash
curl http://localhost:5000/api/health
# Deve retornar: {"status": "healthy", "timestamp": "..."}
```

#### Scripts de Inicialização Rápida (Windows)

Para facilitar, o projeto inclui scripts que automatizam a inicialização:

```powershell
# Iniciar apenas o backend
.\start-backend.bat
# ou
.\start-backend.ps1

# Iniciar tudo (backend + abre o frontend no navegador)
.\start-all.bat
# ou
.\start-all.ps1
```

> **Nota**: Se o PowerShell bloquear os scripts `.ps1`, use os arquivos `.bat` (duplo clique) ou execute `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser` uma única vez.

#### Execução no VS Code

Se estiver usando VS Code, a forma mais prática é:

1. Abra a pasta `financial-dashboard` no VS Code
2. Pressione `F5` para iniciar o backend com debugger, **ou**
3. `Ctrl+Shift+P` → "Tasks: Run Task" → selecione:
   - **"Inicializar Database"** — primeira vez
   - **"Iniciar Backend API"** — inicia o Flask
   - **"Abrir Frontend"** — abre o `index.html` no navegador

O backend fica rodando no terminal integrado do VS Code e só para quando você fechar o VS Code ou parar a tarefa.

#### Parar os Serviços

```bash
# Parar o Flask (Ctrl+C no terminal do backend)
# Fechar o frontend (basta fechar a aba/janela do navegador)

# Desativar o ambiente virtual (no terminal do backend)
deactivate
```

#### Troubleshooting Local

**Erro: "python: command not found"**
- Verifique se o Python está instalado: `python --version`
- No Windows, pode ser necessário usar `py` em vez de `python`

**Erro: "Erro ao conectar com o servidor" no frontend**
- Verifique se o backend está rodando em `http://localhost:5000`
- Teste o health check: `curl http://localhost:5000/api/health`
- **Se o backend parou**: reinicie-o em um terminal dedicado (`cd backend && python app.py`), use `start-all.bat`, ou `F5` no VS Code. Veja a seção "⚠️ Importante: Mantendo o Backend Rodando" acima.

**Erro: "ModuleNotFoundError"**
- Certifique-se de ativou o venv no backend
- Reinstale as dependências: `pip install -r requirements.txt`

**Erro: "EADDRINUSE: address already in use"**
- Outro processo está usando a porta 3000 ou 5000
- Mude as portas ou pare o processo conflitante

**Erro: CORS no navegador**
- Verifique se o backend está rodando
- O CORS deve estar habilitado no Flask (já configurado)

#### Configuração de Variáveis de Ambiente (Opcional)

Para configurações avançadas, você pode criar um arquivo `.env` no backend:

```bash
cd backend
cp .env.example .env
# Edite o arquivo .env com suas configurações
```

#### Diferenças entre Docker e Local

| Aspecto | Docker | Local |
|---------|--------|-------|
| **Isolamento** | Total (containers isolados) | Parcial (ambiente compartilhado) |
| **Reprodutibilidade** | Alta (mesma imagem sempre) | Média (depende do ambiente) |
| **Performance** | Leve overhead | Nativa (mais rápido) |
| **Debugging** | Mais complexo | Mais simples |
| **Deploy** | Pronto para produção | Requer configuração extra |
| **Portas** | Configuradas no docker-compose.yml | Arquivo HTML estático (frontend), 5000 (backend) |

## 📖 Uso

### Primeiro Acesso

1. Com o backend rodando, abra o arquivo `frontend/public/index.html` no navegador (ou acesse http://localhost se estiver usando Docker)
2. Clique em "Não tem conta? Cadastre-se"
3. Preencha o formulário de registro
4. Faça login com suas credenciais

### Conversão de Moedas

1. No Dashboard, acesse a aba "Conversor"
2. Digite o valor em Reais (BRL)
3. Clique em "Converter"
4. O resultado em Dólares (USD) será exibido
5. Clique em "Salvar Conversão" para guardar o histórico

### Cotações

1. No Dashboard, acesse a aba "Cotações"
2. Selecione o par de moedas desejado (USD/BRL, EUR/BRL, GBP/BRL, JPY/BRL)
3. Clique em "Carregar Taxa de Câmbio" para obter a cotação atual do Yahoo Finance
4. A taxa aparecerá automaticamente no campo "Taxa de Câmbio Atual"
5. Clique em "Salvar Cotação"

### Perfil

1. No Dashboard, acesse a aba "Perfil"
2. Clique em "Editar" para modificar suas informações
3. Salve as alterações

## 🌐 Uso via Swagger UI

Além da interface web (frontend), todas as funcionalidades da aplicação podem ser testadas diretamente pela **documentação interativa Swagger UI**, que permite executar chamadas reais à API pelo navegador.

### Acessando o Swagger

Com o backend rodando, acesse:

```
http://localhost:5000/docs
```

Você verá a documentação interativa com todos os endpoints organizados por tags, nesta ordem: **Usuários**, **Autenticação**, **Conversões**, **Cotações**, **API Externa** e **Sistema**.

Os endpoints com corpo JSON já trazem **exemplos de preenchimento** — ao clicar em "Try it out", o campo vem preenchido com valores válidos que você pode editar.

### Como usar um endpoint no Swagger

1. Expanda o endpoint desejado (ex: `POST /api/register`)
2. Clique no botão **"Try it out"**
3. Preencha os parâmetros ou o corpo da requisição (body)
4. Clique em **"Execute"**
5. Veja a resposta real da API (código HTTP + JSON de retorno)

### Exemplo de fluxo completo pelo Swagger

#### 1. Registrar um usuário

Endpoint: `POST /api/register`

```json
{
  "name": "Maria Silva",
  "email": "maria@email.com",
  "password": "senha123"
}
```

Resposta esperada (`201`):
```json
{
  "message": "Usuário criado com sucesso",
  "user": {
    "id": 1,
    "name": "Maria Silva",
    "email": "maria@email.com"
  }
}
```

#### 2. Fazer login

Endpoint: `POST /api/login`

```json
{
  "email": "maria@email.com",
  "password": "senha123"
}
```

Resposta esperada (`200`): retorna os dados do usuário, incluindo o `id` que será usado nas próximas chamadas.

#### 3. Converter e salvar no histórico

Endpoint: `POST /api/conversions`

Basta informar o usuário e o valor em reais — a taxa é buscada automaticamente no Yahoo Finance e o valor em USD calculado:

```json
{
  "user_id": 1,
  "amount_brl": 500
}
```

Resposta esperada (`201`): retorna a conversão salva com `amount_usd` e `rate` calculados.

#### 4. Salvar uma cotação

Endpoint: `POST /api/quotes`

Basta informar o usuário e o par de moedas — a taxa é buscada automaticamente no Yahoo Finance:

```json
{
  "user_id": 1,
  "currency_pair": "USDBRL"
}
```

Resposta esperada (`201`): retorna a cotação salva com `rate` preenchido automaticamente (ex: `5.18`).

Pares de moedas aceitos: `USDBRL`, `EURBRL`, `GBPBRL`, `JPYBRL`

#### 5. Consultar dados do Yahoo Finance

Endpoint: `GET /api/external/yahoo-finance/USDBRL=X`

Basta clicar em "Try it out", digitar `USDBRL=X` no campo `symbol` e executar — retorna dados históricos simulados da cotação.

#### 6. Listar e excluir registros

- `GET /api/quotes/1` — lista as cotações do usuário de `id` 1
- `GET /api/conversions/1` — lista as conversões do usuário de `id` 1
- `DELETE /api/quotes/{quote_id}` — exclui uma cotação
- `DELETE /api/conversions/{conversion_id}` — exclui uma conversão
- `PUT /api/users/1` — atualiza nome/email do usuário

### Observações

- O Swagger é ideal para **testar a API** sem precisar do frontend ou de ferramentas externas (Postman, Insomnia)
- Todas as respostas são em **JSON**
- A especificação OpenAPI bruta está disponível em `http://localhost:5000/apispec.json`

## 🔧 Configuração

### Variáveis de Ambiente

#### Backend (.env)
```env
FLASK_APP=app.py
FLASK_ENV=development
SECRET_KEY=your-secret-key-here-change-in-production
DATABASE_URL=sqlite:///../database/financial.db
```

#### Frontend
A URL da API está configurada na constante `API_URL` no arquivo `frontend/public/index.html`:
```javascript
const API_URL = 'http://localhost:5000';
```

## 📚 Documentação da API

A documentação interativa da API está disponível em:
- **Swagger UI**: http://localhost:5000/docs — permite testar todos os endpoints diretamente pelo navegador (veja a seção [🌐 Uso via Swagger UI](#-uso-via-swagger-ui))
- **OpenAPI Spec**: http://localhost:5000/apispec.json

### Endpoints Principais

Os endpoints aparecem no Swagger na mesma ordem das seções abaixo:

#### Usuários
- `GET /api/users/<id>` - Obter usuário
- `PUT /api/users/<id>` - Atualizar usuário

#### Autenticação
- `POST /api/register` - Registrar usuário
- `POST /api/login` - Login

#### Conversões
- `POST /api/conversions` - Converter e salvar conversão (calcula automaticamente com a taxa do Yahoo Finance)
- `GET /api/conversions/<user_id>` - Listar conversões
- `DELETE /api/conversions/<id>` - Excluir conversão
- `POST /api/convert` - Apenas visualizar conversão sem salvar — *endpoint interno usado pelo frontend (não aparece no Swagger)*

#### Cotações
- `POST /api/quotes` - Buscar taxa no Yahoo Finance e salvar cotação
- `GET /api/quotes/<user_id>` - Listar cotações
- `DELETE /api/quotes/<id>` - Excluir cotação

#### API Externa
- `GET /api/external/yahoo-finance/<symbol>` - Dados Yahoo Finance

#### Sistema
- `GET /api/health` - Verificar saúde da API

## 🐳 Docker

### Visão Geral da Arquitetura Docker

O projeto utiliza uma arquitetura de microserviços containerizada com Docker, onde cada módulo executa em seu próprio container isolado:

1. **financial-dashboard-frontend**: HTML/JS estático + Nginx
   - Serve a interface web compilada
   - Porta 80 exposta para acesso externo
   - Imagem otimizada com Alpine Linux

2. **financial-dashboard-backend**: Python + Flask
   - API REST com documentação Swagger
   - Porta 5000 exposta para comunicação
   - Conecta-se ao database via volume compartilhado

3. **financial-dashboard-db**: SQLite
   - Banco de dados persistente
   - Sem porta exposta (acesso interno)
   - Volume para persistência de dados

### Rede e Comunicação

Todos os containers estão conectados a uma rede bridge chamada `financial-network`, permitindo comunicação segura entre os serviços:

- Frontend → Backend (via HTTP na porta 5000 interna)
- Backend → Database (via filesystem compartilhado)
- Backend → Yahoo Finance (via internet)

### Comandos Adicionais Docker

```bash
# Ver status dos containers
docker-compose ps

# Ver logs em tempo real
docker-compose logs -f

# Ver logs de um serviço específico
docker-compose logs -f backend

# Reiniciar todos os containers
docker-compose restart

# Parar e remover containers (mantém volumes)
docker-compose down

# Parar e remover tudo (incluindo volumes)
docker-compose down -v

# Reconstruir um container específico
docker-compose up --build backend

# Executar comando em um container
docker-compose exec backend python --version
docker-compose exec frontend nginx -v

# Ver uso de recursos dos containers
docker stats
```

## 🧪 Testes

### Testar a API

```bash
# Health check
curl http://localhost:5000/api/health

# Registrar usuário
curl -X POST http://localhost:5000/api/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@example.com","password":"password123"}'

# Login
curl -X POST http://localhost:5000/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}'
```

### Testar o Frontend

Com o backend rodando em `http://localhost:5000`, abra o arquivo `frontend/public/index.html` no navegador (ou acesse `http://localhost` se estiver usando Docker) e siga o fluxo de uso descrito acima.

## 🔒 Segurança

- Senhas armazenadas com hash (pbkdf2:sha256)
- CORS configurado para comunicação segura
- Validação de dados no cliente e servidor
- Tratamento adequado de erros

## 📂 Estrutura do Projeto

```
financial-dashboard/
├── backend/              # API REST (Python + Flask)
│   ├── app.py          # Aplicação Flask
│   ├── requirements.txt # Dependências Python
│   ├── Dockerfile      # Configuração Docker
│   └── README.md       # Documentação do Backend
├── frontend/            # Interface web estática
│   ├── public/         # Arquivos estáticos (index.html)
│   ├── nginx.conf      # Configuração Nginx
│   ├── Dockerfile      # Configuração Docker
│   └── README.md       # Documentação do Frontend
├── database/            # Banco de dados SQLite
│   ├── schema.sql      # Schema do banco
│   ├── init_db.py      # Script de inicialização
│   ├── Dockerfile      # Configuração Docker
│   └── README.md       # Documentação do Database
├── .vscode/             # Configurações do VS Code
│   ├── launch.json     # Debug com F5
│   ├── tasks.json      # Tarefas automatizadas
│   ├── settings.json   # Configurações do workspace
│   ├── extensions.json # Extensões recomendadas
│   └── GUIDE.md        # Guia de uso no VS Code
├── docker-compose.yml   # Orquestração Docker
├── start-backend.bat    # Script Windows: inicia backend
├── start-backend.ps1    # Script PowerShell: inicia backend
├── start-all.bat        # Script Windows: inicia tudo
├── start-all.ps1        # Script PowerShell: inicia tudo
├── ARCHITECTURE.md      # Documentação de arquitetura
└── README.md           # Este arquivo
```

## 🤝 Contribuindo

1. Fork o projeto
2. Crie uma branch para sua feature (`git checkout -b feature/MinhaFeature`)
3. Commit suas mudanças (`git commit -m 'Adiciona MinhaFeature'`)
4. Push para a branch (`git push origin feature/MinhaFeature`)
5. Abra um Pull Request

## 📝 Licença

Este projeto é desenvolvido para fins educacionais.

## 👥 Autores

Este projeto foi desenvolvido para fins acadêmicos por Evelyn Oliveira de Souza, estudante de pós graduação em Desenvolvimento Full Stack pela Puc Rio.
