**Spring Security with JWT (JSON Web Tokens)**—this is a common approach for securing APIs in a stateless way. Let’s break it down clearly.

---

## 1. **What is JWT?**

**JWT (JSON Web Token)** is a compact token format used to securely transmit information between parties. A JWT typically has three parts:

```
HEADER.PAYLOAD.SIGNATURE
```

* **Header:** Specifies the token type (`JWT`) and the signing algorithm (`HS256` or `RS256`).
* **Payload:** Contains claims, i.e., user data like username, roles, or expiration.
* **Signature:** Ensures the token is not tampered with, usually created using a secret key.

Example payload:

```json
{
  "sub": "user123",
  "roles": ["ROLE_USER"],
  "exp": 1710000000
}
```

---

## 2. **Why JWT with Spring Security?**

Spring Security by default uses **session-based authentication**. With JWT:

* No server-side session storage → **stateless authentication**
* Scales well for REST APIs or microservices
* Can carry custom claims for roles/permissions

---

## 3. **Spring Security JWT Flow**

1. **Login** → User sends username & password.
2. **Authentication** → Spring Security validates credentials.
3. **Token Generation** → On success, server generates a JWT.
4. **Client Stores Token** → Typically in memory or localStorage (for browser clients).
5. **Request with JWT** → Client sends JWT in `Authorization: Bearer <token>` header.
6. **Token Validation** → Spring Security filters check token validity.
7. **Access Granted/Denied** → Based on roles/claims inside JWT.

---

## 4. **Key Components in Spring Security JWT**

1. **JWT Utility Class**

   * Generate token
   * Validate token
   * Extract username/roles

```java
public class JwtUtil {
    private String secret = "mysecretkey";

    public String generateToken(UserDetails userDetails) {
        return Jwts.builder()
            .setSubject(userDetails.getUsername())
            .claim("roles", userDetails.getAuthorities())
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + 1000 * 60 * 60)) // 1 hour
            .signWith(SignatureAlgorithm.HS256, secret)
            .compact();
    }

    public boolean validateToken(String token, UserDetails userDetails) {
        final String username = extractUsername(token);
        return (username.equals(userDetails.getUsername()) && !isTokenExpired(token));
    }

    // ... extractUsername, isTokenExpired, etc.
}
```

2. **JWT Filter**
   Intercepts incoming requests and checks the token.

```java
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Autowired
    private JwtUtil jwtUtil;

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
                                    throws ServletException, IOException {

        String authHeader = request.getHeader("Authorization");
        String username = null;
        String jwt = null;

        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            jwt = authHeader.substring(7);
            username = jwtUtil.extractUsername(jwt);
        }

        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            UserDetails userDetails = userDetailsService.loadUserByUsername(username);
            if (jwtUtil.validateToken(jwt, userDetails)) {
                UsernamePasswordAuthenticationToken authToken =
                    new UsernamePasswordAuthenticationToken(
                        userDetails, null, userDetails.getAuthorities());
                authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

3. **Security Configuration**
   Add the JWT filter before `UsernamePasswordAuthenticationFilter`.

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private JwtAuthenticationFilter jwtFilter;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
            .authorizeRequests()
            .antMatchers("/authenticate").permitAll()
            .anyRequest().authenticated();

        http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
    }
}
```

---

## 5. **Endpoints**

* **/authenticate** → Accepts username/password, returns JWT.
* **/api/** → Protected endpoints, requires `Authorization: Bearer <token>`.

---

## 6. **Best Practices**

1. **Use HTTPS** – JWT in plain HTTP can be intercepted.
2. **Short Expiry & Refresh Tokens** – For better security.
3. **Store Secret Securely** – Environment variables or secret manager.
4. **Avoid Sensitive Data in JWT** – JWT payload is base64-encoded, not encrypted.
