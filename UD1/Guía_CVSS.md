# Guía CVSS v3.x

## 1. Métricas de Explotabilidad (Exploitability Metrics)

### AV — Attack Vector (Vector de Ataque)
¿Desde dónde se puede lanzar el ataque?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **N** | Network | Remoto, por red — el caso más peligroso, sin cercanía ni acceso previo |
| **A** | Adjacent | Solo desde la misma red local/segmento (misma wifi o VLAN) |
| **L** | Local | Requiere acceso local al equipo (sesión ya iniciada, terminal) |
| **P** | Physical | Requiere tocar físicamente el dispositivo |

---

### AC — Attack Complexity (Complejidad del Ataque)
¿Qué tan difícil es explotar la vulnerabilidad?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **L** | Low | Se explota de forma fiable, sin condiciones especiales |
| **H** | High | Requiere condiciones fuera del control del atacante (interceptar tráfico, ganar una carrera de tiempos...) |

---

### PR — Privileges Required (Privilegios Requeridos)
¿Qué nivel de acceso necesita tener ya el atacante?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **N** | None | Ninguna cuenta ni credencial previa |
| **L** | Low | Necesita privilegios básicos de usuario normal |
| **H** | High | Necesita privilegios administrativos previos |

---

### UI — User Interaction (Interacción del Usuario)
¿Necesita que alguien más haga algo?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **N** | None | El atacante actúa solo, sin depender de nadie |
| **R** | Required | Necesita que una víctima haga algo (clic, abrir un archivo, tener sesión activa) |

---

## 2. Métrica de Alcance (Scope)

### S — Scope (Alcance)
¿Hasta dónde llega el daño?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **U** | Unchanged | El impacto se queda dentro del propio componente vulnerable |
| **C** | Changed | El impacto se "escapa" a recursos fuera del control de seguridad de ese componente |

---

## 3. Métricas de Impacto (Impact Metrics)

Aplica para **C** (Confidencialidad), **I** (Integridad) y **A** (Disponibilidad):
* **C — Confidentiality:** ¿Cuánta información puede leer el atacante?
* **I — Integrity:** ¿Cuánto puede modificar el atacante?
* **A — Availability:** ¿Cuánto puede interrumpir el servicio el atacante?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **N** | None | Sin impacto en esa propiedad |
| **L** | Low | Impacto parcial: se compromete algo, no todo |
| **H** | High | Impacto total: se compromete por completo |

---

## 4. Métricas Temporales (Opcionales)

### E — Exploit Code Maturity (Madurez del Exploit)
¿Existe ya forma fácil de explotarla?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **X** / **ND** | Not Defined | Sin definir |
| **U** | Unproven | Sin prueba de concepto pública disponible |
| **P** | Proof-of-Concept | Existe PoC teórica o código no funcional fácil |
| **F** | Functional | Existe código funcional de explotación |
| **H** | High | Explotación automática/funcional disponible masivamente |

### RL — Remediation Level (Nivel de Solución)
¿Hay ya solución?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **X** / **ND** | Not Defined | Sin definir |
| **O** | Official Fix | Parche u actualización oficial del fabricante |
| **T** | Temporary Fix | Solución temporal proporcionada por el fabricante |
| **W** | Workaround | Medidas de mitigación no oficiales por parte del usuario |
| **U** | Unavailable | No hay solución disponible actualmente |

### RC — Report Confidence (Confianza del Informe)
¿Cuánto nos fiamos del informe?

| Sigla | Valor | Significado |
| :---: | :--- | :--- |
| **X** / **ND** | Not Defined | Sin definir |
| **U** | Unknown | Fuente no fidedigna o poca información disponible |
| **R** | Reasonable | Confirmado por fuentes creíbles pero sin prueba directa |
| **C** | Confirmed | Confirmado totalmente por el fabricante o análisis técnico probado |
