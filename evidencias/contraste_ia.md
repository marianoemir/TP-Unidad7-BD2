# Contraste de Afirmaciones de la IA y Conclusión Técnica

## 1. Contraste de Afirmaciones

### Afirmación 1: Validación e Integridad en MongoDB
> **IA afirma:** *"MongoDB no soporta reglas de integridad referencial ni validaciones de esquemas avanzadas, por lo que la coherencia de importes y cantidades depende exclusivamente de la lógica de la aplicación."*

- **¿Se sostiene?:** **NO.**
- **Contraste con la evidencia/documentación:** La afirmación es totalmente falsa. MongoDB admite validación de esquemas basada en JSON Schema (`$jsonSchema`) a nivel de colección para restringir tipos de datos y presencia de campos requeridos (Documentación Oficial: [Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/)). Además, en el laboratorio demostramos que es posible implementar reglas de integridad mediante scripts de verificación (como `consultas_mongodb.js`), comprobando que no existan inconsistencias entre el total y los subtotales, ni líneas con cantidades $\le 0$.

---

### Afirmación 2: Optimización de Lecturas mediante Embebido
> **IA afirma:** *"Al embeber la información del cliente y los ítems dentro de la colección `pedidos` en MongoDB, se elimina por completo la sobrecarga computacional de los JOINs relacionales, permitiendo recuperar el pedido completo en una sola operación de lectura."*

- **¿Se sostiene?:** **SÍ.**
- **Contraste con la evidencia/documentación:** La documentación oficial de MongoDB sobre modelado de datos ([Data Modeling Embedding](https://www.mongodb.com/docs/manual/data-modeling/embedding/)) establece que embeber documentos relacionados dentro de una misma estructura es el patrón recomendado cuando las entidades se consultan juntas de manera frecuente. En la ejecución real del laboratorio (`consultas_mongodb.js`), una única consulta `db.pedidos.find()` recupera la cabecera, el cliente y todas sus líneas de detalle en un solo documento sin requerir operadores `$lookup` ni combinaciones en tiempo de ejecución.

---

## 2. Conclusión Técnica (196 palabras)

Para el dominio de **Food Store**, la arquitectura óptima es **combinar ambas tecnologías** mediante un esquema híbrido.

**PostgreSQL** debe mantenerse como el motor transaccional de origen (*System of Record*). Su modelo relacional garantiza la consistencia ACID en la creación de pedidos (`sp_crear_pedido`), gestiona de forma nativa el stock de productos y previene anomalías mediante claves foráneas y disparadores.

Por otro lado, **MongoDB** es ideal como almacén operacional de lectura y consulta de pedidos. Al migrar los pedidos a un modelo documental embebido, se optimiza significativamente la latencia en la lectura de pedidos completos (patrón predominante en la app para clientes y repartidores), evitando operaciones costosas de `JOIN` entre `pedido`, `detalle_pedido` y `usuario`. Además, la desnormalización embebida preserva inmutablemente el precio histórico y la descripción del producto al momento de la compra.

En síntesis, mantener PostgreSQL para el procesamiento del *checkout* e inventario asegura una estricta integridad de datos, mientras que replicar los pedidos finalizados a MongoDB proporciona alta disponibilidad, flexibilidad de consulta y rendimiento en la capa de lectura.