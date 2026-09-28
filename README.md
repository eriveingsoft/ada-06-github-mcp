# ADA-06: Configuración y Análisis con GitHub MCP (Model Context Protocol)

**Asignatura:** Ingeniería de Software asistida por IA  
**Repositorio Analizado:** ada-05-spec-driven-feature  

## Descripción del Proyecto
Este repositorio contiene la evidencia de la integración y uso del GitHub MCP Server en un entorno de desarrollo local (Antigravity). El objetivo principal fue establecer una conexión segura (Zero Trust) utilizando un Token de Acceso Personal (PAT) de solo lectura, y utilizar al agente de IA para navegar autónomamente por el código, Issues y Pull Requests de un repositorio existente para realizar un análisis de trazabilidad y arquitectura sin riesgo de inyección de código.

---

## Preguntas de Reflexión

**1. ¿Qué información obtuvo el agente mediante MCP que normalmente habrías tenido que copiar al prompt?**
Toda la estructura de carpetas, el contenido íntegro de los archivos de código fuente, los metadatos de los commits, y las descripciones exactas del Issue y del Pull Request. Sin MCP, habría tenido que copiar y pegar múltiples archivos a mano, corriendo el riesgo de omitir contexto o superar el límite de tokens.

**2. ¿Cuál es la diferencia entre el GitHub MCP Server y una GitHub Tool?**
El **MCP Server** es el protocolo o servidor intermediario que estandariza la comunicación entre la IA y GitHub. Una **GitHub Tool** es una capacidad o función específica que ese servidor le expone al agente (por ejemplo, `get_file_contents` o `search_code`).

**3. ¿Qué toolset fue necesario para leer código? ¿Cuál para Issues? ¿Cuál para PRs?**
- Para leer código: `repos`
- Para Issues: `issues`
- Para Pull Requests: `pull_requests`

**4. ¿Qué diferencia existe entre permisos del PAT y herramientas expuestas por MCP?**
Los permisos del PAT pertenecen a la capa de **Autorización** (controlan lo que el token puede hacer en la API de GitHub físicamente). Las herramientas expuestas por MCP pertenecen a la capa de **Policy/Exposición** (controlan qué acciones se le muestran a la IA en su menú de opciones). Ambas deben ser coherentes para una seguridad robusta.

**5. ¿Por qué se usó read-only aunque el agente pudiera ser capaz de proponer cambios?**
Para aplicar el principio de menor privilegio (Zero Trust). Mantener al agente en modo lectura protege el repositorio contra modificaciones accidentales, alucinaciones de la IA o ataques de *prompt injection* derivados de contenido no confiable.

**6. ¿Qué evidencia comprobó que el MCP estaba realmente conectado?**
En la interfaz de Antigravity, la conexión se confirmó al ver el indicador verde con la leyenda "22 tools enabled" y cuando el agente pudo listar sus capacidades (`search_code`, `issue_read`, etc.) y leer en tiempo real la estructura de mi repositorio del ADA-05.

**7. ¿Qué información del Issue era un hecho y qué parte fue inferencia del agente?**
Los hechos fueron los requerimientos explícitos y la descripción del problema escritos textualmente en GitHub. Las inferencias fueron las deducciones del agente sobre qué archivos específicos del código (`src/...`) debían modificarse para resolverlo y los posibles *edge cases* no mencionados en el texto original.

**8. ¿Qué relación encontraste entre Issue, PR, código y tests?**
(Trazabilidad): El Issue define el problema o requerimiento original; el PR contiene la propuesta de solución empaquetada; el código modificado representa la implementación real en los archivos fuente, y los tests son la evidencia comprobable de que el requerimiento del Issue se cumplió satisfactoriamente.

**9. ¿Qué riesgo tiene tratar el contenido de un Issue o PR como instrucciones confiables?**
El riesgo principal es el *Prompt Injection*. Si un atacante escribe instrucciones ocultas en un Issue y el agente lo lee, la IA podría confundir esos datos con órdenes directas del usuario y ejecutar acciones maliciosas si tuviera permisos de escritura.

**10. ¿En qué escenario permitirías escritura mediante MCP? ¿Qué acción requeriría aprobación humana?**
Permitiría escritura en un entorno local aislado o para generar borradores de PRs, documentación automatizada o *scaffolding* de código. Sin embargo, acciones destructivas o definitivas (como hacer `merge` a la rama `main`, publicar *Releases* o borrar ramas) siempre deberían requerir intervención y aprobación humana.

**11. ¿Qué cambiarías si el repositorio fuera privado o perteneciera a una organización?**
El Fine-grained PAT necesitaría configurarse explícitamente para solicitar acceso a los recursos de esa Organización (SSO) y los permisos tendrían que ser aprobados por un administrador de la organización en GitHub antes de que MCP pudiera leer los datos.

**12. ¿Qué aporta MCP al ciclo de vida de Ingeniería de Software frente a copiar/pegar contenido en un chat?**
Aporta autonomía, contexto dinámico y reducción de fricción. El agente puede explorar el repositorio por sí mismo, rastrear referencias entre archivos y asegurarse de que está analizando la última versión del código directamente del control de versiones, eliminando los errores humanos al proveer contexto de manera manual.