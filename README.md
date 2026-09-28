# Financial Dashboard Frontend

Interface do usuário para o sistema de Dashboard Financeiro, desenvolvida com HTML, CSS e JavaScript utilizando o framework Bootstrap.

## Descrição

Este frontend fornece uma interface intuitiva para usuários gerenciarem suas cotações de moedas e conversões de BRL para USD. A aplicação é uma página web estática que se comunica com a API REST backend através de chamadas `fetch`, oferecendo uma experiência de usuário moderna e responsiva sem necessidade de build ou instalação de dependências.

## Funcionalidades

- **Autenticação**: Sistema de login e registro de usuários
- **Dashboard Financeiro**: Interface principal com visualização de dados
- **Conversor de Moedas**: Conversão em tempo real de BRL para USD com cotação do dia
- **Gerenciamento de Cotações**: Carregar taxas do Yahoo Finance e salvar/excluir cotações
- **Histórico de Conversões**: Visualizar e excluir conversões salvas
- **Gerenciamento de Perfil**: Atualizar informações do usuário

## Estrutura de Telas

- **Tela de Login** - Formulário de autenticação
- **Tela de Cadastro** - Formulário de registro de novos usuários
- **Dashboard** - Interface principal com abas:
  - **Conversor**: Conversão de BRL para USD
  - **Cotações**: Carregamento e gerenciamento de cotações
  - **Perfil**: Visualização e edição do perfil

## Rotas da API Utilizadas

A aplicação faz chamadas à API backend utilizando diferentes métodos HTTP:

### Métodos HTTP Implementados

- **GET**:
  - `/api/quotes/<user_id>` - Obter cotações do usuário
  - `/api/conversions/<user_id>` - Obter conversões do usuário
  - `/api/external/yahoo-finance/<symbol>` - Obter dados do Yahoo Finance

- **POST**:
  - `/api/login` - Autenticar usuário
  - `/api/register` - Registrar novo usuário
  - `/api/quotes` - Salvar nova cotação
  - `/api/conversions` - Salvar nova conversão
  - `/api/convert` - Converter BRL para USD

- **PUT**:
  - `/api/users/<user_id>` - Atualizar informações do usuário

- **DELETE**:
  - `/api/quotes/<quote_id>` - Excluir cotação
  - `/api/conversions/<conversion_id>` - Excluir conversão

## Instalação

### Pré-requisitos

- **Navegador web** (Chrome, Firefox, Edge, Safari, etc.)
- **Backend rodando** em `http://localhost:5000` (veja `../backend/README.md`)

> **Nota**: Não é necessário Node.js, npm ou qualquer instalação de dependências. O frontend é um arquivo HTML estático que funciona diretamente no navegador.

### Configuração do Ambiente Local

1. Clone o repositório:
```bash
git clone <url-do-repositorio>
cd financial-dashboard/frontend
```

2. Certifique-se de que o backend está rodando:
```bash
# Em outro terminal, no diretório ../backend
python app.py
```

3. Pronto! Nenhuma instalação adicional é necessária.

## Execução

### Modo Local (Recomendado)

Abra o arquivo `public/index.html` diretamente no navegador:

- **Windows**: Dê um duplo clique no arquivo ou execute:
```powershell
start public\index.html
```

- **Linux/Mac**:
```bash
open public/index.html        # macOS
xdg-open public/index.html    # Linux
```

### Modo Servidor Web Local (Opcional)

Se preferir servir o frontend via HTTP (útil para evitar restrições de `file://` em alguns navegadores):

```bash
# Com Python (na pasta frontend/public)
cd public
python -m http.server 3000
```

E acesse `http://localhost:3000`

Outras opções de servidor estático:
```bash
# Com Node.js (se instalado)
npx http-server public -p 3000

# Com PHP (se instalado)
php -S localhost:3000 -t public
```

## Docker

### Construir a imagem Docker
```bash
docker build -t financial-dashboard-frontend .
```

### Executar o container
```bash
docker run -p 80:80 financial-dashboard-frontend
```

A aplicação estará disponível em `http://localhost`

## Estrutura do Projeto

```
frontend/
├── public/
│   └── index.html          # Aplicação completa (HTML + CSS + JS)
├── nginx.conf              # Configuração Nginx para Docker
├── Dockerfile              # Configuração Docker
└── README.md              # Documentação
```

## Componentes da Interface

### Tela de Autenticação
Formulários de login e registro com validação e alternância entre telas.

### Dashboard
Interface principal com navegação por abas (Bootstrap Nav Tabs):

- **Conversor**: Campo para valor em BRL, botão de conversão usando taxa atual do Yahoo Finance, resultado com opção de salvar, e histórico de conversões com opção de exclusão.
- **Cotações**: Seleção de par de moedas (USD/BRL, EUR/BRL, GBP/BRL, JPY/BRL), botão para carregar taxa atual do Yahoo Finance, campo readonly exibindo a taxa obtida, botão de salvar e histórico de cotações com opção de exclusão.
- **Perfil**: Visualização dos dados do usuário (nome, email, ID) e modo de edição.

## API Externa - Yahoo Finance

### Informações sobre a API

**Licença de Uso**: A API do Yahoo Finance é gratuita para uso pessoal e educacional.

**Cadastro**: Não é necessário cadastro para uso básico.

**Rota Utilizada**:
- Endpoint interno do backend: `GET /api/external/yahoo-finance/<symbol>`
- Símbolos suportados: `USDBRL=X`, `EURBRL=X`, `GBPBRL=X`, `JPYBRL=X`
- A chamada é feita através do backend, que processa os dados antes de retornar ao frontend (sem redirecionamento)

**Funcionalidade**:
- Carregar taxas de câmbio atuais do Yahoo Finance
- Dados são processados pelo backend antes de serem exibidos no frontend

## Tecnologias Utilizadas

- **HTML5** - Estrutura da página
- **CSS3** - Estilos personalizados (gradientes, cards, animações)
- **JavaScript (ES6+)** - Lógica da aplicação, chamadas `fetch` assíncronas
- **Bootstrap 5.3.2** - Framework CSS via CDN (navbar, cards, formulários, abas, alertas)

## Desenvolvimento

### Testar Localmente
1. Inicie o backend: `cd ../backend && python app.py`
2. Abra `public/index.html` no navegador
3. Registre um novo usuário ou faça login
4. Teste as funcionalidades de conversão e cotações

### Estilos Personalizados
Os estilos são definidos no bloco `<style>` dentro do próprio `index.html`:
- Gradiente de fundo roxo/azul
- Cards com sombra e bordas arredondadas
- Links em cor preta para contraste no fundo colorido
- Botões com gradiente personalizado

## Troubleshooting

### Erro: "Erro ao conectar com o servidor"
- Verifique se o backend está rodando: `curl http://localhost:5000/api/health`
- Se não estiver, inicie-o: `cd ../backend && python app.py`

### Erro: "CORS policy"
- Verifique se o backend tem CORS habilitado (já configurado no Flask)
- Se o problema persistir, sirva o frontend via servidor web local em vez de `file://`

### Página em branco ou sem estilos
- Verifique a conexão com internet (Bootstrap é carregado via CDN)
- Abra o console do navegador (F12) para verificar erros de JavaScript

## Configuração

A URL da API está configurada no bloco `<script>` do `index.html`:

```javascript
const API_URL = 'http://localhost:5000';
```

Se o backend estiver em outra porta ou host, altere essa constante.

## Segurança

- Senhas transmitidas via JSON para o backend (hash no servidor)
- Validação de formulários no cliente e servidor
- Sem armazenamento de senhas no navegador

## Navegadores Suportados

- Chrome (últimas 2 versões)
- Firefox (últimas 2 versões)
- Safari (últimas 2 versões)
- Edge (últimas 2 versões)

## Licença

Este projeto é desenvolvido para fins educacionais.
