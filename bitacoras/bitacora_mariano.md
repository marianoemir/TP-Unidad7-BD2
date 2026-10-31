# Bitácora personal — Mariano Chirino

Registro de lo que hice y por qué, para poder volver a consultarlo. Se unifica al final en `BITACORA.md`.

## Mi parte

Según `DIVISION_DE_TRABAJO.md`: **Parte 1 completa** (modelo documental, migración con `insertMany()`, dos reglas de integridad y justificación de lo embebido), **consulta 4** en MongoDB (a a d) con `consultas_mongodb.js`, y al final el **armado del informe PDF**, el `README.md` y la unificación de bitácoras. El armado del PDF lo tomé de Facundo para repartir mejor la carga.

Estado al día de hoy (2026-10-31): H0, H1, H2 y H3 hechos; migración repetida con los 7 pedidos (5 de `data.sql` y 2 de prueba de Andrés). Pendiente: informe PDF, `README.md` y unificación de bitácoras.

## Mi entorno (H0)

| Servidor y versión | Conteos de `usuario`, `producto`, `pedido` | Problemas y cómo se resolvieron |
|---|---|---|
| PostgreSQL 17.10 (`postgres -V`). MongoDB servidor 9.0.4 (`db.version()`). `mongosh` 2.14.0. Windows PowerShell 5.1 | **6 / 16 / 6** (`usuario` incluye 1 dado de baja; `producto` incluye 3 de prueba, entre ellos 1 dado de baja; `pedido` incluye el 4 dado de baja) | `psql --version` no devolvió salida; usé la ruta completa `C:\Program Files\PostgreSQL\17\bin\psql.exe`. Además, acentos rotos en la evidencia y fin de línea CRLF/LF en Git (ver "Problemas encontrados") |

Comprobación de H0 (`SELECT id, estado, eliminado FROM pedido ORDER BY id;`), guardada en `evidencias/pg_verificacion_h0.txt`:

| id | estado | eliminado |
|---|---|---|
| 1 | CONFIRMADO | false |
| 2 | TERMINADO | false |
| 3 | PENDIENTE | false |
| 4 | PENDIENTE | **true** |
| 5 | PENDIENTE | false |
| 6 | CANCELADO | false |

Lectura: los pedidos 1, 2, 3, 5 y 6 están vigentes y son los que se migran. El 4 está dado de baja a propósito en `data.sql` y no se migra. Los estados coinciden con los de `data.sql`.

Armado: `plantilla_food_store` con `schema.sql` → `objects.sql` → `data.sql`; `foodstore` creada con esa plantilla y `views.sql` ejecutado. No se corrió la carga masiva (200.000 pedidos) porque no hace falta en esta unidad y rompería la comparación de cantidades.

## Interacciones con la IA (Claude, claude.ai; no OpenCode)

| Prompt enviado a la IA | Respuesta relevante recibida (resumida) | Validación o corrección humana aplicada |
|---|---|---|
| Repo, consignas y división de trabajo; cómo empezar mi parte | Entregó `modelo/exportar_pedidos.sql` (genera el JSON desde PostgreSQL), `consultas_mongodb.js` (crea colección con validador, migra, verifica integridad y corre a–d) y un borrador de `modelo/esquema_documental.md` | La IA no pudo ejecutar nada (sin PostgreSQL ni MongoDB en su entorno, solo revisó sintaxis JS). Lo ejecuté yo: el export y la migración funcionaron a la primera, y la IA no se equivocó en los scripts |
| Cómo armar la base en PostgreSQL y crear la base en Mongo; qué documentar | Indicó que en esta unidad basta `views.sql` en `foodstore`, que Mongo crea base y colección al usarlas, y qué evidencias guardar | Verifiqué los 6 pedidos con el `SELECT` de H0 y los conteos 6/16/6, que coincidieron con lo que la IA anticipó |
| ¿Terminal o Compass? | Recomendó `mongosh` por ser repetible y dejar salida en texto; Compass solo para capturas | Usé `mongosh --file consultas_mongodb.js` |
| Comandos para PowerShell 5.1 | Advirtió que `>` guarda en UTF-16 y propuso `Out-File -Encoding utf8` y `PGCLIENTENCODING=UTF8` | El JSON exportado salió con acentos bien. La primera salida de `mongosh` guardada con `Out-File` mostró acentos rotos (ver problemas) |
| Salida de la migración y consultas | Comparó contra los valores calculados a mano (totales 9400, 11900, 5300, 18700, 3200) y confirmó que todo coincide | Los valores a mano los verifiqué contra `data.sql`. Revisión del repo: el JSON suma bien en los 5 pedidos y no contiene `contrasena` |

## Revisión cruzada que hice

| Qué revisé | En qué servidor lo ejecuté | Qué encontré |
|---|---|---|
| (todavía nada: me corresponde `consultas_postgresql.sql` de Andrés y el contraste de la IA de Facundo) | | |

## Mediciones (valores observados, no estimados)

| Dato | Valor | Cómo se midió |
|---|---|---|
| Conteos `usuario` / `producto` / `pedido` | 6 / 16 / 6 | `SELECT` de conteos en `foodstore` |
| Pedidos exportados | 5 (ids 1, 2, 3, 5, 6) | `modelo/pedidos_export.json` |
| Documentos migrados | 5 (`Insertados: 5`, `countDocuments: 5`) | Salida de `consultas_mongodb.js` |
| Regla 1 (total ≠ suma de subtotales) | 0 documentos (`[]`) | Agregación de verificación |
| Regla 2 (líneas con cantidad ≤ 0) | 0 documentos (`[]`) | `find` de verificación |
| b) estado `PENDIENTE` | ids `[3, 5]` (totales 5300.00 y 18700.00) | `evidencias/mongo_migracion_y_consultas.txt` |
| c) producto 1 (Muzzarella) | ids `[1]` | Idem; un solo pedido porque el producto no se repite |
| d) total > 10000.00 | ids `[2, 5]` (11900.00 y 18700.00) | Idem |
| Totales por pedido | 1: 9400.00 · 2: 11900.00 · 3: 5300.00 · 5: 18700.00 · 6: 3200.00 (promedio 9700.00) | `pedidos_export.json` |
| Fecha de los pedidos | 2026-10-30 | Es el día en que corrí `data.sql` (`fecha` toma `CURRENT_DATE`; cambia según el equipo) |

## Problemas encontrados y cómo se resolvieron

| Fecha | Problema | Causa | Solución |
|---|---|---|---|
| 2026-10-30 | Acentos rotos en la evidencia de Mongo (`cl├ísica`, `Luc├¡a`, `P├®rez`) | Codificación de la consola de PowerShell 5.1 al guardar la salida de `mongosh`; los datos en Mongo y el JSON estaban bien | Configuré la consola en UTF-8 (`[Console]::OutputEncoding = [System.Text.Encoding]::UTF8`) y volví a correr `mongosh --file consultas_mongodb.js \| Out-File -Encoding utf8 ...`. La evidencia final muestra `Tiramisú` correctamente |
| 2026-10-30 | `git status` marca como modificados `base_heredada/*`, `.gitignore`, etc., sin cambios reales | Fin de línea: el repo guarda LF y mi copia en Windows tiene CRLF (`git diff -w` no muestra nada) | No commitear esos archivos y ejecutar `git config core.autocrlf true` (Git convierte los fines de línea al subir y bajar). Si `git status` sigue marcándolos, se ignoran |

## Decisiones tomadas

| Decisión | Alternativas | Por qué |
|---|---|---|
| Migrar los 5 pedidos vigentes de `data.sql` (1, 2, 3, 5, 6) y excluir el 4 | Incluir el 4 | Está dado de baja (`eliminado = TRUE`); migrar solo vigentes |
| Generar el JSON desde PostgreSQL (`exportar_pedidos.sql`) y cargarlo desde `consultas_mongodb.js` | Escribir el `insertMany()` a mano | Evita errores de tipeo y respeta ids, importes y estados de origen |
| `_id` = `pedido.id`; cliente y líneas embebidos; conservar `usuario_id`, `producto_id`, `detalle_id` | Colecciones separadas con referencias | Los pedidos se leen completos con sus líneas; un solo acceso, sin `JOIN` |
| Cliente embebido solo con `usuario_id`, `nombre`, `apellido`, `mail` | Copiar el usuario completo | Nunca `contrasena`; `celular` y `rol` no hacen falta |
| Importes `Decimal128`, cantidades `Int32`, `fecha` como `Date` (medianoche UTC), estados como string | `Double` para importes | Evita el error binario de `Double` en importes |
| Precio de cada línea = `detalle_pedido.precio_unitario` (histórico) | Usar `producto.precio` | El precio de venta no debe cambiar si cambia el catálogo |
| Validador `$jsonSchema` en la colección + verificación posterior a la migración | Solo la verificación | En MongoDB no hay triggers ni `CHECK`; el validador refuerza la regla 2 y la consulta comprueba la regla 1 |
| Estado de comparación `PENDIENTE`, producto 1 (Muzzarella), umbral de d) = 10000 | Otros valores | `PENDIENTE` devuelve 2 pedidos; 10000 queda sobre el promedio (9700) y separa los pedidos grandes. **Umbral provisional**: revisar si Andrés suma pedidos de prueba |
| Tomar el armado del informe PDF (antes de Facundo) | Dejarlo en Facundo | Facundo tenía consignas 6 y 7, la instalación de MongoDB y revisiones; yo ya tenía el modelo y las evidencias de Mongo |
| Ejecutar con `mongosh --file` y no con Compass | Compass | Repetible y deja salida en texto como evidencia; Compass solo para capturas del informe |
| Commitear en `main` | Una rama por parte (`parte-1-modelo`) | Ya había avanzado en `main` y funciona sin problemas; no aporta crear una rama ahora |

## Segunda migración, con los pedidos de prueba (2026-10-31)

Revisé el repo de Andrés y encontré que su tabla de verificación decía 3 pedidos PENDIENTE en ambos motores (ids 3, 5, 7), pero su propia evidencia de PostgreSQL mostraba 4 (3, 5, 7, 8) y MongoDB todavía tenía solo 5 documentos (b = [3,5], c = [1]). Por eso la tabla no estaba respaldada por una ejecución de MongoDB. La corregimos entre los dos.

| Paso | Resultado |
|---|---|
| Comprobé que mi base tenía 6 pedidos y corrí `modelo/pedidos_de_prueba.sql` una sola vez | Se crearon los pedidos 7 (4200.00) y 8 (11700.00), ambos PENDIENTE |
| Agregué 7 y 8 al `IN (...)` de `exportar_pedidos.sql`, repetí export y migración | `Insertados: 7`, `countDocuments: 7` |
| Reglas de integridad | Ambas dan `[]` |
| b) `PENDIENTE` | ids `[3, 5, 7, 8]` |
| c) producto 1 | ids `[1, 7, 8]`; variante `$elemMatch` (cantidad ≥ 2): pedidos 1 y 8 |
| d) total > 10000.00 | ids `[2, 5, 8]` (11900.00, 18700.00, 11700.00) |
| Comparación con PostgreSQL de Andrés | b) 4 y 4, c) 3 y 3; los ids coinciden |

Observación: la fecha de los pedidos 7 y 8 es 2026-10-31 en mi base y 2026-10-30 en la de Andrés, porque `fecha` toma la fecha del día en que se crean. No afecta la comparación.
Umbral de d): sigue siendo 10000; el promedio de los 7 totales es 9200.

## Pendiente

- Sacar de nuevo `compass_coleccion.png` (ahora hay 7 documentos).
- Actualizar `esquema_documental.md`, `README.md` y `CONTEXTO_IA.md` con los 7 pedidos.
- Informe PDF (hasta 5 páginas), cuando Facundo entregue el contraste y la conclusión.
- Unificación de bitácoras en `BITACORA.md` (al final del trabajo).
