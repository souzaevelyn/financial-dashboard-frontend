# Arquitetura do Sistema Financial Dashboard

## Visão Geral

O sistema Financial Dashboard é composto por três módulos principais que se comunicam através do protocolo REST:

1. **Frontend** (HTML/CSS/JavaScript + Bootstrap)
2. **Backend API** (Python + Flask)
3. **Database** (SQLite)

## Diagrama de Arquitetura

```
┌─────────────────────────────────────────────────────────────────┐
│                         USUÁRIO                                 │
│                    (Navegador Web)                              │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         │ HTTP/HTTPS
                         │
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                      FRONTEND MODULE                            │
│             (HTML/CSS/JavaScript + Bootstrap)                   │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│  │   Login      │  │  Cadastro    │  │  Dashboard   │           │
│  │    Tela      │  │    Tela      │  │    Tela      │           │
│  └──────────────┘  └──────────────┘  └──────────────┘           │
│         │                  │                  │                 │
│         └──────────────────┴──────────────────┘                 │
│                            │                                    │
│                    Fetch API (HTTP Client)                      │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ REST API Calls
                             │ (GET, POST, PUT, DELETE)
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                      BACKEND API MODULE                         │
│                     (Python + Flask)                            │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   Flask Application                     │    │
│  │                                                         │    │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐      │    │
│  │  │   Auth      │  │   Quotes    │  │Conversions  │      │    │
│  │  │  Routes     │  │   Routes    │  │   Routes    │      │    │
│  │  └─────────────┘  └─────────────┘  └─────────────┘      │    │
│  │                                                         │    │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐      │    │
│  │  │   Users     │  │  External   │  │   System    │      │    │
│  │  │  Routes     │  │    API      │  │  Routes     │      │    │
│  │  └─────────────┘  └─────────────┘  └─────────────┘      │    │
│  └─────────────────────────────────────────────────────────┘    │
│                            │                                    │
│                    SQLAlchemy ORM                               │
│                            │                                    │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ SQL Queries
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                     DATABASE MODULE                             │
│                        (SQLite)                                 │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│  │    users     │  │    quotes    │  │ conversions  │           │
│  │    table     │  │    table     │  │    table     │           │
│  └──────────────┘  └──────────────┘  └──────────────┘           │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              financial.db (SQLite Database)              │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                             │
                             │ External API Calls
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                   YAHOO FINANCE API                             │
│                   (Serviço Externo)                             │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │     Dados Históricos de Cotações de Moedas              │    │
│  │     (USDBRL, EURBRL, GBPBRL, etc.)                      │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

## Fluxo de Dados

### 1. Autenticação do Usuário
```
Usuário → Frontend (Login) → Backend API (/api/login) → Database (users table)
→ Backend API (validação) → Frontend (token/session) → Dashboard
```

### 2. Conversão de Moedas
```
Usuário → Frontend (Dashboard) → Backend API (/api/convert)
→ Backend API (cálculo) → Frontend (resultado)
→ Backend API (/api/conversions) → Database (conversions table)
```

### 3. Salvamento de Cotações
```
Usuário → Frontend (Dashboard) → Backend API (/api/quotes)
→ Database (quotes table) → Frontend (atualização)
```

### 4. Consulta ao Yahoo Finance
```
Usuário → Frontend (Dashboard) → Backend API (/api/external/yahoo-finance)
→ Yahoo Finance API → Backend API (processamento) → Frontend (dados)
```

## Tecnologias por Módulo

### Frontend Module
- **Linguagens**: HTML5, CSS3, JavaScript (ES6+)
- **UI Library**: Bootstrap 5.3.2 (via CDN)
- **HTTP Client**: Fetch API (nativa do navegador)
- **Navegação**: Telas controladas por JavaScript (show/hide de seções)
- **Build**: Não requer build — arquivo estático servido diretamente
- **Container**: Docker (Nginx + Alpine)

### Backend API Module
- **Language**: Python 3.11
- **Framework**: Flask 3.0.0
- **ORM**: Flask-SQLAlchemy 3.1.1
- **CORS**: Flask-CORS 4.0.0
- **Documentation**: Flasgger 0.9.7.1 (Swagger)
- **Security**: Werkzeug 3.0.1 (password hashing)
- **Container**: Docker (Python + Slim)

### Database Module
- **Database**: SQLite 3
- **Schema**: SQL schema com 3 tabelas principais
- **Indices**: Índices para performance
- **Container**: Docker (Python + Slim)

## Comunicação Entre Módulos

### Protocolo
- **Protocolo**: HTTP/HTTPS
- **Formato**: JSON
- **Portas**:
  - Frontend: arquivo estático (desenvolvimento), 80 (produção via Nginx)
  - Backend: 5000
  - Database: Acesso local via filesystem

### Métodos HTTP Utilizados
- **GET**: Obter dados (usuários, cotações, conversões)
- **POST**: Criar recursos (login, registro, cotações, conversões)
- **PUT**: Atualizar recursos (perfil do usuário)
- **DELETE**: Remover recursos (cotações, conversões)

## Endpoints da API

### Autenticação
- `POST /api/register` - Registro de usuário
- `POST /api/login` - Login de usuário

### Usuários
- `GET /api/users/<id>` - Obter dados do usuário
- `PUT /api/users/<id>` - Atualizar dados do usuário

### Cotações
- `POST /api/quotes` - Salvar cotação
- `GET /api/quotes/<user_id>` - Listar cotações do usuário
- `DELETE /api/quotes/<id>` - Excluir cotação

### Conversões
- `POST /api/conversions` - Salvar conversão
- `GET /api/conversions/<user_id>` - Listar conversões do usuário
- `DELETE /api/conversions/<id>` - Excluir conversão
- `POST /api/convert` - Converter BRL para USD

### API Externa
- `GET /api/external/yahoo-finance/<symbol>` - Obter dados do Yahoo Finance

### Sistema
- `GET /api/health` - Health check da API

## Docker Compose (Sugestão)

Para orquestrar os três containers, pode-se usar docker-compose:

```yaml
version: '3.8'

services:
  database:
    build: ./database
    volumes:
      - ./database/data:/app/data
    networks:
      - financial-network

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    depends_on:
      - database
    volumes:
      - ./database:/app/../database
    environment:
      - DATABASE_URL=sqlite:///../database/financial.db
    networks:
      - financial-network

  frontend:
    build: ./frontend
    ports:
      - "80:80"
    depends_on:
      - backend
    networks:
      - financial-network

networks:
  financial-network:
    driver: bridge
```

## Segurança

### Frontend
- Validação de formulários no cliente
- Armazenamento seguro de tokens
- Comunicação HTTPS em produção

### Backend
- Hash de senhas (pbkdf2:sha256)
- CORS configurado
- Validação de dados no servidor
- Tratamento de erros adequado

### Database
- Relacionamentos com CASCADE DELETE
- Índices para performance
- SQL injection protection via ORM

## Escalabilidade

### Horizontal Scaling
- Frontend: Múltiplas instâncias atrás de load balancer
- Backend: Múltiplas instâncias com shared database
- Database: Para produção, considerar migração para PostgreSQL ou MySQL

### Vertical Scaling
- Aumentar recursos dos containers
- Otimizar queries e índices
- Implementar caching (Redis)

## Monitoramento

### Logs
- Frontend: Console do navegador e logs do servidor
- Backend: Logs do Flask e erros da aplicação
- Database: Logs do SQLite

### Métricas
- Tempo de resposta das APIs
- Taxa de erro
- Uso de recursos dos containers

## Deploy Sugerido

### Desenvolvimento
- Frontend: Abrir `public/index.html` no navegador (arquivo estático)
- Backend: `python app.py` (porta 5000)
- Database: SQLite local

### Produção
- Frontend: Docker com Nginx (porta 80)
- Backend: Docker com Gunicorn (porta 5000)
- Database: Docker com volume persistente
- Proxy reverso: Nginx ou Apache
- SSL: Certificado TLS/SSL
