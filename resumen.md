# Resumen del reto: Centinela (Hackatón by Paseo)

> Resumen de [Hackathon By Paseo.md](Kit_Equipos/Hackathon%20By%20Paseo.md), con enlaces a los archivos del kit. Guía de inicio original: [Kit_Equipos/README.md](Kit_Equipos/README.md).

## La idea en una frase

En 3 días, cada equipo de 4–5 personas construye **Centinela**: un sistema de agentes de IA que **vigila los datos de una empresa**, detecta problemas antes de que cuesten dinero, explica por qué pasan y **propone una acción**. Una persona aprueba o rechaza esa acción.

Pasa de "un tablero que alguien tiene que mirar" a "una operación que se vigila sola".

## La empresa (ficticia)

**Distribuidora Andina S.A.S.** distribuye productos de consumo masivo. Tiene 2 bodegas (Medellín y Bogotá), 500 clientes, 200 productos y 25 vendedores. El dataset cubre 12 meses (oct 2025 – sep 2026) y suma unas 270 mil filas y ≈ $23.500 M en ventas (COP).

📂 Datos en [Kit_Equipos/datos/csv/](Kit_Equipos/datos/csv/) (15 tablas). Campos, tipos y ejemplos de cada tabla en [diccionario_de_datos.md](Kit_Equipos/diccionario_de_datos.md).

## Qué hay que encontrar: 6 escenarios sembrados

El jurado escondió problemas en los datos. Centinela debe descubrir **qué clientes, productos o vendedores** están afectados.

| # | Problema | Vista SQL que lo mide | Umbral de alerta ([metricas.yaml](Kit_Equipos/datos/metricas.yaml)) | Política |
|---|---|---|---|---|
| 1 | **Margen que se erosiona** | `v_margen_semanal_linea` | Caída > 3 puntos vs. las 8 semanas previas, o margen bajo el mínimo de la línea | [Inventario y precios](Kit_Equipos/politicas/Politica_Inventario_y_Precios.md) §4 |
| 2 | **Mora creciente** | `v_cartera_cliente`, `v_dias_pago_mensual` | Más de 15 días vencido, saldo > cupo, o días de pago +50% vs. su histórico | [Crédito y cartera](Kit_Equipos/politicas/Politica_Credito_y_Cartera.md) §4–5 |
| 3 | **Quiebre de stock inminente** | `v_cobertura_inventario` | < 10 días en clase A; crítico si < 5 días con pedidos pendientes | [Inventario y precios](Kit_Equipos/politicas/Politica_Inventario_y_Precios.md) §2 |
| 4 | **Descuentos fuera de política** | `v_descuentos_fuera_politica` | Cualquier línea sobre el tope sin aprobación especial | [Descuentos comerciales](Kit_Equipos/politicas/Politica_Descuentos_Comerciales.md) §2–3 |
| 5 | **Cliente que se va** | `v_actividad_cliente` | > 3 veces su intervalo habitual entre pedidos (clientes con 10 o más pedidos) | — |
| 6 | **Oculto**: se revela al cierre | ? | ? | ? |

**Mínimo para aprobar:** detectar 3 de los 5 anunciados.

Tablas de referencia que salen de las políticas:
- [ref_topes_descuento.csv](Kit_Equipos/datos/csv/ref_topes_descuento.csv)
- [ref_margen_minimo_linea.csv](Kit_Equipos/datos/csv/ref_margen_minimo_linea.csv)

## Los 4 agentes

```
Vigía → Analista → Estratega → [👤 Aprobación humana] → Ejecutor
 detecta   explica     propone                          actúa
```

- **Vigía:** revisa los KPIs y detecta anomalías. *"El margen de Hogar cayó 6 puntos en 3 semanas."*
- **Analista:** busca la causa en los datos y en las [políticas](Kit_Equipos/politicas/). *"El proveedor X subió el costo de 4 SKU."*
- **Estratega:** propone de 1 a 3 acciones con su valor en pesos y su nivel de confianza. *"Subir el precio 3% recupera $42 M al mes."*
- **Ejecutor:** el **único** que actúa, y solo después de la aprobación. Todo lo deja en borrador (correo, tarea, orden).

Un **orquestador** coordina el flujo y lo detiene antes de cualquier acción externa.

## Qué hay que entregar

1. **MVP funcionando** con 5 piezas obligatorias:
   - el Vigía detecta escenarios
   - el Analista explica la causa con evidencia
   - **bandeja** de decisiones con aprobar/rechazar
   - **chat** para preguntar sobre los datos
   - **bitácora** de cada decisión
2. **Demo de 5 minutos.**
3. **Pitch de negocio de 3 minutos.**

Quedan **fuera de alcance:** la conexión a sistemas reales, el login empresarial y el despliegue en producción. Van en el pitch como hoja de ruta.

## Experiencia del usuario

El problema llega al usuario; el usuario no tiene que buscarlo. El recorrido es:

**Bandeja** ordenada por dinero en riesgo → **abre una alerta** (qué pasó, por qué, qué se propone y cuánto vale) → **pregunta** en el chat → **decide** (aprueba, edita o rechaza con motivo) → queda **registrado**.

| Pantalla | Para quién |
|---|---|
| Bandeja de decisiones | Gerente, líderes de proceso |
| Detalle de alerta | Líderes de proceso |
| Chat anclado | Todos |
| Bitácora | Auditoría, gerencia |
| Configuración | Analista |

Principios de diseño:
- ver el valor en 30 segundos
- explicar en 3 niveles: frase → evidencia → consultas
- mostrar la confianza
- lenguaje de negocio, sin jerga
- accesible y usable en celular

## Stack sugerido

| Capa | Recomendado |
|---|---|
| Modelos | Claude: uno grande para razonar y uno rápido para clasificar |
| Orquestación | LangGraph (permite pausar para la aprobación humana) |
| Herramientas | Servidores MCP: SQL de solo lectura, búsqueda en políticas (RAG), acciones |
| Datos | PostgreSQL + pgvector |
| Backend | Python + FastAPI |
| Frontend | Next.js + Tailwind + shadcn/ui (Streamlit baja la nota de UX) |
| Observabilidad | Langfuse + promptfoo |

## Lo que ya viene en el kit

| Archivo | Para qué sirve |
|---|---|
| [01_esquema.sql](Kit_Equipos/datos/sql/01_esquema.sql) | Crea las tablas en PostgreSQL (esquema `centinela`) |
| [02_carga.sql](Kit_Equipos/datos/sql/02_carga.sql) | Carga los CSV |
| [03_capa_semantica.sql](Kit_Equipos/datos/sql/03_capa_semantica.sql) | Crea las 7 vistas `v_*` y `fecha_corte()`. Los agentes consultan **solo estas vistas** |
| [metricas.yaml](Kit_Equipos/datos/metricas.yaml) | Fórmula y umbral de alerta de cada KPI (la "fuente de verdad" del Vigía) |
| [politicas/](Kit_Equipos/politicas/) | 3 políticas en PDF + `.md`, para RAG |
| [diccionario_de_datos.md](Kit_Equipos/diccionario_de_datos.md) | Campos y ejemplos de cada tabla (el original es `.xlsx`) |
| [plantilla_casos_prueba.csv](Kit_Equipos/evaluaciones/plantilla_casos_prueba.csv) | Formato del set de evaluación. Tipos de caso: `pregunta`, `alerta`, `seguridad` |
| [generar_dataset.py](Kit_Equipos/generador/generar_dataset.py) | Genera otro dataset con `SEMILLA=<n>`, para comprobar que la solución no está "aprendida de memoria" |

**API mínima sugerida:**
- `POST /simulacion/avanzar?dias=1`: el jurado adelanta el reloj y las alertas deben ir apareciendo.
- `GET /alertas`, `GET /alertas/{id}`, `POST /alertas/{id}/decision`
- `POST /chat`
- `GET /bitacora`

**Reloj simulado:** todas las consultas se filtran con `fecha <= día simulado` (por defecto `centinela.fecha_corte()`).

## Reglas clave (el jurado las va a probar)

- 🔢 **Regla de oro:** los números los calcula SQL o Python. El modelo **nunca inventa cifras** y cada número enlaza la consulta que lo produjo.
- 🛡️ **Inyección de instrucciones:** el jurado meterá un **texto malicioso en una política**. Los documentos son datos, nunca órdenes. Ver el caso `EJ-03` en la [plantilla de evaluación](Kit_Equipos/evaluaciones/plantilla_casos_prueba.csv).
- 🔒 La base de datos se usa con un usuario de solo lectura y los datos personales se enmascaran (Ley 1581).
- 📋 La bitácora es inmutable y debe haber un set de evaluación con los escenarios.
- En el hackatón, la autonomía se queda en **"Propone"** y nunca llega a "Ejecuta" sin una persona de por medio.

## Por dónde empezar

1. Levantar PostgreSQL ([pasos en el README del kit](Kit_Equipos/README.md#puesta-en-marcha-5-minutos)):
   ```bash
   createdb centinela
   cd Kit_Equipos/datos
   psql -d centinela -f sql/01_esquema.sql
   psql -d centinela -f sql/02_carga.sql
   psql -d centinela -f sql/03_capa_semantica.sql
   ```
2. Consultar las 7 vistas con los umbrales de [metricas.yaml](Kit_Equipos/datos/metricas.yaml) y ver qué escenarios saltan a la vista: es el trabajo del Vigía.
3. Hacer el flujo de punta a punta para **un** escenario (Vigía → Analista → bandeja → aprobar → bitácora) y después ampliar.
4. Ir llenando la [plantilla de evaluación](Kit_Equipos/evaluaciones/plantilla_casos_prueba.csv) con casos reales.
