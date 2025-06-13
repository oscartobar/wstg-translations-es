# Prólogo de Eoin Keary

El problema del software inseguro es quizás el desafío técnico más importante de nuestro tiempo. El drástico auge de las aplicaciones web que facilitan los negocios, las redes sociales, etc., no ha hecho más que agravar la necesidad de establecer un enfoque sólido para desarrollar y proteger internet, las aplicaciones web y los datos.

En el Proyecto Abierto de Seguridad de Aplicaciones Web® (OWASP®), buscamos un mundo donde el software inseguro sea la anomalía, no la norma. La Guía de Pruebas de OWASP desempeña un papel fundamental en la solución de este grave problema. Es fundamental que nuestro enfoque para probar software en busca de problemas de seguridad se base en los principios de la ingeniería y la ciencia. Necesitamos un enfoque consistente, repetible y definido para probar aplicaciones web. Un mundo sin estándares mínimos en ingeniería y tecnología es un mundo sumido en el caos.

Es evidente que no se puede crear una aplicación segura sin realizar pruebas de seguridad. Las pruebas forman parte de un enfoque más amplio para construir un sistema seguro. Muchas organizaciones de desarrollo de software no incluyen las pruebas de seguridad en su proceso estándar. Lo que es aún peor es que muchos proveedores de seguridad ofrecen pruebas con distintos grados de calidad y rigor.

Las pruebas de seguridad, por sí solas, no son una medida eficaz de la seguridad de una aplicación, ya que existen infinitas maneras en que un atacante podría dañarla, y simplemente no es posible probarlas todas. No podemos protegernos a nosotros mismos, ya que solo disponemos de un tiempo limitado para probar y defendernos donde un atacante no tenga tales restricciones.

En conjunto con otros proyectos de OWASP, como la Guía de Revisión de Código, la Guía de Desarrollo y herramientas como [ZAP](https://www.zaproxy.org/), este es un excelente punto de partida para crear y mantener aplicaciones seguras. Esta Guía de Pruebas le mostrará cómo verificar la seguridad de su aplicación en ejecución. Recomiendo encarecidamente utilizar estas guías como parte de sus iniciativas de seguridad de aplicaciones.

## ¿Por qué OWASP?

Crear una guía como esta es una tarea enorme que requiere la experiencia de cientos de personas en todo el mundo. Existen muchas maneras diferentes de detectar fallos de seguridad, y esta guía recoge el consenso de los principales expertos sobre cómo realizar estas pruebas de forma rápida, precisa y eficiente. OWASP ofrece a profesionales de seguridad con ideas afines la posibilidad de colaborar y desarrollar un enfoque práctico líder para cualquier problema de seguridad.

La importancia de que esta guía esté disponible de forma completamente gratuita y abierta es crucial para la misión de la fundación. Permite a cualquier persona comprender las técnicas utilizadas para detectar problemas de seguridad comunes. La seguridad no debe ser un arte oscuro ni un secreto a voces que solo unos pocos puedan practicar. Debe estar abierta a todos y no ser exclusiva de los profesionales de la seguridad, sino también de los responsables de control de calidad, desarrolladores y técnicos. El proyecto de creación de esta guía mantiene esta experiencia en manos de quienes la necesitan: usted, yo y cualquier persona involucrada en el desarrollo de software.

Esta guía debe llegar a los desarrolladores y testers de software. No hay suficientes expertos en seguridad de aplicaciones en el mundo para lograr una reducción significativa del problema general. La responsabilidad inicial de la seguridad de las aplicaciones debe recaer en los desarrolladores, ya que son ellos quienes escriben el código. No debería sorprender que los desarrolladores no produzcan código seguro si no lo prueban ni consideran los tipos de errores que introducen vulnerabilidades.

Mantener esta información actualizada es un aspecto fundamental de este proyecto de guía. Al adoptar el enfoque wiki, la comunidad de OWASP puede evolucionar y ampliar la información de esta guía para mantenerse al día con el cambiante panorama de amenazas a la seguridad de las aplicaciones.

Esta guía es un gran testimonio de la pasión y la energía que nuestros miembros y voluntarios del proyecto tienen por este tema. Sin duda, ayudará a cambiar el mundo, línea por línea.

## Adaptación y Priorización

Debería adoptar esta guía en su organización. Es posible que necesite adaptar la información para que se ajuste a las tecnologías, los procesos y la estructura organizativa de su organización.

En general, existen varios roles dentro de las organizaciones que pueden usar esta guía:

- Los desarrolladores deben usar esta guía para asegurarse de que están produciendo código seguro. Estas pruebas deben formar parte de los procedimientos normales de código y pruebas unitarias.
- Los testers de software y el equipo de control de calidad deben usar esta guía para ampliar el conjunto de casos de prueba que aplican a las aplicaciones. Detectar estas vulnerabilidades a tiempo ahorra tiempo y esfuerzo considerables posteriormente.
- Los especialistas en seguridad deben usar esta guía en combinación con otras técnicas para verificar que no se hayan pasado por alto vulnerabilidades de seguridad en una aplicación.
- Los gerentes de proyecto deben considerar la razón de ser de esta guía y que los problemas de seguridad se manifiestan a través de errores en el código y el diseño.

Lo más importante que debe recordar al realizar pruebas de seguridad es repriorizar continuamente. Hay infinitas maneras en que una aplicación puede fallar, y las organizaciones siempre tienen tiempo y recursos limitados para las pruebas. Asegúrese de invertir el tiempo y los recursos de forma inteligente. Céntrese en las vulnerabilidades de seguridad que representan un riesgo real para su negocio. Intente contextualizar el riesgo en función de la aplicación y sus casos de uso.

Esta guía se considera mejor como un conjunto de técnicas que puede utilizar para detectar diferentes tipos de vulnerabilidades de seguridad. Sin embargo, no todas las técnicas son igualmente importantes. Evite usar la guía como una lista de verificación, ya que siempre aparecen nuevas vulnerabilidades y ninguna guía puede ser una lista exhaustiva de "cosas que probar", sino un buen punto de partida.

## El rol de las herramientas automatizadas

Existen varias empresas que venden herramientas automatizadas de análisis y pruebas de seguridad. Recuerde las limitaciones de estas herramientas para que pueda utilizarlas para lo que son eficaces. Como dijo Michael Howard en la Conferencia OWASP AppSec de 2006 en Seattle: "¡Las herramientas no hacen que el software sea seguro! Ayudan a escalar el proceso y a aplicar las políticas".

Y lo más importante, estas herramientas son genéricas, lo que significa que no están diseñadas para su código personalizado, sino para aplicaciones en general. Esto significa que, si bien pueden detectar algunos problemas genéricos, no tienen el conocimiento suficiente de su aplicación para detectar la mayoría de las fallas. En mi experiencia, los problemas de seguridad más graves son aquellos que no son genéricos, sino que están profundamente arraigados en la lógica de negocio y el diseño personalizado de su aplicación.

Estas herramientas también pueden ser muy útiles, ya que detectan numerosos problemas potenciales. Si bien ejecutarlas no requiere mucho tiempo, cada uno de los problemas potenciales requiere tiempo para investigar y verificar. Si el objetivo es encontrar y eliminar las fallas más graves lo antes posible, considere si es mejor invertir su tiempo en herramientas automatizadas o en las técnicas descritas en esta guía. Aun así, estas herramientas forman parte de un programa de seguridad de aplicaciones bien equilibrado. Si se utilizan correctamente, pueden respaldar sus procesos generales para producir código más seguro.

## Llamada a la acción

Si está desarrollando, diseñando o probando software, le recomiendo encarecidamente que se familiarice con la guía de pruebas de seguridad de este documento. Es una excelente guía para probar los problemas más comunes que enfrentan las aplicaciones hoy en día, pero no es exhaustiva. Si encuentra algún error, por favor, añada una nota a la página de discusión o modifique el contenido usted mismo. Estará ayudando a miles de personas que usan esta guía.

Considere unirse a nosotros (https://owasp.org/membership/) como miembro individual o corporativo para que podamos seguir produciendo materiales como esta guía de pruebas y todos los demás proyectos importantes de OWASP.

Gracias a todos los colaboradores pasados ​​y futuros de esta guía; su trabajo contribuirá a que las aplicaciones en todo el mundo sean más seguras.

--Eoin Keary, Miembro de la Junta Directiva de OWASP, 19 de abril de 2013

Open Web Application Security Project y OWASP son marcas registradas de OWASP Foundation, Inc.
