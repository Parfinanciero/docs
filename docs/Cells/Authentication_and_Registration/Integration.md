## 📝 5. Integración con Otros Servicios

### 📌 Dependencias Externas

Este microservicio de autenticación y registro se integra con múltiples servicios externos para garantizar un flujo de trabajo seguro y eficiente. A continuación, se detallan las principales dependencias junto con sus roles dentro del sistema:

- **Firebase Authentication**: Gestiona la autenticación OAuth 2.0 con Google y Microsoft. Proporciona tokens de identidad que se validan en el backend.
- **PostgreSQL**: Base de datos relacional utilizada para almacenar usuarios, roles y permisos, asegurando persistencia y seguridad de la información.
- **Spring Security**: Framework de seguridad que implementa la gestión de autenticación y autorización en el backend.
- **JWT (JSON Web Tokens)**: Mecanismo para la autenticación segura entre microservicios y clientes mediante la generación de tokens firmados.
- **API Gateway**: Actúa como un intermediario entre los clientes y los servicios internos, asegurando seguridad y control de tráfico.

---

### 🔹 Contratos de API con Otros Servicios

El microservicio de autenticación se comunica con otros microservicios dentro del ecosistema de Parfinanciero mediante APIs RESTful. A continuación, se describen las interacciones principales:

#### **1. Integración con el Microservicio de Finanzas**
**Propósito:** Garantizar que solo usuarios autenticados puedan acceder a las funciones financieras.

**Mecanismo:**
- Los clientes envían una solicitud con su token JWT en el encabezado `Authorization`.
- El microservicio de Finanzas delega la validación del token al microservicio de autenticación.
- Si el token es válido, se permite el acceso; de lo contrario, se devuelve un error `401 Unauthorized`.

#### **2. Integración con el Microservicio de Gestión de Roles**
**Propósito:** Asignar y verificar roles de usuario dentro del ecosistema.

**Mecanismo:**
- Durante el inicio de sesión, el microservicio de autenticación consulta los roles del usuario en la base de datos.
- Al validar un token JWT, se extraen los permisos del usuario y se transmiten a otros servicios.
- Los servicios pueden restringir funciones según los roles asignados (`ADMIN`, `USER`, `ANALYST`, etc.).

#### **3. Integración con el Microservicio de Notificaciones**
**Propósito:** Enviar notificaciones al usuario tras eventos de autenticación y registro.

**Mecanismo:**
- Cuando un usuario se registra, el microservicio de autenticación emite un evento para que el servicio de Notificaciones genere alertas.
- Se envía un correo electrónico de bienvenida o verificación de cuenta.
- Se generan alertas para eventos como intentos de inicio de sesión sospechosos.

---

### 🔄 Mecanismos de Autenticación y Autorización

Este microservicio implementa múltiples métodos de autenticación y autorización para garantizar un entorno seguro y eficiente:

#### **1. Autenticación basada en Firebase OAuth 2.0**
- Se permite el inicio de sesión con credenciales de Google y Microsoft.
- Firebase proporciona un token de identidad, que el backend valida y utiliza para generar un JWT propio para futuras solicitudes internas.

#### **2. Autenticación basada en JWT**
- Cada usuario autenticado recibe un token JWT, que contiene su `ID`, `roles` y otros metadatos.
- En cada solicitud protegida, el cliente envía el JWT en el encabezado `Authorization`.
- Los microservicios validan el JWT antes de procesar la solicitud para garantizar que el usuario tenga los permisos adecuados.

#### **3. Middleware de Seguridad**
- Todas las solicitudes a endpoints protegidos son interceptadas.
- Se verifica la validez del token JWT.
- Se comprueba que el usuario tenga los permisos adecuados antes de ejecutar una acción.

#### **Validación de JWT en Spring Security**

Este es un ejemplo de implementación en Java para la validación de tokens JWT utilizando `Spring Security`:

```java
public class JwtRequestFilter extends OncePerRequestFilter {
    @Autowired
    private JwtTokenUtil jwtTokenUtil;
    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
            throws ServletException, IOException {
        final String authorizationHeader = request.getHeader("Authorization");
        
        String username = null;
        String jwt = null;
        
        if (authorizationHeader != null && authorizationHeader.startsWith("Bearer ")) {
            jwt = authorizationHeader.substring(7);
            username = jwtTokenUtil.extractUsername(jwt);
        }
        
        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            UserDetails userDetails = this.userDetailsService.loadUserByUsername(username);
            
            if (jwtTokenUtil.validateToken(jwt, userDetails)) {
                UsernamePasswordAuthenticationToken authToken = new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
                authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }
        chain.doFilter(request, response);
    }
}
```

---

### 📌 Beneficios de la Implementación
- **Seguridad**: Implementación de autenticación robusta basada en JWT y OAuth 2.0.
- **Escalabilidad**: Integración modular con otros microservicios.
- **Eficiencia**: Validación rápida de tokens y autorización basada en roles.
- **Mantenibilidad**: Código estructurado para fácil actualización y mejora.

