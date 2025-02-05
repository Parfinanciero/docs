🏗️ 3. Arquitectura del Sistema

🔹 Diseño del Sistema

El microservicio de Autenticación y Registro ha sido diseñado con una arquitectura basada en microservicios para garantizar escalabilidad, modularidad y seguridad. Su implementación sigue los principios de desacoplamiento, lo que permite que otros módulos de Parfinanciero interactúen con él sin depender de su implementación interna.

Este enfoque garantiza que los servicios puedan evolucionar de manera independiente, permitiendo la incorporación de nuevas funcionalidades sin afectar la estabilidad del sistema. Además, la arquitectura está diseñada para ser altamente disponible y tolerante a fallos, asegurando que la autenticación y gestión de sesiones se mantenga operativa en todo momento.

🛠️ Tecnologías y Frameworks Empleados


Framework Backend: Spring Boot (Java) para la construcción del servicio.

Base de Datos: PostgreSQL para almacenamiento estructurado de usuarios y roles.

Autenticación: Firebase Authentication para la implementación de OAuth 2.0 con Google y Microsoft.

Seguridad: Implementación de JSON Web Tokens (JWT) para la gestión segura de sesiones y permisos.

Contenerización: Docker para garantizar entornos de ejecución estables y reproducibles.

Gestión de Configuración: Spring Cloud Config para la administración centralizada de configuración de servicios.

Comunicación entre Microservicios: RESTful APIs con seguridad basada en OAuth 2.0 y JWT.

Monitoreo y Logging: Prometheus y Grafana para el monitoreo del servicio y visualización de métricas clave.

🔄 Flujo de Datos Internos

Registro de Usuarios:

Un usuario inicia sesión con Google o Microsoft a través de Firebase.

Firebase genera un token de autenticación válido.

El token es enviado al microservicio, que valida su autenticidad y extrae los datos del usuario.

Si la validación es exitosa, se registra el usuario en PostgreSQL con la información necesaria.

Autenticación y Manejo de Sesiones:

Un usuario autenticado recibe un JWT generado por el sistema.

Este token es usado en cada solicitud subsecuente para validar la identidad del usuario.

Los middleware de seguridad interceptan las peticiones y validan la firma del JWT.