# Huella digital del servidor web

|ID |
|------------|
|WSTG-INFO-02|

## Resumen

La huella digital del servidor web consiste en identificar el tipo y la versión del servidor web en el que se ejecuta un objetivo. Si bien la huella digital del servidor web suele estar integrada en herramientas de pruebas automatizadas, es importante que los investigadores comprendan los fundamentos de cómo estas herramientas intentan identificar software y su utilidad.

Descubrir con precisión el tipo de servidor web en el que se ejecuta una aplicación permite a los evaluadores de seguridad determinar si la aplicación es vulnerable a ataques. En particular, los servidores que ejecutan versiones antiguas de software sin parches de seguridad actualizados pueden ser susceptibles a vulnerabilidades específicas de la versión.

## Objetivos de la prueba

- Determinar la versión y el tipo de un servidor web en ejecución para permitir un mayor descubrimiento de cualquier vulnerabilidad conocida.

## Cómo realizar la prueba

Las técnicas utilizadas para la huella digital del servidor web incluyen [banner grabbing](https://en.wikipedia.org/wiki/Banner_grabbing), obtener respuestas a solicitudes malformadas y usar herramientas automatizadas para realizar análisis más robustos que combinan tácticas. La premisa fundamental sobre la que operan todas estas técnicas es la misma. Todas buscan obtener una respuesta del servidor web que luego pueda compararse con una base de datos de respuestas y comportamientos conocidos y, por lo tanto, corresponderse con un tipo de servidor conocido.

### Captura de Banners

La captura de banners se realiza enviando una solicitud HTTP al servidor web y examinando su [response header](https://developer.mozilla.org/en-US/docs/Glossary/Response_header). Esto se puede lograr mediante diversas herramientas, como `telnet` para solicitudes HTTP o `openssl` para solicitudes mediante TLS/SSL.

Por ejemplo, aquí se muestra la respuesta a una solicitud enviada a un servidor Apache.

```http
HTTP/1.1 200 OK
Date: Thu, 05 Sep 2019 17:42:39 GMT
Server: Apache/2.4.41 (Unix)
Last-Modified: Thu, 05 Sep 2019 17:40:42 GMT
ETag: "75-591d1d21b6167"
Accept-Ranges: bytes
Content-Length: 117
Connection: close
Content-Type: text/html
...
```

Aquí hay otra respuesta, esta vez enviada por nginx.

```http
HTTP/1.1 200 OK
Server: nginx/1.17.3
Date: Thu, 05 Sep 2019 17:50:24 GMT
Content-Type: text/html
Content-Length: 117
Last-Modified: Thu, 05 Sep 2019 17:40:42 GMT
Connection: close
ETag: "5d71489a-75"
Accept-Ranges: bytes
...
```

Así se ve una respuesta enviada por lighttpd.

```sh
HTTP/1.0 200 OK
Content-Type: text/html
Accept-Ranges: bytes
ETag: "4192788355"
Last-Modified: Thu, 05 Sep 2019 17:40:42 GMT
Content-Length: 117
Connection: close
Date: Thu, 05 Sep 2019 17:57:57 GMT
Server: lighttpd/1.4.54
```

En estos ejemplos, el tipo y la versión del servidor se exponen claramente. Sin embargo, las aplicaciones que priorizan la seguridad pueden ofuscar la información de su servidor modificando el encabezado. Por ejemplo, a continuación se muestra un extracto de la respuesta a una solicitud de un sitio con un encabezado modificado:

```sh
HTTP/1.1 200 OK
Server: Website.com
Date: Thu, 05 Sep 2019 17:57:06 GMT
Content-Type: text/html; charset=utf-8
Status: 200 OK
...
```

En los casos en que la información del servidor esté oculta, los evaluadores pueden determinar el tipo de servidor basándose en el orden de los campos del encabezado. Tenga en cuenta que en el ejemplo de Apache anterior, los campos siguen este orden:

- Fecha
- Servidor
- Última modificación
- ETag
- Rangos de aceptación
- Longitud del contenido
- Conexión
- Tipo de contenido

Sin embargo, tanto en el ejemplo de nginx como en el de servidor oculto, los campos comunes siguen este orden:

- Servidor
- Fecha
- Tipo de contenido

Los evaluadores pueden usar esta información para determinar que el servidor oculto es nginx. Sin embargo, considerando que varios servidores web pueden compartir el mismo orden de campos y que estos se pueden modificar o eliminar, este método no es definitivo.

### Envío de solicitudes malformadas

Los servidores web pueden identificarse examinando sus respuestas de error y, en los casos en que no se hayan personalizado, sus páginas de error predeterminadas. Una forma de obligar a un servidor a presentarlas es enviando solicitudes incorrectas o malformadas intencionalmente. Por ejemplo, aquí se muestra la respuesta a una solicitud del método inexistente «SANTA CLAUS» desde un servidor Apache.

```sh
GET / SANTA CLAUS/1.1


HTTP/1.1 400 Bad Request
Date: Fri, 06 Sep 2019 19:21:01 GMT
Server: Apache/2.4.41 (Unix)
Content-Length: 226
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>400 Bad Request</title>
</head><body>
<h1>Bad Request</h1>
<p>Your browser sent a request that this server could not understand.<br />
</p>
</body></html>
```

Aquí está la respuesta a la misma solicitud de nginx.

```sh
GET / SANTA CLAUS/1.1


<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.17.3</center>
</body>
</html>
```

Aquí está la respuesta a la misma solicitud de lighttpd.

```sh
GET / SANTA CLAUS/1.1


HTTP/1.0 400 Bad Request
Content-Type: text/html
Content-Length: 345
Connection: close
Date: Sun, 08 Sep 2019 21:56:17 GMT
Server: lighttpd/1.4.54

<?xml version="1.0" encoding="iso-8859-1"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
         "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="https://www.w3.org/1999/xhtml/" xml:lang="en" lang="en">
 <head>
  <title>400 Bad Request</title>
 </head>
 <body>
  <h1>400 Bad Request</h1>
 </body>
</html>
```

Dado que las páginas de error predeterminadas ofrecen numerosos factores que diferencian entre los tipos de servidores web, su análisis puede ser un método eficaz para la identificación de servidores web, incluso cuando los campos del encabezado del servidor están ocultos.

### Uso de herramientas de análisis automatizado

Como se mencionó anteriormente, la identificación de servidores web suele incluirse como una función de las herramientas de análisis automatizado. Estas herramientas pueden realizar solicitudes similares a las mostradas anteriormente, así como enviar otras sondas más específicas del servidor. Las herramientas automatizadas pueden comparar las respuestas de los servidores web mucho más rápido que las pruebas manuales y utilizan grandes bases de datos de respuestas conocidas para intentar la identificación del servidor. Por estas razones, las herramientas automatizadas tienen mayor probabilidad de producir resultados precisos.

A continuación, se presentan algunas herramientas de análisis de uso común que incluyen la función de identificación de servidores web.

- [Netcraft](https://toolbar.netcraft.com/site_report), una herramienta en línea que escanea sitios en busca de información, incluidos detalles del servidor web.
- [Nikto](https://github.com/sullo/nikto), una herramienta de escaneo de línea de comandos de código abierto.
- [Nmap](https://nmap.org/), una herramienta de línea de comandos de código abierto que también tiene una GUI, [Zenmap](https://nmap.org/zenmap/).

## Remediación

Si bien la información expuesta del servidor no constituye necesariamente una vulnerabilidad en sí misma, sí puede ayudar a los atacantes a explotar otras vulnerabilidades existentes. Además, puede llevar a los atacantes a encontrar vulnerabilidades específicas de la versión del servidor que pueden utilizarse para explotar servidores sin parches. Por ello, se recomienda tomar precauciones. Estas acciones incluyen:

- Ocultar la información del servidor web en los encabezados, como en el caso de Apache. [mod_headers module](https://httpd.apache.org/docs/current/mod/mod_headers.html).
- Usando un [servidor proxy inverso](https://en.wikipedia.org/wiki/Proxy_server#Reverse_proxies) reforzado Para crear una capa adicional de seguridad entre el servidor web e Internet.
- Garantizar que los servidores web se mantengan actualizados con el software y los parches de seguridad más recientes.
