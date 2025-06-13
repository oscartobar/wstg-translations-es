# Metodologías de Pruebas de Penetración

## Resumen

- [Guías de Pruebas OWASP](#owasp-testing-guides)
- Guía de Pruebas de Seguridad Web (WSTG)
- Guía de Pruebas de Seguridad Móvil (MSTG)
- Metodología de Pruebas de Seguridad de Firmware
- [Estándar de Ejecución de Pruebas de Penetración](#penetration-testing-execution-standard)
- [Guía de Pruebas de Penetración PCI](#pci-penetration-testing-guide)
- [Guía de Pruebas de Penetración PCI DSS](#pci-dss-penetration-testing-guidance)
- [Requisitos de Pruebas de Penetración PCI DSS](#pci-dss-penetration-testing-requirements)
- [Marco de Pruebas de Penetración](#penetration-testing-framework)
- [Guía Técnica para Pruebas de Seguridad de la Información y Evaluación](#guía-técnica-para-pruebas-y-evaluación-de-seguridad-de-la-información)
- [Manual de Metodología de Pruebas de Seguridad de Código Abierto](#manual-de-metodología-de-pruebas-de-seguridad-de-código-abierto)
- [Referencias](#referencias)

## Guías de Pruebas de OWASP

Para la ejecución de pruebas de seguridad técnica, se recomiendan encarecidamente las guías de pruebas de OWASP. Según el tipo de aplicación, las guías de prueba se enumeran a continuación para servicios web/en la nube, aplicaciones móviles (Android/iOS) o firmware de IoT, respectivamente.

- [Guía de Pruebas de Seguridad Web de OWASP](https://owasp.org/www-project-web-security-testing-guide/)
- [Guía de Pruebas de Seguridad Móvil de OWASP](https://owasp.org/www-project-mobile-security-testing-guide/)
- [Metodología de Pruebas de Seguridad de Firmware de OWASP](https://github.com/scriptingxss/owasp-fstm)

## Estándar de Ejecución de Pruebas de Penetración

El Estándar de Ejecución de Pruebas de Penetración (PTES) define las pruebas de penetración en siete fases. En particular, las Directrices Técnicas de PTES ofrecen sugerencias prácticas sobre los procedimientos de prueba y recomendaciones para herramientas de pruebas de seguridad.

- Interacciones previas a la interacción
- Recopilación de inteligencia
- Modelado de amenazas
- Análisis de vulnerabilidades
- Explotación
- Postexplotación
- Informes

[Directrices técnicas del PTES](http://www.pentest-standard.org/index.php/PTES_Technical_Guidelines)

## Guía de pruebas de penetración de PCI

El requisito 11.3 del Estándar de Seguridad de Datos de la Industria de Tarjetas de Pago (PCI DSS) define las pruebas de penetración. PCI también define la Guía de pruebas de penetración.

### Guía de pruebas de penetración del PCI DSS

La guía de pruebas de penetración del PCI DSS proporciona orientación sobre lo siguiente:

- Componentes de las pruebas de penetración
- Requisitos de un evaluador de penetración
- Metodologías de pruebas de penetración
- Directrices para la elaboración de informes de pruebas de penetración

### Requisitos de Pruebas de Penetración PCI DSS

El requisito PCI DSS se refiere al Requisito 11.3 del Estándar de Seguridad de Datos de la Industria de Tarjetas de Pago (PCI DSS).

- Basado en enfoques aceptados por la industria.
- Cobertura para CDE y sistemas críticos.
- Incluye pruebas externas e internas.
- Pruebas para validar la reducción del alcance.
- Pruebas de la capa de aplicación.
- Pruebas de la capa de red para la red y el sistema operativo.

[Guía de Pruebas de Penetración PCI DSS](https://www.pcisecuritystandards.org/documents/Penetration-Testing-Guidance-v1_1.pdf)

## Marco de Pruebas de Penetración

El Marco de Pruebas de Penetración (PTF) ofrece una guía práctica y completa para pruebas de penetración. También detalla el uso de las herramientas de pruebas de seguridad en cada categoría. Las principales áreas de las pruebas de penetración incluyen:

- Huella de red (Reconocimiento)
- Descubrimiento y sondeo
- Enumeración
- Descifrado de contraseñas
- Evaluación de vulnerabilidades
- Auditoría de AS/400
- Pruebas específicas de Bluetooth
- Pruebas específicas de Cisco
- Pruebas específicas de Citrix
- Red troncal
- Pruebas específicas de servidor
- Seguridad VoIP
- Penetración inalámbrica
- Seguridad física
- Informe final (plantilla)

[Marco de pruebas de penetración](http://www.vulnerabilityassessment.co.uk/Penetration%20Test.html)

## Guía técnica para pruebas y evaluación de seguridad de la información

La Guía técnica para pruebas y evaluación de seguridad de la información (NIST 800-115) fue publicada por el NIST e incluye algunas técnicas de evaluación que se enumeran a continuación.

- Técnicas de Revisión
- Técnicas de Identificación y Análisis de Objetivos
- Técnicas de Validación de Vulnerabilidades de Objetivos
- Planificación de la Evaluación de Seguridad
- Ejecución de la Evaluación de Seguridad
- Actividades de Post-Prueba

Puede acceder a la norma NIST 800-115 [aquí](https://csrc.nist.gov/publications/detail/sp/800-115/final)

## Manual de Metodología de Pruebas de Seguridad de Código Abierto

El Manual de Metodología de Pruebas de Seguridad de Código Abierto (OSSTMM) es una metodología para evaluar la seguridad operativa de ubicaciones físicas, el flujo de trabajo, la seguridad humana, la seguridad física, la seguridad inalámbrica, la seguridad de las telecomunicaciones, la seguridad de las redes de datos y el cumplimiento normativo. OSSTMM puede servir como referencia complementaria de la norma ISO 27001, en lugar de una guía práctica o técnica para pruebas de penetración de aplicaciones.

OSSTMM incluye las siguientes secciones clave:

- Análisis de Seguridad
- Métricas de Seguridad Operacional
- Análisis de Confianza
- Flujo de Trabajo
- Pruebas de Seguridad Humana
- Pruebas de Seguridad Física
- Pruebas de Seguridad Inalámbrica
- Pruebas de Seguridad de Telecomunicaciones
- Pruebas de Seguridad de Redes de Datos
- Normativa de Cumplimiento
- Informes con el STAR (Informe de Auditoría de Pruebas de Seguridad)

[Manual de Metodología de Pruebas de Seguridad de Código Abierto](https://www.isecom.org/OSSTMM.3.pdf)

## Referencias

- [Estándar de Seguridad de Datos PCI - Guía de Pruebas de Penetración](https://www.pcisecuritystandards.org/documents/Penetration-Testing-Guidance-v1_1.pdf)
- [Estándar PTES](http://www.pentest-standard.org/index.php/Main_Page)
- [Manual de Metodología de Pruebas de Seguridad de Código Abierto (OSSTMM)](https://www.isecom.org/research.html#content5-9d)
- [Guía técnica para pruebas y evaluación de seguridad de la información NIST SP 800-115](https://csrc.nist.gov/publications/detail/sp/800-115/final)
- [Guía de pruebas de seguridad HIPAA](https://www.hhs.gov/hipaa/for-professionals/security/guidance/cybersecurity/index.html)
- [Marco de pruebas de penetración 0.59](http://www.vulnerabilityassessment.co.uk/Penetration%20Test.html)
- [Guía de pruebas de seguridad móvil OWASP](https://owasp.org/www-project-mobile-security-testing-guide/)
- [Kali Linux](https://www.kali.org/)
- [Suplemento informativo: Requisito 11.3 Pruebas de penetración](https://www.pcisecuritystandards.org/pdfs/infosupp_11_3_penetration_testing.pdf)
