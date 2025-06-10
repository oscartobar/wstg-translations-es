# Autorización a Nivel de Objeto Incorrecta de la API

|ID |
|------------|
|WSTG-APIT-02|

## Resumen

La Autorización a Nivel de Objeto Incorrecta (BOLA) se produce cuando una API no aplica correctamente las comprobaciones de autorización para cada objeto al que accede el cliente. Los atacantes pueden manipular los identificadores de objeto en las solicitudes de la API (como ID, GUID o tokens) para acceder o modificar recursos a los que no están autorizados. Esta vulnerabilidad es crítica en las API debido a su acceso directo a los objetos subyacentes y a su prevalencia en las aplicaciones modernas.

La explotación de BOLA puede provocar acceso no autorizado a datos confidenciales, suplantación de identidad de usuarios, escalada horizontal de privilegios (acceso a los recursos de otros usuarios) y escalada vertical de privilegios (obtención de acceso no autorizado a nivel de administrador).

## Objetivos de la prueba

- El objetivo de esta prueba es identificar si la API aplica las comprobaciones de **autorización a nivel de objeto** adecuadas, garantizando que los usuarios solo puedan acceder y manipular los objetos con los que están autorizados a interactuar.

## Cómo realizar pruebas

### Comprender los puntos finales de la API y las referencias a objetos

Revise la documentación de la API (p. ej., la especificación de OpenAPI), el tráfico o utilice un proxy de interceptación (p. ej., **Burp Suite**, **ZAP**) para identificar los puntos finales que aceptan identificadores de objeto de interés. Estos pueden ser **ID**, **UUID** u otras referencias.

Ejemplos:

- `GET /api/users/{user_id}`
- `GET /api/orders/{order_id}`
- `POST /graphql`\
`query: {user(id: "123") }`

Con los conocimientos adquiridos en el paso anterior, revise y recopile identificadores de objeto de terceros (p. ej., ID de usuario, ID de pedido, etc.) que puedan utilizarse posteriormente en la manipulación de identificadores de objeto.

Además, genere una lista de posibles identificadores de objeto para ataques de fuerza bruta. Por ejemplo, si una API recupera una orden de compra de un usuario autenticado, genere varios ID de orden de compra para realizar pruebas.

### Manipular identificadores de objetos en solicitudes de API

Para determinar si los usuarios pueden acceder o modificar objetos que no les pertenecen modificando los identificadores de objeto en la solicitud de API, cambie el identificador de objeto (p. ej., ID de usuario, ID de orden) en la URL o el cuerpo de la solicitud.

Ejemplo: Modifique una solicitud como `GET /api/users/123/profile` (donde 123 es el ID de usuario actual) a `GET /api/users/124/profile` (donde 124 es el ID de otro usuario).

Según el contexto de la aplicación, utilice dos cuentas diferentes para realizar las pruebas. Con la cuenta A, cree recursos que pertenezcan exclusivamente a esa cuenta (p. ej., una orden de compra) y con la cuenta B, intente acceder al recurso desde la cuenta A (p. ej., una orden de compra).

### Prueba de acceso a nivel de objeto con diferentes métodos HTTP

Prueba varios **métodos HTTP** para detectar vulnerabilidades BOLA:

- **GET**: Intenta acceder a objetos no autorizados manipulando el ID del objeto en la solicitud.
- **POST/PUT/PATCH**: Intenta crear o modificar objetos que pertenecen a otros usuarios.
- **DELETE**: Intenta eliminar un objeto propiedad de otro usuario.

### Prueba de BOLA en las API de GraphQL

Para las **API de GraphQL**, envíe una consulta con un ID de objeto modificado en los parámetros de consulta (consulte [Pruebas de GraphQL](https://owasp.org/www-project-web-security-testing-guide/stable/4-Pruebas_de_Seguridad_de_aplicaciones_Web/12-API_Testing/01-Testing_GraphQL)):

Ejemplo: `query { user(id: "124") { name, email } }`.

### Prueba de acceso masivo a objetos

Comprueba si la API permite el **acceso masivo** no autorizado a los objetos. Esto podría ocurrir en endpoints que devuelven listas de objetos.

Ejemplo: `GET /api/users` devuelve datos de todos los usuarios en lugar de solo los datos del usuario autenticado.

## Indicadores de BOLA

- **Explotación exitosa**: Si al modificar el ID de un objeto en la solicitud se devuelven datos o se permiten acciones en objetos que pertenecen a otros usuarios, la API es vulnerable a BOLA.
- **Respuestas de error**: Las API con la seguridad adecuada generalmente devolverían un error `403 Prohibido` o `401 No autorizado` para el acceso no autorizado a objetos. Una respuesta `200 OK` para el objeto de otro usuario indica BOLA.
- **Respuestas inconsistentes**: Si algunos endpoints aplican la autorización y otros no, esto indica controles de seguridad incompletos o inconsistentes.

## Solución

- **Verificaciones de propiedad de objetos**: Asegúrese de que se realicen verificaciones de autorización a nivel de objeto para cada solicitud de API. Verifique siempre que el usuario que realiza la solicitud esté autorizado para acceder al objeto solicitado.
- **Control de acceso basado en roles (RBAC)**: Implemente políticas de RBAC que definan qué roles pueden acceder o modificar objetos específicos. - **Principio de Mínimo Privilegio**: Aplique el principio de mínimo privilegio para garantizar que los usuarios solo puedan acceder al conjunto mínimo de objetos necesarios para su rol.
- **Usar UUID o IDs no secuenciales**: Prefiera identificadores de objetos no predecibles ni secuenciales (por ejemplo, **UUID** en lugar de enteros simples) para dificultar la enumeración y los ataques de fuerza bruta.

## Herramientas

- **ZAP**: Los escáneres automatizados o las herramientas de proxy manuales pueden ayudar a probar las referencias de objetos en las solicitudes de API.
- **Burp Suite**: Use las herramientas **Repeater** o **Intruder** para manipular los IDs de objetos y enviar múltiples solicitudes para probar el control de acceso.
- **Postman**: Envíe solicitudes con IDs de objetos modificados y observe las respuestas.
- **Herramientas de Fuzzing**: Use fuzzers para realizar un análisis de fuerza bruta de los IDs de objetos y comprobar si hay accesos no autorizados.

## Referencias

- [OWASP API Security Top 10: BOLA](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)
- [OWASP Testing Guide: Testing for Insecure Direct Object References (IDOR)](https://owasp.org/www-project-web-security-testing-guide/stable/4-Pruebas_de_Seguridad_de_aplicaciones_Web/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References)
- [OWASP Testing Guide: Testing for GraphQL](https://owasp.org/www-project-web-security-testing-guide/stable/4-Pruebas_de_Seguridad_de_aplicaciones_Web/12-API_Testing/01-Testing_GraphQL)  
