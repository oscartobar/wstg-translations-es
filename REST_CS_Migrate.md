# Hoja de referencia para la evaluación REST

## Acerca de los servicios web RESTful

Los servicios web son una implementación de la tecnología web utilizada para la comunicación máquina a máquina. Por lo tanto, se utilizan para la comunicación entre aplicaciones, Web 2.0 y mashups, y para que las aplicaciones de escritorio y móviles llamen a un servidor.

Los servicios web RESTful (a menudo llamados simplemente REST) ​​son una variante ligera de los servicios web basada en el patrón de diseño RESTful. En la práctica, los servicios web RESTful utilizan solicitudes HTTP similares a las llamadas HTTP normales, a diferencia de otras tecnologías de servicios web como SOAP, que utiliza un protocolo complejo.

## Propiedades clave relevantes de los servicios web RESTful

- Uso de métodos HTTP (`GET`, `POST`, `PUT` y `DELETE`) como verbo principal para la operación solicitada.
- Especificaciones de parámetros no estándar:
- Como parte de la URL.
- En los encabezados.
- Parámetros y respuestas estructurados mediante JSON o XML en los valores de los parámetros, el cuerpo de la solicitud o el cuerpo de la respuesta. Estos son necesarios para comunicar información útil a la máquina.
- Autenticación personalizada y gestión de sesiones, a menudo utilizando tokens de seguridad personalizados: esto es necesario, ya que la comunicación máquina a máquina no permite secuencias de inicio de sesión.
- Falta de documentación formal. Sun Microsystems presentó una propuesta de estándar para describir servicios web RESTful llamada WADL (https://www.w3.org/Submission/wadl/), pero nunca se adaptó oficialmente.

## El desafío de las pruebas de seguridad de servicios web RESTful

- Inspeccionar la aplicación no revela la superficie de ataque, es decir, las URL y la estructura de parámetros que utiliza el servicio web RESTful. Las razones son:
- Ninguna aplicación utiliza todas las funciones y parámetros disponibles expuestos por el servicio.
- Los utilizados suelen activarse dinámicamente mediante el código del lado del cliente y no como enlaces en las páginas.
- La aplicación cliente a menudo no es una aplicación web y no permite la inspección del enlace de activación ni del código relevante. Los parámetros no son estándar, lo que dificulta determinar qué es solo una parte de la URL o un encabezado constante y qué parámetro merece ser sometido a fuzzing (https://owasp.org/www-community/Fuzzing).

Al ser una interfaz de máquina, la cantidad de parámetros utilizados puede ser muy grande; por ejemplo, una estructura JSON puede incluir docenas de parámetros. Cada uno de ellos alarga significativamente el tiempo de prueba.

Los mecanismos de autenticación personalizados requieren ingeniería inversa y hacen que las herramientas populares sean ineficaces, ya que no pueden rastrear una sesión de inicio de sesión.

## Cómo realizar pruebas de penetración en un servicio web RESTful

Determinar la superficie de ataque mediante documentación: Las pruebas de penetración RESTful pueden ser más eficaces si se permite cierto nivel de pruebas de caja blanca y se puede obtener información sobre el servicio.

Esta información garantizará una cobertura más completa de la superficie de ataque. Información a buscar:

- Descripción formal del servicio: Si bien para otros tipos de servicios web como SOAP suele estar disponible una descripción formal, generalmente en WSDL, esto rara vez ocurre con REST. Dicho esto, tanto WSDL 2.0 como WADL pueden describir REST y se utilizan a veces.

- Una guía para desarrolladores sobre el uso del servicio puede ser menos detallada, pero es común encontrarla, e incluso podría considerarse una "caja negra".

- Código fuente o configuración de la aplicación: en muchos frameworks, incluyendo .NET, la definición del servicio REST puede obtenerse fácilmente de los archivos de configuración en lugar del código.

Recopilar solicitudes completas mediante un [proxy](https://www.zaproxy.org/). Si bien es un paso importante en las pruebas de penetración, es aún más importante para las aplicaciones basadas en REST, ya que la interfaz de usuario de la aplicación podría no ofrecer pistas sobre la superficie de ataque real.

Tenga en cuenta que el proxy debe ser capaz de recopilar solicitudes completas, no solo URL, ya que los servicios REST utilizan más que solo parámetros GET.

Analice las solicitudes recopiladas para determinar la superficie de ataque:

- Busque parámetros no estándar:
- Busque encabezados HTTP anormales; estos suelen ser parámetros basados ​​en encabezados.
- Determine si un segmento de URL presenta un patrón repetitivo en las URL. Estos patrones pueden incluir una fecha, un número o una cadena similar a un ID, lo que indica que el segmento de URL es un parámetro incrustado en la URL.
- Por ejemplo: `https://server/srv/2013-10-21/use.php`
- Busque valores de parámetros estructurados; estos pueden ser JSON, XML o una estructura no estándar. Si el último elemento de una URL no tiene extensión, podría ser un parámetro. Esto es especialmente cierto si la tecnología de la aplicación suele usar extensiones o si un segmento anterior sí la tiene.

Por ejemplo: `https://server/svc/Grid.asmx/GetRelatedListItems`

Buscar segmentos de URL con gran variabilidad: un único segmento de URL con muchos valores puede ser un parámetro y no un directorio físico.

Por ejemplo, si la URL `https://server/src/XXXX/page` se repite con cientos de valores para `XXXX`, es probable que `XXXX` sea un parámetro.

Verificar parámetros no estándar: en algunos casos (pero no en todos), establecer el valor de un segmento de URL sospechoso de ser un parámetro con un valor que se espera que no sea válido puede ayudar a determinar si se trata de un elemento de ruta de un parámetro. Si se trata de un elemento de ruta, el servidor web devolverá un mensaje *404*, mientras que si se trata de un valor no válido para un parámetro, la respuesta sería un mensaje a nivel de aplicación, ya que el valor es válido a nivel de servidor web.

Análisis de las solicitudes recopiladas para optimizar el fuzzing (https://owasp.org/www-community/Fuzzing): tras identificar los posibles parámetros para fuzzing, analice los valores recopilados de cada uno para determinar:

- Valores válidos e inválidos, para que el fuzzing (https://owasp.org/www-community/Fuzzing) pueda centrarse en los valores inválidos marginales.
- Por ejemplo, enviar *0* para un valor que siempre es un entero positivo.
- Secuencias que permiten fuzzing más allá del rango presumiblemente asignado al usuario actual.

Por último, al fuzzing (https://owasp.org/www-community/Fuzzing), no olvide emular el mecanismo de autenticación utilizado.

Recursos relacionados

- [Hoja de trucos de seguridad REST](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html) - La otra cara de esta hoja de trucos
- [Servicios RESTful, punto ciego de seguridad web](https://www.youtube.com/watch?v=pWq4qGLAZHI) - Una presentación en video que profundiza en la mayoría de los temas de esta hoja de trucos.
