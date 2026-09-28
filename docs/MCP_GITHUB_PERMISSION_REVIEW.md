# Permission Review

| Capability | Needed | Granted | Control | Decision |
| :--- | :--- | :--- | :--- | :--- |
| Read repository contents | Yes | Yes | PAT Contents: Read | Keep |
| Read Issues | Yes | Yes | PAT Issues: Read | Keep |
| Read Pull Requests | Yes | Yes | PAT Pull requests: Read | Keep |
| Create/edit Issue | No | No | MCP read-only | Block |
| Comment on PR | No | No | MCP read-only | Block |
| Push files / branch | No | No | MCP read-only + PAT read | Block |
| Other repositories | No | No | Fine-grained PAT selected repo | Block |

## Análisis de Riesgos

| Riesgo | Ejemplo | Mitigación |
| :--- | :--- | :--- |
| Over-permissioned token | Token con acceso a todos los repos o permisos write | Fine-grained PAT + repositorio único + Read only |
| Credential leakage | PAT en commit, captura o AI log | No versionar credenciales; revocar si se filtra |
| Prompt injection | Issue/PR contiene instrucciones maliciosas dirigidas al agente | Tratar contenido de GitHub como datos no confiables; mantener read-only |
| Unnecessary tools | Habilitar actions/security/admin sin necesitarlos | Toolsets repos, issues, pull_requests únicamente |
| Scope confusion | Agente analiza otro repositorio | Nombrar owner/repo explícitamente en prompts |

### Investigación: Lockdown Mode vs. Token Read-Only

El **Lockdown Mode** está diseñado para proteger el repositorio en casos donde un tercero intenta introducir prompts maliciosos (por ejemplo, en la descripción de un Issue). Su función es filtrar y aislar este contenido no confiable para evitar que, al momento de utilizar un agente de IA para leer el repositorio, este sea engañado y ejecute acciones indebidas.

Sin embargo, esta mitigación es diferente y no sustituye a la frontera de autorización que proporciona un **Token Read-Only**. El token de solo lectura actúa como la barrera de seguridad definitiva: si el Lockdown Mode falla, el agente sufre un *prompt injection* y este intenta realizar una acción destructiva (como borrar archivos), simplemente no podrá hacerlo. La API bloqueará la acción porque el agente está restringido estrictamente a permisos de lectura, haciendo imposible cualquier modificación.