### **1. What is OAuth2?**

**Answer:** OAuth2 is a framework for **authorization** that allows an application to access user data from another service without sharing the user’s password.

---

### **2. What is the difference between OAuth2 and Spring Security?**

**Answer:**

* **OAuth2** – Handles **authorization** (who can access what).
* **Spring Security** – A **Java framework** that provides authentication and authorization, including OAuth2 support.

---

### **3. What are OAuth2 roles?**

**Answer:**

* **Resource Owner** – The user.
* **Client** – The application requesting access.
* **Authorization Server** – Issues tokens.
* **Resource Server** – Hosts protected resources.

---

### **4. What is an Access Token?**

**Answer:** A token that allows a client to access protected resources for a limited time.

---

### **5. What is a Refresh Token?**

**Answer:** A token used to get a **new access token** when the current one expires, without asking the user to log in again.

---

### **6. Name common OAuth2 grant types.**

**Answer:** Authorization Code, Implicit, Client Credentials, Password, Refresh Token.

---

### **7. How does Spring Security validate a JWT token?**

**Answer:** It verifies the **signature** using a public key or JWK endpoint and checks claims like expiry and issuer.

---

### **8. How to enable OAuth2 login in Spring Boot?**

**Answer:**

1. Add `spring-boot-starter-oauth2-client` dependency.
2. Configure the OAuth2 provider in `application.properties` or `application.yml`.
3. Enable login with `http.oauth2Login()`.

---

### **9. Difference between OAuth2 and OpenID Connect (OIDC)?**

**Answer:**

* OAuth2 → Authorization (access to resources)
* OIDC → Authentication (identify the user, provides ID token)

---

### **10. What dependency is needed to make a Spring Boot app an OAuth2 resource server?**

**Answer:** `spring-boot-starter-oauth2-resource-server`

Do you want me to do that?
