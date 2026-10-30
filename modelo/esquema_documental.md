# Esquema documental — colección `pedidos` (base `foodstore_nosql`)

> BORRADOR de Mariano (Parte 1). Los valores concretos (ids, estado, producto, umbral) se confirman al ejecutar.

## 1. Subconjunto elegido (consigna 1)

Pedidos y detalle de pedidos de Food Store. Tablas y claves en PostgreSQL:

| Tabla | Clave primaria | Claves foráneas | Qué se toma |
|---|---|---|---|
| `pedido` | `id` | `usuario_id → usuario.id` | fecha, estado, total, forma_pago |
| `detalle_pedido` | `id` | `pedido_id → pedido.id`, `producto_id → producto.id` · `UNIQUE (pedido_id, producto_id)` | cantidad, precio_unitario, subtotal |
| `usuario` | `id` | — | nombre, apellido, mail (copia embebida) |
| `producto` | `id` | `categoria_id → categoria.id` | nombre (copia embebida) |

## 2. Documento propuesto

```js
{
  _id: 1,                              // pedido.id de origen
  fecha: ISODate("..."),               // pedido.fecha (DATE)
  estado: "CONFIRMADO",                // estado_pedido
  forma_pago: "EFECTIVO",
  total: NumberDecimal("9400.00"),     // pedido.total tal cual está en PostgreSQL
  cliente: { usuario_id: 1, nombre: "Ana", apellido: "García", mail: "ana.garcia@mail.com" },   // OBJETO EMBEBIDO
  lineas: [                            // ARREGLO de objetos embebidos
    { detalle_id: 1, producto_id: 1, nombre: "Muzzarella", cantidad: NumberInt(2),
      precio_unitario: NumberDecimal("3500.00"), subtotal: NumberDecimal("7000.00") }
  ],
  origen: "data.sql"                   // "prueba" para pedidos creados con sp_crear_pedido
}
```

**Identificadores conservados:** `_id` = `pedido.id`; `cliente.usuario_id`; `lineas[].detalle_id`; `lineas[].producto_id`.
**No se migra:** `contrasena`, `celular`, `rol`, ni registros con `eliminado = TRUE` (el pedido 4).

## 3. Tipos (y por qué)

| PostgreSQL | MongoDB | Motivo |
|---|---|---|
| `BIGINT` (ids) | número | Conservar el identificador de origen |
| `NUMERIC(12,2)` | `Decimal128` | Evita el error binario de `Double` en importes |
| `INTEGER` (cantidad) | `Int32` | Respetar el tipo entero |
| `DATE` | `Date` (medianoche UTC) | Consultable por rango; la zona horaria es una decisión a anotar |
| ENUM | string | Conservar los valores existentes |

## 4. Dos reglas de integridad (consigna 3)

1. **Coherencia de importes:** `total` = suma de `subtotal`, y cada `subtotal` = `cantidad × precio_unitario`. En PostgreSQL la garantizan los triggers; en MongoDB se copian sin recalcular y se **verifican después de migrar** (agregación en `consultas_mongodb.js`, debe dar 0 documentos).
2. **Líneas válidas y vigentes:** `cantidad > 0`, estado dentro del conjunto permitido y sin registros con `eliminado = TRUE`. Se refuerza con el validador `$jsonSchema` de la colección y con una consulta de comprobación.

## 5. Qué se embebió y por qué (consigna 3)

- **Líneas de detalle:** se leen siempre junto con el pedido (acceso frecuente a pedidos completos), no tienen sentido sin él y son pocas por pedido → un solo acceso, sin `JOIN`.
- **Cliente y nombre de producto (datos esenciales):** evitan consultar otras colecciones para mostrar un pedido.
- **Precio histórico:** se guarda `precio_unitario` de la línea, nunca el precio actual del producto.
- **Costo asumido:** lo embebido es una copia al momento de migrar; si el cliente cambia de mail, el documento no se actualiza.
- **Qué NO se embebió:** el catálogo completo de productos, categorías ni datos sensibles del usuario.
