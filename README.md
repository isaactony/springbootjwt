```markdown
# Authentication Process in a Spring Boot Application

This guide walks through the authentication process in a Spring Boot application using JWT (JSON Web Tokens) and how the various components interact.

## User Authentication Request

The authentication process begins when a client sends a POST request to the `/authenticate` endpoint, passing the username and password in the request body.

**Example:**

```json
POST /authenticate
{
  "username": "user",
  "password": "password"
}
```

## Authentication in `AuthController`

1. The `AuthController` receives the authentication request.
2. It uses the `AuthenticationManager` to authenticate the user with the provided credentials.
3. If authentication is successful, the controller calls `JwtUtil` to generate a JWT token for the authenticated user.
4. The JWT token is then returned to the client in the response.

## Storing and Using JWT

- The client stores the JWT token (typically in local storage or a cookie).
- For subsequent requests to protected endpoints, the client includes this JWT in the `Authorization` header of the HTTP request.

**Example:**

```http
GET /protected-resource
Authorization: Bearer <JWT>
```

## Request Interception by `JwtRequestFilter`

1. Every incoming request is intercepted by `JwtRequestFilter`.
2. The filter checks the `Authorization` header for a valid JWT.
3. If a JWT is found, the filter uses `JwtUtil` to parse the token and extract the username.
4. The filter then loads the user’s details from the database using `CustomUserDetailsService`.

## Setting the Security Context

- If the JWT is valid, `JwtRequestFilter` creates a `UsernamePasswordAuthenticationToken` object containing the user’s details and authorities (roles).
- This authentication token is then set in the Spring Security context (`SecurityContextHolder`), effectively authenticating the user for the current request.

## Accessing Protected Resources

- With the user authenticated, Spring Security allows access to protected resources (e.g., endpoints that require specific roles or permissions).
- If the JWT is invalid or missing, the request is denied, and a `401 Unauthorized` response is returned.

## Security Configuration

- `SecurityConfig` defines the security rules, such as which endpoints are public (e.g., `/authenticate`) and which require authentication.
- It disables CSRF (typically in stateless REST APIs) and configures session management to be stateless (since JWT is used for authentication instead of sessions).
- The `JwtRequestFilter` is integrated into the Spring Security filter chain, ensuring that it processes every request before other security filters.

---
```
