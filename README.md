# Backend Lociones AAA + Natillera

Ejercicio de clases: **backend en Java** para **Lociones AAA** (venta de ropa y pijamas), que opera en paralelo con una **natillera colombiana** (ahorro grupal con aportes, préstamos y liquidación anual).

---

## 1. Contexto y restricciones del cliente

| Restricción | Implicación para el backend |
|---|---|
| Usuarios con **dificultades visuales** | Respuestas simples y claras, con versión en texto plano apta para lectura en voz |
| Usuarios que **no saben usar el celular** | No depender de app móvil; flujos con pocos pasos |
| Solo usan **Linux** | Backend ejecutable en Linux (JAR) y compatible con lectores de pantalla (Orca) desde el cliente |

**Principio de diseño:** pocos pasos, mensajes de error en lenguaje natural y confirmación antes de acciones críticas (pagos, préstamos).

---

## 2. Stack propuesto

- **Lenguaje:** Java 21
- **Framework:** Spring Boot 3 (Web, Data JPA, Security, Validation)
- **Base de datos:** PostgreSQL (o MySQL)
- **Seguridad:** JWT + roles
- **Documentación:** Swagger / OpenAPI
- **Pruebas:** JUnit 5
- **Build:** Maven

---

## 3. Arquitectura

Monolito modular por capas, un paquete por módulo:

```
com.locionesaaa
├── auth
├── usuarios
├── catalogo
├── inventario
├── clientes
├── ventas
├── pagos
├── natillera
├── accesibilidad
└── auditoria
```

Cada módulo: `controller` → `service` → `repository` → `entity` / `dto`.

---

## 4. Módulos a desarrollar

### Módulo 1 — Autenticación y Seguridad (`auth`)
- Login con usuario/contraseña
- Emisión de JWT
- Roles: `ADMIN`, `VENDEDOR`, `TESORERO`, `SOCIO`

### Módulo 2 — Usuarios (`usuarios`)
- CRUD de usuarios y asignación de roles
- Preferencias de accesibilidad (tamaño de texto, lectura en voz)
- Activar/desactivar usuarios

### Módulo 3 — Catálogo de Productos (`catalogo`)
- CRUD de productos: ropa y pijamas
- Categorías, tallas y colores
- Descripciones cortas y claras
- Precios

### Módulo 4 — Inventario (`inventario`)
- Stock por producto/talla/color
- Entradas y salidas
- Alerta de stock mínimo

### Módulo 5 — Clientes (`clientes`)
- CRUD de clientes
- Historial de compras

### Módulo 6 — Ventas y Pedidos (`ventas`)
- Crear venta con detalle de productos
- Estados: `PENDIENTE`, `PAGADO`, `ENTREGADO`, `CANCELADO`
- Cálculo de totales y descuentos
- Comprobante simple en texto legible

### Módulo 7 — Pagos (`pagos`)
- Registro de pagos y abonos (efectivo, transferencia)
- Saldo pendiente por cliente

### Módulo 8 — Natillera (`natillera`)
- **Socios:** inscripción y estado
- **Ciclo:** apertura y cierre del año
- **Aportes/cuotas:** registro y mora
- **Préstamos:** solicitud, aprobación, interés y abonos
- **Liquidación final:** reparto de ahorros e intereses por socio
- Estado de cuenta por socio (resumen simple)

### Módulo 9 — Accesibilidad (`accesibilidad`)
- Endpoints que devuelven **resumen en texto plano** de saldos, pedidos y estados
- Mensajes de error en español claro (sin códigos técnicos)
- Valores en formato hablado ("quince mil pesos", "veinte de noviembre")

### Módulo 10 — Auditoría (`auditoria`)
- Registro de quién hizo qué y cuándo (dinero, préstamos e inventario)

---

## 5. Asignación por grupos (4 grupos)

| Grupo | Módulos | Dependencias |
|---|---|---|
| **Grupo 1 – Seguridad y Base** | 1 Auth, 2 Usuarios, 10 Auditoría | Ninguna (base del proyecto) |
| **Grupo 2 – Comercio** | 3 Catálogo, 4 Inventario, 5 Clientes | Grupo 1 |
| **Grupo 3 – Ventas y Pagos** | 6 Ventas, 7 Pagos | Grupos 1 y 2 |
| **Grupo 4 – Natillera y Accesibilidad** | 8 Natillera, 9 Accesibilidad | Grupos 1 y 3 |

> El Grupo 4 tiene los módulos más complejos; el Grupo 1 puede apoyar con Auditoría una vez termine Auth y Usuarios.

---

## 6. Entidades principales

`Usuario`, `Rol`, `Producto`, `Categoria`, `Stock`, `Cliente`, `Venta`, `DetalleVenta`, `Pago`, `Socio`, `CicloNatillera`, `Aporte`, `Prestamo`, `Auditoria`.

---

## 7. Endpoints de ejemplo

```
POST   /api/auth/login
GET    /api/productos
POST   /api/ventas
GET    /api/clientes/{id}/saldo
POST   /api/natillera/aportes
POST   /api/natillera/prestamos
GET    /api/natillera/socios/{id}/estado-cuenta
GET    /api/accesibilidad/resumen/{tipo}/{id}
```

---

## 8. Entregables

1. API REST documentada (Swagger)
2. Script de base de datos
3. Pruebas unitarias básicas por módulo
4. Manual de uso en lenguaje sencillo

---

## 9. Convenciones del equipo

- Ramas: `main`, `develop`, `feature/<modulo>-<descripcion>`
- Commits en español con prefijo (`feat:`, `fix:`, `docs:`)
- Pull Request revisado por al menos un compañero
- Nombres de clases consistentes en todo el proyecto

---

## 10. Consideraciones

- Validar con el cliente las reglas de la natillera (intereses, multas, fecha de liquidación) antes de codificar.
- Proteger los datos personales de los socios (Ley 1581 de 2012).
