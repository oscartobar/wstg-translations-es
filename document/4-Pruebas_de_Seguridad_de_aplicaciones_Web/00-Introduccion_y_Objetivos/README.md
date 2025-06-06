# 4.0 Introducción y Objetivos

Esta sección describe la metodología de pruebas de seguridad de aplicaciones web de OWASP y explica cómo detectar vulnerabilidades en la aplicación debido a deficiencias en los controles de seguridad identificados.

## ¿Qué son las pruebas de seguridad de aplicaciones web?

Una prueba de seguridad es un método para evaluar la seguridad de un sistema informático o red mediante la validación y verificación metódica de la eficacia de los controles de seguridad de la aplicación. Una prueba de seguridad de aplicaciones web se centra únicamente en la evaluación de la seguridad de una aplicación web. El proceso implica un análisis activo de la aplicación para detectar debilidades, fallos técnicos o vulnerabilidades. Cualquier problema de seguridad detectado se presentará al propietario del sistema, junto con una evaluación de su impacto, una propuesta de mitigación o una solución técnica.

## ¿Qué es una vulnerabilidad?

Una vulnerabilidad es un fallo o debilidad en el diseño, la implementación, la operación o la gestión de un sistema que podría explotarse para comprometer los objetivos de seguridad del sistema.

## ¿Qué es una amenaza?

Una amenaza es cualquier cosa (un atacante externo malicioso, un usuario interno, una inestabilidad del sistema, etc.) que pueda dañar los activos de una aplicación (recursos valiosos, como los datos de una base de datos o del sistema de archivos) al explotar una vulnerabilidad.

## ¿Qué es una prueba?

Una prueba es una acción para demostrar que una aplicación cumple con los requisitos de seguridad de las partes interesadas.

## El enfoque de esta guía

El enfoque de OWASP es abierto y colaborativo:

- Abierto: todos los expertos en seguridad pueden participar en el proyecto aportando su experiencia. Todo es gratuito.
- Colaborativo: se realiza una lluvia de ideas antes de escribir los artículos para que el equipo pueda compartir ideas y desarrollar una visión colectiva del proyecto. Esto implica un consenso general, una audiencia más amplia y una mayor participación.

Este enfoque suele dar como resultado una metodología de pruebas definida que será:

- Consistente
- Reproducible
- Rigurosa
- Bajo control de calidad

Los problemas que se deben abordar se documentan y prueban completamente. Es importante utilizar diversos métodos para evaluar todas las vulnerabilidades conocidas y documentar todas las actividades de prueba de seguridad.

## ¿Qué es la metodología de pruebas de OWASP?

Las pruebas de seguridad nunca serán una ciencia exacta donde se pueda definir una lista completa de todos los posibles problemas que deben evaluarse. De hecho, las pruebas de seguridad son solo una de las diversas técnicas adecuadas para evaluar la seguridad de las aplicaciones web en determinadas circunstancias. El objetivo de este proyecto es recopilar todas las técnicas de prueba posibles, explicarlas y mantener la guía actualizada. La metodología de pruebas de seguridad de aplicaciones web de OWASP se basa en el enfoque de caja negra. El evaluador tiene poca o ninguna información sobre la aplicación que se va a probar.

El modelo de prueba consta de:

- Evaluador: Quién realiza las actividades de prueba
- Herramientas y metodología: El núcleo de este proyecto de la Guía de Pruebas
- Aplicación: La caja negra para probar

Las pruebas se pueden clasificar como pasivas o activas:

### Pruebas pasivas

Durante las pruebas pasivas, el evaluador intenta comprender la lógica de la aplicación y la explora como un usuario final. Se pueden utilizar herramientas para recopilar información. Por ejemplo, se puede usar un proxy HTTP(S) para observar todas las solicitudes y respuestas HTTP(S). Al final de esta fase, el evaluador debería comprender, en general, todos los puntos de acceso y la funcionalidad del sistema (p. ej., encabezados HTTP, parámetros, cookies, API, uso/patrones de tecnología, etc.). La sección [Recopilación de información](../01-Recopilacion_de_informacion/README.md) explica cómo realizar pruebas pasivas.

Por ejemplo, un evaluador podría encontrar una página en la siguiente URL: "https://www.example.com/login/auth_form"

Esto podría indicar un formulario de autenticación donde la aplicación solicita un nombre de usuario y una contraseña.

Los siguientes parámetros representan dos puntos de acceso a la aplicación: "https://www.example.com/appx?a=1&b=1"

En este caso, la aplicación tiene dos puntos de acceso (parámetros "a" y "b"). Todos los puntos de entrada encontrados en esta fase representan objetivos para las pruebas. Realizar un seguimiento del directorio o árbol de llamadas de la aplicación y de todos los puntos de acceso puede ser útil durante las pruebas activas.

### Pruebas Activas

Durante las pruebas activas, el tester utiliza las metodologías descritas en las siguientes secciones.

El conjunto de pruebas activas se divide en 12 categorías:

- Recopilación de Información
- Pruebas de Gestión de Configuración e Implementación
- Pruebas de Gestión de Identidad
- Pruebas de Autenticación
- Pruebas de Autorización
- Pruebas de Gestión de Sesiones
- Pruebas de Validación de Entrada
- Pruebas de Gestión de Errores
- Pruebas de Criptografía Deficiente
- Pruebas de Lógica de Negocio
- Pruebas del lado del Cliente
- Pruebas de API