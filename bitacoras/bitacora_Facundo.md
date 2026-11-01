# Bitácora Personal — Facundo (TP Unidad 7: Food Store - NoSQL con MongoDB)

> **Nota de corrección (2026-10-31, hecha por Mariano al unificar):** al revisar el contraste de la IA se corrigieron `evidencias/contraste_ia.md` y esta bitácora. Las filas modificadas llevan la marca *(corregido)* o *(agregado)*; el resto se conserva como lo registró Facundo.

## 1. Datos de entorno y versiones
- **Sistema operativo:** Windows (PowerShell)
- **MongoDB Server:** 9.0.4
- **MongoDB Shell (mongosh):** 2.14.1
- **Herramientas:** Conexión local mediante servidor de servicios de Windows y terminal de comandos.

---

## 2. Registro de actividades y avance

| Fecha | Tarea / Consigna | Estado | Descripción de lo realizado y observaciones |
|---|---|---|---|
| 31/10/2026 | H0: Configuración del entorno | Hecho | Instalación y verificación del servicio local de MongoDB y `mongosh`. Comprobación exitosa mediante `mongosh --eval "db.runCommand({ ping: 1 })"` (respuesta `ok: 1`). |
| 31/10/2026 | H0: Ejecución de `consultas_mongodb.js` | Hecho | Ejecución del script desde la raíz del repo. Se insertaron correctamente los documentos (7 documentos totales, incluyendo pedidos de prueba). Las consultas a, b, c y d arrojaron los resultados esperados coincidentes con el equipo. |
| 31/10/2026 | H5: Consulta a la IA (Consigna 6) | Hecho | Planteo del prompt formal comparando PostgreSQL y MongoDB para Food Store (modelo relacional, pedidos completos y precios históricos). Respuesta guardada en `evidencias/respuesta_ia.md`. |
| 31/10/2026 | H5b: Contraste y Conclusión (Consigna 7) | Hecho *(corregido)* | Selección de dos afirmaciones clave de la respuesta de la IA. Contraste de la validación de esquemas (MongoDB sí permite validación con `$jsonSchema`, aunque no tiene claves foráneas) y del patrón de embebido. Conclusión técnica guardada en `evidencias/contraste_ia.md`. La primera versión marcaba la afirmación 1 como «totalmente falsa» y la 2 como «sí se sostiene», y atribuía a MongoDB una ventaja de precio histórico que PostgreSQL también tiene; se corrigieron entre Mariano y Facundo (ver «Incidencias»). La conclusión final tiene unas 164 palabras (el encabezado original decía 196). |

---

## 3. Incidencias, bloqueos y soluciones
- **Incidencia:** Ninguna relevante durante la ejecución de `consultas_mongodb.js` con `mongosh`. Las versiones locales respondieron de forma idéntica a las evidencias previas del equipo, incorporando los pedidos de prueba (ids 7 y 8).
- **Incidencia *(agregada)*:** el contraste original sobrevaloraba lo que mostraba el laboratorio. La afirmación 1 de la IA es cierta en una parte (MongoDB no tiene claves foráneas) y falsa en otra (sí valida esquemas), y la evidencia más fuerte era el validador `$jsonSchema` de `consultas_mongodb.js`, que no se citaba. La afirmación 2 está respaldada por la documentación y por la consulta c) (pedido completo sin `$lookup`), pero el rendimiento no se midió. Se reescribió con esos matices.
- **Pendiente *(agregado)*:** `evidencias/respuesta_ia.md` no incluye el prompt enviado ni la herramienta de IA usada; hay que agregarlos.

---

## 4. Revisión cruzada realizada
- Ejecución del script `consultas_mongodb.js` en entorno local propio (MongoDB 9.0.4, `mongosh` 2.14.1): 7 documentos insertados y resultados coherentes con la evidencia de Mariano *(agregado: b) ids 3, 5, 7, 8; c) ids 1, 7, 8; d) ids 2, 5, 8)*.
- *(Agregado)* Pendiente: comprobar que su contraste y su conclusión quedaron fieles en el informe.