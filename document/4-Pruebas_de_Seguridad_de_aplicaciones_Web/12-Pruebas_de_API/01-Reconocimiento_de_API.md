# Reconocimiento de API

|ID |
|------------|
|WSTG-APIT-01|

## Resumen

El reconocimiento es un paso importante en cualquier prueba de penetración, incluyendo las pruebas de penetración de API. El reconocimiento mejora significativamente la eficacia del proceso de prueba al recopilar información sobre la API y comprender mejor el objetivo. Esta fase no solo aumenta la probabilidad de descubrir problemas críticos de seguridad, sino que también garantiza una evaluación exhaustiva de la seguridad de las API.

Esta guía incluye una sección sobre [Recopilación de información](../01-Recopilacion_de_informacion/README.md) que puede aplicarse a la auditoría de API. Sin embargo, existen algunas diferencias. Como investigadores de seguridad, a menudo nos centramos en áreas específicas, y buscar en esta guía las secciones pertinentes puede llevar mucho tiempo. Para garantizar que el investigador tenga un único punto de referencia para las API, esta sección se centra en los elementos que se aplican a ellas y proporciona referencias a contenido de apoyo en otras secciones de la guía.

### Tipos de API

Las API pueden ser públicas o privadas.

#### API Públicas

Las API públicas suelen publicar sus detalles en un documento Swagger/OpenAPI. Acceder a este documento es importante para comprender la superficie de ataque. Igualmente importante es encontrar versiones anteriores de este documento que podrían mostrar código obsoleto, pero aún funcional, con vulnerabilidades de seguridad.

Tenga en cuenta que este documento, por muy bien intencionado que sea, puede no ser preciso y no divulgar la API completa.

Las API públicas también pueden documentarse en bibliotecas compartidas o directorios de API.

#### API Privadas

La visibilidad de las API privadas depende de quién sea el consumidor previsto. Una API puede ser privada, pero solo accesible para clientes suscritos (también conocidos como "socios") o solo para clientes internos, como otros departamentos de la misma empresa. También es importante encontrar API privadas mediante técnicas de reconocimiento. Estas API se pueden descubrir mediante diversas técnicas que explicaremos a continuación.

## Objetivos de la prueba

- Encontrar todos los endpoints de la API compatibles con el código del servidor backend, documentados o no.
- Encontrar todos los parámetros para cada endpoint compatible con el servidor backend, documentados o no.
- Descubrir datos interesantes relacionados con las API en HTML y JavaScript enviados a los clientes.

## Cómo realizar la prueba

### Encontrar la documentación

Tanto en casos públicos como privados, la documentación de la API será útil debido a su calidad y precisión. La documentación pública de la API suele compartirse con todos, mientras que la privada solo se comparte con el cliente objetivo. Sin embargo, en ambos casos, encontrar documentación, ya sea filtrada accidentalmente o de otro tipo, será útil en la investigación.

Independientemente de la visibilidad de la API, buscar documentación de la API puede encontrar documentación antigua, aún no publicada o filtrada accidentalmente. Esta documentación será muy útil para comprender la superficie de ataque que expone la API.

Directorios de API

Otras fuentes alternativas de documentación de API incluyen directorios de API, como:

- GitHub en general
- [GitHub Public APIs Repository](https://github.com/public-apis/public-apis)
- [APIs.guru](https://apis.guru)
- [RapidAPI](https://rapidapi.com/)
- [PublicAPIs](https://publicapis.dev/) and [PublicAPIs](https://publicapis.io/)
- [Postman API Network](https://www.postman.com/explore)

### Búsqueda en sitios conocidos

Si la documentación no es evidente, puede buscar activamente documentación en el objetivo basándose en algunos nombres o rutas obvias. Estos incluyen:

- /api-docs
- /doc
- /swagger
- /swagger.json
- /openapi.json
- /.well-known/schema-discovery

### Robots.txt

`robots.txt` es un archivo de texto que los propietarios de sitios web crean para indicar a los rastreadores web (como los bots de los motores de búsqueda) cómo rastrear e indexar su sitio. Forma parte del Protocolo de Exclusión de Robots (REP), que regula cómo interactúan los bots con los sitios.

Este archivo puede proporcionar pistas adicionales sobre la estructura de la ruta o los endpoints de la API.

La sección [Recopilación de Información](../01-Recopilacion_de_informacion/README.md) hace referencia a robots.txt en varios casos, incluyendo WSTG-INFO-01, WSTG-INFO-03, WSTG-INFO-05 y WSTG-INFO-08.

### GitDorking

Si la aplicación utiliza GitHub, GitLab u otros repositorios públicos basados ​​en Git, también podemos buscar pistas o contenido sensible (también conocido como `GitDorking`). Esta información puede incluir contraseñas, claves de API, archivos de configuración y otros datos confidenciales que los desarrolladores podrían compartir accidental o inadvertidamente en sus repositorios. Las organizaciones pueden compartir accidentalmente código sensible, de muestra o de prueba que podría proporcionar pistas sobre los detalles de la implementación. Las cuentas personales de GitHub de los empleados de la aplicación también podrían publicar accidentalmente información que podría proporcionar pistas.

### Exploración y rastreo de la aplicación

Incluso con la documentación de la API, es recomendable explorar la aplicación. La documentación puede estar desactualizada, ser inexacta o incompleta.

Explorar la aplicación con un proxy de interceptación, como ZAP o Burp Suite, registra los endpoints para su posterior inspección. Además, al usar su funcionalidad de rastreo integrada, los proxies de interceptación pueden ayudar a generar una lista completa de endpoints. En las URL rastreadas, busque enlaces con esquemas de nombres de URL de API obvios. Estos incluyen:

- `https://example.com/api/v1` (o v2, etc.)
- `https://example.com/graphql`

O subdominios que las aplicaciones puedan consumir o de los que dependan:

- `https://api.example.com/api/v1`

Es importante que el pentester intente ejecutar la mayor cantidad de funcionalidad posible en la aplicación. Esto no solo sirve para generar una lista completa de endpoints, sino también para evitar problemas de carga diferida y división de código. Además, su prueba de penetración debe incluir cuentas de muestra con diferentes niveles de privilegio para que su navegador y el rastreo de datos puedan acceder y exponer los endpoints con la mayor funcionalidad posible.

Una vez completada, la información de los endpoints obtenida al navegar y rastrear la aplicación puede ayudar al pentester a crear la documentación de la API del objetivo utilizando otras herramientas como Postman.

### Google Dorking

El uso de técnicas de reconocimiento pasivo como Google Dorking con directivas como `site` e `inurl` nos permite personalizar la búsqueda de palabras clave comunes de la API que el indexador de Google pueda haber encontrado. Consulte [Realizar un reconocimiento de descubrimiento en motores de búsqueda para detectar fugas de información].(../01-Recopilacion_de_informacion/01-Reconocimiento_en_motores_de_Busqueda.md) para mas información.

Aquí hay algunos ejemplos específicos de API:

`site:"mytargetsite.com" inurl:"/api"`

`inurl:apikey filetype:env`

Otras palabras clave pueden incluir `"v1"`, `"api"`, `"graphql"`.

Podemos ampliar Google Dorking para incluir subdominios del objetivo.

Las listas de palabras son útiles para obtener una lista completa de palabras comunes utilizadas en las API.

### Retrospectiva

En general, las API cambian con el tiempo. Sin embargo, es posible que versiones obsoletas o antiguas sigan funcionando, ya sea intencionalmente o por una configuración incorrecta. Estas también deben probarse, ya que es muy probable que contengan vulnerabilidades que las versiones más recientes hayan corregido. Además, los cambios en las API muestran características más nuevas que pueden ser menos robustas y, por lo tanto, un buen candidato para las pruebas.

Para descubrir versiones anteriores, podemos usar la `Wayback Machine` para encontrar endpoints antiguos. Una herramienta útil conocida como TomNomNom [WayBackUrls](https://github.com/tomnomnom/waybackurls) Obtiene todas las URL que Wayback Machine conoce para un dominio..

- [WayBackUrls](https://github.com/tomnomnom/waybackurls). Obtiene todas las URL que Wayback Machine conoce para un dominio.
- [waymore](https://github.com/xnl-h4ck3r/waymore). Encuentra mucha más información de Wayback Machine, Common Crawl, Alien Vault OTX, URLScan y VirusTotal.
- [gau](https://github.com/lc/gau). Obtiene URL conocidas de Open Threat Exchange de AlienVault, Wayback Machine y Common Crawl.

### La aplicación del lado del cliente

Una excelente fuente de información sobre API y otros datos es el HTML y JavaScript que el servidor envía al cliente. En ocasiones, la aplicación cliente filtra información confidencial, como API y secretos. La sección [Revisar el contenido de la página web para detectar fugas de información](../01-Information_Gathering/05-Review_Web_Page_Content_for_Information_Leakage.md) ofrece información general para revisar el contenido web en busca de fugas. Aquí ampliaremos el tema para centrarnos en la revisión del contenido JavaScript en busca de secretos relacionados con la API.

Existen diversas herramientas que podemos utilizar para extraer información confidencial del JavaScript transmitido al navegador. Estas herramientas suelen basarse en uno de dos enfoques: expresiones regulares o árboles de sintaxis abstracta (AST). También existen herramientas generalizadas que nos ayudan a organizar o gestionar archivos JS para su análisis mediante AST y herramientas de expresiones regulares.

Las expresiones regulares son más sencillas al buscar patrones conocidos en contenido JS o HTML. Sin embargo, este enfoque puede pasar por alto contenido no identificado explícitamente en la expresión regular. Dada la estructura de algunos JS, este enfoque puede pasar por alto muchos aspectos. Los AST, por otro lado, son estructuras tipo árbol que representan la sintaxis del código fuente. Cada nodo del árbol corresponde a una parte del código. En JavaScript, un AST divide el código en componentes básicos, lo que permite que las herramientas y compiladores lo comprendan y modifiquen fácilmente.

#### Herramientas generales

1. [Uproot](https://github.com/0xDexter0us/uproot-JS). Un plugin de BurpSuite que guarda en disco los archivos JS encontrados. Esto ayuda a extraer los archivos para su análisis mediante herramientas de línea de comandos.

2. [OpenAPI Support](https://www.zaproxy.org/docs/desktop/addons/openapi-support/). Este complemento de ZAP permite rastrear e importar definiciones de OpenAPI (Swagger), versiones 1.2, 2.0 y 3.0. 3. [Analizador OpenAPI](https://github.com/aress31/openapi-parser). Un plugin de BurpSuite que analiza documentos OpenAPI en BurpSuite para automatizar las evaluaciones de seguridad de las API basadas en OpenAPI.

#### Herramientas de expresiones regulares

1. [JSParser](https://github.com/nahamsec/JSParser). Un script de Python 2.7 que usa Tornado y JSBeautifier para analizar URL relativas de archivos JavaScript.

2. [JSMiner](https://github.com/PortSwigger/js-miner). Un plugin de BurpSuite que busca información interesante dentro de archivos estáticos, principalmente archivos JavaScript y JSON. Esta herramienta escanea pasivamente mientras rastrea la aplicación.

3. [JSpector](https://github.com/hisxo/JSpector). Un plugin de BurpSuite que rastrea pasivamente archivos JavaScript y crea automáticamente problemas con URL, endpoints y métodos peligrosos encontrados en los archivos JS.

4. [Buscador de enlaces](https://github.com/GerbenJavado/LinkFinder). Un script de Python que encuentra endpoints en archivos JavaScript.

#### Herramientas AST

1. [JSLuice](https://github.com/BishopFox/jsluice). Una herramienta de línea de comandos que extrae URL, rutas, secretos y otros datos interesantes del código fuente de JavaScript.

### Otras herramientas de reconocimiento

1. [Detector de superficie de ataque](https://github.com/secdec/attack-surface-detector-burp). Un plugin de BurpSuite que utiliza análisis de código estático para identificar endpoints de aplicaciones web mediante el análisis de rutas y la identificación de parámetros.

2. [Minero de parámetros](https://github.com/portswigger/param-miner). Un plugin de BurpSuite que identifica parámetros ocultos y no vinculados.
3. [xnLinkFinder](https://github.com/xnl-h4ck3r/xnLinkFinder). Una herramienta de Python que se utiliza para descubrir endpoints, parámetros potenciales y una lista de palabras específica para un objetivo determinado.
4. [GAP](https://github.com/xnl-h4ck3r/GAP-Burp-Extension). Una extensión de Burp que busca endpoints potenciales, parámetros y genera una lista de palabras personalizada.

### Fuzzing Activo

El fuzzing activo implica el uso de herramientas con listas de palabras y el filtrado de resultados de solicitudes para forzar la detección de endpoints.

#### Kiterunner

[KiteRunner](https://github.com/assetnote/kiterunner) es una herramienta que realiza el descubrimiento de contenido tradicional y fuerza bruta en rutas/endpoints en aplicaciones y API modernas.

```console
kr [scan|brute] <input> [flags]
```

Para escanear un objetivo en busca de API usando una lista de palabras, podemos:

```console
kr scan https://example.com/api -w /usr/share/wordlists/apis/routes-large.kite --fail-status-codes 404,403
```

#### FFUF/DirBuster/GoBuster

FFUF, DirBuster y GoBuster están diseñados para descubrir rutas y archivos ocultos en servidores web mediante técnicas de fuerza bruta. Los tres utilizan listas de palabras personalizables para generar solicitudes al servidor web de destino, intentando identificar directorios y archivos válidos. Los tres admiten procesamiento multihilo o de alta eficiencia para acelerar el proceso de fuerza bruta.

Algunos archivos de listas de palabras comunes para API incluyen: [SecLists](https://github.com/danielmiessler/SecLists) en la sección Discovery/Web-Content/api, [GraphQL Wordlist](https://github.com/Escape-Technologies/graphql-wordlist) y [Assetnote](https://wordlists.assetnote.io/).

Ejemplo de GoBuster:

`gobuster dir -u <target url> -w <wordlist file>`

## Referencias
### OWASP Recursos

- [REST Assessment Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Assessment_Cheat_Sheet.html)

### Libros

- Corey J. Ball - "Hacking APIs : breaking web application programming interfaces", No Starch, 2022 - ISBN-13: 978-1-7185-0244-4
- Confidence Staveley - "API Security for White Hat Hackers, Packt, 2024 - ISBN 978-1-80056-080-2  
