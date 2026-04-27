Aquí tienes un ejemplo claro y práctico de cómo estructurar una base de datos para una **agencia de carros**, usando un modelo tipo **NoSQL (MongoDB)** con **colecciones, documentos, atributos y tipos de datos**.

---

# 🗄️ Base de Datos: `agencia_carros`

## 📁 Colección: `clientes`

Cada documento representa un cliente.

```json
{
  "_id": ObjectId,
  "nombre": "Juan Pérez",
  "email": "juan@email.com",
  "telefono": "6561234567",
  "direccion": {
    "calle": "Av. Siempre Viva",
    "numero": 123,
    "ciudad": "Juárez",
    "estado": "Chihuahua",
    "codigo_postal": "32000"
  },
  "fecha_registro": ISODate("2026-04-27"),
  "activo": true
}
```

**Tipos de datos:**

* `String`: nombre, email, teléfono
* `Object`: dirección
* `Date`: fecha_registro
* `Boolean`: activo

---

## 📁 Colección: `vehiculos`

Cada documento representa un auto disponible o vendido.

```json
{
  "_id": ObjectId,
  "marca": "Toyota",
  "modelo": "Corolla",
  "anio": 2022,
  "precio": 320000.50,
  "color": "Blanco",
  "tipo": "Sedán",
  "transmision": "Automática",
  "kilometraje": 15000,
  "disponible": true,
  "fecha_ingreso": ISODate("2026-03-15")
}
```

**Tipos de datos:**

* `String`: marca, modelo, color, tipo, transmisión
* `Number`: año, precio, kilometraje
* `Boolean`: disponible
* `Date`: fecha_ingreso

---

## 📁 Colección: `empleados`

Información de los vendedores o personal.

```json
{
  "_id": ObjectId,
  "nombre": "Laura Gómez",
  "puesto": "Vendedor",
  "email": "laura@agencia.com",
  "telefono": "6569876543",
  "fecha_contratacion": ISODate("2025-06-10"),
  "salario": 12000.00,
  "activo": true
}
```

**Tipos de datos:**

* `String`: nombre, puesto, email, teléfono
* `Date`: fecha_contratación
* `Number`: salario
* `Boolean`: activo

---

## 📁 Colección: `ventas`

Registra cada venta realizada.

```json
{
  "_id": ObjectId,
  "cliente_id": ObjectId("ID_CLIENTE"),
  "vehiculo_id": ObjectId("ID_VEHICULO"),
  "empleado_id": ObjectId("ID_EMPLEADO"),
  "fecha_venta": ISODate("2026-04-20"),
  "precio_final": 310000.00,
  "metodo_pago": "Crédito",
  "detalles_pago": {
    "enganche": 50000,
    "mensualidades": 24,
    "monto_mensual": 10833.33
  }
}
```

**Tipos de datos:**

* `ObjectId`: referencias (cliente, vehículo, empleado)
* `Date`: fecha_venta
* `Number`: precio, enganche, mensualidades
* `String`: método de pago
* `Object`: detalles_pago

---

## 📁 Colección: `servicios`

Para mantenimiento o servicios postventa.

```json
{
  "_id": ObjectId,
  "vehiculo_id": ObjectId("ID_VEHICULO"),
  "cliente_id": ObjectId("ID_CLIENTE"),
  "tipo_servicio": "Cambio de aceite",
  "costo": 1200.00,
  "fecha_servicio": ISODate("2026-04-10"),
  "descripcion": "Cambio de aceite y filtro",
  "estatus": "Completado"
}
```

**Tipos de datos:**

* `String`: tipo_servicio, descripción, estatus
* `Number`: costo
* `Date`: fecha_servicio
* `ObjectId`: referencias

---

# 🧠 Relación entre colecciones

* Un **cliente** puede tener muchas **ventas**
* Un **vehículo** pertenece a una sola venta (cuando se vende)
* Un **empleado** realiza muchas ventas
* Un **vehículo** puede tener muchos servicios

---

# 💡 Extra (Buenas prácticas)

* Usar índices en:

  * `email` (clientes)
  * `marca` y `modelo` (vehículos)
* Validar datos (ej: que el precio sea positivo)
* Manejar estados como:

  * `disponible`
  * `activo`
  * `estatus`

---

Si quieres, puedo darte este mismo ejemplo en **SQL (MySQL/PostgreSQL con tablas y llaves foráneas)** o incluso ayudarte a **implementarlo en código (Node.js, Python, etc.)**.
