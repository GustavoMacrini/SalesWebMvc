# SalesWebMvc

Aplicação web em **ASP.NET Core MVC** para gerenciar departamentos, vendedores e registros de vendas, com buscas de vendas por período. O projeto segue a arquitetura MVC com uma camada de serviços (`Services`) entre os controllers e o Entity Framework Core.

## Tecnologias

- .NET Core 2.1 (ASP.NET Core MVC 2.1, Razor)
- Entity Framework Core 2.1 (Code First + Migrations)
- MySQL (provider `Pomelo.EntityFrameworkCore.MySql`)
- Bootstrap 3 (tema Flatly) e jQuery

## Funcionalidades

### Departamentos
- Listar, visualizar detalhes, criar, editar e excluir departamentos (CRUD completo).

### Vendedores (Sellers)
- Listar, visualizar detalhes, criar, editar e excluir vendedores.
- Cada vendedor pertence a um departamento (selecionado em uma lista nos formulários).
- Validações de formulário:
  - **Nome**: obrigatório, de 3 a 60 caracteres.
  - **E-mail**: obrigatório e em formato válido.
  - **Data de nascimento**: obrigatória.
  - **Salário base**: obrigatório, entre 100 e 50.000.
- Não permite excluir um vendedor que tenha vendas registradas (integridade referencial), exibindo uma mensagem de erro.
- Tratamento de erros com página de erro personalizada (Id não informado, Id não encontrado, conflito de concorrência, etc.).
- Operações assíncronas (`async`/`await`).

### Vendas (Sales Records)
- **Simple Search**: lista as vendas em um intervalo de datas (data mínima e máxima), ordenadas da mais recente para a mais antiga, mostrando data, valor, vendedor, departamento e status.
- **Grouping Search**: mesma busca por período, mas agrupada por departamento, exibindo o total de vendas de cada departamento no período.
- Se as datas não forem informadas, o padrão é de 1º de janeiro do ano atual até hoje.
- Status da venda: `Pending`, `Billed` e `Canceled`.

### Outros
- Carga inicial de dados (seed) automática em ambiente de desenvolvimento: 4 departamentos, 6 vendedores e 30 vendas de exemplo.
- Cultura da aplicação configurada para `en-US` (formatação de números e datas).

## Estrutura do projeto

```
SalesWebMvc/
├── Controllers/    # Home, Departments, Sellers, SalesRecords
├── Data/           # DbContext e SeedingService
├── Migrations/     # Migrations do EF Core
├── Models/         # Department, Seller, SalesRecord, Enums, ViewModels
├── Services/       # SellerService, DepartmentService, SalesRecordService e exceções
├── Views/          # Views Razor
└── wwwroot/        # CSS, JS e imagens
```

## Pré-requisitos

- [.NET Core SDK 2.1](https://dotnet.microsoft.com/download/dotnet/2.1) (o projeto tem como alvo `netcoreapp2.1`)
- [MySQL Server](https://dev.mysql.com/downloads/mysql/) em execução
- Git
- (Opcional) Visual Studio 2017/2019/2022 ou VS Code

> O .NET Core 2.1 não tem mais suporte oficial. Se você usa um SDK mais novo, instale também o SDK 2.1 e o runtime 2.1 para compilar e rodar o projeto.

## Como executar

### 1. Clonar o repositório

```bash
git clone https://github.com/GustavoMacrini/SalesWebMvc.git
cd SalesWebMvc/SalesWebMvc
```

### 2. Configurar o banco de dados

A connection string está em `SalesWebMvc/appsettings.json`:

```json
"ConnectionStrings": {
  "SalesWebMvcContext": "server=localhost;userid=developer;password=1234567;database=saleswebmvcappdb"
}
```

Você pode criar o usuário que o projeto espera:

```sql
CREATE USER 'developer'@'localhost' IDENTIFIED BY '1234567';
GRANT ALL PRIVILEGES ON *.* TO 'developer'@'localhost';
FLUSH PRIVILEGES;
```

Ou ajustar `userid`, `password` e `server` na connection string para o seu ambiente. O banco `saleswebmvcappdb` é criado automaticamente pelas migrations.

### 3. Criar as tabelas (migrations)

Na pasta do projeto (onde está o `SalesWebMvc.csproj`):

```bash
dotnet restore
dotnet ef database update
```

No Visual Studio, também é possível usar o Package Manager Console:

```powershell
Update-Database
```

### 4. Executar a aplicação

```bash
dotnet run
```

A aplicação sobe em:

- https://localhost:5001
- http://localhost:5000

No Visual Studio, basta abrir `SalesWebMvc.sln` e pressionar **F5** (o perfil `SalesWebMvc` usa as portas acima; o perfil `IIS Express` usa a porta 42588).

### 5. Dados de exemplo

O seed só roda quando `ASPNETCORE_ENVIRONMENT=Development` (já definido em `launchSettings.json`) e apenas se o banco estiver vazio. Ele é executado ao iniciar a aplicação, depois que as tabelas existirem.

## Rotas principais

| Rota | Descrição |
|------|-----------|
| `/` | Página inicial |
| `/Departments` | CRUD de departamentos |
| `/Sellers` | CRUD de vendedores |
| `/SalesRecords` | Formulários de busca de vendas |
| `/SalesRecords/SimpleSearch?minDate=&maxDate=` | Busca simples por período |
| `/SalesRecords/GroupingSearch?minDate=&maxDate=` | Busca agrupada por departamento |

## Modelo de dados

- **Department** (1) — (N) **Seller** (1) — (N) **SalesRecord**
- `SalesRecord`: data, valor e status (`Pending`, `Billed`, `Canceled`).

## Autor

[Gustavo Macrini](https://github.com/GustavoMacrini)
