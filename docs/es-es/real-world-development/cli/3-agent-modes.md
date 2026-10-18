---
title: "Lección 3 - Modos de agente: Plan y Autopilot"
description: "Utiliza Plan para acordar un enfoque, Autopilot para crear una funcionalidad de filtrado a partir de una incidencia e Interactive para revisar el resultado."
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

Hemos empezado añadiendo una funcionalidad pequeña al proyecto. Los cambios de mayor alcance requieren un proceso más sólido. GitHub Copilot CLI está diseñado para integrarse en el flujo de trabajo de la organización y ayudarnos a crear lo adecuado de la forma correcta. En esta lección iniciarás un proceso habitual de desarrollo con agentes: partirás de una incidencia para crear una funcionalidad, comprobarás que el código es válido y que el comportamiento es el esperado y, finalmente, integrarás el cambio en el proyecto.

> [!NOTE]
> Utilizarás la misma conversación y rama durante todo el flujo de trabajo de la funcionalidad. Normalmente se utilizarían ramas o PR distintas para los diferentes tipos de archivos, pero en este taller agruparemos el trabajo para centrarnos en los conceptos principales.

En esta lección:

- iniciarás una conversación de Copilot a partir de una incidencia de GitHub.
- definirás los requisitos en el modo Plan.
- implementarás la nueva funcionalidad con el modo Autopilot.
- revisarás el código.
- validarás manualmente la funcionalidad en el navegador mediante el puerto reenviado de Codespaces.

Mientras continúas el trabajo, actualizarás las instrucciones del repositorio, personalizarás la habilidad existente `quality-checks`, añadirás validación con MCP y crearás un agente QA antes de abrir la PR de la funcionalidad.

## Escenario

El catálogo de Tailspin Toys crece y quienes lo visitan necesitan filtrar los juegos por categoría y editorial. La incidencia del backlog describe la funcionalidad, pero hay que acordar detalles como la combinación de categorías antes de empezar a programar. Utilizarás el modo Plan para resolver estas decisiones y, después, autorizarás una implementación con un alcance definido mediante Autopilot.

## Contexto

Incorporar agentes de programación con IA al proceso de desarrollo no cambia los fundamentos; de hecho, los hace aún más importantes. La mayoría de las personas desarrolladoras siguen un proceso similar a este:

1. Partir de una incidencia registrada que describa lo que hay que hacer.
2. Crear un plan de implementación.
3. Crear y revisar el código.
4. Ejecutar pruebas para validarlo.
5. Validar manualmente la nueva funcionalidad.
6. Crear una solicitud de incorporación de cambios (PR).
7. Cuando se revise el código y se superen las comprobaciones de integración continua, combinarlo.

> [!NOTE]
> Los detalles concretos varían según el equipo y la organización, pero la mayoría de los procesos se parecen al anterior.

Seguir este enfoque estándar ayuda a garantizar que el código generado por IA cumpla los requisitos y pase el mismo proceso de revisión que el código escrito manualmente.

## Modos de conversación

El **modo de conversación** controla el nivel de autonomía del agente. Pulsa <kbd>Shift</kbd>+<kbd>Tab</kbd> para cambiar de modo:

- **Interactive**: tú y el agente trabajáis en colaboración. El agente propone cambios y espera tus indicaciones para continuar.
- **Plan**: el agente crea primero un plan y no puede editar los archivos del proyecto.
- **Autopilot**: el agente trabaja de forma autónoma: escribe código, ejecuta pruebas e itera hasta completar la tarea.

Empieza en el modo Plan, revisa el plan y, después, utiliza Autopilot para implementarlo.

## Partir de la incidencia

Antes de empezar el trabajo de filtrado, vuelve al codespace y asegúrate de que el repositorio y el terminal estén listos.

1. Vuelve al codespace. Si está detenido, reinícialo.
2. Confirma que se ha combinado la PR de valoración por estrellas.
3. Si el terminal no está abierto, pulsa <kbd>Control</kbd>+<kbd>`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux).
4. Actualiza `main` y crea una rama para el trabajo de filtrado:

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Inicia Copilot CLI:

   ```bash
   copilot --yolo
   ```

6. Pulsa <kbd>Tab</kbd> dos veces para abrir la pestaña **Issues**.
7. Pulsa <kbd>A</kbd> para mostrar todas las incidencias.
8. Utiliza las flechas para resaltar la incidencia titulada **Allow users to filter games by category and publisher**.
9. Pulsa <kbd>C</kbd> para añadir la incidencia al prompt y volver a la pestaña **Session**.

Observa que el prompt ahora empieza por `#7` (o un número similar). El prefijo `#` permite añadir al contexto una incidencia o solicitud de incorporación de cambios (PR) de GitHub.

## Planificar la funcionalidad de filtrado

La planificación permite definir cómo implementar una funcionalidad o realizar una tarea antes de asignarla a Copilot. Conviene dedicar un tiempo a planificar cualquier tarea compleja. Vamos a cambiar al modo Plan y pedir a Copilot que cree el plan.

1. Pulsa <kbd>Shift</kbd>+<kbd>Tab</kbd> para cambiar al modo Plan. Comprueba que el indicador situado bajo el prompt muestre **Plan**.
2. Después de la referencia a la incidencia añadida en el paso anterior, escribe:

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot empezará a crear el plan. Primero explorará el proyecto y después determinará el enfoque más adecuado.

3. Durante el proceso, Copilot quizá te pregunte cómo debería funcionar el filtrado. Responde según tus preferencias; no hay respuestas incorrectas.
4. Cuando el plan esté listo, pulsa <kbd>Control</kbd>+<kbd>E</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) para ampliarlo.
5. Desplázate por el plan y revísalo.
6. Pide a Copilot que revise cualquier parte que no refleje tus decisiones.

## Aprobar Autopilot

Ya has redactado y revisado el plan. Ahora toca implementarlo. Vamos a dejar que Copilot trabaje en la tarea con Autopilot.

Autopilot permite que Copilot itere sobre el problema hasta que considere que está resuelto.

1. Selecciona **Accept plan and build on autopilot (recommended)**, o la opción con un nombre parecido de tu versión instalada.
2. Comprueba que el indicador situado bajo el prompt muestre **Autopilot**.
3. Observa cómo Copilot sigue el plan, genera código y ejecuta pruebas.

> [!NOTE]
> La aprobación puede iniciar la implementación inmediatamente, así que revisa el plan primero. Si Copilot indica que faltan dependencias o que hay un conflicto de puertos, resuelve el problema de configuración antes de dar por completadas las comprobaciones.

## Revisar y verificar la implementación

Una vez generado el código, hay que revisarlo antes de combinarlo, como cualquier otro cambio. Vamos a revisar el código y ejecutar el sitio para comprobar el resultado.

1. Pulsa <kbd>Shift</kbd>+<kbd>Tab</kbd> para pasar al modo Interactive. Comprueba que el indicador del modo no muestre **Plan** ni **Autopilot**.
2. Escribe `/diff` y revisa la implementación del filtrado y sus pruebas.
3. Compara el resultado con la incidencia y con las decisiones tomadas durante la planificación.
4. Cuando termines, pulsa <kbd>Esc</kbd> para salir de la vista del diff.
5. Revisa los resultados de las comprobaciones del proyecto y pide a Copilot que corrija los fallos.

## Explorar la nueva funcionalidad

El código parece correcto, pero ¿funciona? Como antes, vamos a iniciar la aplicación y abrir el sitio en el navegador mediante el puerto reenviado de Codespaces.

1. Pide a Copilot que inicie la aplicación:

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Cuando Codespaces indique que el puerto `4321` está disponible, selecciona **Open in Browser**.
3. Prueba los filtros de categoría y editorial, así como las combinaciones acordadas en el plan.
4. Confirma que el comportamiento de restablecimiento y de resultados vacíos coincida con la incidencia y con tus decisiones.
5. Vuelve al codespace y pide a Copilot que detenga el servidor de desarrollo.

## Resumen y pasos siguientes

Has utilizado distintos modos de conversación para crear y revisar una funcionalidad. En esta lección:

- has iniciado una conversación de Copilot a partir de una incidencia de GitHub.
- has definido los requisitos en el modo Plan.
- has implementado la funcionalidad con el modo Autopilot.
- has revisado el código.
- has validado manualmente la funcionalidad en el navegador mediante el puerto reenviado de Codespaces.

A continuación, veremos cómo se genera el código y cómo garantizar que siga las prácticas documentadas mediante las [instrucciones personalizadas][next-lesson].

## Recursos

- [Autopilot en GitHub Copilot CLI][autopilot]
- [Referencia de comandos de Copilot CLI][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
