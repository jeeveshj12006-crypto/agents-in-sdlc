---
title: "Lección 9 - Comandos de barra de GitHub Copilot CLI"
description: "Explora los comandos de barra para administrar el contexto, seleccionar modelos, compartir sesiones y delegar tareas opcionalmente a la nube."
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

Como cualquier buena herramienta de CLI, GitHub Copilot CLI incluye numerosos comandos de barra. Estos comandos exponen funcionalidades avanzadas, información interna y opciones de configuración adicionales. Ya has utilizado comandos como `/diff`, `/mcp`, `/skills`, `/agent` y `/pr`. Vamos a explorar algunos otros que resultan útiles.

En esta lección:

- utilizarás `/context` y `/compact` para explorar cómo administra Copilot el contexto de la conversación.
- utilizarás `/model` para explorar los modelos disponibles.
- aprenderás cómo `/share` puede exportar o compartir una sesión.
- explorarás comandos opcionales para trabajar en paralelo, utilizar árboles de trabajo y delegar tareas al agente en la nube.

## Escenario

Ya has terminado el flujo de trabajo principal de CLI. Ahora exploraremos otras funcionalidades: administrar el contexto, cambiar de modelo, compartir sesiones y, si lo deseas, delegar tareas al [agente de Copilot en la nube][about-cloud-agent].

## Explorar el contexto de Copilot CLI

Cuando trabajas en tareas largas o complejas, puedes alcanzar el límite de contexto del modelo. Copilot CLI compacta automáticamente la conversación cuando es necesario; también puedes consultar y compactar el contexto con comandos de barra.

1. Vuelve al codespace e inicia Copilot CLI desde la raíz del repositorio si aún no está abierto.
2. Escribe:

   ```plaintext
   /context
   ```

3. Observa el modelo, el uso actual de tokens y cómo se distribuye el contexto entre las instrucciones del sistema, las herramientas, los mensajes y el espacio disponible.
4. Compacta la conversación:

   ```plaintext
   /compact
   ```

5. Vuelve a ejecutar `/context` y compara el resultado. Si la conversación es pequeña, puede que no haya cambiado mucho.

> [!NOTE]
> Copilot CLI compacta automáticamente el contexto cuando se llena la ventana. Utiliza `/compact` para decidir cuándo hacerlo. Si vas a cambiar a una tarea que no esté relacionada y quieres empezar una conversación nueva, utiliza `/clear` o `/new`.

## Elegir un modelo

Los distintos modelos tienen diferentes puntos fuertes, y cada persona puede tener sus preferencias. Copilot CLI permite consultar y seleccionar el modelo que quieres utilizar.

1. Escribe:

   ```plaintext
   /model
   ```

2. Explora los modelos disponibles y la información de uso.
3. Mantén el modelo actual, selecciona otro o pulsa <kbd>Esc</kbd> para cerrar la lista.

## Compartir una sesión

La colaboración y el intercambio de conocimientos ayudan a todo el equipo a mejorar el uso de las herramientas de IA. El comando `/share` permite exportar una sesión a un archivo Markdown o HTML, crear un enlace para compartirla o publicar un gist de GitHub.

1. Escribe `/help` y revisa las opciones de `/share` de la versión instalada.
2. Si quieres compartir la sesión, elige el destino que se ajuste a tus necesidades, como `/share file` para exportarla en formato Markdown local.
3. Revisa el contenido exportado antes de enviarlo a alguien o publicarlo. Las exportaciones pueden incluir prompts, respuestas y detalles del proyecto.

Publicar un enlace o un gist es opcional. No publiques contenido del repositorio o de la conversación que el equipo no quiera compartir.

## Opcional: ampliar el trabajo o delegarlo

Ya has completado el taller principal. Copilot CLI también incluye comandos para tareas de mayor alcance:

- `/fleet` puede dividir tareas independientes entre subagentes y ejecutarlas en paralelo.
- `/worktree` puede crear un árbol de trabajo de Git aislado para otra tarea.
- `/delegate` puede enviar una tarea al agente de Copilot en la nube, que trabaja de forma asíncrona y puede abrir una solicitud de incorporación de cambios.

Estos comandos son opcionales porque pueden crear árboles de trabajo adicionales o iniciar trabajo remoto. Antes de probarlos, inicia una tarea nueva con un alcance claro y revisa el resultado con el proceso habitual. Para profundizar en el trabajo asíncrono con agentes, continúa con el [taller del agente en la nube][cloud-workshop].

## Resumen y pasos siguientes

Los comandos de barra de Copilot CLI permiten configurar la herramienta, compartir sesiones y observar lo que sucede internamente. En esta lección:

- has utilizado `/context` y `/compact` para explorar cómo administra Copilot el contexto de la conversación.
- has utilizado `/model` para explorar los modelos disponibles.
- has aprendido cómo `/share` puede exportar o compartir una sesión.
- has explorado comandos opcionales para trabajar en paralelo, utilizar árboles de trabajo y delegar tareas al agente en la nube.

Copilot CLI incluye muchos más comandos de barra y funcionalidades que puedes explorar. Vamos a cerrar el recorrido [repasando lo que hemos aprendido][next-lesson] y los siguientes pasos para continuar aprendiendo.

## Recursos

- [Referencia de comandos de Copilot CLI][cli-reference]
- [Administrar el contexto en Copilot CLI][context-management]
- [Acerca del agente de Copilot en la nube][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
