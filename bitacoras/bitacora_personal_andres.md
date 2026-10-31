# Bitácora personal — Andrés

Registro de lo que hice y por qué, para poder volver a consultarlo. Al final se unifica en `BITACORA.md`.

> **Nota de corrección (2026-10-31, hecha por Mariano al unificar):** la versión original de esta bitácora afirmaba que los resultados de
> PostgreSQL y MongoDB coincidían "al 100%" con 3 pedidos `PENDIENTE` (ids 3, 5, 7). Eso no estaba respaldado por la evidencia: en ese momento
> MongoDB tenía solo 5 documentos (los de `data.sql`) y la evidencia de PostgreSQL mostraba 4 pedidos `PENDIENTE` (3, 5, 7, 8). Las filas
> corregidas llevan la marca *(corregido)*. El resto se conserva como lo registró Andrés.

## Mi parte

Según la división de trabajo (`DIVISION_DE_TRABAJO.md`):
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
| Solicitud para generar la tabla de verificación y consultas de la Consigna 5 | Generación de consultas con filtros de vigencia (`eliminado = FALSE`) e `IN (1..8)`, más la tabla comparativa. | Se revisó que no incluyera el pedido ID 4 (dado de baja) y se corrigió una autoconversión de formato en Excel para la columna de IDs. *(corregido)* Los números de la tabla (3 y 3, ids 3, 5, 7) **no se contrastaron** con las salidas guardadas: eran incorrectos y se rehízo con los resultados reales de ambos motores (ver "Problemas encontrados"). Las consultas SQL, en cambio, eran correctas. |

## Revisión cruzada que hice

| Qué revisé | En qué servidor lo ejecuté | Qué encontré |
|---|---|---|
| *(corregido)* Coincidencia de IDs entre PostgreSQL y MongoDB (`evidencias/mongo_migracion_y_consultas.txt`) | PostgreSQL (`foodstore7`) | En la primera comparación **no coincidían**: PostgreSQL devolvía 4 pedidos `PENDIENTE` (3, 5, 7, 8) y 3 con `producto_id = 1` (1, 7, 8), y MongoDB devolvía 2 (3, 5) y 1 (1), porque los pedidos de prueba 7 y 8 todavía no estaban migrados. Tras repetir la migración (7 documentos), coinciden: 4 y 4 en b), 3 y 3 en c), con los mismos ids. |

## Mediciones (valores observados, no estimados)

| Dato | Valor | Cómo se midió |
|---|---|---|
| *(corregido)* Pedidos para estado `PENDIENTE` | 4 pedidos (IDs: 3, 5, 7, 8) en PostgreSQL; 4 (3, 5, 7, 8) en MongoDB tras migrar los pedidos de prueba | `evidencias/postgres_b.txt` y `evidencias/mongo_migracion_y_consultas.txt` |
| Pedidos con `producto_id = 1` | 3 pedidos (IDs: 1, 7, 8) en PostgreSQL; 3 (1, 7, 8) en MongoDB tras migrar los pedidos de prueba | `evidencias/postgres_c.txt` (con `JOIN` entre `pedido` y `detalle_pedido`) y `evidencias/mongo_migracion_y_consultas.txt` |
| Totales de los pedidos de prueba | Pedido 7: 4200.00; pedido 8: 11700.00 | `evidencias/postgres_b.txt` |

## Problemas encontrados y cómo se resolvieron

| Fecha | Problema | Causa | Solución |
|---|---|---|---|
| 30/10/2026 | Formato alterado al exportar la tabla a Excel (`3,5,2007` en lugar de `3, 5, 7`). | Excel interpretó la lista de números separados por comas como formato de fecha/número de serie. | Se configuró el formato de celda como Texto plano en Excel para mantener la cadena literal. |
| 31/10/2026 *(agregado)* | La tabla de verificación decía 3 y 3 pedidos `PENDIENTE` (ids 3, 5, 7) y que coincidían, pero la evidencia de PostgreSQL mostraba 4 (3, 5, 7, 8) y MongoDB solo tenía los 5 pedidos de `data.sql`. | La tabla se generó sin contrastarla con las salidas guardadas, y MongoDB todavía no tenía migrados los pedidos de prueba 7 y 8. | Mariano ejecutó `pedidos_de_prueba.sql` en su base, agregó los ids 7 y 8 al export y repitió la migración (7 documentos). Se rehízo la tabla en `evidencias/tabla_verificacion.md` con los números reales de ambos motores y se borraron `tabla_verificacion_Andrés.md` y `.xlsx`. |
| 31/10/2026 *(agregado)* | Archivo temporal `~$tabla_verificacion_Andrés.xlsx` y capturas en `evidencias/Capturas Mongo Compass/` (con espacios) dentro del repo; el informe enlazaba `evidencias/compass_*.png`. | Excel crea el temporal mientras el archivo está abierto; las capturas se guardaron en una subcarpeta. | Se borró el temporal y las capturas pasan a `evidencias/` con los nombres que usa el informe. |

## Decisiones tomadas

| Decisión | Alternativas | Por qué |
|---|---|---|
| Crear y ejecutar los pedidos de prueba opcionales (`pedidos_de_prueba.sql`). | Trabajar únicamente con los 5 pedidos base del `data.sql`. | Para enriquecer la consulta c) por producto, logrando que el `producto_id = 1` aparezca en múltiples pedidos (`[1, 7, 8]`) en lugar de en uno solo. |
| Usar la base de datos `foodstore7`. | Sobrescribir `foodstore`. | Evita conflictos o sobreescrituras en entornos relacionales previos. |
| *(agregado)* Limitar las consultas a los ids migrados (`IN (1, 2, 3, 5, 6, 7, 8)`) y a registros vigentes (`eliminado = FALSE` en cada tabla del `JOIN`). | Consultar toda la tabla. | Para que la comparación sea con el mismo conjunto de pedidos que en MongoDB y no cuente el pedido 4, dado de baja. |

## Pendiente

- Confirmar que la tabla corregida (`evidencias/tabla_verificacion.md`) refleja lo que se ejecutó, y dejar solo un `informe_u7.md` en la raíz.
- Revisar el PDF final contra las evidencias y las 5 páginas, como indica la revisión cruzada.
