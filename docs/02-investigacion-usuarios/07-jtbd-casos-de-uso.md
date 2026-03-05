
<img width="1360" height="1760" alt="Diagrama de caso de uso" src="https://github.com/user-attachments/assets/2c6af54d-1154-4887-bcd3-40d4af165fae" />

# Diagrama de Casos de Uso — Sistema Traductor

## Descripción general

El siguiente diagrama de casos de uso representa las **principales interacciones entre los usuarios y el sistema Traductor**, una aplicación de traducción asistida por inteligencia artificial que opera bajo un enfoque **local-first**, donde los documentos y datos sensibles se procesan y almacenan localmente en el dispositivo del usuario.

El objetivo del diagrama es mostrar:

- Los **actores que interactúan con el sistema**
- Las **funcionalidades principales del sistema**
- Cómo los usuarios acceden a las distintas operaciones del sistema

El diagrama se encuentra representado en el documento adjunto:  
:contentReference[oaicite:0]{index=0}

---

# Límite del Sistema

El rectángulo central del diagrama representa el sistema llamado:

**TRADUCTOR**

Dentro de este límite se encuentran todos los **casos de uso**, que representan las funcionalidades disponibles en la aplicación.

Los **actores se encuentran fuera del sistema**, ya que representan entidades externas que interactúan con el software.

---

# Actores del Sistema

El diagrama incluye tres actores principales.

## Usuario sin autenticar

Este actor representa a un usuario que aún **no ha iniciado sesión en el sistema**.

Su interacción principal es:

- **Iniciar sesión**

Una vez autenticado, el usuario obtiene acceso a las funcionalidades del sistema.

---

## Usuario autenticado

Este actor representa a cualquier usuario que **ya inició sesión** y tiene acceso a funcionalidades del sistema relacionadas con la gestión de documentos y proyectos.

Puede interactuar con:

- importación de archivos
- edición de documentos
- exportación de documentos traducidos
- gestión de proyectos
- visualización de traducciones
- administración de glosarios

---

## Traductor

El actor **Traductor** representa al usuario especializado que utiliza el sistema para realizar traducciones asistidas por IA.

Sus responsabilidades principales incluyen:

- elegir idioma de traducción
- visualizar traducciones
- traducir palabras con contexto
- crear glosarios
- administrar glosarios

Este actor representa el **usuario principal del sistema**, ya que utiliza las capacidades de traducción de la aplicación.

---

# Casos de Uso Principales

El sistema incluye varias funcionalidades organizadas en diferentes dominios.

---

# Gestión de Sesión

Permite controlar el acceso al sistema.

Casos de uso:

- Iniciar sesión
- Cerrar sesión

Estos casos de uso permiten autenticar al usuario antes de acceder a las funcionalidades principales del sistema.

---

# Administración de Documentos

Este conjunto de casos de uso permite gestionar documentos dentro del sistema.

Incluye:

- Importar archivo
- Editar documento
- Exportar documento con traducción
- Eliminar proyecto

Estas acciones permiten trabajar con documentos que posteriormente serán procesados por el sistema de traducción.

---

# Traducciones

Este grupo de funcionalidades corresponde al **núcleo del sistema**.

Casos de uso:

- Elegir idioma
- Ver traducción
- Traducir palabra con su definición contextual

Estas operaciones permiten que el usuario interactúe con los modelos de IA para obtener traducciones.

---

# Gestión de Glosarios

Los glosarios permiten mantener **consistencia terminológica** en las traducciones.

Casos de uso:

- Crear glosario
- Administrar glosario

Esto permite que el sistema utilice terminología específica definida por el usuario.

---

# Flujo de Uso del Sistema

Un flujo típico de interacción con el sistema sería:

1. El usuario abre la aplicación.
2. El usuario inicia sesión.
3. Importa un archivo para traducir.
4. Edita o prepara el documento.
5. Selecciona el idioma de traducción.
6. El sistema genera traducciones utilizando IA.
7. El usuario revisa la traducción.
8. Puede exportar el documento traducido.
9. Finalmente cierra sesión.

---

# Importancia del Diagrama

El diagrama de casos de uso permite visualizar de manera clara:

- las funcionalidades principales del sistema
- qué usuarios pueden acceder a cada operación
- la estructura funcional del sistema Traductor

Además sirve como base para:

- diseño de interfaz
- implementación de permisos
- diseño de arquitectura del sistema
- planificación del desarrollo del software
