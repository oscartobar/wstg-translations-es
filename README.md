# Traducción de la Guía de Pruebas de Seguridad Web - OWASP

[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/OWASP/wstg/issues)
[![OWASP Flagship](https://img.shields.io/badge/owasp-flagship-brightgreen.svg)](https://owasp.org/projects/)
[![Twitter Follow](https://img.shields.io/twitter/follow/owasp_wstg?style=social)](https://twitter.com/owasp_wstg)

[![Creative Commons License](https://licensebuttons.net/l/by-sa/4.0/88x31.png)](https://creativecommons.org/licenses/by-sa/4.0/ "CC BY-SA 4.0")

El repositorio oficial en ingles lo encuentra en https://github.com/OWASP/wstg. Esta es la traduccion al español del repositorio original.

Bienvenido al repositorio oficial de la Guía de Pruebas de Seguridad Web (WSTG) del Proyecto Abierto de Seguridad de Aplicaciones Web® (OWASP®). La WSTG es una guía completa para probar la seguridad de aplicaciones y servicios web. Creada gracias a la colaboración de profesionales de la seguridad y voluntarios dedicados, la WSTG proporciona un marco de buenas prácticas utilizado por expertos en pruebas de penetración y organizaciones de todo el mundo.

Actualmente estamos trabajando en la versión 5.0. Puede consultarla. [Lea el documento actual aquí en GitHub](https://github.com/OWASP/wstg/tree/master/document).

Para la última versión estable, [vea la versión 4.2](https://github.com/OWASP/wstg/releases/tag/v4.2). También disponible [online](https://owasp.org/www-project-web-security-testing-guide/v42/).

- [Cómo referenciar escenarios WSTG](#how-to-reference-wstg-scenarios)
    - [Enlaces](#linking)
- [Contributions, Feature Requests, and Feedback](#contributions-feature-requests-and-feedback)
- [Chat With Us](#chat-with-us)
- [Project Leaders](#project-leaders)
- [Core Team](#core-team)
- [Translations](#translations)

## Cómo referenciar escenarios WSTG

Cada escenario tiene un identificador con el formato `WSTG-<categoría>-<número>`, donde `categoría` es una cadena de 4 caracteres en mayúsculas que identifica el tipo de prueba o punto débil, y `número` es un valor numérico con relleno de ceros del 01 al 99. Por ejemplo: `WSTG-INFO-02` es la segunda prueba de recopilación de información.

Los identificadores pueden cambiar entre versiones. Por lo tanto, es preferible que otros documentos, informes o herramientas utilicen el formato `WSTG-<versión>-<categoría>-<número>`, donde `versión` es la etiqueta de la versión sin puntuación. Por ejemplo: `WSTG-v42-INFO-02` se entendería como la segunda prueba de recopilación de información de la versión 4.2.

Si se utilizan identificadores sin el elemento `<versión>`, se debe asumir que se refieren al contenido más reciente de la Guía de Pruebas de Seguridad Web. A medida que la guía crece y cambia, esto se vuelve problemático, por lo que los escritores o desarrolladores deben incluir el elemento de versión.

### Enlaces

Los enlaces a los escenarios de la Guía de Pruebas de Seguridad Web deben realizarse mediante enlaces versionados, no "estable" ni "último", que cambiarán con el tiempo. Sin embargo, la intención del equipo del proyecto es que los enlaces versionados no cambien. Por ejemplo: `https://owasp.org/www-project-web-security-testing-guide/v42/4-Pruebas_de_Seguridad_de_aplicaciones_Web/01-Information_Gathering/02-Fingerprint_Web_Server.html`. Note: the `v42` element refers to version 4.2.

## Contributions, Feature Requests, and Feedback

We are actively inviting new contributors! To start, read the [contribution guide](CONTRIBUTING.md).

First time here? Here are [GitHub's suggestions for first-time contributors](https://github.com/OWASP/wstg/contribute) to this repository.

This project is only possible thanks to the work of many dedicated volunteers. Everyone is encouraged to help in ways large and small. Here are a few ways you can help:

- Read the current content and help us fix any spelling mistakes or grammatical errors.
- Help with [translation](CONTRIBUTING.md#translation) efforts.
- Choose an existing issue and submit a pull request to fix it.
- Open a new issue to report an opportunity for improvement.

To learn how to contribute successfully, read the [contribution guide](CONTRIBUTING.md).

Successful contributors appear on [the project's list of authors, reviewers, or editors](document/1-Frontispiece/README.md).

## Chat With Us

We're easy to find on Slack:

1. Join the OWASP Group Slack with this [invitation link](https://owasp.org/slack/invite).
2. Join this project's [channel, #testing-guide](https://app.slack.com/client/T04T40NHX/CJ2QDHLRJ).

Feel free to ask questions, suggest ideas, or share your best recipes.

You can @ us on Twitter [@owasp_wstg](https://twitter.com/owasp_wstg).

You can also join our [Google Group](https://groups.google.com/a/owasp.org/forum/#!forum/testing-guide-project).

## Project Leaders

- [Rick Mitchell](https://github.com/kingthorin)
- [Elie Saad](https://github.com/ThunderSon)

## Core Team

- [Rejah Rehim](https://github.com/rejahrehim)
- [Victoria Drake](https://github.com/victoriadrake)

## Translations

- [Portuguese-BR](https://github.com/doverh/wstg-translations-pt)
- [Russian](https://github.com/andrettv/WSTG/tree/master/WSTG-ru)
- [French](https://github.com/clallier94/wstg-translation-fr)
- [Persian (Farsi)](https://github.com/whoismh11/owasp-wstg-fa)

---

Open Web Application Security Project and OWASP are registered trademarks of the OWASP Foundation, Inc.
