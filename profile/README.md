# 🚄 BulletOnRails 
### *Velocidade e estabilidade nos trilhos* 

![Version](https://img.shields.io/badge/version-0.1.0--alpha-blue)
![JDK](https://img.shields.io/badge/JDK-21%2B-green)
![License](https://img.shields.io/badge/license-MIT-red)

<p align="center">
  <img src="logo.png" alt="BulletOnRails Logo" width="200"/>
</p>

## 📋 Índice
- [Visão Geral](#-visão-geral)
- [Características](#-características)
- [Filosofias do Framework](#-filosofias-do-framework)
- [Primeiros Passos](#-primeiros-passos)
- [Estrutura do Projeto](#-estrutura-do-projeto)
- [Exemplos](#-exemplos)
- [Integração com PostgreSQL](#-integração-com-postgresql)
- [Virtual Threads](#-virtual-threads)
- [Contribuindo](#-contribuindo)
- [Roadmap](#-roadmap)
- [Licença](#-licença)

## 🌟 Visão Geral

BulletOnRails é um framework web Java moderno que combina a elegância e produtividade do Ruby on Rails com o poder e desempenho do ecossistema Java. Desenvolvido com foco em convenção sobre configuração, BulletOnRails permite que você construa aplicações web robustas com menos código e mais diversão.

> *"Se Ruby on Rails é um trem de alta velocidade, BulletOnRails é um trem-bala."*

## ✨ Características

- 🏛️ **Arquitetura MVC** - Separação clara de responsabilidades
- 🔄 **Convenção sobre Configuração** - Menos código, mais produtividade
- 🚄 **Alta Performance** - Otimizado para JIT e com suporte a GraalVM
- 🧵 **Virtual Threads** - Concorrência moderna com Project Loom 
- 🐘 **PostgreSQL First** - Suporte de primeira classe para PostgreSQL
- 💡 **API Expressiva** - Interface fluente e intuitiva
- 🛠️ **Scaffolding** - Geração de código automatizada
- 📦 **Zero Configuration** - Funciona imediatamente após instalação

## 🧠 Filosofias do Framework

BulletOnRails foi construído sobre três pilares fundamentais:

### 1️⃣ Convenção sobre Configuração
Seguimos convenções predefinidas para nomear classes, métodos e tabelas, reduzindo drasticamente a necessidade de configuração. Isso significa menos arquivos XML, menos anotações e mais código significativo.

### 2️⃣ Produtividade do Desenvolvedor
Cada aspecto do BulletOnRails foi projetado pensando na felicidade do desenvolvedor. De scaffolding poderoso a recarregamento a quente, queremos que você passe mais tempo criando e menos tempo configurando.

### 3️⃣ Desempenho sem Compromissos
Utilizando os recursos mais recentes do Java, como virtual threads e otimizações específicas para o compilador JIT, BulletOnRails oferece desempenho comparável a frameworks em Rust e Go, sem sacrificar a facilidade de uso.

## 🚀 Primeiros Passos

### Pré-requisitos
- JDK 21 ou superior
- Maven 3.8+ ou Gradle 7.0+
- PostgreSQL 14+ (recomendado)

### Instalação

#### Com Maven

```xml
<dependency>
    <groupId>io.bulletonrails</groupId>
    <artifactId>bulletonrails-core</artifactId>
    <version>0.1.0-alpha</version>
</dependency>
```

#### Com Gradle

```groovy
implementation 'io.bulletonrails:bulletonrails-core:0.1.0-alpha'
```

### Criando um novo projeto

Usando o CLI do BulletOnRails:

```bash
# Instalar CLI globalmente
npm install -g bulletonrails-cli

# Criar um novo projeto
bor new minha-aplicacao

# Navegar para o diretório do projeto
cd minha-aplicacao

# Iniciar o servidor de desenvolvimento
bor server
```

Seu aplicativo estará disponível em `http://localhost:3000`!

# 🏗️ Arquitetura e Componentes do BulletOnRails

<details>



## Visão Geral da Arquitetura

BulletOnRails é construído sobre uma arquitetura MVC (Model-View-Controller) robusta, com componentes adicionais que facilitam o desenvolvimento rápido de aplicações web Java. A seguir, detalhamos os principais componentes do framework e como eles interagem.

```
┌─────────────────────────────────────────┐
│              HTTP Request               │
└───────────────────┬─────────────────────┘
                    ▼
┌─────────────────────────────────────────┐
│             Router                      │
│  Mapeia URLs para ações do controlador  │
└───────────────────┬─────────────────────┘
                    ▼
┌─────────────────────────────────────────┐
│           Middleware Chain              │
│  Processamento de requisição em cadeia  │
└───────────────────┬─────────────────────┘
                    ▼
┌─────────────────────────────────────────┐
│            Controller                   │
│  Coordena a lógica de negócios          │
└─┬─────────────────────────────────────┬─┘
  │                                     │
  ▼                                     ▼
┌────────────────┐               ┌────────────────┐
│     Model      │               │     View       │
│  Dados e       │◄────────────► │  Apresentação  │
│  regras de     │               │  e templates   │
│  negócio       │               │                │
└────────────────┘               └────────────────┘
        ▲                                ▲
        │                                │
        ▼                                │
┌────────────────┐                       │
│   Persistência │                       │
│   Hibernate +  │                       │
│   PostgreSQL   │                       │
└────────────────┘                       │
                                         │
┌────────────────┐                       │
│ Virtual Threads│                       │
│  Concorrência  │───────────────────────┘
│   eficiente    │
└────────────────┘
```

## 🧩 Componentes Principais

### 1. Core Framework

O núcleo do BulletOnRails é composto pelos seguintes módulos:

#### 1.1 ApplicationServer

```java
public class ApplicationServer {
    private static final int DEFAULT_PORT = 3000;
    private final int port;
    private final Router router;
    private final ExecutorService executor;
    
    public ApplicationServer() {
        this(DEFAULT_PORT);
    }
    
    public ApplicationServer(int port) {
        this.port = port;
        this.router = new Router();
        // Usando virtual threads como executor padrão
        this.executor = Executors.newVirtualThreadPerTaskExecutor();
    }
    
    public void start() {
        // Iniciar servidor com Jetty/Netty
        // Configurar handlers para processar requisições usando virtual threads
    }
    
    public void stop() {
        // Parar servidor e liberar recursos
    }
}
```

#### 1.2 Router

```java
public class Router {
    private final Map<String, Map<HttpMethod, RouteHandler>> routes = new HashMap<>();
    
    public void addRoute(HttpMethod method, String path, RouteHandler handler) {
        routes.computeIfAbsent(path, k -> new EnumMap<>(HttpMethod.class))
              .put(method, handler);
    }
    
    public Optional<RouteMatch> matchRoute(HttpMethod method, String path) {
        // Implementa lógica de roteamento com suporte a parâmetros na URL
    }
    
    // Métodos auxiliares para definir rotas
    public void get(String path, RouteHandler handler) {
        addRoute(HttpMethod.GET, path, handler);
    }
    
    public void post(String path, RouteHandler handler) {
        addRoute(HttpMethod.POST, path, handler);
    }
    
    // ... outros métodos HTTP
}
```

#### 1.3 ControllerRegistry

```java
public class ControllerRegistry {
    private final Map<Class<?>, Object> controllers = new HashMap<>();
    
    public void registerControllers(String basePackage) {
        // Escaneia o pacote em busca de classes anotadas com @Controller
        // Instancia e registra os controladores encontrados
    }
    
    public void setupRoutes(Router router) {
        // Para cada controlador registrado, mapeia seus métodos anotados
        // como endpoints de API para rotas no router
    }
}
```

### 2. Model Layer

#### 2.1 BaseModel

```java
public abstract class BaseModel {
    // Métodos CRUD básicos
    public void save() {
        // Lógica para salvar o modelo no banco de dados
    }
    
    public void update(Map<String, Object> attributes) {
        // Atualiza o modelo com os atributos fornecidos
    }
    
    public void delete() {
        // Remove o modelo do banco de dados
    }
    
    // Métodos estáticos para consulta
    public static <T extends BaseModel> T find(Class<T> modelClass, Long id) {
        // Busca uma entidade por ID
    }
    
    public static <T extends BaseModel> List<T> all(Class<T> modelClass) {
        // Retorna todas as entidades do tipo
    }
    
    public static <T extends BaseModel> Query<T> where(Class<T> modelClass, String conditions, Object... params) {
        // Cria uma consulta com condições
    }
}
```

#### 2.2 Query Builder

```java
public class Query<T extends BaseModel> {
    private final Class<T> modelClass;
    private final StringBuilder queryBuilder;
    private final List<Object> parameters;
    private String orderByClause;
    private Integer limitValue;
    private Integer offsetValue;
    
    public Query(Class<T> modelClass, String whereClause, Object... params) {
        this.modelClass = modelClass;
        this.queryBuilder = new StringBuilder("WHERE ").append(whereClause);
        this.parameters = new ArrayList<>(Arrays.asList(params));
    }
    
    public Query<T> orderBy(String clause) {
        this.orderByClause = clause;
        return this;
    }
    
    public Query<T> limit(int limit) {
        this.limitValue = limit;
        return this;
    }
    
    public Query<T> offset(int offset) {
        this.offsetValue = offset;
        return this;
    }
    
    public List<T> execute() {
        // Constrói e executa a consulta SQL
    }
    
    public T first() {
        return limit(1).execute().stream().findFirst().orElse(null);
    }
}
```

#### 2.3 Transaction

```java
public class Transaction {
    public static void execute(Runnable action) {
        // Inicia uma transação de banco de dados
        try {
            // Executa a ação dentro da transação
            action.run();
            // Commit se bem-sucedida
        } catch (Exception e) {
            // Rollback em caso de erro
            throw new RuntimeException("Transaction failed", e);
        }
    }
    
    public static <T> T executeAndReturn(Supplier<T> action) {
        // Similar ao execute, mas retorna um valor
    }
}
```

### 3. View Layer

#### 3.1 ViewResolver

```java
public class ViewResolver {
    private final String viewPath;
    private final TemplateEngine templateEngine;
    
    public ViewResolver(String viewPath) {
        this.viewPath = viewPath;
        this.templateEngine = new TemplateEngine();
    }
    
    public String resolve(String viewName, Map<String, Object> model) {
        // Localiza o template apropriado e o renderiza com o modelo fornecido
    }
}
```

#### 3.2 TemplateEngine

```java
public class TemplateEngine {
    public String render(String template, Map<String, Object> model) {
        // Processa o template, substituindo variáveis pelos valores do modelo
    }
}
```

### 4. Controller Layer

#### 4.1 Controller Annotations

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Controller {
    String value() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Get {
    String value();
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Post {
    String value();
}

// ... outras anotações para métodos HTTP
```

#### 4.2 ControllerMethodInvoker

```java
public class ControllerMethodInvoker {
    public Object invoke(Object controller, Method method, Request request, Response response) {
        // Extrai parâmetros da requisição
        // Converte para os tipos esperados pelo método
        // Invoca o método do controlador
        // Processa o resultado (renderiza view, serializa JSON, etc.)
    }
}
```

### 5. Persistence Layer

#### 5.1 DatabaseConfiguration

```java
public class DatabaseConfiguration {
    private final Properties properties;
    
    public DatabaseConfiguration(String configPath) {
        // Carrega configurações do banco de dados
    }
    
    public Connection getConnection() {
        // Retorna uma conexão com o banco de dados
    }
    
    public static DatabaseConfiguration fromDefaultLocation() {
        // Carrega configuração padrão (config/database.properties)
    }
}
```

#### 5.2 ORM Integration

```java
public class HibernateConfiguration {
    private static SessionFactory sessionFactory;
    
    public static void initialize(DatabaseConfiguration config) {
        // Configura o Hibernate com as convenções do BulletOnRails
        // - Mapeamento de classes para tabelas (singular para plural)
        // - Mapeamento de propriedades para colunas (camelCase para snake_case)
        // - Configuração de conexão com PostgreSQL
    }
    
    public static SessionFactory getSessionFactory() {
        if (sessionFactory == null) {
            throw new IllegalStateException("Hibernate not initialized");
        }
        return sessionFactory;
    }
}
```

#### 5.3 Migration Support

```java
public class Migration {
    private final String version;
    private final String description;
    private final String sql;
    
    public Migration(String version, String description, String sql) {
        this.version = version;
        this.description = description;
        this.sql = sql;
    }
    
    public void execute(Connection connection) {
        // Executa o SQL da migração
    }
}

public class MigrationManager {
    public void runMigrations(String migrationsPath) {
        // Identifica e executa migrações pendentes
    }
}
```

### 6. Concurrency with Virtual Threads

#### 6.1 VirtualThreadExecutor

```java
public class VirtualThreadExecutor {
    private final ExecutorService executor;
    
    public VirtualThreadExecutor() {
        this.executor = Executors.newVirtualThreadPerTaskExecutor();
    }
    
    public <T> CompletableFuture<T> submit(Supplier<T> task) {
        return CompletableFuture.supplyAsync(task, executor);
    }
    
    public CompletableFuture<Void> run(Runnable task) {
        return CompletableFuture.runAsync(task, executor);
    }
}
```

#### 6.2 RequestProcessor

```java
public class RequestProcessor {
    private final VirtualThreadExecutor executor;
    private final Router router;
    private final ControllerRegistry controllerRegistry;
    
    public RequestProcessor(Router router, ControllerRegistry controllerRegistry) {
        this.executor = new VirtualThreadExecutor();
        this.router = router;
        this.controllerRegistry = controllerRegistry;
    }
    
    public CompletableFuture<Response> processRequest(Request request) {
        return executor.submit(() -> {
            // Encontra rota correspondente
            // Executa middlewares
            // Invoca controlador
            // Processa resposta
        });
    }
}
```

## 🔄 Fluxo de Requisição

1. O servidor HTTP recebe uma requisição
2. A requisição é encaminhada para o `RequestProcessor`
3. O `RequestProcessor` usa o `Router` para determinar o controlador e método a ser invocado
4. A cadeia de middlewares é executada
5. O método do controlador é invocado com os parâmetros extraídos da requisição
6. O controlador interage com o modelo conforme necessário
7. O resultado do controlador é processado:
   - Se for um objeto de modelo, é serializado para JSON
   - Se for uma string de visualização, o `ViewResolver` renderiza o template correspondente
8. A resposta é enviada de volta ao cliente

## 📊 Otimizações de Performance

### JIT Optimization

BulletOnRails foi projetado considerando o comportamento do compilador JIT:

1. **Métodos Pequenos e Específicos**: Facilitam o inlining pelo JIT
2. **Classes Finais e Métodos Finais**: Permitem otimizações de dispatch
3. **Minimização de Reflection**: Uso apenas quando absolutamente necessário
4. **Type Specialization**: Evita boxing/unboxing desnecessário
5. **Hot Path Optimization**: Código crítico otimizado para execução frequente

### GraalVM Integration

Suporte nativo para compilação AOT com GraalVM:

1. **Reflection Configuration**: Registra classes que usam reflection
2. **Resource Bundles**: Configuração para inclusão em imagens nativas
3. **Native Image Properties**: Opções otimizadas para compilação nativa

## 🎯 Convenções sobre Configuração

| Componente      | Convenção                                     | Exemplo                                |
|-----------------|-----------------------------------------------|----------------------------------------|
| Model Class     | Singular, PascalCase                          | `User`, `Article`, `ProductCategory`   |
| Database Table  | Plural, snake_case                            | `users`, `articles`, `product_categories` |
| Controller      | Plural, PascalCase, sufixo "Controller"       | `UsersController`, `ArticlesController` |
| Views           | app/views/[controller]/[action].[extension]   | `app/views/users/show.html`            |
| Routes          | Por padrão, baseadas no nome do controlador   | `/users`, `/articles`                  |
| Primary Key     | `id` (Long)                                   | `user.id`, `article.id`                |
| Foreign Keys    | `[singular_model]_id`                         | `user_id`, `article_id`                |
| Timestamps      | `created_at`, `updated_at` (automáticos)      | `user.createdAt`, `article.updatedAt`  |

## 🛠️ Ferramentas e Geração de Código

### CLI Commands

```
bor new [app_name]      # Cria uma nova aplicação
bor generate model [Name] [attributes]   # Gera um novo modelo
bor generate controller [Name] [actions] # Gera um novo controlador
bor generate scaffold [Name] [attributes] # Gera modelo, controlador e views
bor server              # Inicia o servidor de desenvolvimento
bor console             # Inicia um console interativo
bor migrate             # Executa migrações pendentes
```

### Code Generation

Exemplo de geração de scaffold:

```bash
bor generate scaffold User username:string email:string password_hash:string
```

Gera:
- Modelo `User` em `app/models/User.java`
- Controlador `UsersController` em `app/controllers/UsersController.java`
- Views em `app/views/users/` (index, show, new, edit)
- Migração em `db/migrations/`
- Testes para modelo e controlador

  </details>

## 📝 Exemplos

### Definindo um Modelo

```java
@Model
public class User {
    private Long id;
    private String username;
    private String email;
    private String passwordHash;
    
    // Os getters e setters são gerados automaticamente
    
    // Validações
    @Validates
    public boolean validateUsername() {
        return username != null && username.length() >= 3;
    }
}
```

### Criando um Controlador

```java
@Controller
public class UsersController {
    
    @Get("/users")
    public List<User> index() {
        return User.all();
    }
    
    @Get("/users/{id}")
    public User show(Long id) {
        return User.find(id);
    }
    
    @Post("/users")
    public User create(@Body User user) {
        user.save();
        return user;
    }
    
    @Put("/users/{id}")
    public User update(Long id, @Body User userData) {
        User user = User.find(id);
        user.update(userData);
        return user;
    }
    
    @Delete("/users/{id}")
    public void destroy(Long id) {
        User.find(id).delete();
    }
}
```

### Definindo uma Visualização

```html
<!-- app/views/users/show.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Perfil do Usuário</title>
</head>
<body>
    <h1>${user.username}</h1>
    <p>Email: ${user.email}</p>
    
    <a href="/users">Voltar para lista</a>
</body>
</html>
```

## 🐘 Integração com PostgreSQL

BulletOnRails oferece uma integração perfeita com PostgreSQL:

```java
// Configuração automática baseada em convenções de nomenclatura
// app/models/User.java -> tabela "users"
// Propriedade username -> coluna "username"

// Exemplo de consulta usando o ORM integrado
List<User> admins = User.where("role = ?", "admin")
                       .orderBy("created_at DESC")
                       .limit(10);

// Transações simplificadas
Transaction.execute(() -> {
    User user = new User();
    user.setUsername("john_doe");
    user.setEmail("john@example.com");
    user.save();
    
    Profile profile = new Profile();
    profile.setUser(user);
    profile.save();
});
```

## 🧵 Virtual Threads

BulletOnRails utiliza virtual threads do Project Loom por padrão para todas as requisições HTTP:

```java
// O framework gerencia automaticamente threads virtuais
// Não é necessária configuração adicional!

// Para operações personalizadas de longa duração:
@Controller
public class ReportsController {
    
    @Get("/reports/generate")
    public Report generateLongReport() {
        // Isso é executado em uma virtual thread automaticamente
        // sem bloqueio do thread pool principal
        return Report.generate();
    }
}
```

## 👥 Contribuindo

Contribuições são bem-vindas! Por favor, leia nosso [Guia de Contribuição](CONTRIBUTING.md) para mais detalhes.

## 🗺️ Roadmap

- [ ] **v0.2.0** - Suporte a migrações de banco de dados
- [ ] **v0.3.0** - Sistema de autenticação integrado
- [ ] **v0.4.0** - Suporte a WebSockets com virtual threads
- [ ] **v0.5.0** - Integração com GraalVM para compilação nativa
- [ ] **v1.0.0** - API estável e documentação completa

## 📜 Licença

BulletOnRails é licenciado sob a [Licença MIT](LICENSE).

---

<p align="center">
  Feito com ❤️ pela comunidade BulletOnRails
</p>
