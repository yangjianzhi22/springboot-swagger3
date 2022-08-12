# springboot-swagger3

springboot+swagger3整合应用

> 参考: [https://blog.csdn.net/weixin_43740223/article/details/108491386](https://blog.csdn.net/weixin_43740223/article/details/108491386)

## swagger

Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务

## 使用

- 添加依赖

```
<!-- swagger3 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

- 添加开关注解 @EnableOpenApi

```
@SpringBootApplication
@EnableOpenApi
public class Swagger3Application {

	public static void main(String[] args) {
		SpringApplication.run(Swagger3Application.class, args);
	}

}
```

- application.yml配置

```
spring:
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher
```

- 配置摘要信息

```
@Configuration
public class SwaggerConfig {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.OAS_30)
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.yang.swagger3.controller"))
                .build();
    }
}
```

> 访问: http://localhost:8000/swagger-ui/

![1](./docs/1.jpg)

## 使用场景

#### 1. 整合spring security使用

如何访问需要登录认证的接口？只需在访问接口时添加一个合法的Authorization请求头即可

> 参考: [https://blog.csdn.net/sunxiaoju/article/details/110751151](https://blog.csdn.net/sunxiaoju/article/details/110751151)

```
 @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.OAS_30)
                ...
                .securitySchemes(securitySchemes()) // 授权信息设置，必要的header token等认证信息
                .securityContexts(securityContexts()); // 授权信息全局应用
    }

// 设置授权信息
private List<SecurityScheme> securitySchemes() {
    return Collections.singletonList(new ApiKey("token", "token", "header"));
}

// 授权信息全局应用
private List<SecurityContext> securityContexts() {
    return Collections.singletonList(
            SecurityContext.builder()
                    .securityReferences(Collections.singletonList(new SecurityReference("token", new AuthorizationScope[]{new AuthorizationScope("global", "")})))
                    .build()
    );
}
```
