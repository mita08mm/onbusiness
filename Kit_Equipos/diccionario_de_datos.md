# Centinela · diccionario de datos

> Generado desde `diccionario_de_datos.xlsx` (se conserva el original).

_Distribuidora Andina S.A.S. (empresa ficticia) · 12 meses: 2025-10-01 a 2026-09-30 · valores en pesos colombianos (COP)_

## Tablas

| Tabla | Contenido | Filas | Columnas | Archivo |
|---|---|---|---|---|
| vendedores | Fuerza de ventas | 25 | 3 | datos/csv/vendedores.csv |
| clientes | Clientes de la distribuidora | 500 | 9 | datos/csv/clientes.csv |
| proveedores | Proveedores | 40 | 4 | datos/csv/proveedores.csv |
| productos | Catálogo de productos | 200 | 6 | datos/csv/productos.csv |
| bodegas | Centros de distribución | 2 | 3 | datos/csv/bodegas.csv |
| lista_precios | Precio de lista vigente por SKU | 400 | 3 | datos/csv/lista_precios.csv |
| costos_proveedor | Costo de compra vigente por SKU | 2.404 | 4 | datos/csv/costos_proveedor.csv |
| ordenes_compra | Órdenes de compra a proveedores | 3.802 | 10 | datos/csv/ordenes_compra.csv |
| pedidos | Encabezado de pedidos de venta | 20.013 | 7 | datos/csv/pedidos.csv |
| pedidos_detalle | Líneas de pedido | 60.103 | 10 | datos/csv/pedidos_detalle.csv |
| facturas | Facturas electrónicas | 19.085 | 8 | datos/csv/facturas.csv |
| pagos | Pagos recibidos | 17.010 | 5 | datos/csv/pagos.csv |
| inventario_diario | Existencias diarias por SKU y bodega | 146.000 | 7 | datos/csv/inventario_diario.csv |
| ref_topes_descuento | Topes de descuento por segmento (de la política) | 4 | 2 | datos/csv/ref_topes_descuento.csv |
| ref_margen_minimo_linea | Margen mínimo por línea (de la política) | 7 | 2 | datos/csv/ref_margen_minimo_linea.csv |

## vendedores · Fuerza de ventas

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| vendedor_id | texto | Identificador del vendedor | V01 |
| nombre | texto | Nombre (ficticio) | Andrés Gómez |
| region | texto | Región que atiende | Caribe |

## clientes · Clientes de la distribuidora

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| cliente_id | texto | Identificador del cliente | C0001 |
| nombre | texto | Razón social (ficticia) | Mercados La Montaña Express S.A.S. |
| segmento | texto | Grandes superficies, Mayoristas, Minoristas o Institucional | Minoristas |
| ciudad | texto | Ciudad del cliente | Cartagena |
| region | texto | Región comercial | Caribe |
| vendedor_id | texto | Vendedor asignado | V17 |
| plazo_dias | número | Plazo de pago en días | 30 |
| cupo_credito | número | Cupo de crédito en COP | 5.000.000 |
| fecha_alta | fecha | Fecha de creación del cliente | 2019-04-25 |

## proveedores · Proveedores

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| proveedor_id | texto | Identificador | PR01 |
| nombre | texto | Razón social (ficticia) | Industrias Plásticas del Valle |
| lead_time_dias | número | Días entre la orden de compra y la entrega | 6 |
| pais | texto | País de origen | Colombia |

## productos · Catálogo de productos

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| sku | texto | Código del producto | P0001 |
| nombre | texto | Descripción | Juego de ollas x3 (HOG-01) |
| linea | texto | Línea de producto | Hogar |
| proveedor_id | texto | Proveedor principal | PR08 |
| clase_abc | texto | Clasificación por ventas (A = 80% del valor) | A |
| unidad | texto | Unidad de venta | UND |

## bodegas · Centros de distribución

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| bodega_id | texto | Identificador | BOD-MDE |
| nombre | texto | Nombre | Bodega Medellín (Itagüí) |
| ciudad | texto | Ubicación | Medellín |

## lista_precios · Precio de lista vigente por SKU

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| sku | texto | Producto | P0001 |
| fecha_vigencia | fecha | Desde cuándo rige | 2025-10-01 |
| precio_lista | número | Precio de lista antes de descuento, COP | 42.350 |

## costos_proveedor · Costo de compra vigente por SKU

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| sku | texto | Producto | P0001 |
| proveedor_id | texto | Proveedor | PR08 |
| fecha_vigencia | fecha | Desde cuándo rige | 2025-10-01 |
| costo_unitario | número | Costo unitario de compra, COP | 30.050 |

## ordenes_compra · Órdenes de compra a proveedores

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| oc_id | texto | Identificador | OC-000001 |
| proveedor_id | texto | Proveedor | PR08 |
| sku | texto | Producto | P0001 |
| bodega_id | texto | Bodega destino | BOD-MDE |
| fecha_oc | fecha | Fecha de la orden | 2025-10-01 |
| fecha_esperada | fecha | Fecha de entrega comprometida | 2025-10-06 |
| fecha_recibida | fecha | Fecha real de recepción (vacía si no ha llegado) | 2025-10-06 |
| cantidad | número | Unidades | 486 |
| costo_unitario | número | Costo pactado, COP | 30.050 |
| estado | texto | Recibida, En tránsito o Retrasada | Recibida |

## pedidos · Encabezado de pedidos de venta

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| pedido_id | texto | Identificador | PED-000001 |
| fecha | fecha | Fecha del pedido | 2025-10-01 |
| cliente_id | texto | Cliente | C0019 |
| vendedor_id | texto | Vendedor | V17 |
| ciudad | texto | Ciudad de despacho | Cartagena |
| canal | texto | Asesor en campo, Televenta o Portal B2B | Asesor en campo |
| estado | texto | Facturado, Pendiente de despacho o Cancelado | Facturado |

## pedidos_detalle · Líneas de pedido

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| pedido_id | texto | Pedido | PED-000001 |
| linea_n | número | Número de línea | 1 |
| sku | texto | Producto | P0021 |
| cantidad | número | Unidades | 4 |
| precio_lista | número | Precio de lista vigente, COP | 26.040 |
| precio_unitario | número | Precio unitario aplicado antes de descuento, COP | 26.040 |
| descuento_pct | número | Descuento comercial % | 3.8 |
| aprobacion_especial | texto | S si Gerencia Comercial aprobó superar el tope | N |
| valor_neto | número | cantidad × precio_unitario × (1 − descuento), COP | 100.202 |
| costo_unitario | número | Costo vigente a la fecha del pedido, COP | 16.530 |

## facturas · Facturas electrónicas

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| factura_id | texto | Identificador | FE-000001 |
| pedido_id | texto | Pedido facturado | PED-000001 |
| cliente_id | texto | Cliente | C0019 |
| fecha_factura | fecha | Fecha de emisión | 2025-10-01 |
| fecha_vencimiento | fecha | Fecha límite de pago | 2025-10-31 |
| valor_neto | número | Valor antes de IVA, COP | 172.182 |
| iva | número | IVA 19%, COP | 32.715 |
| valor_total | número | Valor a pagar, COP | 204.897 |

## pagos · Pagos recibidos

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| pago_id | texto | Identificador | PG-000001 |
| factura_id | texto | Factura pagada | FE-000018 |
| fecha_pago | fecha | Fecha del pago | 2025-10-12 |
| valor | número | Valor pagado, COP | 393.625 |
| medio_pago | texto | Medio de pago | Transferencia |

## inventario_diario · Existencias diarias por SKU y bodega

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| fecha | fecha | Día | 2025-10-01 |
| bodega_id | texto | Bodega | BOD-BOG |
| sku | texto | Producto | P0001 |
| existencia_inicial | número | Unidades al inicio del día | 505 |
| entradas | número | Unidades recibidas de proveedores | 0 |
| salidas | número | Unidades despachadas | 8 |
| existencia_final | número | Unidades al cierre del día | 497 |

## ref_topes_descuento · Topes de descuento por segmento (de la política)

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| segmento | texto | Segmento | Grandes superficies |
| tope_descuento_pct | número | Descuento máximo sin aprobación especial | 18 |

## ref_margen_minimo_linea · Margen mínimo por línea (de la política)

| Campo | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| linea | texto | Línea | Hogar |
| margen_minimo_pct | número | Margen bruto mínimo % | 25 |
