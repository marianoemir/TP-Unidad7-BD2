# Bitácora personal — Andrés

Registro de lo que hice y por qué, para poder volver a consultarlo. Al final se unifica en `BITACORA.md`.

## Mi parte

Según la división de trabajo (`DIVISION_DE_TRABAJO_UNIDAD7.md`):
- **Consigna 5:** Consultas equivalentes en PostgreSQL (apartados b y c con `JOIN`), script `consultas_postgresql.sql`, tabla de verificación comparando PostgreSQL vs. MongoDB y explicación de diferencias.
- **H1b (Opcional):** Creación e incorporación de los pedidos de prueba mediante `sp_crear_pedido` para enriquecer la variedad de productos y estados en las consultas.

## Mi entorno (H0)

| Servidor y versión | Conteos de `usuario`, `producto`, `pedido` | Problemas y cómo se resolvieron |
|---|---|---|
| PostgreSQL 16 (Local) | 6 / 16 / 8 (6 originales + 2 pedidos de prueba con IDs 7 y 8) | Se creó la base limpia `foodstore7` ejecutando `schema.sql`, `objects.sql`, `data.sql` y `views.sql` sin errores. |

## Interacciones con OpenCode

| Prompt enviado a la IA | Respuesta relevante recibida (resumida) | Validación o corrección humana aplicada |
|---|---|---|
| Consulta sobre la creación de la base limpia `foodstore7` y el orden de los scripts SQL | Guía de ejecución por comandos de terminal utilizando `psql` para crear la base y cargar el esquema y datos. | Se ejecutaron los scripts en la terminal psql sin inconvenientes. |
| Solicitud para generar la tabla de verificación y consultas de la Consigna 5 | Generación de consultas con filtros de vigencia (`eliminado = FALSE`) e `IN (1..8)`, más la tabla comparativa. | Se revisó que no incluyera el pedido ID 4 (dado de baja) y se corrigió una autoconversión de formato en Excel para la columna de IDs. |

## Revisión cruzada que hice

| Qué revisé | En qué servidor lo ejecuté | Qué encontré |
|---|---|---|
| Coincidencia de IDs entre PostgreSQL y MongoDB (`evidencias/mongo_migracion_y_consultas.txt`) | PostgreSQL (`foodstore7`) | Los resultados coinciden al 100%: 3 pedidos para estado `PENDIENTE` (IDs `3, 5, 7`) y 3 pedidos para `producto_id = 1` (IDs `1, 7, 8`). |

## Mediciones (valores observados, no estimados)

| Dato | Valor | Cómo se midió |
|---|---|---|
| Pedidos para estado `PENDIENTE` | 3 pedidos (IDs: 3, 5, 7) | Consulta SQL a `pedido` con `WHERE estado = 'PENDIENTE'` |
| Pedidos con `producto_id = 1` | 3 pedidos (IDs: 1, 7, 8) | Consulta SQL con `JOIN` entre `pedido` y `detalle_pedido` |

## Problemas encontrados y cómo se resolvieron

| Fecha | Problema | Causa | Solución |
|---|---|---|---|
| 30/10/2026 | Formato alterado al exportar la tabla a Excel (`3,5,2007` en lugar de `3, 5, 7`). | Excel interpretó la lista de números separados por comas como formato de fecha/número de serie. | Se configuró el formato de celda como Texto plano en Excel para mantener la cadena literal. |

## Decisiones tomadas

| Decisión | Alternativas | Por qué |
|---|---|---|
| Crear y ejecutar los pedidos de prueba opcionales (`pedidos_de_prueba.sql`). | Trabajar únicamente con los 5 pedidos base del `data.sql`. | Para enriquecer la consulta c) por producto, logrando que el `producto_id = 1` aparezca en múltiples pedidos (`[1, 7, 8]`) en lugar de en uno solo. |
| Usar la base de datos `foodstore7`. | Sobrescribir `foodstore`. | Evita conflictos o sobreescrituras en entornos relacionales previos. |