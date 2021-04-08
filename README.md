## Definir JWT com Spring Boot e Swagger UI

# 1. Introdução
Neste breve tutorial, veremos como configurar a IU do Swagger para incluir um JSON Web Token (JWT) ao chamar nossa API.

# 2. Dependências Maven
Neste exemplo, usaremos springfox-boot-starter, que inclui todas as dependências necessárias para começar a trabalhar com Swagger e Swagger UI. Vamos adicioná-lo ao nosso arquivo pom.xml:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

# 3. Configuração Swagger
Primeiro, precisamos definir nossa ApiKey para incluir JWT como um cabeçalho de autorização:

```
private ApiKey apiKey() { 
    return new ApiKey("JWT", "Authorization", "header"); 
}
```

A seguir, vamos configurar o JWT SecurityContext com um AuthorizationScope global:

```
private SecurityContext securityContext() { 
    return SecurityContext.builder().securityReferences(defaultAuth()).build(); 
} 

private List<SecurityReference> defaultAuth() { 
    AuthorizationScope authorizationScope = new AuthorizationScope("global", "accessEverything"); 
    AuthorizationScope[] authorizationScopes = new AuthorizationScope[1]; 
    authorizationScopes[0] = authorizationScope; 
    return Arrays.asList(new SecurityReference("JWT", authorizationScopes)); 
}
```

E então, configuramos nosso bean API Docket para incluir informações da API, contextos de segurança e esquemas de segurança:

```
@Bean
public Docket api() {
    return new Docket(DocumentationType.SWAGGER_2)
      .apiInfo(apiInfo())
      .securityContexts(Arrays.asList(securityContext()))
      .securitySchemes(Arrays.asList(apiKey()))
      .select()
      .apis(RequestHandlerSelectors.any())
      .paths(PathSelectors.any())
      .build();
}
```

```
private ApiInfo apiInfo() {
    return new ApiInfo(
      "My REST API",
      "Some custom description of API.",
      "1.0",
      "Terms of service",
      new Contact("Sallo Szrajbman", "www.isaccanedo.com", "salloszraj@gmail.com"),
      "License of API",
      "API license URL",
      Collections.emptyList());
}
```

# 4. Controlador REST
Em nosso ClientsRestController, vamos escrever um endpoint getClients simples para retornar uma lista de clientes:

```
@RestController(value = "/clients")
@Api( tags = "Clients")
public class ClientsRestController {

    @ApiOperation(value = "This method is used to get the clients.")
    @GetMapping
    public List<String> getClients() {
        return Arrays.asList("First Client", "Second Client");
    }
}
```

# 5. Swagger UI
Agora, quando iniciamos nosso aplicativo, podemos acessar a IU do Swagger em 
```http://localhost:8080/swagger-ui/URL```.

Quando clicamos no botão Autorizar, a IU do Swagger solicitará o JWT.

Basta inserir nosso token e clicar em Autorizar e, a partir daí, todas as solicitações feitas à nossa API conterão automaticamente o token nos cabeçalhos HTTP:

# 6. Solicitação de API com JWT
Ao enviar a solicitação para nossa API, podemos ver que há um cabeçalho “Authorization” com nosso valor de token:

# 7. Conclusão
Neste artigo, vimos como a IU do Swagger fornece configurações personalizadas para definir o JWT, o que pode ser útil ao lidar com a autorização do nosso aplicativo. Depois de autorizar na IU do Swagger, todas as solicitações incluirão automaticamente nosso JWT.