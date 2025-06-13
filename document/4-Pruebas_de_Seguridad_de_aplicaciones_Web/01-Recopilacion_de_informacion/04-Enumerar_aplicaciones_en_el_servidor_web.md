# Enumerar aplicaciones en el servidor web

|ID |
|------------|
|WSTG-INFO-04|

## Resumen

Un paso fundamental al analizar las vulnerabilidades de las aplicaciones web es identificar qué aplicaciones están alojadas en un servidor web. Muchas aplicaciones presentan vulnerabilidades y estrategias de ataque conocidas que pueden explotarse para obtener control remoto o explotar datos. Además, muchas aplicaciones suelen estar mal configuradas o no actualizadas, debido a la percepción de que solo se usan internamente y, por lo tanto, no representan una amenaza.
Con la proliferación de servidores web virtuales, la relación tradicional 1:1 entre una dirección IP y un servidor web está perdiendo gran parte de su significado original. Es frecuente tener varios sitios o aplicaciones cuyos nombres simbólicos se resuelven en la misma dirección IP. Este escenario no se limita a los entornos de alojamiento, sino que también se aplica a entornos corporativos comunes.

A los profesionales de seguridad a veces se les asigna un conjunto de direcciones IP como objetivo para realizar pruebas. Se podría argumentar que este escenario se asemeja más a una prueba de penetración, pero en cualquier caso, se espera que dicha asignación pruebe todas las aplicaciones web accesibles a través de este objetivo. El problema radica en que la dirección IP dada aloja un servicio HTTP en el puerto 80, pero si un evaluador accede a ella especificando la dirección IP (que es lo único que conoce), este informa "No hay ningún servidor web configurado en esta dirección" o un mensaje similar. Sin embargo, ese sistema podría "ocultar" varias aplicaciones web, asociadas a nombres simbólicos (DNS) no relacionados. Obviamente, el alcance del análisis se ve considerablemente afectado dependiendo de si el evaluador prueba todas las aplicaciones o solo las que conoce.

A veces, la especificación del objetivo es más completa. El evaluador puede recibir una lista de direcciones IP y sus nombres simbólicos correspondientes. Sin embargo, esta lista podría contener información parcial, es decir, podría omitir algunos nombres simbólicos sin que el cliente sea consciente de ello (esto es más probable en grandes organizaciones).

Otros problemas que afectan el alcance de la evaluación se presentan con aplicaciones web publicadas en URL no obvias (p. ej., "https://www.example.com/some-strange-URL"), que no se referencian en ningún otro lugar. Esto puede ocurrir por error (debido a configuraciones incorrectas) o intencionalmente (por ejemplo, interfaces administrativas no anunciadas).

Para solucionar estos problemas, es necesario realizar el descubrimiento de aplicaciones web.

## Objetivos de la prueba

- Enumerar las aplicaciones dentro del alcance que existen en un servidor web.

## Cómo realizar la prueba

El descubrimiento de aplicaciones web es un proceso que busca identificar aplicaciones web en una infraestructura determinada. Esta última suele especificarse como un conjunto de direcciones IP (quizás un bloque de red), pero puede consistir en un conjunto de nombres simbólicos DNS o una combinación de ambos. Esta información se proporciona antes de ejecutar una evaluación, ya sea una prueba de penetración clásica o una evaluación centrada en aplicaciones. En ambos casos, a menos que las reglas de participación especifiquen lo contrario (p. ej., probar solo la aplicación ubicada en la URL «https://www.example.com/»), la evaluación debe procurar ser lo más exhaustiva posible, es decir, debe identificar todas las aplicaciones accesibles a través del objetivo dado. Los siguientes ejemplos examinan algunas técnicas que pueden emplearse para lograr este objetivo.

> Algunas de las siguientes técnicas se aplican a servidores web con conexión a Internet, en concreto, a los servicios de búsqueda web DNS y de IP inversa, y al uso de motores de búsqueda. Algunos ejemplos utilizan direcciones IP privadas (como «192.168.1.100»), que, a menos que se indique lo contrario, representan direcciones IP *genéricas* y se utilizan únicamente con fines de anonimato.

Hay tres factores que influyen en la cantidad de aplicaciones relacionadas con un nombre DNS (o una dirección IP) determinado:

1. **URL base diferente**

El punto de entrada obvio para una aplicación web es `www.example.com`; es decir, con esta notación abreviada, pensamos que la aplicación web se origina en `https://www.example.com/` (lo mismo aplica para HTTPS). Sin embargo, aunque esta es la situación más común, no hay nada que obligue a la aplicación a iniciarse en `/`.

Por ejemplo, el mismo nombre simbólico puede estar asociado a tres aplicaciones web, como: `https://www.example.com/app1` `https://www.example.com/app2` `https://www.example.com/app3`

En este caso, la URL `https://www.example.com/` no estaría asociada a una página relevante. Las tres aplicaciones permanecerían **ocultas** a menos que el tester sepa explícitamente cómo acceder a ellas; es decir, que conozca *app1*, *app2* o *app3*. Normalmente no es necesario publicar aplicaciones web de esta manera, a menos que el propietario no desee que sean accesibles de forma estándar y esté dispuesto a informar a los usuarios sobre su ubicación exacta. Esto no significa que estas aplicaciones sean secretas, sino que su existencia y ubicación no se anuncian explícitamente.

2. **Puertos no estándar**

Si bien las aplicaciones web suelen usar los puertos 80 (HTTP) y 443 (HTTPS), estos números de puerto no son fijos ni obligatorios. De hecho, las aplicaciones web pueden asociarse con puertos TCP arbitrarios y se puede hacer referencia a ellas especificando el número de puerto de la siguiente manera: `http[s]://www.example.com:port/`. Por ejemplo, `https://www.example.com:20000/`.

3. **Hosts virtuales**

El DNS permite asociar una sola dirección IP con uno o más nombres simbólicos. Por ejemplo, la dirección IP `192.168.1.100` podría estar asociada a los nombres DNS `www.example.com`, `helpdesk.example.com`, `webmail.example.com`. No es necesario que todos los nombres pertenezcan al mismo dominio DNS. Esta relación 1 a N puede reflejarse para servir contenido diferente mediante el uso de hosts virtuales. La información que especifica el host virtual al que nos referimos está integrada en el encabezado HTTP 1.1 [Host header](https://tools.ietf.org/html/rfc2616#section-14.23).

Nadie sospecharía la existencia de otras aplicaciones web además del obvio `www.example.com`, a menos que conozca `helpdesk.example.com` y `webmail.example.com`.


### Enfoques para abordar el problema 1: URL no estándar

No es posible determinar con certeza la existencia de aplicaciones web con nombres no estándar. Al no ser estándar, no existen criterios fijos que rijan la convención de nomenclatura; sin embargo, existen diversas técnicas que el evaluador puede utilizar para obtener información adicional.

En primer lugar, si el servidor web está mal configurado y permite la navegación por directorios, es posible detectar estas aplicaciones. Los escáneres de vulnerabilidades pueden ser útiles en este sentido.

En segundo lugar, estas aplicaciones pueden ser referenciadas por otras páginas web y es posible que hayan sido rastreadas e indexadas por motores de búsqueda. Si el evaluador sospecha la existencia de estas aplicaciones **ocultas** en `www.example.com`, podría buscarlas utilizando el operador *site* y examinando el resultado de una consulta para `site: www.example.com`. Entre las URL devueltas podría haber una que apunte a una aplicación no obvia.

Otra opción es buscar URLs que podrían ser candidatas para aplicaciones no publicadas. Por ejemplo, una interfaz de correo web podría ser accesible desde URLs como "https://www.example.com/webmail", "https://webmail.example.com/" o "https://mail.example.com/". Lo mismo ocurre con las interfaces administrativas, que pueden estar publicadas en URLs ocultas (por ejemplo, una interfaz administrativa de Tomcat) y, sin embargo, no estar referenciadas en ningún sitio. Por lo tanto, una búsqueda tipo diccionario (o "adivinación inteligente") podría arrojar algunos resultados. Los escáneres de vulnerabilidades pueden ser útiles en este sentido.

### Enfoques para abordar el problema 2: Puertos no estándar

Es fácil comprobar la existencia de aplicaciones web en puertos no estándar. Un escáner de puertos como Nmap puede realizar el reconocimiento de servicios mediante la opción "-sV" e identificará servicios http[s] en puertos arbitrarios. Lo que se requiere es un escaneo completo de todo el espacio de direcciones del puerto TCP de 64k.

Por ejemplo, el siguiente comando buscará, mediante un escaneo de conexión TCP, todos los puertos abiertos en la IP `192.168.1.100` e intentará determinar qué servicios están vinculados a ellos (solo se muestran los parámetros *esenciales*; Nmap ofrece un amplio conjunto de opciones, cuya explicación queda fuera del alcance):

`nmap –Pn –sT –sV –p0-65535 192.168.1.100`

Basta con examinar la salida y buscar HTTP o la indicación de servicios encapsulados en TLS (que deben comprobarse para confirmar que son HTTPS). Por ejemplo, la salida del comando anterior podría ser similar a:

```bash
Interesting ports on 192.168.1.100:
(The 65527 ports scanned but not shown below are in state: closed)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 3.5p1 (protocol 1.99)
80/tcp    open  http        Apache httpd 2.0.40 ((Red Hat Linux))
443/tcp   open  ssl         OpenSSL
901/tcp   open  http        Samba SWAT administration server
1241/tcp  open  ssl         Nessus security scanner
3690/tcp  open  unknown
8000/tcp  open  http-alt?
8080/tcp  open  http        Apache Tomcat/Coyote JSP engine 1.1
```

En este ejemplo, se puede observar que:

- Hay un servidor HTTP Apache ejecutándose en el puerto 80.
- Parece que hay un servidor HTTPS en el puerto 443 (pero esto debe confirmarse, por ejemplo, visitando `https://192.168.1.100` con un navegador).
- En el puerto 901 hay una interfaz web Samba SWAT.
- El servicio en el puerto 1241 no es HTTPS, sino el demonio Nessus encapsulado en TLS.
- El puerto 3690 presenta un servicio no especificado (Nmap devuelve su *huella digital* —omitida aquí para mayor claridad— junto con instrucciones para enviarla para su incorporación a la base de datos de huellas digitales de Nmap, siempre que se sepa a qué servicio representa).
- Otro servicio no especificado en el puerto 8000; posiblemente sea HTTP, ya que no es raro encontrar servidores HTTP en este puerto. Examinemos este problema:

```bash
$ telnet 192.168.10.100 8000
Trying 192.168.1.100...
Connected to 192.168.1.100.
Escape character is '^]'.
GET / HTTP/1.0

HTTP/1.0 200 OK
pragma: no-cache
Content-Type: text/html
Server: MX4J-HTTPD/1.0
expires: now
Cache-Control: no-cache

<html>
...
```

Esto confirma que, de hecho, se trata de un servidor HTTP. Como alternativa, los evaluadores podrían haber visitado la URL con un navegador web o haber usado los comandos GET o HEAD de Perl, que imitan interacciones HTTP como la mencionada anteriormente (sin embargo, las solicitudes HEAD podrían no ser aceptadas por todos los servidores).

- Apache Tomcat ejecutándose en el puerto 8080.

Los escáneres de vulnerabilidades pueden realizar la misma tarea, pero primero verifique que el escáner elegido pueda identificar servicios HTTP[S] que se ejecutan en puertos no estándar. Por ejemplo, Nessus puede identificarlos en puertos arbitrarios (siempre que se le indique que escanee todos los puertos) y, con respecto a Nmap, proporcionará varias pruebas sobre vulnerabilidades conocidas del servidor web, así como sobre la configuración TLS/SSL de los servicios HTTPS. Como se mencionó anteriormente, Nessus también puede detectar aplicaciones o interfaces web populares que, de otro modo, podrían pasar desapercibidas (por ejemplo, una interfaz administrativa de Tomcat).

### Enfoques para abordar el problema 3: Hosts virtuales

Existen diversas técnicas para identificar nombres DNS asociados a una dirección IP `x.y.z.t`.

#### Transferencias de zona DNS

Esta técnica tiene un uso limitado actualmente, dado que los servidores DNS no suelen respetar las transferencias de zona. Sin embargo, podría ser útil intentarlo. En primer lugar, los evaluadores deben determinar los servidores de nombres que sirven `x.y.z.t`. Si se conoce un nombre simbólico para `x.y.z.t` (por ejemplo, `www.example.com`), sus servidores de nombres se pueden determinar mediante herramientas como `nslookup`, `host` o `dig`, solicitando registros DNS NS.

Si no se conocen nombres simbólicos para `x.y.z.t`, pero la definición del destino contiene al menos un nombre simbólico, los evaluadores pueden intentar aplicar el mismo proceso y consultar el servidor de nombres de ese nombre (con la esperanza de que `x.y.z.t` también sea atendido por ese servidor). Por ejemplo, si el destino consiste en la dirección IP `x.y.z.t` y el nombre `mail.example.com`, determine los servidores de nombres para el dominio `example.com`.

El siguiente ejemplo muestra cómo identificar los servidores de nombres para `www.owasp.org` mediante el comando `host`:

```bash
$ host -t ns www.owasp.org
www.owasp.org is an alias for owasp.org.
owasp.org name server ns1.secure.net.
owasp.org name server ns2.secure.net.
```

Ahora se puede solicitar una transferencia de zona a los servidores de nombres para el dominio `example.com`. Si el evaluador tiene suerte, podría recibir como respuesta una lista de las entradas DNS para este dominio. Esto incluirá el obvio `www.example.com` y los menos obvios `helpdesk.example.com` y `webmail.example.com` (y posiblemente otros). Revise todos los nombres devueltos por la transferencia de zona y considere todos los relacionados con el objetivo evaluado.

Intentando solicitar una transferencia de zona para `owasp.org` desde uno de sus servidores de nombres:

```bash
$ host -l www.owasp.org ns1.secure.net
Using domain server:
Name: ns1.secure.net
Address: 192.220.124.10#53
Aliases:

Host www.owasp.org not found: 5(REFUSED)
; Transfer failed.
```

#### Consultas DNS Inversas

Este proceso es similar al anterior, pero se basa en registros DNS inversos (PTR). En lugar de solicitar una transferencia de zona, configure el tipo de registro como PTR y realice una consulta a la dirección IP indicada. Si los evaluadores tienen suerte, podrían recibir una entrada de nombre DNS como respuesta. Esta técnica se basa en la existencia de mapas de nombres IP a simbólicos, lo cual no está garantizado.

#### Búsquedas DNS Web

Este tipo de búsqueda es similar a la transferencia de zona DNS, pero se basa en servicios web que permiten búsquedas basadas en nombres en DNS. Uno de estos servicios es el servicio [Netcraft Search DNS](https://searchdns.netcraft.com/?host). El evaluador puede consultar una lista de nombres pertenecientes al dominio de su elección, como `example.com`. A continuación, comprobará si los nombres obtenidos son pertinentes al objetivo que está examinando.

Servicios de IP inversa

Los servicios de IP inversa son similares a las consultas inversas de DNS, con la diferencia de que los evaluadores consultan una aplicación web en lugar de un servidor de nombres. Existen varios servicios de este tipo. Dado que tienden a devolver resultados parciales (y a menudo diferentes), es mejor utilizar varios servicios para obtener un análisis más completo.

- [MxToolbox Reverse IP](https://mxtoolbox.com/ReverseLookup.aspx)
- [DNSstuff](https://www.dnsstuff.com/) (multiple services available)
- [Net Square](https://web.archive.org/web/20190515092354/https://www.net-square.com/mspawn.html) (multiple queries on domains and IP addresses, requires installation)

#### Búsqueda en Google

Tras recopilar información con las técnicas anteriores, los evaluadores pueden confiar en los motores de búsqueda para refinar y mejorar su análisis. Esto puede revelar evidencia de nombres simbólicos adicionales pertenecientes al objetivo o de aplicaciones accesibles a través de URL no obvias.

Por ejemplo, considerando el ejemplo anterior de `www.owasp.org`, el evaluador podría consultar Google y otros motores de búsqueda para buscar información (y, por lo tanto, nombres DNS) relacionada con los dominios recién descubiertos `webgoat.org`, `webscarab.com` y `webscarab.net`.

Las técnicas de búsqueda en Google se explican en [Pruebas: Arañas, Robots y Rastreadores](01-reconocimiento_de_motores_de_busqueda_para_detectar_fugas_de_informacion.md).

#### Certificados digitales

Si el servidor acepta conexiones mediante HTTPS, el nombre común (CN) y el nombre alternativo del sujeto (SAN) del certificado pueden contener uno o más nombres de host. Sin embargo, si el servidor web no cuenta con un certificado de confianza o se utilizan comodines, es posible que no se devuelva información válida.

El CN y el SAN se pueden obtener inspeccionando manualmente el certificado o mediante otras herramientas como OpenSSL:

```sh
openssl s_client -connect 93.184.216.34:443 </dev/null 2>/dev/null | openssl x509 -noout -text | grep -E 'DNS:|Subject:'

Subject: C = US, ST = California, L = Los Angeles, O = Internet Corporation for Assigned Names and Numbers, CN = www.example.org
DNS:www.example.org, DNS:example.com, DNS:example.edu, DNS:example.net, DNS:example.org, DNS:www.example.com, DNS:www.example.edu, DNS:www.example.net
```

## Herramientas

- Herramientas de búsqueda de DNS como `nslookup`, `dig` y similares.
- Motores de búsqueda (Google, Bing y otros buscadores importantes).
- Servicio de búsqueda web especializado en DNS: ver texto.
- [Nmap](https://nmap.org/)
- [Nessus Vulnerability Scanner](https://www.tenable.com/products/nessus)
- [Nikto](https://www.cirt.net/nikto2)
