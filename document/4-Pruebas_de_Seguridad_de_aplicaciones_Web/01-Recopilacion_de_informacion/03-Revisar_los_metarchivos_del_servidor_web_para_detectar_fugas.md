# Revisar los metarchivos del servidor web para detectar fugas de información

|ID |
|------------|
|WSTG-INFO-03|

## Resumen

Esta sección describe cómo probar varios archivos de metadatos para detectar fugas de información en las rutas o funcionalidades de la aplicación web. Además, la lista de directorios que deben evitar las arañas, robots o rastreadores también puede crearse como dependencia de [Mapear rutas de ejecución a través de la aplicación](07-Map_Execution_Paths_Through_Application.md). También se puede recopilar otra información para identificar la superficie de ataque, detalles tecnológicos o para su uso en estrategias de ingeniería social.

## Objetivos de la prueba

- Identificar rutas y funcionalidades ocultas u ofuscadas mediante el análisis de los archivos de metadatos.
- Extraer y mapear otra información que pueda ayudar a comprender mejor los sistemas en cuestión.

## Cómo realizar la prueba

> Cualquiera de las acciones realizadas a continuación con `wget` también puede realizarse con `curl`. Muchas herramientas de Pruebas Dinámicas de Seguridad de Aplicaciones (DAST), como ZAP y Burp Suite, incluyen comprobaciones o análisis de estos recursos como parte de su funcionalidad de araña/rastreador. También se pueden identificar utilizando varios [Google Dorks](https://en.wikipedia.org/wiki/Google_hacking) o aprovechando funciones de búsquedas avanzadas como `inurl:`.


### Robots

Las arañas web, robots o rastreadores recuperan una página web y luego recorren recursivamente los hipervínculos para recuperar más contenido. Su comportamiento aceptado está especificado por el [Robots Exclusion Protocol](https://www.robotstxt.org) de el archivo [robots.txt](https://www.robotstxt.org/) en el directorio raíz web.

A modo de ejemplo, se cita a continuación el inicio del archivo `robots.txt` de [Google](https://www.google.com/robots.txt), muestreado el 5 de mayo de 2020:

```text
User-agent: *
Disallow: /search
Allow: /search/about
Allow: /search/static
Allow: /search/howsearchworks
Disallow: /sdch
...
```

La directiva [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)  se refiere a la araña, robot o rastreador web específico. Por ejemplo, «User-Agent: Googlebot» se refiere a la araña de Google, mientras que «User-Agent: bingbot» se refiere a un rastreador de Microsoft. «User-Agent: *» en el ejemplo anterior se aplica a todos los [web spiders/robots/crawlers](https://support.google.com/webmasters/answer/6062608?visit_id=637173940975499736-3548411022&rd=1).

La directiva `Disallow` especifica qué recursos están prohibidos por arañas, robots o rastreadores. En el ejemplo anterior, se prohíben los siguientes:

```text
...
Disallow: /search
...
Disallow: /sdch
...
```

Las arañas/robots/rastreadores web pueden [ignorar intencionalmente](https://blog.isc2.org/isc2_blog/2008/07/the-attack-of-t.html) las directivas «Disallow» especificadas en un archivo «robots.txt». Por lo tanto, «robots.txt» no debe considerarse un mecanismo para imponer restricciones sobre cómo terceros acceden, almacenan o republican el contenido web.

El archivo `robots.txt` se recupera del directorio raíz del servidor web. Por ejemplo, para recuperar `robots.txt` de `www.google.com` usando `wget` o `curl`:

```bash
$ curl -O -Ss https://www.google.com/robots.txt && head -n5 robots.txt
User-agent: *
Disallow: /search
Allow: /search/about
Allow: /search/static
Allow: /search/howsearchworks
...
```

#### Analizar robots.txt con las Herramientas para webmasters de Google

Los propietarios de sitios web pueden usar la función "Analizar robots.txt" de Google para analizar el sitio como parte de sus Herramientas para webmasters de Google (https://www.google.com/webmasters/tools). Esta herramienta puede ayudar con las pruebas y el procedimiento es el siguiente:

1. Inicia sesión en Herramientas para webmasters de Google con una cuenta de Google.
2. En el panel de control, introduce la URL del sitio que se va a analizar.
3. Elige entre los métodos disponibles y sigue las instrucciones en pantalla.

### Etiquetas META

Las etiquetas `<META>` se encuentran en la sección `HEAD` de cada documento HTML y deben ser coherentes en todo el sitio web en caso de que el punto de inicio del robot/araña/rastreador no comience desde un enlace del documento que no sea la raíz web, es decir, un [deep link](https://en.wikipedia.org/wiki/Deep_linking). La directiva Robots también se puede especificar utilizando un [META tag](https://www.robotstxt.org/meta.html).

#### Etiqueta META de Robots

Si no existe la entrada `<META NAME="ROBOTS" ... >`, el "Protocolo de Exclusión de Robots" toma por defecto `INDEX,FOLLOW`, respectivamente. Por lo tanto, las otras dos entradas válidas definidas por el "Protocolo de Exclusión de Robots" tienen el prefijo `NO...`, es decir, `NOINDEX` y `NOFOLLOW`.

Según las directivas "Disallow" del archivo `robots.txt` en la raíz web, se realiza una búsqueda de `<META NAME="ROBOTS"` en cada página web. El resultado se compara con el archivo robots.txt en la raíz web.

Etiquetas de información META miscelánea

Las organizaciones suelen incorporar etiquetas META informativas en el contenido web para facilitar diversas tecnologías, como lectores de pantalla, vistas previas de redes sociales, indexación en motores de búsqueda, etc. Esta metainformación puede ser útil para los evaluadores, ya que les permite identificar las tecnologías utilizadas y las rutas/funciones adicionales que explorar y probar. La siguiente metainformación se obtuvo de `www.whitehouse.gov` a través de Ver código fuente de la página el 5 de mayo de 2020:

```html
...
<meta property="og:locale" content="en_US" />
<meta property="og:type" content="website" />
<meta property="og:title" content="The White House" />
<meta property="og:description" content="We, the citizens of America, are now joined in a great national effort to rebuild our country and to restore its promise for all. – President Donald Trump." />
<meta property="og:url" content="https://www.whitehouse.gov/" />
<meta property="og:site_name" content="The White House" />
<meta property="fb:app_id" content="1790466490985150" />
<meta property="og:image" content="https://www.whitehouse.gov/wp-content/uploads/2017/12/wh.gov-share-img_03-1024x538.png" />
<meta property="og:image:secure_url" content="https://www.whitehouse.gov/wp-content/uploads/2017/12/wh.gov-share-img_03-1024x538.png" />
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:description" content="We, the citizens of America, are now joined in a great national effort to rebuild our country and to restore its promise for all. – President Donald Trump." />
<meta name="twitter:title" content="The White House" />
<meta name="twitter:site" content="@whitehouse" />
<meta name="twitter:image" content="https://www.whitehouse.gov/wp-content/uploads/2017/12/wh.gov-share-img_03-1024x538.png" />
<meta name="twitter:creator" content="@whitehouse" />
...
<meta name="apple-mobile-web-app-title" content="The White House">
<meta name="application-name" content="The White House">
<meta name="msapplication-TileColor" content="#0c2644">
<meta name="theme-color" content="#f5f5f5">
...
```

### Mapas del sitio

Un mapa del sitio es un archivo donde un desarrollador u organización puede proporcionar información sobre las páginas, los vídeos y otros archivos que ofrece el sitio o la aplicación, y la relación entre ellos. Los motores de búsqueda pueden usar este archivo para navegar por el sitio de forma más eficiente. Asimismo, los evaluadores pueden utilizar los archivos 'sitemap.xml' para obtener información más detallada sobre el sitio o la aplicación investigada.

El siguiente extracto proviene del mapa del sitio principal de Google, recuperado el 5 de mayo de 2020.

```bash
$ wget --no-verbose https://www.google.com/sitemap.xml && head -n8 sitemap.xml
2020-05-05 12:23:30 URL:https://www.google.com/sitemap.xml [2049] -> "sitemap.xml" [1]

<?xml version="1.0" encoding="UTF-8"?>
<sitemapindex xmlns="https://www.google.com/schemas/sitemap/0.84">
  <sitemap>
    <loc>https://www.google.com/gmail/sitemap.xml</loc>
  </sitemap>
  <sitemap>
    <loc>https://www.google.com/forms/sitemaps.xml</loc>
  </sitemap>
...
```

Al explorar desde allí, un evaluador podría desear recuperar el mapa del sitio de Gmail `https://www.google.com/gmail/sitemap.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="https://www.sitemaps.org/schemas/sitemap/0.9" xmlns:xhtml="https://www.w3.org/1999/xhtml">
  <url>
    <loc>https://www.google.com/intl/am/gmail/about/</loc>
    <xhtml:link href="https://www.google.com/gmail/about/" hreflang="x-default" rel="alternate"/>
    <xhtml:link href="https://www.google.com/intl/el/gmail/about/" hreflang="el" rel="alternate"/>
    <xhtml:link href="https://www.google.com/intl/it/gmail/about/" hreflang="it" rel="alternate"/>
    <xhtml:link href="https://www.google.com/intl/ar/gmail/about/" hreflang="ar" rel="alternate"/>
...
```

### Security TXT

[security.txt](https://securitytxt.org) fue ratificado por el IETF como [RFC 9116 - Un formato de archivo para facilitar la divulgación de vulnerabilidades de seguridad](https://www.rfc-editor.org/rfc/rfc9116.html), que permite a los sitios web definir políticas de seguridad y datos de contacto. Existen múltiples razones por las que esto podría ser interesante en escenarios de prueba, entre ellas:

- Identificar rutas o recursos adicionales para incluir en el descubrimiento/análisis.
- Recopilación de inteligencia de código abierto.
- Encontrar información sobre recompensas por errores, etc.
- Ingeniería social.

El archivo puede estar presente en la raíz del servidor web o en el directorio `.well-known/`, por ejemplo:

- `https://example.com/security.txt`
- `https://example.com/.well-known/security.txt`

A continuación, se muestra un ejemplo real obtenido de LinkedIn el 5 de mayo de 2020:


```bash
$ wget --no-verbose https://www.linkedin.com/.well-known/security.txt && cat security.txt
2020-05-07 12:56:51 URL:https://www.linkedin.com/.well-known/security.txt [333/333] -> "security.txt" [1]
# Conforms to IETF `draft-foudil-securitytxt-07`
Contact: mailto:security@linkedin.com
Contact: https://www.linkedin.com/help/linkedin/answer/62924
Encryption: https://www.linkedin.com/help/linkedin/answer/79676
Canonical: https://www.linkedin.com/.well-known/security.txt
Policy: https://www.linkedin.com/help/linkedin/answer/62924
```

Las claves públicas OpenPGP contienen metadatos que proporcionan información sobre la clave. A continuación, se presentan algunos elementos de metadatos comunes que se pueden extraer de una clave pública OpenPGP:

- **Key ID**: El ID de clave es un identificador corto derivado del material de la clave pública. Ayuda a identificar la clave y suele mostrarse como un valor hexadecimal de ocho caracteres.
- **Key Fingerprint**: La huella de clave es un identificador más largo y único derivado del material de la clave. Suele mostrarse como un valor hexadecimal de 40 caracteres. Las huellas de clave se utilizan comúnmente para verificar la integridad y la autenticidad de una clave pública.
- **Key Algorithm**: El algoritmo de clave representa el algoritmo criptográfico utilizado por la clave pública. OpenPGP admite varios algoritmos como RSA, DSA y ECC (criptografía de curva elíptica).
- **Key Size**: El tamaño de clave se refiere a la longitud o tamaño de la clave criptográfica en bits. Indica la solidez de la clave y determina el nivel de seguridad que ofrece. 
- **Key Creation Date**:La Fecha de Creación de la Clave indica cuándo se generó o creó la clave.
- **Key Expiration Date**: Las Claves Públicas OpenPGP pueden tener una fecha de caducidad establecida, después de la cual se consideran inválidas. La Fecha de Caducidad de la Clave especifica cuándo la clave deja de ser válida.
- **User IDs**: Las claves públicas pueden tener uno o más ID de Usuario asociados que identifican al propietario o la entidad asociada a la clave. Los ID de Usuario suelen incluir información como el nombre, la dirección de correo electrónico y comentarios opcionales del propietario de la clave.

### Humans TXT

`humans.txt` es una iniciativa para conocer a las personas detrás de un sitio web. Se presenta como un archivo de texto que contiene información sobre las diferentes personas que han contribuido a la creación del sitio. Este archivo suele (aunque no siempre) contener información relacionada con sitios/rutas de trabajo o carreras profesionales.

El siguiente ejemplo se obtuvo de Google el 5 de mayo de 2020:


```bash
$ wget --no-verbose  https://www.google.com/humans.txt && cat humans.txt
2020-05-07 12:57:52 URL:https://www.google.com/humans.txt [286/286] -> "humans.txt" [1]
Google is built by a large team of engineers, designers, researchers, robots, and others in many different sites across the globe. It is updated continuously, and built with more tools and technologies than we can shake a stick at. If you'd like to help us out, see careers.google.com.
```

### Otras fuentes de información .well-known

Existen otras RFC y borradores de internet que sugieren usos estandarizados de los archivos dentro del directorio `.well-known/`. Puede encontrar una lista de estas [aquí](https://en.wikipedia.org/wiki/List_of_/.well-known/_services_offered_by_webservers) or [here](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml).

Sería bastante sencillo para un tester revisar el RFC/borradores y crear una lista para proporcionarla a un rastreador o fuzzer, con el fin de verificar la existencia o el contenido de dichos archivos.

## Herramientas

- Navegador (Ver código fuente o funcionalidad de herramientas de desarrollo)
- curl
- wget
- Burp Suite
- ZAP
