# Hackatón by Paseo · 3 días · AI School

> Transcripción en Markdown de `Hackathon By Paseo.pdf` (20 láminas). Octubre de 2026 · On Business.

## Menú de contenido

| # | Sección | Temas |
|---|---|---|
| 01 | El reto | Resumen del reto · Por qué este caso |
| 02 | El caso de uso | Problema y usuarios · Los 4 agentes · Recorrido del usuario |
| 03 | Alcance y datos | Alcance del MVP · Datos del reto · Escenarios sembrados · Kit de insumos |
| 04 | Solución técnica | Arquitectura · Stack · Backend · API y capa semántica · Frontend y UX · Pantallas · IA responsable |
| 05 | Metodología y agenda | Roles del equipo · Construcción por capas · Agenda · Reglas y definición de terminado |
| 06 | Evaluación | Entregables · Criterios · Pruebas del jurado |
| 07 | Comercialización | Hoja de ruta · Modelo de negocio y PI · Fuentes |

*(Las secciones 05–07 aparecen en el menú pero no tienen láminas en este PDF.)*

---

## 01 · El reto

### En 3 días, cada equipo construye Centinela

Un sistema de agentes de IA que **vigila los datos del negocio**, detecta problemas antes de que cuesten dinero, explica por qué pasan y propone (o ejecuta, con aprobación humana) la acción para corregirlos.

Es el paso de los tableros que alguien tiene que mirar a una operación que se vigila sola. Todos los equipos usan el mismo dataset sintético, con problemas sembrados que el jurado usa para probar cada solución.

- **3** días presenciales en la sede de On Business
- **4–5** personas por equipo: negocio, datos, backend y frontend
- **6** escenarios por descubrir: 5 anunciados y 1 oculto

**Resultado esperado:** MVP funcionando · Demo de 5 minutos · Pitch de negocio de 3 minutos

---

## 02 · El caso de uso

### Los 4 agentes: tres leen, solo uno actúa y únicamente tras aprobación

Flujo: **Vigía → Analista → Estratega → [Aprobación humana] → Ejecutor**

| Agente | Qué hace | Ejemplo |
|---|---|---|
| **Vigía** | Revisa los KPIs y detecta anomalías o tendencias contra reglas y estadística | "El margen de la línea Hogar cayó 6 puntos en 3 semanas" |
| **Analista** | Investiga la causa raíz cruzando datos y consultando las políticas | "La causa: el proveedor X subió el costo de 4 SKU" |
| **Estratega** | Propone 1–3 acciones con impacto en pesos y nivel de confianza | "Ajustar precio 3% en 4 SKU recupera $42 M/mes" |
| **Ejecutor** | Ejecuta la acción aprobada con herramientas permitidas y deja registro | Correo en borrador, tarea, orden en borrador |

**Orquestador:** coordina a los agentes, guarda el estado de cada alerta y detiene el flujo antes de toda acción con efecto externo.

### El recorrido del usuario: del aviso a la decisión en minutos

1. **Abre la bandeja** — Ve sus decisiones ordenadas por pesos en riesgo.
2. **Abre una alerta** — Qué pasó, por qué (con evidencia), qué se propone y cuánto vale.
3. **Pregunta** — Profundiza en lenguaje natural: "¿qué otros clientes compran esos SKU?"
4. **Decide** — Aprueba, edita o rechaza. Si rechaza, explica por qué y Centinela aprende.
5. **Queda registrado** — El Ejecutor actúa y todo queda en la bitácora de auditoría.

**Principio:** el usuario no busca el problema; el problema llega a él, explicado y con una propuesta lista para aprobar.

---

## 03 · Alcance y datos

*Qué debe funcionar y con qué datos se prueba.*

### Alcance del MVP

**Base (obligatorio):**
- Vigía detecta al menos 3 de los 5 escenarios
- Analista explica la causa con evidencia
- Bandeja con aprobar / rechazar
- Chat para preguntar sobre los datos
- Bitácora de cada decisión

**Fuera de alcance:** conexión a sistemas reales de clientes, autenticación empresarial completa y despliegue productivo (van en el pitch como hoja de ruta).

### Datos del reto: 12 meses de una distribuidora ficticia

- **269.595** filas en 15 tablas
- **12** meses: oct 2025 – sep 2026
- **≈ $23.500 M** en ventas netas (COP)
- **CSV + SQL** — PostgreSQL listo para usar

| Tabla | Contenido | Filas |
|---|---|---|
| clientes | Segmento, ciudad, cupo, plazo | 500 |
| vendedores | Fuerza de ventas por región | 25 |
| productos | SKU, línea, proveedor, clase ABC | 200 |
| proveedores | Lead time y país | 40 |
| lista_precios · costos_proveedor | Precios y costos con vigencias | 2.804 |
| ordenes_compra | OC a proveedores, retrasos | 3.802 |
| pedidos | Encabezado, canal y estado | 20.013 |
| pedidos_detalle | Cantidad, precio, descuento, costo | 60.103 |
| facturas · pagos | Cartera: vencimientos y pagos | 36.095 |
| inventario_diario | Existencias por SKU, bodega y día | 146.000 |
| politicas/ (PDF) | Crédito, descuentos, inventario (RAG) | 3 |

### Kit de insumos: todo lo necesario para empezar en 5 minutos

`Centinela_Kit_Equipos.zip`

| Ruta | Contenido |
|---|---|
| `datos/csv/` | 15 tablas en CSV (UTF-8) |
| `datos/sql/01_esquema.sql` | Tablas en PostgreSQL |
| `datos/sql/02_carga.sql` | Carga de los CSV |
| `datos/sql/03_capa_semantica.sql` | 7 vistas de métricas |
| `datos/metricas.yaml` | Definición única de KPIs |
| `politicas/` | 3 políticas en PDF |
| `diccionario_de_datos.xlsx` | Campos, tipos y ejemplos |
| `evaluaciones/` | Plantilla de casos de prueba |
| `generador/` | Datasets alternativos |
| `README.md` | Guía de inicio |

**Puesta en marcha:**

```bash
createdb centinela
cd datos
psql -d centinela -f sql/01_esquema.sql
psql -d centinela -f sql/02_carga.sql
psql -d centinela -f sql/03_capa_semantica.sql
```

---

## 04 · Solución técnica

*Arquitectura, stack, backend, frontend e IA responsable.*

### Arquitectura de referencia

Capas, de arriba hacia abajo:

1. **Frontend · Next.js** — Bandeja de decisiones · Detalle · Chat · Bitácora · Configuración
2. **API · FastAPI** — REST + streaming (SSE) · reloj simulado · roles y permisos
3. **Orquestador de agentes · LangGraph** — Vigía → Analista → Estratega → ◆ aprobación → Ejecutor
4. **Herramientas · servidores MCP** — SQL de solo lectura sobre vistas de métricas · búsqueda en políticas (RAG) · acciones en borrador o sandbox
5. **Datos** — PostgreSQL (dataset + capa semántica) · pgvector (políticas) · bitácora inmutable

Transversales:
- **Modelos de lenguaje:** Claude u otro, vía API. Uno grande para razonar, uno rápido para clasificar.
- **Observabilidad:** Langfuse (trazas) · promptfoo (pruebas)

> Los agentes leen con herramientas controladas; solo el Ejecutor actúa, y tras aprobación humana.

### Stack recomendado (los equipos pueden cambiar piezas si lo justifican)

| Capa | Recomendado | Alternativas válidas |
|---|---|---|
| Modelos de lenguaje | Claude: un modelo grande para razonar, uno rápido para clasificar | GPT, Gemini; modelos abiertos vía Ollama |
| Orquestación de agentes | LangGraph (estado y pausa para aprobación humana) | Claude Agent SDK, OpenAI Agents SDK, ADK, CrewAI |
| Herramientas | Servidores MCP: SQL de solo lectura, políticas, acciones | Function calling nativo del modelo |
| Datos | PostgreSQL + pgvector | DuckDB para análisis local |
| Detección de anomalías | Reglas de negocio + estadística (z-score, tendencia) | scikit-learn (Isolation Forest), Prophet |
| Backend | Python + FastAPI + Pydantic | Node.js + Hono / NestJS |
| Frontend | Next.js + React + Tailwind + shadcn/ui + Recharts | Streamlit solo para prototipo (baja la nota de UX) |
| Observabilidad y evaluaciones | Langfuse + promptfoo | LangSmith, Arize Phoenix, Ragas |
| Despliegue | Docker Compose; demo en Cloud Run, Render o Vercel | Local con túnel seguro |

**Regla de oro:** los números los calcula SQL o Python; el modelo razona, explica y redacta, nunca inventa cifras.

### Backend: cifras correctas, acciones controladas y costo predecible

| Tema | Descripción |
|---|---|
| **Capa semántica** | KPIs definidos una vez como vistas SQL; los agentes no consultan tablas crudas. |
| **Reloj simulado** | Endpoint para avanzar día a día: el jurado adelanta la operación y ve aparecer las alertas. |
| **Ciclo de vida** | Nueva → en análisis → propuesta → aprobada o rechazada → ejecutada, con estado persistido. |
| **Aprobación humana** | El flujo se detiene antes de cualquier acción externa; sin aprobación no hay acción. |
| **Acciones seguras** | Lista cerrada de herramientas, idempotentes, en borrador o sandbox. |
| **Priorización** | Alertas ordenadas por pesos en riesgo; una misma causa no genera 10 alertas. |
| **Costo y latencia** | Modelo rápido para lo simple, caché, tope de tokens y costo registrado por alerta. |
| **Robustez** | Reintentos, timeouts y "no tengo evidencia suficiente" como respuesta válida. |

### API y capa semántica incluidas en el kit

**API mínima**

| Método | Ruta | Para qué |
|---|---|---|
| POST | `/simulacion/avanzar?dias=1` | Mueve el reloj y dispara al Vigía |
| GET | `/alertas?estado=propuesta` | Bandeja de decisiones |
| GET | `/alertas/{id}` | Causa, evidencia y acciones |
| POST | `/alertas/{id}/decision` | Aprobar, editar o rechazar + motivo |
| POST | `/chat` | Preguntas en lenguaje natural (streaming) |
| GET | `/bitacora` | Auditoría de decisiones y acciones |

**Vistas de métricas (PostgreSQL)**

| Vista | Contenido |
|---|---|
| `v_ventas` | Ventas netas con costo y margen por línea de pedido |
| `v_margen_semanal_linea` | Margen semanal por línea de producto |
| `v_cartera_cliente` | Saldo abierto, vencido y días de pago por cliente |
| `v_dias_pago_mensual` | Tendencia de días de pago por mes de factura |
| `v_cobertura_inventario` | Cobertura en días y unidades pendientes por SKU y bodega |
| `v_descuentos_fuera_politica` | Líneas sobre el tope y descuento en exceso |
| `v_actividad_cliente` | Días sin comprar frente al intervalo habitual |

`fecha_corte()` devuelve el último día del dataset; con el reloj simulado se reemplaza por el día simulado.

### Frontend y UX: una bandeja de decisiones, no un tablero

Ejemplo de tarjeta de alerta (mockup):

> **Alta** · Margen · línea Hogar — Confianza alta · hoy 7:00 a. m.
> **El margen de la línea Hogar cayó 6 puntos en 3 semanas**
> **$42 M/mes recuperables**
> **POR QUÉ:** El proveedor X subió el costo de 4 SKU y el precio de venta no se ajustó.
> **PROPUESTA:** Ajustar el precio 3% en esos 4 SKU y renegociar con el proveedor X.
> [Aprobar] [Editar] [Rechazar] · Ver cómo llegué aquí ›

Principios:
- **Valor en 30 segundos:** al entrar, el dinero en riesgo hoy y las 3 decisiones clave.
- **Explicación en 3 niveles:** una frase → evidencia → "cómo llegué aquí" con las consultas.
- **El humano decide:** aprobar, editar o rechazar a un clic; rechazar pide motivo.
- **Confianza visible:** supuestos y nivel de confianza; si duda, lo dice.
- **Agentes a la vista:** se ve el paso en curso mientras analizan.
- **Lenguaje de negocio:** pesos colombianos, fechas claras, cero jerga.
- **Accesible y responsive:** severidad no solo por color; teclado; celular.

### Pantallas del MVP

| Pantalla | Para quién | Qué muestra |
|---|---|---|
| Bandeja de decisiones | Gerente, líderes de proceso | Alertas ordenadas por pesos en riesgo, con urgencia, confianza y acciones |
| Detalle de alerta | Líderes de proceso | Qué pasó, causa con evidencia, acciones propuestas con impacto y aprobación |
| Chat anclado | Todos | Preguntas sobre la alerta o los datos; respuestas con cifra, gráfico y fuente |
| Bitácora | Auditoría, gerencia | Quién aprobó qué, cuándo, qué ejecutó el agente y con qué resultado |
| Configuración | Analista | KPIs vigilados, umbrales, responsables y nivel de autonomía por tipo de acción |

### IA responsable: un agente que actúa solo se compra si se puede controlar

| Riesgo | Control mínimo exigido |
|---|---|
| Cifras inventadas | Todo número viene de una consulta registrada; la respuesta enlaza la consulta que lo produjo |
| Acción no deseada | Aprobación humana antes de toda acción externa; lista cerrada de herramientas; modo borrador |
| Inyección de instrucciones | Datos y documentos son datos, nunca órdenes; el jurado prueba un texto malicioso en una política |
| Acceso indebido a datos | Usuario de base de datos de solo lectura; roles por área |
| Datos personales | Enmascarar datos sensibles antes de enviarlos al modelo; Ley 1581 de protección de datos |
| Falta de trazabilidad | Bitácora inmutable: alerta, evidencia, propuesta, decisión, acción y resultado |
| Degradación del agente | Set de evaluación con los escenarios sembrados, ejecutado en cada cambio importante |

**Niveles de autonomía por tipo de acción:** Informa → **Propone** → Ejecuta. En el hackatón todo queda en "Propone"; la autonomía plena se gana en producción con historial de aciertos.

---

## Éxitos…

¡Empieza ya a demostrar tus capacidades!
