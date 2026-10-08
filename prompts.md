# Biblioteca de prompts — Laboratorio 07

## Tarea 1: Clasificación de issues
**Herramienta:** ChatGPT 

### Versión A — Sin ejemplos (zero-shot)
**Prompt:**

```text
Clasifica cada issue de GitHub indicando su tipo, prioridad y módulo.
1. "Al pagar con tarjeta aparece error 500 y el pedido queda duplicado."
2. "Agregar modo oscuro en la pantalla de perfil."
3. "¿El endpoint /api/pedidos acepta paginación?"
4. "El login tarda 8 segundos cuando hay más de 100 usuarios conectados."
5. "Actualizar la librería axios por una alerta de seguridad."
```

**Resultado (resumen):**

| # | Tipo | Prioridad | Módulo |
| :-: | :--- | :--- | :--- |
| 1 | Bug | Crítica | Pagos / Pedidos |
| 2 | Feature | Baja | Perfil / Interfaz |
| 3 | Pregunta | Baja | API / Pedidos |
| 4 | Bug / Rendimiento | Alta | Login / Autenticación |
| 5 | Seguridad | Crítica | Dependencias / Backend |

**Explicación breve:**
1. **Bug:** el error 500 y la duplicación del pedido son fallos graves que afectan directamente al pago.
2. **Feature:** el modo oscuro es una nueva funcionalidad solicitada.
3. **Pregunta:** se está consultando si el endpoint admite paginación, no reportando un fallo.
4. **Rendimiento:** el login presenta una demora importante bajo carga.
5. **Seguridad:** la actualización de Axios responde a una alerta de seguridad y debe atenderse con alta prioridad.

### Versión B — Few-shot (3 ejemplos)
**Prompt:**

```text
# Instrucción
Clasifica cada issue siguiendo exactamente el formato de los ejemplos.
Tipos válidos: BUG, FEATURE, CONSULTA, RENDIMIENTO, SEGURIDAD.
Prioridad: ALTA (afecta dinero, datos o seguridad), MEDIA o BAJA.

# Ejemplos
Issue: "La app se cierra al subir una foto de perfil."
Tipo: BUG | Prioridad: MEDIA | Módulo: perfil

Issue: "Permitir exportar el historial de pedidos a Excel."
Tipo: FEATURE | Prioridad: BAJA | Módulo: pedidos

Issue: "Las contraseñas se guardan en texto plano en la tabla usuarios."
Tipo: SEGURIDAD | Prioridad: ALTA | Módulo: autenticación

# Issues a clasificar
1. "Al pagar con tarjeta aparece error 500 y el pedido queda duplicado."
2. "Agregar modo oscuro en la pantalla de perfil."
3. "¿El endpoint /api/pedidos acepta paginación?"
4. "El login tarda 8 segundos cuando hay más de 100 usuarios conectados."
5. "Actualizar la librería axios por una alerta de seguridad."
```

**Resultado (resumen):**

Issue: "Al pagar con tarjeta aparece error 500 y el pedido queda duplicado."
Tipo: BUG | Prioridad: ALTA | Módulo: pagos

Issue: "Agregar modo oscuro en la pantalla de perfil."
Tipo: FEATURE | Prioridad: BAJA | Módulo: perfil

Issue: "El endpoint /api/pedidos acepta paginación?"
Tipo: CONSULTA | Prioridad: BAJA | Módulo: pedidos

Issue: "El login tarda 8 segundos cuando hay más de 100 usuarios conectados."
Tipo: RENDIMIENTO | Prioridad: MEDIA | Módulo: autenticación

Issue: "Actualizar la librería axios por una alerta de seguridad."
Tipo: SEGURIDAD | Prioridad: ALTA | Módulo: dependencias

**Mejora observada:**
1. **Estandarización estricta de categorías:** A diferencia de la versión sin ejemplos (donde se inventaron tipos como "Pregunta" o combinaciones como "Bug / Rendimiento"), la versión few-shot utilizó de forma estricta únicamente los tipos permitidos (`BUG`, `FEATURE`, `CONSULTA`, `RENDIMIENTO`, `SEGURIDAD`).
2. **Consistencia en la prioridad:** Se aplicó correctamente el criterio especificado para definir la prioridad ALTA en temas que afectan dinero o seguridad (como el pago duplicado y la alerta de Axios).
3. **Homogeneidad de formato:** Se mantuvo exactamente la misma estructura monolínea (`Tipo: X | Prioridad: Y | Módulo: Z`) en los 5 issues, facilitando su integración en un script o pipeline automatizado.
---


## Tarea 2: Razonamiento lógico
**Herramienta:** Gemini

### Versión A — Sin ejemplos (zero-shot)
**Prompt:**
```text
Un sistema tiene 7 microservicios que se despliegan por rondas.
Un servicio solo se despliega cuando todas sus dependencias ya están desplegadas.
Los servicios sin dependencias pendientes se despliegan en paralelo en la misma ronda.
- usuarios depende de auth
- pagos depende de usuarios
- pedidos depende de usuarios, pagos y catalogo
- notificaciones depende de pedidos
- gateway depende de auth y notificaciones
- auth y catalogo no tienen dependencias
¿Cuántas rondas se necesitan como mínimo? Responde solo con el número.
```

**Resultado (resumen):**
...

3
### Versión B — Few-shot (3 ejemplos)
**Prompt:**

```# Instrucción
Resuelve el problema razonando ronda por ronda, igual que en los ejemplos.

# Ejemplo 1
Servicios: A, B. B depende de A.
Razonamiento: Ronda 1: A (sin dependencias). Ronda 2: B (A ya está desplegado).
Respuesta: 2 rondas.

# Ejemplo 2
Servicios: A, B, C. C depende de A y B.
Razonamiento: Ronda 1: A y B en paralelo. Ronda 2: C.
Respuesta: 2 rondas.

# Ejemplo 3
Servicios: A, B, C, D. B y C dependen de A. D depende de B y C.
Razonamiento: Ronda 1: A. Ronda 2: B y C en paralelo. Ronda 3: D.
Respuesta: 3 rondas.

# Problema
Un sistema tiene 7 microservicios que se despliegan por rondas.
Un servicio solo se despliega cuando todas sus dependencias ya están desplegadas.
Los servicios sin dependencias pendientes se despliegan en paralelo en la misma ronda.
- usuarios depende de auth
- pagos depende de usuarios
- pedidos depende de usuarios, pagos y catalogo
- notificaciones depende de pedidos
- gateway depende de auth y notificaciones
- auth y catalogo no tienen dependencias
¿Cuántas rondas se necesitan como mínimo?
```
**Resultado (resumen):**

Razonamiento:

Ronda 1: auth y catalogo (no tienen dependencias).

Ronda 2: usuarios (sus dependencias auth ya están desplegadas).

Ronda 3: pagos (sus dependencias usuarios ya están desplegadas).

Ronda 4: pedidos (sus dependencias usuarios, pagos y catalogo ya están desplegadas).

Ronda 5: notificaciones (sus dependencias pedidos ya están desplegadas).

Ronda 6: gateway (sus dependencias auth y notificaciones ya están desplegadas).

Respuesta: 6 rondas.

**Mejora observada:**
1. **Detección de errores y alucinaciones:** En la Versión A (zero-shot), el modelo alucinó una respuesta incorrecta de "3" rondas debido a la restricción de responder solo con un número. En cambio, al exigir el desglose paso a paso (Chain of Thought) en la Versión B, el modelo realizó la deducción lógica correcta determinando que la secuencia requerida dura 6 rondas.  
2. **Auditabilidad:** La versión few-shot permite verificar manualmente cada paso del despliegue y detectar exactamente en qué ronda se habilita cada microservicio.   
---

## Tarea 3: Extracción estructurada
**Herramienta:** Claude

### Versión A — Sin ejemplos (zero-shot)
**Prompt:**

```text
Extrae los datos importantes de estos reportes de error en formato JSON.
Reporte 1: "Desde ayer, en la versión 2.3.1 de Android, cuando un cliente aplica el cupón BIENVENIDA20 el total no se actualiza. Lo reportó Ana Díaz de soporte."
Reporte 2: "En la web el botón Descargar factura no hace nada y no muestra ningún error. Reportado por Luis Paredes el 3 de octubre."
```
**Resultado (resumen):**

```json
[
  {
    "id": 1,
    "plataforma": "Android",
    "version": "2.3.1",
    "funcionalidad_afectada": "Aplicación de cupón de descuento",
    "cupon": "BIENVENIDA20",
    "descripcion": "Al aplicar el cupón, el total no se actualiza",
    "mensaje_de_error": null,
    "inicio_del_problema": "ayer",
    "reportado_por": "Ana Díaz",
    "area_del_reportante": "Soporte",
    "fecha_del_reporte": null
  },
  {
    "id": 2,
    "plataforma": "Web",
    "version": null,
    "funcionalidad_afectada": "Descarga de factura",
    "cupon": null,
    "descripcion": "El botón Descargar factura no hace nada",
    "mensaje_de_error": "No se muestra ningún error",
    "inicio_del_problema": null,
    "reportado_por": "Luis Paredes",
    "area_del_reportante": null,
    "fecha_del_reporte": "3 de octubre"
  }
]
```

### Versión B — Few-shot (3 ejemplos)
**Prompt:**

```# Instrucción
Convierte cada reporte en un objeto JSON con el mismo esquema de los ejemplos.
Si un dato no aparece, usa null. Devuelve solo un arreglo JSON válido.

# Ejemplo 1
Reporte: "En iOS, versión 2.2.0, la app se congela al abrir el mapa. Reportó Carla Soto."
{"plataforma": "iOS", "version_app": "2.2.0", "modulo": "mapa", "sintoma": "la app se congela", "reportado_por": "Carla Soto", "fecha": null}

# Ejemplo 2
Reporte: "El 12 de septiembre, en la web, el formulario de registro acepta correos sin @"
{"plataforma": "web", "version_app": null, "modulo": "registro", "sintoma": "acepta correos sin @", "reportado_por": null, "fecha": "12 de septiembre"}

# Ejemplo 3
Reporte: "Jorge Ruiz indica que en Android 2.3.0 las notificaciones llegan duplicadas."
{"plataforma": "Android", "version_app": "2.3.0", "modulo": "notificaciones", "sintoma": "notificaciones duplicadas", "reportado_por": "Jorge Ruiz", "fecha": null}

# Reportes a convertir
Reporte 1: "Desde ayer, en la versión 2.3.1 de Android, cuando un cliente aplica el cupón BIENVENIDA20 el total no se actualiza. Lo reportó Ana Díaz de soporte."
Reporte 2: "En la web el botón Descargar factura no hace nada y no muestra ningún error. Reportado por Luis Paredes el 3 de octubre."
```
**Resultado (resumen):**
```json
[
  {
    "plataforma": "Android",
    "version_app": "2.3.1",
    "modulo": "cupones",
    "sintoma": "el total no se actualiza al aplicar el cupón BIENVENIDA20",
    "reportado_por": "Ana Díaz",
    "fecha": "ayer"
  },
  {
    "plataforma": "web",
    "version_app": null,
    "modulo": "facturas",
    "sintoma": "el botón Descargar factura no hace nada y no muestra ningún error",
    "reportado_por": "Luis Paredes",
    "fecha": "3 de octubre"
  }
]
```
**Mejora observada:**
1. **Adherencia estricta al esquema:** En la Versión A (zero-shot), la IA infirió e inventó campos adicionales no solicitados (como `area_del_reportante`, `cupon` e `inicio_del_problema`). En cambio, en la Versión B (few-shot), el modelo respetó rigurosamente la estructura y los nombres de las claves definidos en los ejemplos (`plataforma`, `version_app`, `modulo`, `sintoma`, `reportado_por`, `fecha`).
2. **Estandarización y previsibilidad:** La inclusión de ejemplos garantizó que la respuesta devuelva un arreglo JSON limpio y uniforme, facilitando su consumo directo en bases de datos o sistemas automatizados sin requerir un mapeo adicional.

---

## Tarea 4: Role prompting y prompts estructurados
**Herramienta:** DeepSeek

### Versión A — Sin rol
**Prompt:**
```text
Revisa este código:
def login(usuario, clave):
    query = "SELECT * FROM usuarios WHERE nombre='" + usuario + "' AND clave='" + clave + "'"
    return db.execute(query).fetchone() is not None
```    

**Resultado (resumen):**

### Revisión de seguridad del código
El código presenta vulnerabilidades críticas de seguridad. Te detallo los problemas:

### Problema principal: Inyección SQL

La función construye la consulta concatenando directamente los parámetros de entrada:

```Python
query = "SELECT * FROM usuarios WHERE nombre='" + usuario + "' AND clave='" + clave + "'"
```
### Esto permite SQL Injection. Ejemplos de ataques:

#### Bypass de autenticación:

```Python
login("admin'--", "cualquier_cosa")
# Query resultante:
# SELECT * FROM usuarios WHERE nombre='admin'--' AND clave='cualquier_cosa'
# El -- comenta el resto, entra como admin sin saber la contraseña
```
#### Peor aún, si el motor permite múltiples sentencias:

```Python
login("x'; DROP TABLE usuarios;--", "")
```
### Problemas secundarios

1. **Contraseñas en texto plano:** Se comparan directamente contra la BD. Deberían estar hasheadas (bcrypt, argon2, etc.) y compararse con `hashlib` / `bcrypt.checkpw`.

2. **Sin manejo de errores:** Si la BD falla, la excepción se propaga sin control.

3. **Sin límite de intentos:** Vulnerable a fuerza bruta.

4. `SELECT *`: Innecesario; basta con `SELECT 1` o `SELECT COUNT(*)`.

5. `db` no está definido en el snippet (probablemente global, mala práctica).

### Versión corregida

```Python
import bcrypt

def login(usuario: str, clave: str) -> bool:
    if not usuario or not clave:
        return False

    # Consulta parametrizada (placeholders según tu driver)
    query = "SELECT clave_hash FROM usuarios WHERE nombre = ?"
    fila = db.execute(query, (usuario,)).fetchone()

    if fila is None:
        # Comparación dummy para evitar timing attacks
        bcrypt.checkpw(b"dummy", b"$2b$12$" + b"x" * 53)
        return False

    return bcrypt.checkpw(clave.encode("utf-8"), fila[0])
```
### Notas sobre los placeholders

* **SQLite / MySQL** (`sqlite3`, `mysql-connector`): usa `?` o `%s` según el driver.

* **PostgreSQL** (`psycopg2`): usa `%s`.

* **SQLAlchemy:** usa :nombre o el ORM (session.query(...).filter_by(...)).

El driver **escapa automáticamente** los valores, eliminando la inyección SQL.

### Recomendaciones adicionales

* Guardar contraseñas con **bcrypt/argon2**, nunca en texto plano ni MD5/SHA1.

* Añadir **rate limiting** (p. ej., Flask-Limiter) para frenar fuerza bruta.

* Registrar intentos fallidos con IP y timestamp.

* Usar **MFA** si es posible.

* Nunca devolver mensajes que distingan "usuario no existe" vs "clave incorrecta".


### Versión B — Con rol y XML

**Prompt:**
```xml
<role>
Eres un Ingeniero Principal de Seguridad de Software especializado en OWASP Top 10 y auditoría de código backend en Python/SQL.
</role>

<task>
Audita la función `login()` adjunta, identifica todas las vulnerabilidades críticas y proporciona una versión refactorizada lista para producción.
</task>

<context>
El código pertenece al módulo de autenticación de una API REST. Recibe credenciales desde un formulario web.
</context>

<rules>
1. Clasifica las vulnerabilidades según OWASP Top 10.
2. Explica el impacto práctico de explotación para un equipo junior.
3. Proporciona el código corregido usando parámetros y hashing seguro de contraseñas.
4. Responde en el formato estructurado indicado.
</rules>

<output_format>
## 1. Análisis de Vulnerabilidades
| Vulnerabilidad | Riesgo | Corrección |

## 2. Código Corregido
[Código en Python refactorizado y comentado]

## 3. Notas para el equipo junior
[Puntos clave pedagógicos]
</output_format>

<code>
def login(usuario, clave):
    query = "SELECT * FROM usuarios WHERE nombre='" + usuario + "' AND clave='" + clave + "'"
    return db.execute(query).fetchone() is not None
</code>
```

**Resultado (resumen):**

Revisión de Seguridad: Función login()
### 1. Análisis de Vulnerabilidades

| Vulnerabilidad | Riesgo | Corrección |
| :--- | :--- | :--- |
| **SQL Injection (OWASP A03:2021 – Injection)** por concatenación de strings en `usuario` | Un atacante puede inyectar SQL. Ejemplo: `usuario = "admin'--"` bypassea la autenticación sin conocer la clave. También permite `UNION SELECT`, borrado de tablas (`'; DROP TABLE usuarios;--`) o exfiltración completa de la BD. Es la vulnerabilidad más crítica (CVSS ~9.8). | Usar **consultas parametrizadas** (`?` placeholders) para que el driver trate los valores como datos, nunca como SQL. |
| **SQL Injection** por concatenación en `clave` | Mismo impacto que el anterior. Además, la contraseña viaja en texto plano dentro de la query, quedando expuesta en logs de SQLite, trazas de errores y monitores de BD. | Parametrizar `clave` y **nunca** almacenarla en claro. |
| **Almacenamiento de contraseñas en texto plano** | Si la BD se filtra (backup, dump, insider), todas las credenciales quedan comprometidas. Viola OWASP A02:2021 – Cryptographic Failures. Además permite *credential stuffing* contra otros servicios. | Hashear con **bcrypt**, **argon2** o **scrypt** (con salt). Comparar usando `check_password_hash`. |
| **Uso de `SELECT *`** | Devuelve columnas innecesarias (hashes, roles, PII) y acopla el código al esquema. Facilita fugas si la respuesta se serializa. | Seleccionar solo lo necesario, p. ej. `SELECT 1` o `SELECT clave_hash`. |
| **Falta de manejo de errores** | Excepciones de SQLite pueden propagarse y revelar estructura de la BD o el query completo al cliente (Information Disclosure). | Envolver en `try/except`, loggear internamente y devolver error genérico. |
| **Ausencia de rate limiting / bloqueo por intentos** | Permite ataques de fuerza bruta o *credential stuffing* masivo. | Añadir throttling por IP/usuario y bloqueo temporal tras N fallos. |
| **Comparación no constante en el tiempo** (si se compara hash manualmente) | Ataques de *timing* para inferir la contraseña carácter a carácter. | Usar funciones de verificación que hagan comparación en tiempo constante (las librerías de hashing ya lo hacen). |

### 2. Código corregido
```python
import sqlite3
import bcrypt
import logging

logger = logging.getLogger(__name__)

# ---- Registro (referencia: así se debe guardar la clave) ----
def crear_usuario(db: sqlite3.Connection, usuario: str, clave: str) -> None:
    clave_hash = bcrypt.hashpw(clave.encode("utf-8"), bcrypt.gensalt())
    db.execute(
        "INSERT INTO usuarios (nombre, clave_hash) VALUES (?, ?)",
        (usuario, clave_hash),
    )
    db.commit()

# ---- Login corregido ----
def login(db: sqlite3.Connection, usuario: str, clave: str) -> bool:
    """
    Verifica credenciales de forma segura.
    Devuelve True solo si el usuario existe y la contraseña coincide.
    """
    if not usuario or not clave:
        return False

    try:
        cursor = db.execute(
            "SELECT clave_hash FROM usuarios WHERE nombre = ? LIMIT 1",
            (usuario,),  # <-- parametrizado: el driver escapa el valor
        )
        row = cursor.fetchone()
    except sqlite3.Error as e:
        # Log interno, respuesta genérica al cliente
        logger.exception("Error consultando usuario: %s", e)
        return False

    if row is None:
        # Mitiga enumeración de usuarios: mismo tiempo de respuesta
        # que un usuario existente con clave incorrecta.
        bcrypt.checkpw(b"dummy", bcrypt.hashpw(b"dummy", bcrypt.gensalt()))
        return False

    clave_hash = row[0]
    try:
        return bcrypt.checkpw(clave.encode("utf-8"), clave_hash)
    except (ValueError, TypeError):
        # Hash corrupto o formato inválido
        logger.warning("Hash inválido para usuario %r", usuario)
        return False
```

### 3. Notas para el equipo junior

1. **Regla de oro:** Nunca concatenar entrada del usuario en SQL. Siempre usar `?` (SQLite) o `%s` (psycopg2) según el driver[cite: 23, 24].
2. **Nunca guardar contraseñas en claro:** El campo en la base de datos debe llamarse `clave_hash`, no `clave`[cite: 24].
3. **Manejo de salts:** `bcrypt` ya incluye un salt aleatorio en cada hash, por lo que dos usuarios con la misma contraseña tendrán hashes distintos[cite: 24].
4. **Defensa en profundidad:** Además del fix principal, es recomendable añadir rate limiting (p. ej., `slowapi`), HTTPS obligatorio y logs de auditoría para intentos fallidos[cite: 24].
5. **No confíes en el cliente:** El retorno booleano `True/False` debe ir acompañado de una sesión o token firmado (JWT con expiración o cookie `HttpOnly` + `Secure`)[cite: 24].

---

**Mejora observada:**
1. **Estructura y legibilidad:** Al asignarle un rol técnico explícito y delimitaciones XML, el modelo formateó la respuesta exactamente como una auditoría profesional, organizando los hallazgos en una tabla comparativa clara con clasificación OWASP y nivel CVSS[cite: 18, 19].
2. **Profundidad y defensibilidad del código:** La respuesta no solo solucionó la vulnerabilidad de SQL Injection[cite: 18], sino que implementó prácticas avanzadas como protección contra ataques de temporización (*timing attacks*)[cite: 20, 22], manejo explícito de excepciones[cite: 20, 22] y notas educativas de arquitectura para el equipo desarrollador[cite: 23, 24].
---



## Rúbrica de evaluación

La evaluación de cada versión se realiza sumando el puntaje de 4 criterios clave (cada uno calificado de 1 a 5 puntos), obteniendo una nota final sobre 20:

| Criterio | Descripción | Puntaje Max. |
| :--- | :--- | :---: |
| **Exactitud** | La respuesta es correcta y técnicamente precisa. | 5 pts |
| **Formato** | Respeta el formato, esquema (JSON/XML/Tabla) o estructura solicitada. | 5 pts |
| **Consistencia** | Aplica el mismo criterio y rigor en todos los casos. | 5 pts |
| **Utilidad** | El resultado se puede usar directamente en producción o en el proyecto. | 5 pts |
| **TOTAL** | **Suma de los 4 criterios por tarea** | **20 pts** |

---

## Comparación: sin ejemplos vs. técnica avanzada

| Tarea | Versión A | Versión B | Mejora principal |
| :--- | :---: | :---: | :--- |
| **1. Clasificación de issues** | 12/20 | 19/20 | Estandarización estricta del formato y taxonomía de categorías. |
| **2. Razonamiento lógico** | 10/20 | 20/20 | Explicación paso a paso que evitó alucinaciones y facilitó la auditoría. |
| **3. Extracción estructurada** | 14/20 | 20/20 | Generación de un esquema JSON estricto sintácticamente válido con manejo de nulls. |
| **4. Revisión de código (rol)** | 13/20 | 19/20 | Análisis profesional enfocado en OWASP, respuesta estructurada en tabla y corrección lista para producción. |


## Plantillas reutilizables

### 1. Plantilla: Clasificación few-shot
```text
Actúa como {{rol}}.
Clasifica cada {{elemento}} siguiendo el formato de los ejemplos.
Categorías válidas: {{categorias}}.

Ejemplo 1: {{entrada_1}} -> {{salida_1}}
Ejemplo 2: {{entrada_2}} -> {{salida_2}}
Ejemplo 3: {{entrada_3}} -> {{salida_3}}

Elementos a clasificar: {{datos}}
```
### 2. Plantilla: Razonamiento paso a paso (Chain of Thought)
```text 
Eres un experto en {{dominio}}.
Analiza el siguiente problema o consulta siguiendo este proceso de razonamiento:

1. Identifica las variables clave y restricciones.
2. Analiza paso a paso la lógica requerida.
3. Verifica la coherencia de la respuesta antes de concluir.

Problema a resolver: {{problema}}

Proporciona tu respuesta con la siguiente estructura:
- **Razonamiento:** [Paso a paso]
- **Conclusión / Respuesta final:** [Resultado final]
```

### 3. Plantilla: Extracción a JSON estructurado

Eres un sistema automatizado de procesamiento de datos.
Extrae la información del siguiente texto y responde ÚNICAMENTE en formato JSON válido.

Esquema requerido:
```json
{{esquema_json}}
```

**Reglas:**
1. No incluyas texto explicativo, solo el objeto JSON.
2. Si un campo no está presente en el texto, asigna el valor null.

Texto de entrada: {{texto}}