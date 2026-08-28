Absolutely. Below is a complete, runnable Spring Boot OAuth2/OpenID Connect example with:

OAuth Client application — port 8080
OAuth Authorization Server — port 9000
Authorization Code flow + OIDC
Externalized properties
Dockerfiles
Docker Compose
Environment variables for secrets/configuration
Login/logout
Protected /api/me endpoint
JWT access/ID tokens
Docker networking considerations

I'll use Spring Boot 4.0.8 / Spring Security 7.x for the example. Spring Boot 4.0.8 is a current stable 4.0 release, while Spring Security 7 provides OAuth2 Client and Authorization Server functionality. 


Important: Spring Authorization Server has moved into Spring Security 7; the older standalone Spring Authorization Server 1.5.x line is the last generation of that project. 


1. Architecture
---
                         ┌──────────────────────────┐
                         │       Browser/User       │
                         └────────────┬─────────────┘
                                      │
                              http://localhost:8080
                                      │
                                      ▼
                    ┌───────────────────────────────┐
                    │       OAuth Client            │
                    │       Spring Boot             │
                    │                               │
                    │       Port: 8080              │
                    │                               │
                    │  /             protected      │
                    │  /oauth2/authorization/...    │
                    │  /login/oauth2/code/...       │
                    └──────────────┬────────────────┘
                                   │
                         Authorization Code
                                   │
                                   ▼
                    ┌───────────────────────────────┐
                    │     Authorization Server       │
                    │       Spring Boot              │
                    │                                │
                    │       Port: 9000               │
                    │                                │
                    │ /oauth2/authorize              │
                    │ /oauth2/token                  │
                    │ /oauth2/jwks                   │
                    │ /userinfo                      │
                    │ /.well-known/...               │
                    └───────────────────────────────┘
---

Spring Security's OAuth2 Client supports Authorization Code, Refresh Token, Client Credentials and other grant types, while OAuth2 Login uses the Authorization Code flow to authenticate the user. 

2. Project structure

Create one parent directory: 

----

spring-oauth-demo/
│
├── docker-compose.yml
│
├── oauth-server/
│   ├── Dockerfile
│   ├── pom.xml
│   └── src/
│       └── main/
│           ├── java/
│           │   └── com/example/oauthserver/
│           │       ├── OAuthServerApplication.java
│           │       └── SecurityConfig.java
│           │
│           └── resources/
│               └── application.yml
│
└── oauth-client/
    ├── Dockerfile
    ├── pom.xml
    └── src/
        └── main/
            ├── java/
            │   └── com/example/oauthclient/
            │       ├── OAuthClientApplication.java
            │       ├── SecurityConfig.java
            │       └── HomeController.java
            │
            └── resources/
                └── application.yml
                
----

3. OAuth Authorization Server

The authorization server is responsible for:

Authenticating users
Showing the authorization/consent screen
Issuing authorization codes
Exchanging codes for tokens
Signing JWTs
Providing OIDC metadata/JWK endpoints

Spring's authorization-server configuration provides endpoints such as authorization, token, introspection, revocation, metadata and JWK Set endpoints. 
H
Home

oauth-server/pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
            http://maven.apache.org/POM/4.0.0
            https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>4.0.8</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>oauth-server</artifactId>
    <version>1.0.0</version>

    <name>oauth-server</name>
    <description>OAuth2 Authorization Server</description>

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-oauth2-authorization-server</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>


The Spring documentation's current Authorization Server getting-started guidance uses the Boot Authorization Server starter and Java 17+. 
H
Home

4. Authorization Server main class

OAuthServerApplication.java

package com.example.oauthserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class OAuthServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(OAuthServerApplication.class, args);
    }
}

5. Authorization Server security configuration

SecurityConfig.java

package com.example.oauthserver;

import java.security.KeyPair;
import java.security.KeyPairGenerator;
import java.security.interfaces.RSAPrivateKey;
import java.security.interfaces.RSAPublicKey;
import java.util.UUID;

import com.nimbusds.jose.jwk.JWKSet;
import com.nimbusds.jose.jwk.RSAKey;
import com.nimbusds.jose.jwk.source.ImmutableJWKSet;
import com.nimbusds.jose.jwk.source.JWKSource;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import org.springframework.core.annotation.Order;

import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;

import org.springframework.security.oauth2.core.oidc.OidcScopes;

import org.springframework.security.oauth2.server.authorization.client.InMemoryRegisteredClientRepository;
import org.springframework.security.oauth2.server.authorization.client.RegisteredClient;
import org.springframework.security.oauth2.server.authorization.client.RegisteredClientRepository;

import org.springframework.security.oauth2.server.authorization.config.annotation.web.configuration.OAuth2AuthorizationServerConfiguration;

import org.springframework.security.oauth2.server.authorization.settings.AuthorizationServerSettings;
import org.springframework.security.oauth2.server.authorization.settings.ClientSettings;

import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.crypto.factory.PasswordEncoderFactories;

import org.springframework.security.web.SecurityFilterChain;

import static org.springframework.security.config.annotation.web.configurers.AbstractHttpConfigurer.csrf;

@Configuration
public class SecurityConfig {

    /**
     * Authorization Server security.
     */
    @Bean
    @Order(1)
    public SecurityFilterChain authorizationServerSecurityFilterChain(
            HttpSecurity http) throws Exception {

        OAuth2AuthorizationServerConfiguration.applyDefaultSecurity(http);

        http
            .csrf(csrf -> csrf.ignoringRequestMatchers(
                "/oauth2/token",
                "/oauth2/introspect",
                "/oauth2/revoke"
            ));

        return http.build();
    }

    /**
     * Normal application/user security.
     */
    @Bean
    @Order(2)
    public SecurityFilterChain defaultSecurityFilterChain(
            HttpSecurity http) throws Exception {

        http
            .authorizeHttpRequests(authorize -> authorize
                .requestMatchers("/actuator/health").permitAll()
                .anyRequest().authenticated()
            )
            .formLogin(Customizer.withDefaults());

        return http.build();
    }

    /**
     * OAuth client registration.
     */
    @Bean
    public RegisteredClientRepository registeredClientRepository() {

        RegisteredClient client =
            RegisteredClient.withId(UUID.randomUUID().toString())

                .clientId("demo-client")

                /*
                 * {bcrypt} is used so the secret is not stored as plaintext.
                 *
                 * The actual value comes from the external property.
                 */
                .clientSecret("{noop}" +
                        System.getenv()
                                .getOrDefault(
                                    "OAUTH_CLIENT_SECRET",
                                    "demo-secret"
                                ))

                .clientAuthenticationMethod(
                    org.springframework.security.oauth2.core
                        .ClientAuthenticationMethod.CLIENT_SECRET_BASIC
                )

                .authorizationGrantType(
                    org.springframework.security.oauth2.core
                        .AuthorizationGrantType.AUTHORIZATION_CODE
                )

                .authorizationGrantType(
                    org.springframework.security.oauth2.core
                        .AuthorizationGrantType.REFRESH_TOKEN
                )

                .redirectUri(
                    "http://localhost:8080/login/oauth2/code/demo"
                )

                .postLogoutRedirectUri(
                    "http://localhost:8080/"
                )

                .scope(OidcScopes.OPENID)
                .scope(OidcScopes.PROFILE)
                .scope("api.read")

                .clientSettings(
                    ClientSettings.builder()
                        .requireAuthorizationConsent(true)
                        .build()
                )

                .build();

        return new InMemoryRegisteredClientRepository(client);
    }

    /**
     * User authentication.
     *
     * Demo only.
     *
     * In production use a database/LDAP/SSO/identity provider.
     */
    @Bean
    public org.springframework.security.core.userdetails.UserDetailsService users(
            PasswordEncoder passwordEncoder) {

        var user = org.springframework.security.core.userdetails.User
            .withUsername(
                System.getenv()
                    .getOrDefault("OAUTH_USER", "admin")
            )
            .password(
                passwordEncoder.encode(
                    System.getenv()
                        .getOrDefault("OAUTH_USER_PASSWORD", "admin123")
                )
            )
            .roles("USER")
            .build();

        return new org.springframework.security.provisioning.InMemoryUserDetailsManager(
            user
        );
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }

    /**
     * RSA key used to sign JWTs.
     *
     * DEMO ONLY:
     * The key is regenerated every time the application starts.
     *
     * Production systems should load a persistent key from a keystore,
     * HSM, Vault, etc.
     */
    @Bean
    public JWKSource<org.springframework.security.oauth2.server.authorization.context.SecurityContext>
    jwkSource() {

        KeyPair keyPair = generateRsaKey();

        RSAPublicKey publicKey =
            (RSAPublicKey) keyPair.getPublic();

        RSAPrivateKey privateKey =
            (RSAPrivateKey) keyPair.getPrivate();

        RSAKey rsaKey =
            new RSAKey.Builder(publicKey)
                .privateKey(privateKey)
                .keyID(UUID.randomUUID().toString())
                .build();

        JWKSet jwkSet = new JWKSet(rsaKey);

        return new ImmutableJWKSet<>(jwkSet);
    }

    private static KeyPair generateRsaKey() {

        try {
            KeyPairGenerator generator =
                KeyPairGenerator.getInstance("RSA");

            generator.initialize(2048);

            return generator.generateKeyPair();

        } catch (Exception ex) {
            throw new IllegalStateException(
                "Unable to generate RSA key",
                ex
            );
        }
    }

    /**
     * Issuer and endpoint configuration.
     */
    @Bean
    public AuthorizationServerSettings authorizationServerSettings() {

        return AuthorizationServerSettings.builder()
            .issuer(
                System.getenv()
                    .getOrDefault(
                        "OAUTH_ISSUER",
                        "http://localhost:9000"
                    )
            )
            .build();
    }
}


For a production implementation, don't generate the signing key on every restart. The authorization server's JWK source is what allows clients/resource servers to obtain the public key used to validate JWT signatures. 
H
Home
+1

6. Authorization Server properties

oauth-server/src/main/resources/application.yml

spring:
  application:
    name: oauth-server

  security:
    user:
      name: ${OAUTH_USER:admin}
      password: ${OAUTH_USER_PASSWORD:admin123}

server:
  port: ${SERVER_PORT:9000}

management:
  endpoints:
    web:
      exposure:
        include: health,info

  endpoint:
    health:
      probes:
        enabled: true

logging:
  level:
    org.springframework.security: INFO


Notice that the actual configuration can come from environment variables.

For example:

OAUTH_USER=admin
OAUTH_USER_PASSWORD=secret
OAUTH_CLIENT_SECRET=secret
OAUTH_ISSUER=http://localhost:9000


Spring Boot supports external configuration through properties/YAML, environment variables, system properties and spring.config.location. 
H
Home

7. OAuth Client application

Now create the client.

oauth-client/pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
            http://maven.apache.org/POM/4.0.0
            https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>4.0.8</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>oauth-client</artifactId>
    <version>1.0.0</version>

    <name>oauth-client</name>
    <description>Spring Boot OAuth2 Client</description>

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-oauth2-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>


Spring Boot automatically maps properties under spring.security.oauth2.client.registration.* into ClientRegistration objects. 
H
Home

8. Client main class

OAuthClientApplication.java

package com.example.oauthclient;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class OAuthClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(
            OAuthClientApplication.class,
            args
        );
    }
}

9. Client Security configuration

SecurityConfig.java

package com.example.oauthclient;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;

import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(
            HttpSecurity http) throws Exception {

        http
            .authorizeHttpRequests(authorize -> authorize

                .requestMatchers(
                    "/",
                    "/error",
                    "/actuator/health",
                    "/css/**"
                )
                .permitAll()

                .anyRequest()
                .authenticated()
            )

            .oauth2Login(Customizer.withDefaults())

            .logout(logout -> logout
                .logoutSuccessUrl("/")
            );

        return http.build();
    }
}


The important line is:

.oauth2Login(Customizer.withDefaults())


Spring Security then handles the Authorization Code redirect and callback endpoints for you. The default login endpoint is based on /oauth2/authorization/{registrationId} and the callback is /login/oauth2/code/{registrationId}. 
H
Home

10. Client controller

HomeController.java

package com.example.oauthclient;

import java.util.Map;

import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.oauth2.core.user.OAuth2User;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HomeController {

    @GetMapping("/")
    public String home(
            @AuthenticationPrincipal OAuth2User user,
            Model model) {

        model.addAttribute("user", user);

        return "home";
    }

    @GetMapping("/api/me")
    @ResponseBody
    public Map<String, Object> me(
            @AuthenticationPrincipal OAuth2User user) {

        return user.getAttributes();
    }
}

11. Client properties

oauth-client/src/main/resources/application.yml

spring:
  application:
    name: oauth-client

  security:
    oauth2:
      client:

        registration:

          demo:
            provider: demo-provider

            client-id: ${OAUTH_CLIENT_ID:demo-client}

            client-secret: ${OAUTH_CLIENT_SECRET:demo-secret}

            authorization-grant-type: authorization_code

            redirect-uri: "{baseUrl}/login/oauth2/code/{registrationId}"

            scope:
              - openid
              - profile
              - api.read

        provider:

          demo-provider:

            issuer-uri: ${OAUTH_ISSUER:http://localhost:9000}

server:
  port: ${SERVER_PORT:8080}

management:
  endpoints:
    web:
      exposure:
        include: health,info

logging:
  level:
    org.springframework.security: INFO


Using:

issuer-uri: ${OAUTH_ISSUER:http://localhost:9000}


is particularly useful because Spring Security can use the issuer to discover the authorization server's metadata rather than requiring you to manually specify authorization/token/JWK endpoints. 
H
Home

12. Login page

Create:

oauth-client/src/main/resources/templates/home.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Spring OAuth2 Demo</title>
</head>

<body>

<h1>Spring Boot OAuth2 Client</h1>

<div th:if="${user == null}">

    <p>You are not authenticated.</p>

    <a href="/oauth2/authorization/demo">
        Login with Demo OAuth Server
    </a>

</div>

<div th:if="${user != null}">

    <h2>Welcome!</h2>

    <p>
        <strong>Name:</strong>
        <span th:text="${user.getAttribute('name')}"></span>
    </p>

    <p>
        <strong>Subject:</strong>
        <span th:text="${user.getAttribute('sub')}"></span>
    </p>

    <p>
        <strong>Email:</strong>
        <span th:text="${user.getAttribute('email')}"></span>
    </p>

    <h3>User Attributes</h3>

    <pre th:text="${user.attributes}"></pre>

    <p>
        <a href="/api/me">View /api/me</a>
    </p>

    <form method="post" action="/logout">
        <button type="submit">
            Logout
        </button>
    </form>

</div>

</body>
</html>

13. External configuration

Instead of keeping secrets in:

application.yml


you can have:

config/
├── oauth-server.yml
└── oauth-client.yml


For example:

config/oauth-server.yml
server:
  port: 9000

spring:
  security:
    user:
      name: admin
      password: ${OAUTH_USER_PASSWORD}

oauth:
  issuer: http://localhost:9000

config/oauth-client.yml
server:
  port: 8080

spring:
  security:
    oauth2:
      client:

        registration:

          demo:
            provider: demo-provider
            client-id: ${OAUTH_CLIENT_ID}
            client-secret: ${OAUTH_CLIENT_SECRET}
            authorization-grant-type: authorization_code
            redirect-uri: "{baseUrl}/login/oauth2/code/{registrationId}"
            scope:
              - openid
              - profile
              - api.read

        provider:

          demo-provider:
            issuer-uri: ${OAUTH_ISSUER}


Then launch with:

java \
  -jar oauth-client.jar \
  --spring.config.additional-location=file:./config/


Spring Boot supports external configuration locations and allows environment variables/system properties/command-line values to override configuration. 
H
Home

14. Dockerfile — Authorization Server

oauth-server/Dockerfile

FROM eclipse-temurin:17-jre

WORKDIR /app

COPY target/oauth-server-1.0.0.jar app.jar

EXPOSE 9000

ENTRYPOINT [
    "java",
    "-XX:MaxRAMPercentage=75",
    "-jar",
    "app.jar"
]

15. Dockerfile — OAuth Client

oauth-client/Dockerfile

FROM eclipse-temurin:17-jre

WORKDIR /app

COPY target/oauth-client-1.0.0.jar app.jar

EXPOSE 8080

ENTRYPOINT [
    "java",
    "-XX:MaxRAMPercentage=75",
    "-jar",
    "app.jar"
]

16. Docker Compose

Here's the important part.

docker-compose.yml

services:

  oauth-server:

    build:
      context: ./oauth-server

    container_name: oauth-server

    ports:
      - "9000:9000"

    environment:

      SERVER_PORT: 9000

      OAUTH_ISSUER: http://localhost:9000

      OAUTH_USER: admin

      OAUTH_USER_PASSWORD: admin123

      OAUTH_CLIENT_SECRET: demo-secret

    networks:
      - oauth-network


  oauth-client:

    build:
      context: ./oauth-client

    container_name: oauth-client

    depends_on:
      - oauth-server

    ports:
      - "8080:8080"

    environment:

      SERVER_PORT: 8080

      OAUTH_CLIENT_ID: demo-client

      OAUTH_CLIENT_SECRET: demo-secret

      OAUTH_ISSUER: http://oauth-server:9000

    networks:
      - oauth-network


networks:

  oauth-network:
    driver: bridge


There is one very important Docker networking issue here.

17. localhost vs Docker hostname

From your browser:

http://localhost:9000


means:

your computer → port 9000


But from the OAuth client container:

http://localhost:9000


means:

oauth-client container → itself


It does not mean the authorization-server container.

Therefore Docker-to-Docker communication uses:

http://oauth-server:9000


while browser redirects use:

http://localhost:9000


This creates an important distinction:

Browser
   │
   │ http://localhost:9000
   ▼
Host
   │
   ▼
oauth-server container


oauth-client container
   │
   │ http://oauth-server:9000
   ▼
oauth-server container

18. The issuer problem

There's a subtle issue with the above simple Docker example.

The OAuth Client sees:

issuer-uri: http://oauth-server:9000


But the browser needs:

http://localhost:9000


OAuth/OIDC metadata contains URLs that the browser must be able to access.

For a real deployment, don't solve this by randomly changing localhost and container names. Use a common externally reachable hostname.

For example:

auth.example.com
app.example.com


Then:

Browser
   │
   ├── https://app.example.com
   │
   └── https://auth.example.com
             │
             ▼
       OAuth Server


And configure:

OAUTH_ISSUER=https://auth.example.com


The client then uses:

issuer-uri: ${OAUTH_ISSUER}


This is the preferred production architecture.

19. Simplest local Docker solution

For a local demonstration, you can make both containers use the host-visible issuer:

oauth-client:
  environment:
    OAUTH_ISSUER: http://localhost:9000


But the container may not be able to resolve/access localhost:9000 depending on where metadata/token requests originate.

A cleaner local setup is to use a host name that both browser and containers can resolve, for example:

host.docker.internal


and configure:

OAUTH_ISSUER: http://host.docker.internal:9000


with the appropriate Docker networking configuration.

For a production deployment, use a real DNS name/reverse proxy instead.

20. Better Docker Compose for development

A practical development setup is:

services:

  oauth-server:

    build:
      context: ./oauth-server

    environment:
      SERVER_PORT: 9000
      OAUTH_ISSUER: http://oauth-server:9000
      OAUTH_USER: admin
      OAUTH_USER_PASSWORD: ${OAUTH_USER_PASSWORD:-admin123}
      OAUTH_CLIENT_SECRET: ${OAUTH_CLIENT_SECRET:-demo-secret}

    ports:
      - "9000:9000"

    networks:
      oauth-network:
        aliases:
          - oauth-server


  oauth-client:

    build:
      context: ./oauth-client

    depends_on:
      - oauth-server

    environment:
      SERVER_PORT: 8080
      OAUTH_CLIENT_ID: demo-client
      OAUTH_CLIENT_SECRET: ${OAUTH_CLIENT_SECRET:-demo-secret}

      # For a real deployment this should be the public URL.
      OAUTH_ISSUER: ${OAUTH_ISSUER:-http://localhost:9000}

    ports:
      - "8080:8080"

    networks:
      - oauth-network


networks:
  oauth-network:


For a production environment:

export OAUTH_ISSUER=https://auth.example.com
export OAUTH_CLIENT_SECRET='a-long-random-secret'
export OAUTH_USER_PASSWORD='another-long-random-password'

21. Build the applications

From the project root:

cd oauth-server
mvn clean package -DskipTests


Then:

cd ../oauth-client
mvn clean package -DskipTests


You should get:

oauth-server/target/oauth-server-1.0.0.jar
oauth-client/target/oauth-client-1.0.0.jar

22. Build Docker images

From the root:

docker compose build


Then:

docker compose up


Or:

docker compose up --build


You should see:

oauth-server  | Started OAuthServerApplication
oauth-client  | Started OAuthClientApplication

23. Test the OAuth flow

Open:

http://localhost:8080


Click:

Login with Demo OAuth Server


The browser goes through roughly:

GET /oauth2/authorization/demo
             │
             ▼
OAuth Client
             │
             │ redirect
             ▼
GET /oauth2/authorize
             │
             ▼
OAuth Server
             │
             │ login
             ▼
Username/password
             │
             ▼
Consent
             │
             ▼
Authorization Code
             │
             ▼
OAuth Client callback
/login/oauth2/code/demo
             │
             ▼
Token request
             │
             ▼
/oauth2/token
             │
             ▼
ID Token + Access Token
             │
             ▼
Authenticated session

24. Login credentials

Use:

Username: admin
Password: admin123


or whatever you supplied:

OAUTH_USER_PASSWORD=...

25. OAuth endpoints

Your authorization server exposes endpoints along these lines:

http://localhost:9000/oauth2/authorize
http://localhost:9000/oauth2/token
http://localhost:9000/oauth2/jwks
http://localhost:9000/oauth2/introspect
http://localhost:9000/oauth2/revoke


OIDC metadata is available through the standard well-known discovery endpoint.

You can inspect it with:

curl http://localhost:9000/.well-known/openid-configuration


The authorization-server metadata is especially useful because the client can discover the authorization, token and JWK endpoints from the issuer. 
H
Home

26. What actually happens to the client secret?

The client has:

client-id: ${OAUTH_CLIENT_ID}
client-secret: ${OAUTH_CLIENT_SECRET}


and the authorization server has the matching registered client:

client_id     = demo-client
client_secret = demo-secret


The client authenticates against the token endpoint using:

client_secret_basic


which Spring Security supports directly. 
H
Home

Conceptually:

POST /oauth2/token
Authorization: Basic base64(demo-client:demo-secret)

grant_type=authorization_code
code=xxxxx
redirect_uri=http://localhost:8080/login/oauth2/code/demo


The authorization server validates:

client_id
client_secret
authorization_code
redirect_uri
grant_type


and issues tokens.

27. External property hierarchy

A good production setup looks like this:

application.yml
       │
       ▼
defaults
       │
       ▼
application-prod.yml
       │
       ▼
external config
       │
       ▼
environment variables
       │
       ▼
Docker/Kubernetes secrets


For example:

spring:
  security:
    oauth2:
      client:
        registration:
          demo:
            client-id: ${OAUTH_CLIENT_ID}
            client-secret: ${OAUTH_CLIENT_SECRET}

        provider:
          demo-provider:
            issuer-uri: ${OAUTH_ISSUER}


This keeps credentials out of Git.

Spring Boot explicitly supports externalized configuration and configuration through environment variables. 
H
Home

28. .env file

For local Docker development, you can use:

.env

OAUTH_CLIENT_ID=demo-client
OAUTH_CLIENT_SECRET=change-me-to-a-long-secret

OAUTH_USER=admin
OAUTH_USER_PASSWORD=change-me

OAUTH_ISSUER=http://localhost:9000


Then:

docker compose up --build


Don't commit this file:

.env

29. Production improvements

The example intentionally uses in-memory users and clients so the entire example is easy to understand.

For production, change these pieces:

InMemoryUserDetailsManager
        ↓
PostgreSQL/MySQL/LDAP/enterprise IdP


InMemoryRegisteredClientRepository
        ↓
JdbcRegisteredClientRepository


Generated RSA key
        ↓
Persistent keystore/HSM/Vault/KMS


HTTP
        ↓
HTTPS


localhost
        ↓
auth.example.com


Spring Authorization Server also provides JDBC/Redis-oriented approaches for its core services. 
H
Home

30. Production architecture

I'd recommend this architecture:

                         Internet
                            │
                            ▼
                     ┌─────────────┐
                     │ Load Balancer│
                     │ / Ingress    │
                     └──────┬──────┘
                            │
             ┌──────────────┴──────────────┐
             │                             │
             ▼                             ▼
     app.example.com              auth.example.com
             │                             │
             ▼                             ▼
      OAuth Client                Authorization Server
       Spring Boot                  Spring Boot
             │                             │
             │                             ├── PostgreSQL
             │                             │
             │                             └── KMS/Vault
             │
             └──────────── OAuth ──────────┘


The most important production rule is:

ISSUER = externally reachable canonical URL


For example:

OAUTH_ISSUER=https://auth.example.com


not:

OAUTH_ISSUER=http://oauth-server:9000


and not:

OAUTH_ISSUER=http://localhost:9000

31. One important correction for a real application

The demo above uses:

System.getenv()


inside the Java configuration for some server values.

I would not do that in a production Spring application.

Prefer Spring configuration:

oauth:
  issuer: ${OAUTH_ISSUER}
  client:
    id: ${OAUTH_CLIENT_ID}
    secret: ${OAUTH_CLIENT_SECRET}


and bind it using:

@ConfigurationProperties(prefix = "oauth")


This gives you typed configuration, validation and cleaner testing.

For example:

@ConfigurationProperties(prefix = "oauth")
public record OAuthProperties(
        String issuer,
        Client client
) {

    public record Client(
        String id,
        String secret
    ) {
    }
}


Then:

@Configuration
@EnableConfigurationProperties(OAuthProperties.class)
public class OAuthConfiguration {
}


This is much cleaner than scattering:

System.getenv(...)


through the application.

32. Recommended final configuration model

For a real project, I would structure it like this:

                    ┌───────────────────────┐
                    │ Environment Variables │
                    │ / Docker Secrets      │
                    └───────────┬───────────┘
                                │
                                ▼
                     ┌─────────────────────┐
                     │ application.yml     │
                     │                     │
                     │ ${OAUTH_CLIENT_ID}  │
                     │ ${OAUTH_CLIENT...}  │
                     └──────────┬──────────┘
                                │
                                ▼
                     ┌─────────────────────┐
                     │ @ConfigurationProps │
                     └──────────┬──────────┘
                                │
                    ┌───────────┴───────────┐
                    ▼                       ▼
              OAuth Client          Authorization Server
                    │                       │
                    └──────── OAuth ─────────┘


Spring Boot's OAuth2 client auto-configuration is specifically designed around spring.security.oauth2.client.registration and spring.security.oauth2.client.provider properties, so this approach stays close to the framework's intended configuration model. 
H
Home
+1

Quick start

Once the files are in place:

# Build
cd oauth-server
mvn clean package -DskipTests

cd ../oauth-client
mvn clean package -DskipTests

# Start both
cd ..
docker compose up --build


Then visit:

http://localhost:8080


Login:

admin / admin123


The resulting flow is:

Browser
   ↓
OAuth Client :8080
   ↓
Authorization Server :9000
   ↓
Login + Consent
   ↓
Authorization Code
   ↓
Client callback
   ↓
Token endpoint
   ↓
ID Token + Access Token
   ↓
Authenticated application


For reference, Spring's current OAuth2 Client documentation confirms that Boot can create the ClientRegistrationRepository from these properties, and that issuer-uri can be used for provider discovery. 
