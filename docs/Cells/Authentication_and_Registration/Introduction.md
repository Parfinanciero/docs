📝 2. Introducción

📌 Contexto y Justificación

La transformación digital y la creciente dependencia de servicios financieros en línea han generado la necesidad de sistemas de autenticación más seguros y eficientes. Las soluciones tradicionales de autenticación basadas en credenciales estáticas han demostrado ser vulnerables a ataques como phishing, fuerza bruta y robo de identidad. Parfinanciero, como plataforma integral de gestión financiera, requiere un mecanismo robusto que garantice tanto la seguridad como la accesibilidad para sus usuarios.

En este contexto, la integración de un sistema de autenticación basado en OAuth 2.0 con Google y Microsoft representa una solución moderna que minimiza riesgos de seguridad y mejora la experiencia del usuario. Asimismo, el uso de tokens JWT permite una comunicación segura entre los microservicios de la plataforma, asegurando una gestión eficiente de sesiones y permisos de usuario.

❓ Descripción del Problema

Las plataformas financieras deben garantizar que solo usuarios legítimos puedan acceder a información confidencial y realizar operaciones seguras. Sin embargo, los sistemas tradicionales presentan varios desafíos:

Vulnerabilidades en la autenticación: Métodos tradicionales como contraseñas estáticas pueden ser fácilmente comprometidos.

Fricción en la experiencia de usuario: Los usuarios suelen olvidar contraseñas o recurren a combinaciones inseguras, lo que complica la autenticación.

Dificultades en la integración con otros servicios: Sin un sistema de autenticación centralizado, cada servicio debe implementar sus propias reglas de validación.

Gestión de sesiones ineficiente: Sin un mecanismo estandarizado de gestión de sesiones, los usuarios pueden experimentar cierres de sesión inesperados o accesos no autorizados.

🎯 Beneficios Esperados

Seguridad Mejorada: Implementación de OAuth 2.0 y JWT para reforzar la protección de credenciales y sesiones mediante estándares de la industria.

Experiencia de Usuario Optimizada: Al permitir autenticación con Google y Microsoft, se reduce la necesidad de recordar múltiples credenciales y se agiliza el proceso de inicio de sesión.

Interoperabilidad: Facilita la integración con los distintos microservicios de Parfinanciero, permitiendo una gestión de usuarios centralizada.

Escalabilidad y Mantenimiento: Un sistema basado en OAuth y JWT permite una administración eficiente de usuarios y acceso a servicios sin necesidad de implementar múltiples mecanismos de autenticación.

Cumplimiento Normativo: Cumple con los estándares de seguridad y privacidad requeridos en plataformas financieras modernas, garantizando el manejo adecuado de datos sensibles.

👥 Público Objetivo

Este servicio está diseñado para atender a múltiples actores dentro del ecosistema de Parfinanciero:

Usuarios finales: Personas que acceden a la plataforma para gestionar su información financiera de manera segura y sencilla.

Administradores del sistema: Responsables de supervisar la gestión de usuarios, permisos y accesos a los distintos servicios dentro de la plataforma.

Desarrolladores e integradores: Equipos encargados de conectar otros microservicios con este sistema de autenticación para garantizar un acceso seguro y uniforme a los datos.

Entidades regulatorias y de seguridad: Organismos interesados en que la plataforma cumpla con normativas de privacidad y seguridad en la gestión de identidad digital.
