# División de trabajo — TP Unidad 7 (Food Store: NoSQL con MongoDB)

Equipo de tres integrantes. Este documento dice **quién hace qué, qué necesita de los demás y en qué orden** se
avanza. Se actualiza si el reparto cambia.

## 1. Reparto

| Integrante | A su cargo |
|---|---|
| **Mariano Chirino** | **Parte 1** (modelo documental, migración con `insertMany()`, dos reglas de integridad y justificación de lo embebido), **consigna 4** (consultas a a d en MongoDB, `consultas_mongodb.js`), **armado del informe PDF** (`informe_u7.pdf`), `README.md` y unificación de bitácoras |
| **Andrés** | **Consigna 5**: consultas equivalentes b) y c) en PostgreSQL (con `JOIN`), `consultas_postgresql.sql`, tabla de verificación y explicación de diferencias. Además los **pedidos de prueba** (opcionales, ver H1b) |
| **Facundo** | **Parte 3** (consignas 6 y 7): consulta a la IA, contraste de dos afirmaciones y conclusión de hasta 200 palabras |

## 2. Estado actual (30/10/2026)

| Tarea | Estado |
|---|---|
| Entorno de Mariano (PostgreSQL y MongoDB) | Hecho |
| Modelo documental, migración de 5 pedidos y consultas a) a d) en MongoDB | Hecho (evidencia en `evidencias/mongo_migracion_y_consultas.txt`) |
| Entorno de Andrés y Facundo (H0) | Por hacer |
| Consigna 5, pedidos de prueba, consulta a la IA, contraste, conclusión, PDF | Por hacer |

### Datos ya definidos (para que nadie espere)

| Dato | Valor | Cómo se ve en MongoDB (resultado real) |
|---|---|---|
| Pedidos migrados | 1, 2, 3, 5 y 6 (el 4 está dado de baja y no entra) | 5 documentos |
| Estado «seleccionado» (consulta b) | `PENDIENTE` | ids `[3, 5]` |
| Producto «seleccionado» (consulta c) | `producto_id = 1` (Muzzarella) | ids `[1]` |
| Umbral de la consulta d) | `total > 10000.00` (provisional: queda sobre el promedio de los totales, 9700) | ids `[2, 5]` |

Si se agregan pedidos de prueba (H1b), estos valores se actualizan y Mariano avisa.

## 3. Dependencias: quién necesita de quién

| # | Tarea | Responsable | Necesita de… | Le sirve a… |
|---|---|---|---|---|
| H0 | Entorno propio (sección 4) | Cada uno | — | Todo lo demás |
| H1 | Decisiones: ids migrados, estado, producto y umbral | Mariano | H0 | Andrés (escribe sus consultas con esos valores) |
| H1b | Pedidos de prueba (opcional) | Andrés | H0 | Mariano (los suma a la migración) |
| H2 | Modelo documental y migración | Mariano | H1 (y H1b si se usa) | H3 |
| H3 | Consultas a) a d) en MongoDB y sus salidas | Mariano | H2 | Andrés (compara contra ellas) y Facundo (contraste) |
| H4 | Consultas b) y c) en PostgreSQL y tabla de verificación | Andrés | H1 para escribirlas, H3 para comparar | El informe |
| H5 | Consulta a la IA y su respuesta completa | Facundo | H0 | H5b |
| H5b | Contraste de dos afirmaciones y conclusión (en `evidencias/contraste_ia.md`) | Facundo | H5; H3 y H4 si contrasta con el laboratorio | H6 |
| H6 | `informe_u7.pdf` (hasta 5 páginas) | Mariano | Modelo y capturas (H2, H3), tabla de verificación (H4), contraste y conclusión (H5b) | Entrega |
| H7 | `README.md` y unificación de bitácoras | Mariano | Las bitácoras de los tres | Entrega |

```
H0 entorno (los 3)
  ├─> H1 decisiones (Mariano) ──┐
  ├─> H1b pedidos de prueba, opcional (Andrés) ──┴─> H2 modelo y migración (Mariano) ──> H3 consultas MongoDB (Mariano) ──┐
  │                                                                                                                       ├─> H4 PostgreSQL y tabla (Andrés)
  └─> H5 consulta a la IA (Facundo) ──────────────────────────> H5b contraste y conclusión (Facundo) <─────────────────────┘
                                                                                    └─> H6 PDF, H7 README y bitácora (Mariano)
```

- **Andrés ya puede escribir sus consultas**: H1 y H3 están hechos (sección 2). Solo le falta su entorno (H0).
- **Mariano arma el PDF con lo que le entregan los otros dos:** de Andrés, la tabla de verificación y la explicación de diferencias (H4); de Facundo, `evidencias/contraste_ia.md` con las dos afirmaciones contrastadas y la conclusión (H5b). El modelo y las evidencias de MongoDB ya están.
- **Facundo no espera a nadie para empezar**: la consulta a la IA (H5) solo necesita conocer el modelo relacional.
  Para contrastar con el laboratorio usa las salidas de `evidencias/`.
- **Los pedidos de prueba son opcionales.** Con los cinco pedidos de `data.sql` el trabajo ya cumple; los de prueba
  solo hacen más interesante la consulta c), porque hoy el producto 1 aparece en un único pedido. Hay que decidirlo
  pronto para no rehacer la migración y la comparación al final.

## 4. Armado del entorno (H0)

### Todos: PostgreSQL

1. En pgAdmin, crear `plantilla_food_store` y ejecutar `base_heredada/schema.sql` → `objects.sql` → `data.sql`,
   **uno por vez y en ese orden**. Desconectar la plantilla (cerrar sus pestañas de consulta, clic derecho →
   *Disconnect*) y crear `foodstore` con *Template* = `plantilla_food_store`.
2. En `foodstore`, ejecutar `views.sql`. **No** correr `carga_masiva.sql` (agrega 200.000 pedidos y rompe la
   comparación de cantidades).
3. Comprobar con `SELECT id, estado, eliminado FROM pedido ORDER BY id;`: deben salir **6 pedidos** (ids 1 a 6) y el
   pedido 4 con `eliminado = true`. Conteos esperados de `usuario`, `producto` y `pedido`: **6 / 16 / 6**.

### Quién necesita MongoDB

| Integrante | ¿Instala MongoDB? | Por qué |
|---|---|---|
| **Mariano** | Ya lo tiene | Ejecuta la migración y las consultas |
| **Andrés** | **No** | Compara contra los ids de `evidencias/mongo_*.txt`; solo necesita PostgreSQL |
| **Facundo** | **Sí** (servidor local y `mongosh`) | Revisa `consultas_mongodb.js` ejecutándolo en su equipo y puede contrastar afirmaciones de la IA con un resultado propio |

Para Facundo: probar con `mongosh --eval "db.runCommand({ ping: 1 })"`, que debe responder `ok: 1`. Después, desde la
raíz del repo, ejecutar `mongosh --file consultas_mongodb.js` (lee `modelo/pedidos_export.json`, que ya está en el
repo, así que no necesita exportar nada de PostgreSQL). Una cadena de conexión con contraseña **no va al repo**.

Cada uno anota en su bitácora las versiones y los problemas del armado.

## 5. Qué hace cada uno, en detalle

### Mariano

1. **Hecho:** decisiones (H1), modelo documental y migración (H2), consultas a) a d) con sus salidas (H3), dos
   reglas de integridad y justificación de lo embebido (en `modelo/esquema_documental.md`).
2. **Pendiente:**
   - Si Andrés crea pedidos de prueba: correr su script **una sola vez** en su propia base, agregar los ids nuevos
     al `IN (...)` de `modelo/exportar_pedidos.sql`, repetir export y migración, y avisar los valores nuevos de la
     sección 2.
   - Revisar la consigna 5 de Andrés y el contraste de la IA de Facundo (sección 6).
   - **Armar `informe_u7.pdf` (H6)**, de hasta **5 páginas**, con: descripción sintética del modelo (de
     `modelo/esquema_documental.md`), evidencias de la colección y de las consultas (salidas de `evidencias/` y
     capturas de Compass), la tabla de verificación de Andrés, el contraste de la IA y la conclusión de Facundo.
     Sin imágenes decorativas ni transcripciones extensas. Identifica a los integrantes y el repositorio. Cada valor
     del informe debe tener evidencia en `evidencias/`.
   - `README.md` y unificación de bitácoras en `BITACORA.md`, verificando cada fila contra la evidencia.

### Andrés — consigna 5

1. **(Opcional) Pedidos de prueba (H1b):** sobre una base recién armada (6 pedidos), crear los pedidos con
   `CALL sp_crear_pedido(...)` (nunca con `INSERT` a mano) y guardar el script en `modelo/pedidos_de_prueba.sql`
   indicando que son de prueba. Se ejecutan una sola vez por base: así los ids nuevos (7, 8…) coinciden en todas las
   bases. Ejemplo que haría que el producto 1 aparezca en tres pedidos:
   ```sql
   CALL sp_crear_pedido(2, 'TARJETA',  '[{"producto_id":1,"cantidad":1},{"producto_id":8,"cantidad":1}]'::jsonb);
   CALL sp_crear_pedido(3, 'EFECTIVO', '[{"producto_id":1,"cantidad":3},{"producto_id":7,"cantidad":1}]'::jsonb);
   ```
   Con ese ejemplo, el resultado esperado pasaría a ser: b) `[3, 5, 7, 8]`, c) `[1, 7, 8]`, d) `[2, 5, 8]`
   (verificar siempre con la ejecución real).
2. **Consultas (H4):** en `consultas_postgresql.sql`, las equivalentes a b) y c) con los valores de la sección 2.
   La c) lleva `JOIN` entre `pedido` y `detalle_pedido`. Las dos se limitan a los **mismos ids migrados**
   (`id IN (...)`) y a registros vigentes (`eliminado = FALSE` en **cada** tabla del `JOIN`).
3. **Tabla de verificación:** cantidad e ids en ambos motores y si coinciden. Los de MongoDB salen de
   `evidencias/mongo_migracion_y_consultas.txt`. Si no coinciden, **explica la diferencia y la corrige** en la
   consulta o en el modelo (avisando a Mariano), nunca ajustando datos.
4. Guardar las salidas en `evidencias/postgres_*.txt` (no `.log`: el `.gitignore` los excluye).

### Facundo — consignas 6 y 7

1. **Consulta a la IA (H5):** compara PostgreSQL y MongoDB para almacenar y consultar pedidos de Food Store. La
   consulta **menciona** el modelo actual (tablas `pedido` y `detalle_pedido` con `usuario` y `producto`), el acceso
   frecuente a pedidos completos y la necesidad de conservar la coherencia de cantidades y precios. Guarda la
   respuesta **completa** en `evidencias/respuesta_ia.md`.
2. **Contraste (H5b):** elige **dos afirmaciones concretas** y las confronta con la documentación oficial de MongoDB
   o con un resultado del laboratorio (por ejemplo, el comportamiento de las reglas de integridad o la consulta por
   producto en `evidencias/`), diciendo si se sostienen.
3. **Conclusión:** hasta **200 palabras**, con **al menos dos criterios técnicos**, que responda si conviene
   mantener PostgreSQL, usar MongoDB para este subconjunto o combinar ambas tecnologías.
4. **Entrega a Mariano:** el contraste (dos afirmaciones, cada una con su fuente o resultado y si se sostiene) y la
   conclusión, en `evidencias/contraste_ia.md`, para que entren al PDF.
5. **Instalar MongoDB** (sección 4) y ejecutar `consultas_mongodb.js` para la revisión cruzada.

## 6. Revisión cruzada

| Se revisa | Lo revisa | Cómo |
|---|---|---|
| Modelo y migración | Andrés | Que ids, importes y estados de `modelo/pedidos_export.json` coincidan con su PostgreSQL, y que no se haya migrado `contrasena` |
| `consultas_mongodb.js` | Facundo | Lo ejecuta en su equipo y compara con las salidas guardadas |
| `consultas_postgresql.sql` | Mariano | Lo ejecuta en su servidor y verifica que se limite a los mismos ids y a registros vigentes |
| Contraste de la IA | Mariano | Que cada afirmación tenga una fuente o un resultado real detrás |
| `informe_u7.pdf` | Andrés y Facundo | Andrés: que cada valor tenga evidencia en `evidencias/` y que respete las 5 páginas. Facundo: que su contraste y su conclusión estén fieles en el informe |

Cada revisor anota en su bitácora qué revisó y qué encontró. Si no encontró nada, también se anota.

## 7. Trabajo con Git

- Se trabaja directamente en `main`. Antes de empezar y antes de subir: `git pull`. Cada uno modifica sus propios
  archivos, así que los conflictos son poco probables.
- Mensajes de commit descriptivos: `feat(modelo): ...`, `feat(mongodb): ...`, `feat(postgresql): ...`,
  `docs(ia): ...`. Un commit por entregable; los arreglos con un mensaje que diga qué se arregló.
- Sin credenciales (cadenas de conexión, contraseñas) en ningún commit.
- En Windows, Git puede marcar archivos como modificados solo por el fin de línea (CRLF/LF). Si pasa, no los
  commitees (se arregla con `git config core.autocrlf true`).

## 8. Bitácora

La llevamos aunque la consigna no la pida, para poder volver a ver qué hicimos y por qué.

- **Cada integrante arma la suya**, en `bitacoras/bitacora_<nombre>.md`, a partir de `bitacoras/PLANTILLA_bitacora.md`
  (la de Mariano, `bitacora_mariano.md`, sirve de ejemplo).
- **Al final, Mariano las unifica** en `BITACORA.md`, comparando cada fila contra la evidencia del repo.
- Se anota lo que realmente pasó, incluidos los casos en que la IA **no** se equivocó. Las respuestas se resumen;
  nunca se anotan contraseñas ni cadenas de conexión.
- **El contraste de la IA (consigna 7) se documenta además en `evidencias/respuesta_ia.md`** y en el informe.
