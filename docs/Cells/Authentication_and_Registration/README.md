# Autenticación y Registro - Parfinanciero

## 💡 Descripción General
Este microservicio es responsable de la autenticación y registro de usuarios en la plataforma **Parfinanciero**. Proporciona soporte para autenticación con Google y Microsoft mediante **OAuth 2.0** e implementa la gestión de usuarios en **PostgreSQL**. La integración segura con los demás microservicios se logra a través de **tokens JWT**.

---

## 📈 Objetivo del Servicio
- Permitir a los usuarios autenticarse de manera segura mediante **OAuth 2.0** con **Google y Microsoft**.
- Gestionar el registro y almacenamiento de usuarios en **PostgreSQL**.
- Implementar una integración segura con otros microservicios a través de **tokens JWT**.
- Desarrollar un middleware de seguridad para validar accesos y roles.

---

## ⚙️ Funcionalidades Clave
- **Registro y autenticación** mediante Google y Microsoft.
- **Gestión de usuarios** en PostgreSQL.
- **Generación y validación de tokens JWT** para autenticación segura.
- **Endpoints de autenticación y manejo de sesiones**.
- **Middleware de seguridad** para validación de roles y permisos.

---

## 🌐 Importancia dentro del Ecosistema de Parfinanciero
Este microservicio es **esencial** para el funcionamiento seguro de la plataforma **Parfinanciero**, ya que:
- **Controla el acceso** de usuarios y gestiona sus permisos.
- **Asegura la integración segura** con otros microservicios.
- **Facilita la gestión de roles** dentro del sistema.

