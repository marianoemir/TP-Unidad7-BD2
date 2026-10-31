# Bitácora Personal — Facundo (TP Unidad 7: Food Store - NoSQL con MongoDB)

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
| 31/10/2026 | H5b: Contraste y Conclusión (Consigna 7) | Hecho | Selección de dos afirmaciones clave de la respuesta de la IA. Contraste de la validación de esquemas (demostrando que MongoDB sí permite validación con `$jsonSchema` frente a lo que afirmaba la IA) y del patrón de embebido. Redacción de la conclusión técnica de 196 palabras y guardado en `evidencias/contraste_ia.md`. |

---

## 3. Incidencias, bloqueos y soluciones
- **Incidencia:** Ninguna relevante durante la ejecución de los scripts de Node/MongoDB. Las versiones locales respondieron de forma idéntica a las evidencias previas del equipo, incorporando de manera transparente los pedidos de prueba (ids 7 y 8).

---

## 4. Revisión cruzada realizada
- Ejecución y validación cruzada del script `consultas_mongodb.js` en entorno local propio con resultados exitosos y coherentes frente al documento de evidencias de Mariano.