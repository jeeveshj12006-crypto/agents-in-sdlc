---
title: "Ejercicio 2 - Añadir funcionalidad con el modo agente de Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← Lección anterior: Instrucciones personalizadas][previous-lesson] · [Siguiente lección: MCP con Playwright →][next-lesson]

Incluso las actualizaciones más sencillas de una aplicación suelen requerir cambios en varios archivos y tareas como ejecutar pruebas. Normalmente, el trabajo de desarrollo implica localizar todos los archivos pertinentes, aplicar los cambios, ejecutar las pruebas, depurar, averiguar qué archivo falta y volver a modificarlo. Y así sucesivamente.

El modo agente de Copilot está pensado para este tipo de trabajo.

El modo agente de Copilot puede trabajar con mayor autonomía en el IDE. Se comporta de forma parecida a una persona desarrolladora: primero explora la estructura del proyecto, aplica los cambios necesarios, ejecuta tareas como las pruebas y corrige automáticamente los errores que detecta. Vamos a ver cómo utilizarlo para añadir funciones al sitio.

> [!NOTE]
> Aunque los nombres se parecen, el modo agente y el agente en la nube están diseñados para experiencias distintas. El modo agente trabaja en el IDE y permite obtener comentarios e interactuar con rapidez. El agente en la nube actúa como un compañero de programación que trabaja de forma asíncrona con incidencias y solicitudes de incorporación de cambios.

En este ejercicio aprenderás a:

- utilizar el modo agente de Copilot para explorar el proyecto e identificar los archivos pertinentes.
- utilizar el modo agente de GitHub Copilot para implementar funcionalidades en la interfaz y en la capa de datos.
- revisar los cambios y las pruebas que genere antes de integrarlos en el código base.

## Escenario

A medida que crece la lista de juegos, quieres permitir que las personas filtren por categoría y editorial. En el ejercicio anterior ya has añadido un helper de editoriales. Ahora completarás con el modo agente de Copilot el trabajo restante en la capa de datos, la interfaz y las pruebas.

## Ejecutar el sitio web de Tailspin Toys

Antes de hacer cambios, exploremos el sitio de Tailspin Toys para conocer sus funcionalidades actuales.

El sitio web es una plataforma de financiación colectiva para juegos de mesa con temática de desarrollo de software. Permite consultar juegos y ver información detallada sobre ellos. Se trata de una única aplicación Astro que genera páginas HTML estáticas durante la compilación. Las páginas consultan una base de datos SQLite local directamente desde su frontmatter mediante Drizzle ORM: no hay una API de backend independiente ni un framework de interfaz en el cliente. Los helpers reutilizables para acceder a los datos están en `src/lib/`; la interactividad se añade mediante elementos `<script>` de Astro pequeños y específicos que utilizan las API estándar del DOM.

### Iniciar el sitio web

Para simplificar el inicio del sitio, se ha incluido la tarea `npm run dev`, que ejecuta el único servidor de desarrollo de Astro. Puedes usarla en el codespace de GitHub:

1. Vuelve al codespace. Después del ejercicio anterior deberías estar en `main`.
2. Abre un terminal en el codespace con <kbd>Control</kbd>+<kbd>`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux).
3. Crea una rama para el trabajo de filtrado y cámbiate a ella:

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. Ejecuta el siguiente comando para iniciar el sitio:

   ```bash
   npm run dev
   ```

   Cuando el servidor de desarrollo de Astro esté listo, aparecerá un mensaje con la URL, similar a este:

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> Si aparece un cuadro de diálogo que te pide abrir una ventana del navegador para `http://localhost:4321`, ciérralo seleccionando **x**.

5. Para abrir el sitio, mantén pulsada la tecla <kbd>Command</kbd> (Mac) o <kbd>Ctrl</kbd> (Windows/Linux) y selecciona la dirección del servidor Astro `http://localhost:4321` en el terminal.

> [!NOTE]
> En un codespace, seleccionar un enlace `localhost` en el terminal redirige automáticamente a `https://<your-codespace-name>-4321.app.github.dev/`. Se trata de un túnel privado al codespace, que ahora sirve el sitio web.

### Explorar el sitio web

Con el sitio en ejecución, puedes explorar sus funcionalidades principales:

- **Página de inicio**: muestra una lista de juegos de mesa con sus títulos, imágenes y descripciones.
- **Página de detalles del juego**: al seleccionar un juego, se abre una página con más información, como el título, la descripción, la editorial y la categoría.

## Explorar el backlog con Copilot

> [!TIP]
> **Abrir Copilot Chat**
>
> Antes de empezar los pasos siguientes, vuelve al codespace, abre el panel de Copilot Chat y selecciona **New Chat** para iniciar una conversación limpia. El modo y el modelo pueden variar según el ejercicio; cada paso indicará cuáles utilizar.

La implementación inicial del sitio funciona, pero queremos añadir nuevas capacidades. Empecemos por revisar el backlog. Al crear el proyecto desde la plantilla se generó automáticamente un conjunto de incidencias. Pide a GitHub Copilot que te las muestre.

1. Selecciona **Agent** en el selector de agentes de la vista Chat. El agente puede planificar e implementar cambios en varios archivos, ejecutar comandos de terminal e invocar herramientas.

   ![Captura del selector de agentes en la vista Chat](../../../_images/shared-chat-mode-selector.png)

2. Selecciona **Claude Sonnet 4.5** en la lista de modelos disponibles.

> [!CAUTION]
> Los autores del taller no expresan preferencia por un modelo. Al crearlo utilizamos Claude Sonnet 4.5 y por eso lo hemos incluido en estas instrucciones. Esperamos que las sugerencias de código sean relativamente coherentes para ofrecer una buena experiencia. Sin embargo, como los LLM son probabilísticos, puede que las sugerencias no coincidan con las que se muestran en el taller. Es normal y esperable.

> [!NOTE]
> Debido al carácter probabilístico de los LLM, Copilot puede utilizar un comando MCP distinto y aun así completar la tarea.

3. Pide a Copilot que muestre el backlog enviándole este prompt:

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. Selecciona **Continue** para ejecutar el comando que muestra todas las incidencias.
5. Revisa la lista generada.

Observa que Copilot también ha priorizado las incidencias según lo que considera más útil para las personas usuarias.

## Revisar los archivos de instrucciones

Antes de pedir al agente que genere código, conviene revisar el archivo de instrucciones que proporcionará contexto para el trabajo. Utilizarás el archivo de [interfaz de usuario (UI)](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md), que describe cómo abordar la incorporación de funcionalidades al sitio.

1. En el codespace, abre `.github/instructions/ui.instructions.md`.
2. Fíjate en las directrices generales para añadir funcionalidades. Incluyen:
   - una descripción general de la arquitectura.
   - principios de diseño de componentes, capacidad de prueba y accesibilidad.
   - enlaces a instrucciones específicas para distintos tipos de archivo, incluidos:
     - Astro.
     - Tailwind CSS.
     - Drizzle.

> [!TIP]
> Los archivos de instrucciones pueden incluir enlaces a otros archivos de instrucciones y a archivos del proyecto. Las rutas son relativas al archivo de instrucciones. Esto permite reutilizar directrices, dividir conjuntos grandes de instrucciones en partes más manejables e incluir ejemplos y plantillas.

## Implementar la funcionalidad de filtrado

Para completar el filtrado, hay que hacer al menos tres cambios en la aplicación:

- añadir o perfeccionar la lógica de filtrado restante en la capa de datos (`src/lib/`).
- añadir o actualizar las pruebas del comportamiento de filtrado.
- actualizar la página de la lista de juegos para incluir la interfaz de filtrado.

Además, las pruebas deben ejecutarse y superarse antes de integrar los cambios en el código base. El modo agente de Copilot puede encargarse de estas tareas.

1. Puedes continuar en la conversación actual o seleccionar **New Chat** para iniciar otra.
2. Selecciona **Add Context**, **Instructions** y **ui** para añadir el archivo de instrucciones.

   ![Captura de la selección del archivo de instrucciones UI](../../../_images/ex3-select-instructions-file.png)

3. Comprueba que **Agent** siga seleccionado en el selector de agentes de la vista Chat.

   ![Captura del selector de agentes en la vista Chat](../../../_images/shared-chat-mode-selector.png)

4. Comprueba que **Claude Sonnet 4.5** siga seleccionado como modelo.
5. Pide a Copilot que implemente la funcionalidad según la incidencia correspondiente del backlog:

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Observa cómo Copilot explora el proyecto e identifica los archivos pertinentes. Debería encontrar los helpers de la capa de datos, la interfaz y las pruebas. Después, modificará los archivos y ejecutará las pruebas.

   ![Captura de Copilot mientras explora los archivos del proyecto](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot realizará varias tareas, como explorar el proyecto, modificar archivos y ejecutar pruebas. Según la complejidad de la tarea y del código base, puede tardar unos minutos. Durante el proceso, quizá aparezcan los botones **Keep** y **Undo** en el editor. Cuando Copilot termine, verás estas opciones para todos los cambios, así que no tienes que seleccionarlas mientras el trabajo siga en curso.

7. Cuando Copilot lo solicite, selecciona **Continue** para ejecutar las pruebas.

   ![Captura de un diálogo de Copilot Chat que pide confirmar la ejecución de pruebas](../../../_images/ex3-agent-mode-run-tests.png)

8. Puede que haya pausas y que algunas pruebas fallen durante el proceso. No pasa nada: Copilot alterna entre generar código y ejecutar pruebas hasta completar la tarea y dejar de detectar errores.

   ![Captura de una sesión de Copilot Agent Mode completada](../../../_images/ex3-agent-mode-proposed-changes.png)

9. Explora el código generado y comprueba si presenta algún problema.

> [!CAUTION]
> Recuerda que siempre es importante revisar el código generado por Copilot o cualquier otra herramienta de IA.

10. Vuelve al navegador donde está abierto el sitio y explora la nueva funcionalidad.
11. Cuando hayas confirmado que todo funciona y hayas revisado el código, selecciona **Keep** en la ventana de Copilot Chat.

## Resumen y pasos siguientes

En este ejercicio has explorado cómo utilizar el modo agente de GitHub Copilot para añadir capacidades a Tailspin Toys. Has aprendido:

- a implementar funcionalidades en la interfaz y en la capa de datos con el modo agente de GitHub Copilot.
- a explorar el proyecto e identificar archivos pertinentes para coordinar cambios.
- a revisar los cambios y las pruebas generados antes de integrarlos en el código base.

Ahora vamos a [probar la funcionalidad con el servidor MCP de Playwright][next-lesson] y a abrir una solicitud de incorporación de cambios.

### Ejercicio adicional: implementar la paginación

A medida que crezca la lista de juegos, será necesario permitir la paginación. Utiliza lo aprendido en este ejercicio y pide a Copilot que la implemente en el sitio. Al preparar la solicitud:

- sigue las prácticas recomendadas existentes y utiliza los archivos de instrucciones disponibles.
- decide cómo quieres implementar la paginación: por ejemplo, si las personas usuarias podrán elegir el tamaño de página o si será fijo.
- incluye en el prompt las directrices necesarias para que Copilot implemente la solución como quieres.
- puede que tengas que iterar con GitHub Copilot, solicitar cambios y proporcionar contexto. Es lo normal al trabajar con Copilot.

## Recursos

- [Modos Ask, Edit y Agent de Copilot: para qué sirven y cuándo usarlos][choose-mode]
- [Modo agente en VS Code][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
