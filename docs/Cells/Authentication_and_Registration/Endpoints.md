## 📝 4. Endpoints de la API

### 📌 Documentación de los Endpoints
Este microservicio expone los siguientes endpoints para la autenticación y gestión de usuarios. Se detallan los métodos HTTP, rutas, parámetros requeridos y respuestas esperadas, incluyendo explicaciones técnicas sobre su funcionamiento interno.

---

### 🔹 1. Registro de Usuario
- **Método:** `POST`
- **URL:** `/api/auth/register`

#### **Parámetros de Entrada**
```json
{
  "email": "usuario@ejemplo.com",
  "password": "ContraseñaSegura123",
  "name": "Juan Pérez"
}
```

#### **Explicación del Código**
Este endpoint permite registrar nuevos usuarios en la plataforma.

1. **Validación de Datos:** Se valida que los campos `email`, `password` y `name` cumplan con el formato adecuado y no estén vacíos.
2. **Verificación de Existencia:** Se consulta en la base de datos si ya existe un usuario con el mismo correo electrónico.
3. **Hash de Contraseña:** Se usa un algoritmo de hashing seguro (por ejemplo, BCrypt) para cifrar la contraseña antes de guardarla en la base de datos.
4. **Creación del Usuario:** Se crea una nueva entrada en la base de datos PostgreSQL con los datos validados y encriptados.
5. **Respuesta:** Se retorna una respuesta indicando el éxito o error de la operación.

#### **Fragmento de Código (Java)**
```java
@PostMapping("/register")
public ResponseEntity<?> registerUser(@RequestBody RegisterRequest request) {
    if (userService.existsByEmail(request.getEmail())) {
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body("El usuario ya existe.");
    }
    User user = new User(request.getEmail(), passwordEncoder.encode(request.getPassword()), request.getName());
    userService.save(user);
    return ResponseEntity.status(HttpStatus.CREATED).body("Usuario registrado exitosamente.");
}
```

#### **Estructura de Respuestas**
**Éxito (201 Created)**
```json
{
  "message": "Usuario registrado exitosamente.",
  "user": {
    "id": "123e4567-e89b-12d3-a456-426614174000",
    "email": "usuario@ejemplo.com",
    "name": "Juan Pérez"
  }
}
```

**Códigos de Estado HTTP Posibles:**
- `201 Created`: Usuario registrado exitosamente.
- `400 Bad Request`: Datos inválidos o correo electrónico en uso.
- `500 Internal Server Error`: Error del servidor.

---

### 🔹 2. Inicio de Sesión
- **Método:** `POST`
- **URL:** `/api/auth/login`

#### **Parámetros de Entrada**
```json
{
  "email": "usuario@ejemplo.com",
  "password": "ContraseñaSegura123"
}
```

#### **Explicación del Código**
Este endpoint permite a los usuarios autenticarse y recibir un token JWT.

1. **Validación de Datos:** Se verifica que los campos `email` y `password` estén presentes y en el formato correcto.
2. **Autenticación:** Se busca el usuario en la base de datos y se compara la contraseña ingresada con la almacenada (hash).
3. **Generación de JWT:** Si las credenciales son correctas, se genera un token JWT con información del usuario.
4. **Respuesta:** Se retorna el token JWT y los detalles del usuario autenticado.

#### **Fragmento de Código (Java)**
```java
@PostMapping("/login")
public ResponseEntity<?> loginUser(@RequestBody LoginRequest request) {
    User user = userService.findByEmail(request.getEmail())
                  .orElseThrow(() -> new UsernameNotFoundException("Usuario no encontrado"));
    if (!passwordEncoder.matches(request.getPassword(), user.getPassword())) {
        return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Credenciales inválidas.");
    }
    String token = jwtProvider.generateToken(user);
    return ResponseEntity.ok(new LoginResponse(token, user.getId(), user.getEmail(), user.getName()));
}
```

---

### 🔹 3. Autenticación con Proveedores Externos (Google/Microsoft)
- **Método:** `POST`
- **URL:** `/api/auth/oauth`

#### **Parámetros de Entrada**
```json
{
  "provider": "google",
  "token": "ya29.a0AfH6SMA..."
}
```

#### **Explicación del Código**
Este endpoint permite la autenticación de usuarios a través de proveedores externos como Google o Microsoft.

1. **Validación de Datos:** Se verifica que los campos `provider` y `token` estén presentes y en el formato adecuado.
2. **Verificación del Token:** Se consulta con el proveedor correspondiente (Google/Microsoft) para validar el token recibido.
3. **Obtención de Información del Usuario:** Se recupera la información del usuario autenticado a través del token del proveedor.
4. **Registro/Inicio de Sesión:** Si el usuario no existe en la base de datos, se crea un nuevo registro; de lo contrario, se le permite el acceso.
5. **Generación de JWT:** Se genera un token JWT válido para el usuario autenticado.
6. **Respuesta:** Se retorna el token JWT junto con los detalles del usuario autenticado.

#### **Fragmento de Código (Java)**
```java
@PostMapping("/oauth")
public ResponseEntity<?> oauthLogin(@RequestBody OAuthRequest request) {
    OAuthUser oAuthUser = externalAuthService.verifyToken(request.getProvider(), request.getToken());
    User user = userService.findOrCreateUser(oAuthUser);
    String token = jwtProvider.generateToken(user);
    return ResponseEntity.ok(new LoginResponse(token, user.getId(), user.getEmail(), user.getName()));
}
```
---

### 🔹 4. Obtención de Información del Usuario Autenticado
- **Método:** `GET`
- **URL:** `/api/auth/me`

#### **Parámetros de Entrada**
Encabezado de autorización:
```http
Authorization: Bearer <token-jwt>
```

#### **Explicación del Código**
Este endpoint permite a un usuario autenticado obtener su información de perfil.

1. **Autenticación:** Se extrae el token JWT de la cabecera `Authorization` y se verifica su validez.
2. **Decodificación del Token:** Se obtiene el identificador del usuario desde el token.
3. **Consulta en la Base de Datos:** Se recupera la información del usuario autenticado.
4. **Respuesta:** Se retorna la información del usuario si es válido.

#### **Fragmento de Código (Java)**
```java
@GetMapping("/me")
public ResponseEntity<?> getUserProfile(@RequestHeader("Authorization") String authHeader) {
    String token = authHeader.replace("Bearer ", "");
    if (jwtProvider.validateToken(token)) {
        String userId = jwtProvider.getUserIdFromJWT(token);
        Optional<User> user = userService.findById(userId);
        return user.map(ResponseEntity::ok)
                   .orElseGet(() -> ResponseEntity.status(HttpStatus.NOT_FOUND).body("Usuario no encontrado."));
    }
    return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Token inválido o expirado.");
}
```

#### **Estructura de las Respuestas**
**Éxito (200 OK):**
```json
{
  "id": "123e4567-e89b-12d3-a456-426614174000",
  "email": "usuario@ejemplo.com",
  "name": "Juan Pérez",
  "roles": ["USER"]
}
```

**Error (401 Unauthorized):**
```json
{
  "error": "Token inválido o expirado."
}
```

---

### 🔹 5. Cierre de Sesión
- **Método:** `POST`
- **URL:** `/api/auth/logout`

#### **Parámetros de Entrada**
Encabezado de autorización:
```http
Authorization: Bearer <token-jwt>
```

#### **Explicación del Código**
Este endpoint permite a un usuario cerrar sesión invalidando su token JWT actual.

1. **Autenticación:** Se extrae y valida el token JWT del usuario.
2. **Invalidación del Token:** Se marca el token como inválido o se elimina de una lista de tokens activos.
3. **Persistencia:** Si se usa un sistema de almacenamiento de tokens, se actualiza la base de datos para desactivar el token.
4. **Respuesta:** Se retorna un mensaje indicando que la sesión se ha cerrado correctamente.

#### **Fragmento de Código (Java)**
```java
@PostMapping("/logout")
public ResponseEntity<?> logoutUser(@RequestHeader("Authorization") String authHeader) {
    String token = authHeader.replace("Bearer ", "");
    if (jwtProvider.validateToken(token)) {
        tokenBlacklistService.addToBlacklist(token);
        return ResponseEntity.ok("Sesión cerrada exitosamente.");
    }
    return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Token inválido o expirado.");
}
```

#### **Estructura de las Respuestas**
**Éxito (200 OK):**
```json
{
  "message": "Sesión cerrada exitosamente."
}
```

**Error (401 Unauthorized):**
```json
{
  "error": "Token inválido o expirado."
}
```

