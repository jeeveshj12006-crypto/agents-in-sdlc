---
title: "Ejercicio 2 - Agente de GitHub Copilot en la nube"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lección anterior: Instrucciones personalizadas][previous-lesson] |
|:--|

Es probable que casi todas las organizaciones tengan problemas de deuda técnica. Puede tratarse de problemas de seguridad sin resolver, código heredado que necesita actualizarse o solicitudes de funcionalidades relegadas al backlog por falta de tiempo. El agente de GitHub Copilot en la nube está diseñado para realizar tareas como actualizar código y añadir funcionalidades de forma autónoma. Cuando termina, crea una solicitud de incorporación de cambios (PR) en borrador para que una persona la revise. Así puedes delegar tareas tediosas, acelerar el desarrollo y centrarte en cuestiones de mayor alcance.

En este ejercicio explorarás cómo utilizar el agente de GitHub Copilot en la nube para:

- personalizar el entorno de generación de código.
- asegurarte de que las operaciones se realizan de forma segura.
- comprender la importancia de delimitar claramente las incidencias.
- asignar incidencias a Copilot.

## Escenarios

Tailspin Toys tiene algunas tareas de deuda técnica pendientes. Los contratistas que desarrollaron la primera versión del sitio dejaron la documentación en un estado poco adecuado: falta por completo. Como primer paso, quieren añadir comentarios TSDoc a todas las funciones exportadas de la aplicación.

Además, el equipo de diseño quiere mejorar la forma de descubrir juegos. Quiere que la página de detalles de cada juego muestre otros títulos de la misma categoría para que las personas puedan seguir explorando. De momento no necesitan una implementación pulida: solo algo que permita probar y aceptar la experiencia de usuario. Es un impedimento, pero hay otras incidencias de mayor prioridad.

Estas son tareas que pueden quedar relegadas fácilmente y que conviene asignar al agente de GitHub Copilot en la nube. El agente puede trabajar en ellas de forma asíncrona mientras el equipo se ocupa de otras tareas y, después, revisar el trabajo para comprobar que todo esté en orden.

## Presentación del agente de GitHub Copilot en la nube

El [agente de GitHub Copilot en la nube](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) puede trabajar en segundo plano de forma parecida a una persona desarrolladora. Por ejemplo, puedes [asignarle una incidencia de GitHub](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions). Cuando recibe una incidencia, Copilot crea una solicitud de incorporación de cambios en borrador para registrar el progreso, prepara el entorno y empieza a trabajar. Puedes consultar la sesión mientras sigue en curso o una vez terminada. Cuando la solución esté lista para revisarse, te mencionará en la solicitud.

## La importancia de delimitar bien las instrucciones

GitHub Copilot no hace magia: no puedes dejar de pensar. Incluso las operaciones que parecen sencillas se vuelven complejas cuando se examinan a fondo, por lo que es importante [delimitar bien las tareas para el agente de GitHub Copilot en la nube][cloud-agent-best-practices]. Trátalo como a un compañero de programación con IA: trabaja por etapas, aprende, experimenta y adapta el enfoque. Los fundamentos del desarrollo de software no cambian por añadir IA generativa.

## Instrucciones personalizadas en este repositorio

En ejercicios anteriores has conocido las instrucciones personalizadas y cómo orientan a Copilot. Si ya has completado un ejercicio práctico sobre ellas, habrás visto `.github/copilot-instructions.md` en acción; si no, este es un buen momento para echarle un vistazo. Antes de asignar trabajo al agente de GitHub Copilot en la nube, revisa los archivos de instrucciones incluidos en el repositorio para que después puedas reconocer su efecto.

Abre estos archivos en la interfaz web de GitHub de tu repositorio o en un codespace que ya esté en ejecución:

- `.github/copilot-instructions.md`: revisa la sección **Code standards**, especialmente las convenciones de TypeScript y los comentarios TSDoc.
- `.github/instructions/unit-tests.instructions.md`: observa el frontmatter `applyTo`, que limita estas instrucciones a los archivos `**/*.test.ts`.

Cuando asignes al agente en la nube la incidencia `Code lacks documentation` en la siguiente sección, fíjate en los comentarios TSDoc, las convenciones de TypeScript y los encabezados de comentarios de la solicitud resultante. Estos elementos proceden de los archivos de instrucciones. Volveremos a mencionarlo al revisar las solicitudes en un ejercicio posterior.

## Configurar el entorno de desarrollo del agente de GitHub Copilot en la nube

La creación de código suele requerir un entorno específico y la ejecución de algunos scripts de configuración para garantizar que todo funcione correctamente, independientemente de quién haga el trabajo. Lo mismo ocurre cuando se asignan tareas a Copilot, que trabaja de forma parecida a una persona ingeniera de software.

El agente en la nube utiliza [GitHub Actions][github-actions] para preparar su entorno de trabajo. Puedes personalizarlo con un [flujo de trabajo de configuración especial][setup-workflow], definido en `.github/workflows/copilot-setup-steps.yml`, que se ejecuta antes de que empiece a trabajar. Así puede disponer de las herramientas y dependencias necesarias. Este flujo ya está configurado para facilitar el laboratorio y permitirte aprender. Da acceso a Node.js, las dependencias del proyecto, los binarios del navegador para las pruebas de extremo a extremo y la base de datos SQLite migrada y con datos iniciales para la aplicación Astro:

```yaml
name: "Copilot Setup Steps"

# Permite probar los pasos de configuración desde la pestaña "Actions" del repositorio
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Establece los permisos mínimos necesarios para *tus pasos*. Copilot recibirá su propio token para sus operaciones.
    permissions:
      # Si quieres clonar el repositorio durante los pasos de configuración, por ejemplo para instalar dependencias, necesitarás el permiso `contents: read`. Si no lo clonas durante la configuración, Copilot lo hará automáticamente cuando terminen los pasos.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Configuración del frontend y la aplicación: Node.js (la aplicación completa utiliza ahora Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migra y carga datos iniciales en la base de datos SQLite local para que las compilaciones y pruebas dispongan de datos.
      - name: Set up the database
        run: npm run db:setup
```

El archivo se parece a cualquier otro flujo de trabajo de GitHub, pero incluye algunos detalles importantes:

- Contiene un único trabajo llamado `copilot-setup-steps`. Se ejecuta en GitHub Actions antes de que Copilot empiece a trabajar en la solicitud de incorporación de cambios.
- El activador `workflow_dispatch` permite ejecutar manualmente el flujo desde la pestaña **Actions** del repositorio. Es útil para comprobar que funciona, en lugar de esperar a que Copilot lo ejecute.

## Añadir documentación

Aunque todo el mundo comprende la importancia de la documentación, la mayoría de los proyectos tienen información obsoleta o carecen de ella. Este tipo de deuda técnica suele quedar sin resolver, lo que reduce la productividad y dificulta el mantenimiento del código y la incorporación de nuevas personas al equipo. Copilot es especialmente útil para crear documentación, por lo que esta incidencia es ideal para asignársela al agente en la nube. Trabajará en segundo plano para generar la documentación necesaria. En un ejercicio posterior volverás para revisar el resultado.

1. En una nueva pestaña del navegador, ve al repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue** para abrir el cuadro de diálogo de creación.
4. Selecciona **Blank issue**.
5. En **Title**, escribe `Code lacks documentation`.
6. En **Description**, escribe:

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. Selecciona **Create** para crear la incidencia.
8. A la derecha, selecciona **Assign to Copilot** para abrir el cuadro de diálogo de asignación.

  ![Asignar una incidencia a Copilot](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.
10. Selecciona la pestaña **Pull Requests**.
11. Abre la solicitud de incorporación de cambios (PR) que acaba de generar Copilot. Su título será parecido a `[WIP]: Code lacks documentation`. Si no aparece, espera un momento y actualiza la página.
12. Al cabo de unos minutos, Copilot debería haber creado una lista de tareas.

> [!NOTE]
> La lista de tareas puede tardar varios minutos en aparecer en la PR. Copilot está preparando el entorno (ejecutando el flujo de trabajo que se acaba de describir), analizando el proyecto y decidiendo cómo abordar el problema.

13. Revisa la lista y las tareas que va a completar.
14. Desplázate por la cronología de la solicitud; deberías ver una actualización que indica que Copilot ha empezado a trabajar en la incidencia.
15. Selecciona el botón **View session**.

  ![Vista de la sesión de Copilot](../../../_images/ex4-view-session.png)

> [!CAUTION]
> Puede que tengas que actualizar la página para ver el indicador actualizado.

16. Observa que puedes desplazarte por la sesión en directo y seguir cómo Copilot resuelve el problema. Verás cómo explora el código y analiza el estado, se detiene a pensar y decidir un plan adecuado y, por último, crea código.

El proceso tardará varios minutos. Una de las principales ventajas del agente de GitHub Copilot en la nube es que puede trabajar de forma asíncrona y dejarte ocuparte de otras tareas. Aprovecharemos esa funcionalidad: mientras le asignamos otra tarea, trabajaremos en añadir funcionalidades a la aplicación.

## Añadir una sección de juegos relacionados a la página de detalles

Como se ha indicado, una de las grandes ventajas del agente de GitHub Copilot en la nube es que permite dividir el trabajo: tú puedes centrarte en un conjunto de tareas mientras el agente se ocupa de otro. Aunque quizá no se tarde mucho en añadir la sección de juegos relacionados que pide el equipo de diseño, ese tiempo se puede dedicar a otras tareas. Vamos a asignársela al agente.

1. Vuelve al repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue** para abrir el cuadro de diálogo de creación.
4. Selecciona **Blank issue**.
5. En **Title**, escribe `Show related games on the game details page`.
6. En **Description**, escribe:

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. Selecciona **Create** para crear la incidencia.
8. A la derecha, selecciona **Assign to Copilot** para abrir el cuadro de diálogo de asignación.

  ![Asignar una incidencia a Copilot](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.

Poco después, deberías ver varios emoji 👀 en el primer comentario de la incidencia, señal de que Copilot ya está trabajando.

![Copilot utiliza el emoji de los ojos para indicar que está trabajando en la incidencia](../../../_images/ex4-issue-eyes-emoji.png)

Copilot ya está trabajando en la segunda solicitud. Como el agente de GitHub Copilot en la nube trabaja de forma parecida a una persona ingeniera de software, no tienes que supervisarlo activamente: puedes revisar su trabajo cuando termine. Ahora vamos a crear y utilizar agentes personalizados.

## Resumen y pasos siguientes

En esta lección has explorado el [agente de GitHub Copilot en la nube][copilot-agents]. Puedes asignar incidencias a Copilot para que las resuelva de forma asíncrona. Puede ayudarte con deuda técnica, nuevas funcionalidades o migraciones de código entre frameworks.

Has aprendido:

- a personalizar el entorno de generación de código.
- a asegurarte de que las operaciones se realizan de forma segura.
- por qué es importante delimitar claramente las incidencias.
- a asignar incidencias a Copilot.

Mientras el agente en la nube trabaja, vamos a crear y utilizar agentes personalizados. [El agente de GitHub Copilot en la nube también puede usar servidores MCP][cloud-agent-mcp] y dispone de las instrucciones personalizadas que has explorado en los módulos anteriores.

## Recursos

- [Acerca del agente de GitHub Copilot en la nube][copilot-agents]
- [Asignar incidencias de GitHub a Copilot][assign-issue]
- [Prácticas recomendadas para el flujo de trabajo de configuración del agente en la nube][cloud-agent-best-practices]

| [Siguiente lección: Agentes personalizados →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
