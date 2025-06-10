# Resumen de Pruebas de API

## Introducción a las API Web

Una Interfaz de Programación de Aplicaciones Web (API) facilita la comunicación y el intercambio de datos entre diferentes sistemas de software a través de una red o internet. Las API web permiten que diferentes aplicaciones interactúen entre sí de forma estandarizada y eficiente, lo que les permite aprovechar las funcionalidades y los datos de cada una.

La adopción de diferentes tecnologías, como la computación en la nube, las arquitecturas de microservicios y las aplicaciones de página única, ha contribuido a la adopción de las API como un movimiento arquitectónico.

Al igual que con la introducción de cualquier concepto nuevo, puede haber fallas y vulnerabilidades que requieran pruebas. De lo contrario, las API con poca seguridad pueden proporcionar una ruta directa sin restricciones a datos confidenciales.

Este capítulo intenta guiar al investigador de seguridad en los conceptos necesarios para probar API. Esta sección, en particular, investiga las diferentes tecnologías de API y su historia.

## ¿Qué tecnología de API?

Antes de hacer suposiciones sobre el tipo de API que estamos probando, puede ser útil conocer el alcance completo del espacio de problemas que el investigador de seguridad puede encontrar. Estas incluyen:

1. API de Transferencia de Estado Representacional (REST)
2. API de Protocolo Simple de Acceso a Objetos (SOAP)
3. API de GraphQL
4. Llamadas a Procedimiento Remoto gRPC (gRPC)
5. API de WebSockets

## API REST (Transferencia de Estado Representacional)

### ¿Qué es REST?

REST es un conjunto de reglas y convenciones para interactuar con recursos web. Los componentes clave de URI, métodos HTTP, encabezados y códigos de estado respaldan los principios de REST.

### Historia

Debido a su simplicidad, escalabilidad y compatibilidad con la infraestructura web existente, las API basadas en REST se han convertido en la arquitectura de API más común en internet al momento de escribir este artículo. Las API basadas en REST no se materializaron de inmediato, sino que tuvieron un largo camino desde la investigación hasta su adopción.

En 1994, Roy Fielding, uno de los principales autores de la especificación HTTP, comenzó a trabajar en REST como parte de su tesis doctoral en la Universidad de California, Irvine. En el año 2000, publicó su tesis. [Architectural Styles and the Design of Network-based Software Architectures](https://ics.uci.edu/~fielding/pubs/dissertation/top.htm), Donde introdujo y definió REST como un estilo arquitectónico. REST fue diseñado para aprovechar las características existentes de HTTP, enfatizando la escalabilidad, las interacciones sin estado y una interfaz uniforme.

En la década de 2010, REST se convirtió en el estándar de facto para las API web debido a su simplicidad y compatibilidad con la arquitectura subyacente de la web. El uso generalizado de las API RESTful fue impulsado por el crecimiento de las aplicaciones móviles, la computación en la nube y la arquitectura de microservicios. El desarrollo de herramientas y marcos como Swagger/OpenAPI, RAML y API Blueprint facilitó el diseño, la documentación y las pruebas de las API REST.

Para la década de 2020, los desarrollos modernos evolucionaron REST con tecnologías como GraphQL. Además, la especificación OpenAPI/Swagger se convirtió en un estándar ampliamente adoptado para describir las API REST, lo que permitió una mejor integración y automatización.

### Identificadores Uniformes de Recursos

Las API REST utilizan Identificadores Uniformes de Recursos (URI) para acceder a los recursos. Los URI son un elemento crucial de una arquitectura REST. Un URI es una cadena de caracteres que identifica de forma única un recurso específico. Se utilizan ampliamente en internet para localizar e interactuar con recursos, como páginas web, archivos y servicios.

Un URI consta de varios componentes, cada uno con una función específica. La sintaxis genérica de URI, tal como se define en [RFC3986](https://tools.ietf.org/html/rfc3986) asi:

> `URI = scheme "://" authority "/" path [ "?" query ] [ "#" fragment ]`

Para REST, el **esquema** suele ser `HTTP` o `HTTPS`, pero indica genéricamente el protocolo o método utilizado para acceder al recurso. Otros esquemas comunes incluyen `ftp`, `mailto` y `file`.

La **autoridad** especifica el nombre de dominio o la dirección IP del servidor donde reside el recurso y puede incluir un número de puerto. También puede incluir la información del usuario como subcomponente.

La **ruta** especifica la ubicación específica del recurso en el servidor. Nos interesa la ruta del URI como la relación entre el usuario y los recursos. Por ejemplo, `https://api.example.com/admin/testing/report` puede mostrar un informe de prueba. Existe una relación entre el usuario `admin` y sus informes.

La ruta de cualquier URI definirá un modelo de recurso de la API REST. Los recursos se separan mediante una barra diagonal y se basan en un diseño descendente.

Por ejemplo:

- `https://api.example.com/admin/testing/report`
- `https://api.example.com/admin/testing/`
- `https://api.example.com/admin/admin/`

La **consulta** proporciona parámetros adicionales para el recurso. Comienza con un `?` y consta de pares clave-valor separados por un `&`.

El **fragmento** indica una parte específica del recurso, como una sección dentro de una página web. Comienza con un `#`. Cabe destacar que los identificadores de fragmentos solo se procesan en el lado del cliente y no se envían al servidor.

### Métodos HTTP

Las API REST utilizan métodos HTTP estándar para realizar operaciones en los recursos siguiendo el [los HTTP Requests](https://tools.ietf.org/html/rfc7231#section-4) definidos en [RFC7231](https://tools.ietf.org/html/rfc7231). Estos métodos se corresponden con CRUD, las cuatro funciones básicas del almacenamiento persistente en informática. CRUD significa Crear, Leer, Actualizar y Eliminar, que son las cuatro operaciones que se pueden realizar con los datos.

Los métodos de solicitud HTTP son:

| Métodos | Descripción |
|---------|-----------------------------------------------|
| GET | Obtener la representación del estado del recurso |
| POST | Crear un nuevo recurso |
| PUT | Actualizar un recurso |
| DELETE | Eliminar un recurso |
| HEAD | Obtener los metadatos asociados al estado del recurso |
| OPTIONS | Lista de los métodos disponibles |

#### Encabezados

REST se basa en encabezados para facilitar la comunicación de información adicional dentro de la solicitud o respuesta. Estos incluyen:

- `Content-Type`: Indica el tipo de medio del recurso (p. ej., `application/json`).
- `Authorization`: Contiene las credenciales para la autenticación (p. ej., tokens). - `Aceptar`: Especifica los tipos de medios aceptables para la respuesta.

#### Códigos de estado

Las API de aplicación que cumplen con los principios REST utilizan el código de estado de respuesta de un mensaje HTTP para notificar al cliente el resultado de su solicitud.

| Código de respuesta | Mensaje de respuesta | Descripción |
|---------------|-----------------------------------|------------------------------------------------------------------------------------------|
| 200 | OK | Solicitud del cliente procesada correctamente |
| 201 | Creado | Nuevo recurso creado |
| 301 | Movido permanentemente | Redirección permanente |
| 304 | Sin modificar | Respuesta relacionada con el almacenamiento en caché que se devuelve cuando el cliente tiene la misma copia del recurso que el servidor |
| 307 | Redirección temporal | Redirección temporal del recurso |
| 400 | Solicitud incorrecta | Solicitud mal formada por el cliente |
| 401 | No autorizado | El cliente no puede realizar solicitudes ni acceder a un recurso específico |
| 402 | Prohibido | El cliente no puede acceder al recurso |
| 404 | No encontrado | El recurso no existe o es incorrecto según la solicitud |
| 405 | Método no permitido | El método utilizado no es válido o es desconocido |
| 500 | Error interno del servidor | El servidor no pudo procesar la solicitud debido a un error interno |

## Referencias

1. [OWASP REST Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html)
2. [OWASP REST Assessment Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Assessment_Cheat_Sheet.html)
3. [OWASP API Security Project](https://owasp.org/www-project-api-security/)
4. [OWASP API Security Tools](https://owasp.org/www-community/api_security_tools)
