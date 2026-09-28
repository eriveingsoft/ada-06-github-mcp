# GitHub MCP Engineering Review — ADA-06 

## 1. Repository 
* **Owner / Repo:** `eriveingsoft/ada-05-spec-driven-feature`
* **Visibility:** Public
* **Branch / reference analyzed:** `master` (commit `9a771d980a4787bd81ceb64f478ca7902bea6bc8`), evaluando la rama de fusión del PR `fix-valores-nulos` (SHA: `582e5cb1cfa95643df2bf0212facc3785b772a56`).

## 2. MCP Connection 
* **Server:** `github-readonly`
* **Mode:** READ-ONLY 
* **Toolsets:** 
  - **repos:** `get_file_contents`, `list_branches`, `list_commits`, `get_commit`, `list_repository_collaborators`, `list_releases`, `get_latest_release`, `get_release_by_tag`, `list_tags`, `get_tag`.
  - **issues:** `list_issues`, `issue_read`, `list_issue_fields`, `list_issue_types`, `get_label`.
  - **pull_requests:** `list_pull_requests`, `pull_request_read`.
  - **search:** `search_code`, `search_commits`, `search_issues`, `search_pull_requests`, `search_repositories`.

## 3. Repository Understanding 
* **Purpose:** Buscador de clientes por línea de comandos (Customer Search CLI) desarrollado en Python bajo la metodología *Spec-Driven Development* (ADA-05). Permite filtrar registros locales desde `customers.json` por nombre o correo electrónico, aplicando normalización de acentos/diacríticos, insensibilidad a mayúsculas, validación de caracteres de entrada y ordenamiento alfabético.
* **Architecture summary:** Arquitectura desacoplada en tres capas principales con modelo de dominio inmutable:
  1. *Capa CLI (`src/main.py`):* Parser de argumentos de terminal (`argparse`), control de codificación UTF-8, formateo de salida de resultados y gestión de códigos de retorno (`exit code` 0 y 1).
  2. *Motor de Búsqueda y Validaciones (`src/search_logic.py`):* Validación de longitud (>= 2 caracteres) y lista blanca de caracteres; normalización Unicode (NFD); filtrado en memoria y ordenamiento alfabético por nombre.
  3. *Repositorio de Acceso a Datos (`src/data_access.py`):* Lectura y deserialización del archivo `customers.json` con control de excepciones (`FileNotFoundError`, `ValueError`).
  4. *Modelo de Dominio (`src/domain.py`):* Dataclass `Customer` (`id`, `name`, `email`) con deserializador seguro `from_dict`.
* **Important files:** 
  - Código fuente: `src/main.py`, `src/domain.py`, `src/search_logic.py`, `src/data_access.py`.
  - Datos y configuración: `customers.json`, `pytest.ini`.
  - Especificación y gobernanza: `SPEC.md`, `ARCHITECTURE.md`, `REQUIREMENTS.md`, `TASKS.md`, `docs/traceability.md`, `README.md`.
* **Tests:** Suite automatizada bajo `pytest` estructurada en 3 módulos:
  - `tests/test_setup.py`: Integridad del archivo semilla `customers.json` y del entorno (NFR-01, NFR-02).
  - `tests/test_domain.py`: Pruebas unitarias de `Customer.from_dict`, tipos de datos y manejo del repositorio.
  - `tests/test_search.py`: Normalización de texto, validaciones de términos, invocación CLI y verificación de latencia (< 500 ms, NFR-03).
  - Total de pruebas en el repositorio: **48 pruebas** (todas pasando).

## 4. Issue Analysis 
* **Issue:** Issue #1 — *"Prevenir casteo a 'None' y añadir validación estricta en Customer.from_dict"* (Estado: `CLOSED`).
* **Facts from Issue:** 
  - `Customer.from_dict` ejecutaba `str(data["name"])`, convirtiendo valores `null` del JSON en el string literal `"None"`.
  - Causaba contaminación de registros en memoria con datos corruptos y generaba falsos positivos en las búsquedas.
  - Ausencia de validación para cadenas vacías (`""`) o con solo espacios (`"   "`).
  - Comportamiento solicitado: Validar estrictamente y lanzar `ValueError` descriptivo si `id`, `name` o `email` son `None`, o si `name` o `email` están vacíos tras `.strip()`.
  - Definición explícita de criterios de aceptación: **AC-06** a **AC-09** y actualización documental en `SPEC.md`.
* **Ambiguities:** 
  - No se especificó el texto literal exacto que debía incluir el `ValueError`.
  - No se definió el comportamiento para cuando el campo `id` es provisto como una cadena vacía (`id=""`).
  - No se detalló si `name` o `email` debían rechazar tipos no-string válidos en Python (ej. enteros o booleanos).
* **Related requirements/spec:** 
  - `REQUIREMENTS.md`: FR-05 (integridad de atributos de clientes devueltos), FR-06 (respuestas de no coincidencia limpias), NFR-02 (persistencia JSON), NFR-03 (pruebas en pytest).
  - `SPEC.md`: Sección *Domain Model*, sección *Validation Rules* (reglas de nulos y vacíos), criterios de aceptación **AC-06**, **AC-07**, **AC-08**, **AC-09** y escenarios de prueba **TS-06**, **TS-07**.
* **Related code:** `src/domain.py` (`Customer.from_dict`) y `src/data_access.py` (`load_customers`).
* **Related tests:** `tests/test_domain.py::test_customer_from_dict_validation`.

## 5. Pull Request Review 
* **PR:** PR #2 — *"validar nulos y vacíos "* (Estado: `CLOSED / MERGED`, rama `fix-valores-nulos` hacia `master`).
* **Changed behavior:** Implementa política *fail-fast* en `Customer.from_dict`: comprueba si alguno de los campos obligatorios (`id`, `name`, `email`) es `None` lanzando `ValueError`, y comprueba si `name` o `email` quedan vacíos tras `.strip()` lanzando `ValueError`.
* **Changed files:** 
  - `src/domain.py` (+12, -1 líneas): Lógica de validación estricta.
  - `tests/test_domain.py` (+46 líneas): 11 nuevos casos de prueba parametrizados.
  - `SPEC.md` (+8 líneas): Inclusión formal de AC-06..AC-09 y TS-06..TS-07.
  - `TASKS.md` (+7, -1 líneas): Documentación de la tarea técnica T-07.
  - `docs/traceability.md` (+5, -1 líneas): Actualización de la matriz de cobertura.
  - `src/__pycache__/domain.cpython-314.pyc` y `tests/__pycache__/test_domain.cpython-314-pytest-9.0.2.pyc`: Binarios compilados modificados.
* **Tests:** 
  - `test_customer_from_dict_rejects_none_fields_ac_06` (parametrizado en `id`, `name`, `email`).
  - `test_customer_from_dict_rejects_empty_or_whitespace_strings_ac_07` (parametrizado con `""`, `"   "`, `" \t \n "`).
  - `test_customer_from_dict_prevents_casting_none_to_literal_string_ac_06` (regresión directa de Issue #1).
  - `test_customer_from_dict_valid_edge_cases` (no-regresión con `id=0` e identificadores alfanuméricos).
* **Observations:** Alta rigurosidad en la metodología Spec-Driven Development; sincronización completa entre código, especificación y trazabilidad. Salida CLI controlada ante errores.
* **Risks:** Política *fail-fast* estricta: un solo registro malformado en `customers.json` detiene la inicialización de toda la CLI.
* **Questions:** ¿Debe admitirse `id=""` como string vacío válido? ¿Debería existir un mecanismo de recuperación parcial (*skip & log warning*) en `load_customers` para entornos de producción?
* **Potential defects:** 
  1. Inclusión accidental de binarios compilados `.pyc` de CPython 3.14 en el repositorio Git.
  2. Omisión de validación de espacios vacíos en el campo `id` cuando se utiliza como cadena de texto.

## 6. Traceability 
* **Issue → Requirement/Spec → PR → Code → Test:**
  - **Issue #1** (Requerimiento de validación estricta de nulos y vacíos)
    $$\downarrow$$
  - **Requisitos / Especificación:** `REQUIREMENTS.md` (FR-05, FR-06, NFR-03) $\rightarrow$ `SPEC.md` (*Validation Rules*, AC-06, AC-07, AC-08, AC-09, TS-06, TS-07) $\rightarrow$ `TASKS.md` (Tarea T-07)
    $$\downarrow$$
  - **Pull Request #2:** Rama `fix-valores-nulos` (Commit `582e5cb` - *"fix(domain): validar nulos y vacíos en Customer.from_dict"*)
    $$\downarrow$$
  - **Código Fuente:** `src/domain.py` (Líneas 29-37 en `Customer.from_dict`)
    $$\downarrow$$
  - **Pruebas Automatizadas:** `tests/test_domain.py` (`test_customer_from_dict_rejects_none_fields_ac_06`, `test_customer_from_dict_rejects_empty_or_whitespace_strings_ac_07`, `test_customer_from_dict_prevents_casting_none_to_literal_string_ac_06`)
    $$\downarrow$$
  - **Matriz de Control:** `docs/traceability.md` (Filas de T-07 registradas con estado `Done`).
  ## 6. Traceability

| Issue / Need | PR | Code / Files | Test / Evidence | Estado |
| :--- | :--- | :--- | :--- | :--- |
| Issue #1 / Validación estricta de nulos y vacíos | PR #2 | `src/domain.py` | `tests/test_domain.py` | Covered |

## 7. Permission Review 
* **Authentication:** Conexión mediante GitHub Personal Access Token (PAT) inyectado en el entorno local del agente.
* **GitHub token permissions:** Conexión con privilegios restringidos a lectura (*read-only*).
* **MCP read-only:** Activado y forzado. El servidor MCP (`github-readonly`) carece de herramientas de escritura o mutación.
* **Enabled toolsets:** 22 herramientas distribuidas en repositorios, issues, pull requests, releases, tags y búsqueda semántica/léxica.
* **Write capabilities exposed:** **Ninguna (0 capacidades de escritura).** Imposibilidad arquitectónica de crear issues, registrar comentarios o modificar archivos en el repositorio remoto desde esta sesión MCP.

## 8. Security Notes 
* **Credential exposure:** El token de GitHub y las credenciales del sistema operativo se mantuvieron aisladas en variables de entorno; no fueron registradas, emitidas en logs ni expuestas en las respuestas.
* **Prompt injection:** Al inspeccionar contenido de issues, PRs y código remoto, existe riesgo de *Indirect Prompt Injection*. La configuración de solo lectura (`READ-ONLY`) actúa como una barrera de contención determinista, garantizando que instrucciones maliciosas en el texto no puedan provocar efectos secundarios destructivos en GitHub.
* **Excess permissions:** No se detectaron permisos excesivos. Se aplicó el principio de mínimo privilegio (*least privilege*), impidiendo mutaciones en la infraestructura de código.
* **Repository scope:** Todas las consultas y llamadas MCP estuvieron delimitadas exclusivamente al repositorio objetivo `eriveingsoft/ada-05-spec-driven-feature`.

## 9. Human Review 
**What did you verify yourself?** 
Verifiqué manualmente la correcta configuración del Token de Acceso Personal (PAT) asegurando que tuviera permisos estrictos de "Read-only". Comprobé personalmente la lista de las 22 herramientas expuestas por el servidor MCP en Antigravity para confirmar que ninguna tuviera capacidades de escritura. Además, validé que las relaciones establecidas en la tabla de trazabilidad (Issue → PR → Código) existieran realmente en los commits del repositorio del ADA-05, evitando aceptar alucinaciones de la IA.

**What AI conclusions did you reject or modify?** 
Ninguna conclusión principal fue rechazada. Tras revisar las respuestas de la IA sobre la arquitectura, la trazabilidad del Issue/PR y el análisis de seguridad, determiné que sus deducciones eran precisas y coherentes. Me limité a confirmar que sus inferencias estuvieran respaldadas por la evidencia en el repositorio, pero no fue necesario modificar o descartar su análisis final, ya que se apegó a un perfil técnico y estructurado.

## 10. Conclusion 
**What did MCP add to the engineering workflow?** 
El Model Context Protocol (MCP) eliminó por completo la fricción del "copiar y pegar" contexto, permitiendo al agente inspeccionar remotamente la arquitectura, los archivos fuente, los Issues y los Pull Requests en tiempo real. Esto aceleró drásticamente la revisión de código y el análisis de trazabilidad. Al mismo tiempo, demostró que es posible integrar asistencia de IA avanzada en el ciclo de vida del software manteniendo una postura de seguridad, aplicando el principio de menor privilegio mediante tokens granulares para proteger la integridad del código fuente.