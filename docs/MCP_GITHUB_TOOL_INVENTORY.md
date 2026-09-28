# Inventario de Herramientas MCP - GitHub

| Tool / capacidad | Toolset | Read/Write | Uso en el ADA | Riesgo |
| :--- | :--- | :---: | :--- | :--- |
| `get_commit` | Commits / Git | Read | Inspeccionar mensajes de commit, autoría, metadatos y diferencias de cambios puntuales en el repositorio. | Bajo (Solo lectura) |
| `get_file_contents` | Código / Archivos | Read | Consultar el contenido de archivos o directorios remotos sin necesidad de clonar el repositorio completo. | Bajo (Solo lectura; considerar exposición de secretos si existiesen en código) |
| `get_label` | Issues / Metadatos | Read | Obtener detalles y especificaciones de una etiqueta concreta (nombre, color, descripción) para clasificación. | Bajo (Solo lectura) |
| `get_latest_release` | Releases / Versiones | Read | Identificar la versión estable más reciente y consultar sus notas de lanzamiento (*changelog*). | Bajo (Solo lectura) |
| `get_release_by_tag` | Releases / Versiones | Read | Consultar información detallada y notas de versión de un release asociado a un tag específico. | Bajo (Solo lectura) |
| `get_tag` | Tags / Git | Read | Obtener metadatos sobre una etiqueta Git específica (commit apuntado, autor, fecha). | Bajo (Solo lectura) |
| `issue_read` | Issues | Read | Leer la descripción, estado, etiquetas y discusiones de un issue específico para diagnóstico o planificación. | Bajo (Solo lectura) |
| `list_branches` | Ramas / Git | Read | Listar todas las ramas del repositorio para conocer la topología de trabajo y ramas activas. | Bajo (Solo lectura) |
| `list_commits` | Commits / Git | Read | Revisar el histórico reciente de commits en una rama determinada con soporte de paginación. | Bajo (Solo lectura) |
| `list_issue_fields` | Issues / Metadatos | Read | Consultar campos personalizados disponibles para issues a nivel de repositorio u organización. | Bajo (Solo lectura) |
| `list_issue_types` | Issues / Metadatos | Read | Listar los tipos de issues permitidos (bug, feature, task, etc.) en el repositorio u organización. | Bajo (Solo lectura) |
| `list_issues` | Issues | Read | Listar y paginar el backlog de issues abiertos o cerrados para seguimiento de tareas pendientes. | Bajo (Solo lectura) |
| `list_pull_requests` | Pull Requests | Read | Enumerar pull requests para supervisar solicitudes de cambios y su estado de revisión. | Bajo (Solo lectura) |
| `list_releases` | Releases / Versiones | Read | Obtener el histórico cronológico de versiones y entregas publicadas del proyecto. | Bajo (Solo lectura) |
| `list_repository_collaborators` | Gobernanza / Usuarios | Read | Inspeccionar la lista de usuarios y colaboradores con permisos sobre el repositorio. | Bajo (Solo lectura; involucra visibilidad de identidades de colaboradores) |
| `list_tags` | Tags / Git | Read | Enumerar todas las etiquetas Git generadas en el repositorio. | Bajo (Solo lectura) |
| `pull_request_read` | Pull Requests | Read | Obtener el detalle completo de un PR (descripción, commits, comentarios, estado de checks) para code review. | Bajo (Solo lectura) |
| `search_code` | Búsqueda | Read | Localizar de manera exacta o por patrones funciones, clases, imports y variables en el código fuente. | Bajo (Solo lectura; consumo de cuota de API en repositorios muy extensos) |
| `search_commits` | Búsqueda | Read | Buscar commits por palabras clave en mensajes, autores o fechas en la rama principal. | Bajo (Solo lectura) |
| `search_issues` | Búsqueda | Read | Búsqueda semántica y en lenguaje natural de issues para identificar duplicados o reportes asociados. | Bajo (Solo lectura) |
| `search_pull_requests` | Búsqueda | Read | Buscar PRs utilizando filtros avanzados (autor, rama, menciones o términos clave). | Bajo (Solo lectura) |
| `search_repositories` | Búsqueda | Read | Descubrir repositorios complementarios, dependencias o proyectos de referencia en GitHub. | Bajo (Solo lectura) |
| **Write tools** (ej. `create_issue`, `push_files`, `pull_request_review_write`) | — | **No disponibles** | No deben aparecer debido a la política y configuración `read-only` | Alto (si estuvieran habilitadas permitirían mutaciones remotas) |