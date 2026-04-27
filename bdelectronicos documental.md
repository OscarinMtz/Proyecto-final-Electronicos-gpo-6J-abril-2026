¡Excelente elección! Optar por una base de datos documental (como MongoDB o Firestore) es perfecto para una tienda de electrónicos, ya que la flexibilidad del esquema permite manejar productos con especificaciones muy variadas (desde una resistencia de 1 ohmio hasta una Smart TV de 75 pulgadas).

Aquí tienes un ejemplo de diseño lógico para una tienda llamada **"ElectroTech"**.

---

## 1. Estructura de la Base de Datos

En el modelo documental, no hablamos de tablas y filas, sino de **Colecciones** y **Documentos** (objetos JSON/BSON).

### Colección: `productos`
Esta es la colección más compleja debido a que cada categoría tiene atributos distintos.

| Atributo | Tipo de Dato | Descripción |
| :--- | :--- | :--- |
| `sku` | **String** | Identificador único del producto (ej: "LPT-MAC-001"). |
| `nombre` | **String** | Nombre comercial del producto. |
| `marca` | **String** | Fabricante (ej: "Sony", "Apple"). |
| `precio` | **Decimal/Double** | Precio de venta actual. |
| `stock` | **Integer** | Cantidad disponible en almacén. |
| `categorias` | **Array [String]** | Etiquetas para búsqueda (ej: ["computación", "laptops"]). |
| `especificaciones` | **Object (Nested)** | **Atributos variables** según el tipo de producto. |
| `imagenes` | **Array [String]** | Lista de URLs a las fotos del producto. |
| `fecha_creacion` | **Timestamp** | Fecha en que se dio de alta. |



---

## 2. Ejemplo de Documentos (JSON)

Para que veas cómo se aplica la flexibilidad, comparemos un **Laptop** con unos **Audífonos**:

### Documento: Laptop
```json
{
  "sku": "LP-DELL-XPS13",
  "nombre": "Dell XPS 13",
  "precio": 1299.99,
  "especificaciones": {
    "procesador": "Intel i7",
    "ram": "16GB",
    "disco_duro": "512GB SSD",
    "pantalla": "13.4 pulgadas"
  },
  "stock": 15
}
```

### Documento: Audífonos
```json
{
  "sku": "AUD-SONY-WH1000",
  "nombre": "Sony WH-1000XM4",
  "precio": 348.00,
  "especificaciones": {
    "tipo": "Over-ear",
    "cancelacion_ruido": true,
    "autonomia": "30 horas",
    "bluetooth": "5.0"
  },
  "stock": 42
}
```

---

## 3. Colección: `clientes`
Aquí almacenamos la información del usuario y sus preferencias.

| Atributo | Tipo de Dato | Descripción |
| :--- | :--- | :--- |
| `email` | **String** | ID único y correo de contacto. |
| `password_hash` | **String** | Contraseña encriptada. |
| `perfil` | **Object** | Nombre, apellido y teléfono. |
| `direcciones` | **Array [Object]** | Lista de domicilios de entrega (Calle, Ciudad, CP). |
| `metodos_pago` | **Array [Object]** | Tokens de tarjetas o cuentas vinculadas. |

---

## 4. Colección: `ordenes_compra`
En bases documentales, solemos "desnormalizar" un poco para que la consulta sea rápida.

| Atributo | Tipo de Dato | Descripción |
| :--- | :--- | :--- |
| `orden_id` | **ObjectId** | ID generado automáticamente. |
| `cliente_id` | **String** | Referencia al email del cliente. |
| `items` | **Array [Object]** | Lista de productos comprados (sku, nombre, precio unitario). |
| `total` | **Decimal** | Monto final cobrado. |
| `estado` | **String** | "Pendiente", "Enviado", "Entregado". |
| `fecha` | **Timestamp** | Momento de la transacción. |

---

> **Tip de experto:** En una base de datos documental, la regla de oro es: **"Agrupa los datos que se consultan juntos"**. Por eso, dentro de una orden de compra, guardamos el nombre y precio del producto en ese momento, por si el precio original cambia en el futuro, el historial de la compra del cliente se mantiene intacto.

¿Te gustaría que profundizáramos en cómo se harían las consultas (queries) para filtrar estos productos por precio o marca?
