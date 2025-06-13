# Pruebas de API

Las API web han ganado mucha popularidad, ya que permiten que programas de terceros interactúen con sitios web de forma más eficiente y sencilla. En esta guía, analizaremos algunos conceptos básicos sobre las API y cómo probar su seguridad.

## Conceptos básicos

REST (Transferencia de Estado Representacional) es una arquitectura que se implementa mientras los desarrolladores diseñan las API.
Las API de aplicaciones web que siguen el estilo REST se denominan API REST.
Las API REST utilizan URI (Identificadores Uniformes de Recursos) para acceder a los recursos. La sintaxis genérica de URI, tal como se define en [RFC3986](https://tools.ietf.org/html/rfc3986), es la siguiente:

> URI = scheme "://" authority "/" path [ "?" query ] [ "#" fragment ]

Nos interesa la ruta del URI como la relación entre el usuario y los recursos. Por ejemplo, "https://api.test.xyz/admin/testing/report" muestra el informe de las pruebas y existe una relación entre el usuario administrador y sus informes.

La ruta de cualquier URI definirá el modelo de recursos de la API REST. Los recursos se separan por una barra diagonal y se basan en un diseño descendente.
Por ejemplo:

- "https://api.test.xyz/admin/testing/report"
- "https://api.test.xyz/admin/testing/"
- "https://api.test.xyz/admin/"

Las solicitudes de la API REST siguen los [Métodos de Solicitud HTTP](https://tools.ietf.org/html/rfc7231#section-4) definidos en [RFC7231](https://tools.ietf.org/html/rfc7231)

| Métodos | Descripción |
|---------|-----------------------------------------------|
| GET | Obtener la representación del estado del recurso |
| POST | Crear un nuevo recurso |
| PUT | Actualizar un recurso |
| DELETE | Eliminar un recurso |
| HEAD | Obtener los metadatos asociados al estado del recurso |
| OPTIONS | Listar los métodos disponibles |

Las API REST utilizan el código de estado de respuesta del mensaje HTTP para notificar al cliente el resultado de su solicitud.

| Código de respuesta | Mensaje de respuesta | Descripción |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------|
| 200 | OK | Solicitud del cliente procesada correctamente |
| 201 | Creado | Nuevo recurso creado |
| 301 | Movido permanentemente | Redirección permanente |
| 304 | Sin modificar | Respuesta relacionada con el almacenamiento en caché que se devuelve cuando el cliente tiene la misma copia del recurso que el servidor |
| 307 | Redirección temporal | Redirección temporal del recurso |
| 400 | Solicitud incorrecta | Solicitud mal formada del cliente |
| 401 | No autorizado | El cliente no puede realizar solicitudes ni acceder a un recurso en particular |
| 402 | Prohibido | El cliente no puede acceder al recurso |
| 404 | No encontrado | El recurso no existe o es incorrecto según la solicitud |
| 405 | Método no permitido | Método no válido o desconocido |
| 500 | Error interno del servidor | El servidor no pudo procesar la solicitud debido a un error interno |

Las cabeceras HTTP se utilizan en solicitudes y respuestas.
Al realizar solicitudes a la API, se utiliza la cabecera Content-Type, establecida en `application/json`, ya que el cuerpo del mensaje contiene datos en formato JSON.

Los tipos de autenticación web se basan en:

- Tokens de portador: Se identifican mediante la cabecera `Authorization: Bearer <token>`. Al iniciar sesión, el usuario recibe un token de portador que se envía en cada solicitud para autenticarse y autorizar el acceso a recursos protegidos por OAuth 2.0.
- Cookies HTTP: Se identifican mediante la cabecera `Cookie: <name>=<unique value>`. Si el usuario inicia sesión correctamente, el servidor responde con la cabecera `Set-Cookie`, que especifica su nombre y valor único. En cada solicitud, el navegador la añade automáticamente a las solicitudes dirigidas a ese servidor, siguiendo [SOP](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy).
- Autenticación HTTP básica: Se identifica mediante el encabezado `Authorization: Basic <base64 value>`. Cuando un usuario intenta iniciar sesión, la solicitud se envía con el encabezado mencionado, que contiene un valor base64, cuyo contenido es `username:password`. Este es uno de los métodos de autenticación más débiles, ya que transmite el nombre de usuario y la contraseña en cada solicitud de forma cifrada, lo que facilita su recuperación.

## Cómo realizar pruebas

### Método de prueba genérico

Paso 1: Listar los endpoints y crear un método de solicitud diferente: Inicie sesión con el perfil de usuario y utilice una herramienta de rastreo para listar los endpoints de este rol.
Para examinar los endpoints, deberá crear diferentes métodos de solicitud y observar el comportamiento de la API.

Paso 2: Aprovechar errores: Como ya sabemos cómo listar y examinar endpoints con métodos HTTP en el paso 1, buscaremos maneras de aprovechar errores. A continuación, se presentan algunas estrategias de prueba:

- Pruebas IDOR
- Escalada de privilegios

### Pruebas específicas: autenticación (basada en tokens)

La autenticación basada en tokens se implementa enviando un token firmado (verificado por el servidor) con cada solicitud HTTP.

El formato de token más utilizado es el JSON Web Token (JWT), definido en [RFC7519](https://tools.ietf.org/html/rfc7519). La guía [Testing JSON Web Tokens](/document/4-Pruebas_de_Seguridad_de_aplicaciones_Web/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens.md) contiene más detalles sobre cómo probar JWT.

## Casos de prueba relacionados

- [IDOR](https://github.com/OWASP/wstg/blob/master/document/4-Pruebas_de_Seguridad_de_aplicaciones_Web/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References.md)
- [Escalada de privilegios](https://github.com/OWASP/wstg/blob/master/document/4-Pruebas_de_Seguridad_de_aplicaciones_Web/05-Authorization_Testing/03-Testing_for_Privilege_Escalation.md)
- Todo [Sesión Casos de prueba de administración (https://github.com/OWASP/wstg/tree/master/document/4-Pruebas_de_Seguridad_de_aplicaciones_Web/06-Session_Management_Testing)
- [Pruebas de tokens web JSON](/document/4-Pruebas_de_Seguridad_de_aplicaciones_Web/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens.md)

## Herramientas

- ZAP
- Conjunto de herramientas Burp

## Referencias

- [Métodos HTTP REST](https://restfulapi.net/http-methods/)
- [URI RFC3986](https://tools.i)
