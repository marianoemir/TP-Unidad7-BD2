### Tabla de Verificación de Consultas (PostgreSQL vs. MongoDB)

| Consulta | Cantidad en PostgreSQL | Cantidad en MongoDB | Coinciden los IDs | IDs Recuperados |
| :--- | :---: | :---: | :---: | :---: |
| Estado seleccionado (`PENDIENTE`) | 3 | 3 | **Sí** | 3, 5, 7 |
| Producto seleccionado (`producto_id = 1`) | 3 | 3 | **Sí** | 1, 7, 8 |

**Explicación:**
Los resultados coinciden perfectamente en ambos motores. En PostgreSQL se aplicó el filtro `eliminado = FALSE` tanto en la tabla `pedido` como en `detalle_pedido` mediante `JOIN`, garantizando que el pedido ID 4 (eliminado) no sea contabilizado, alineándose con los documentos migrados a MongoDB.