# Documentación Final – Microservicio de Transacciones (CraftYourStyle)

**Estado:** Aprobado (CRUD probado en Docker y Postman)

---

## 1. Descripción General
El microservicio de **Transacciones** gestiona los **métodos de pago (cuentas bancarias)** de los usuarios del e‑commerce CraftYourStyle. Implementa operaciones CRUD completas, validaciones en el controlador, acceso a datos vía repositorio y ejecución en contenedor Docker.

**Funciones principales**
- Registro de cuentas (débito / crédito)
- Consulta de cuentas por usuario
- Actualización parcial de datos
- Eliminación segura por usuario
- Integración (mock temporal) con microservicio de usuarios

**Tecnologías**
- Node.js + Express
- TypeScript
- MySQL (mysql2)
- Docker
- Postman (pruebas)

**Puerto:** 10101

---

## 2. Arquitectura

```
Cliente HTTP (Postman / Frontend)
        ↓
Routes (Express)
        ↓
Controller (validaciones + reglas)
        ↓
Repository (SQL)
        ↓
MySQL
```

- **Controller:** valida entradas, maneja errores y respuestas HTTP.
- **Repository:** encapsula las consultas SQL.
- **DTO:** define la estructura de datos.

---

## 3. Estructura del Proyecto

```
transacciones/
├── index.ts
├── config/
│   └── db-config.ts
├── DTO/
│   └── transaccionesDto.ts
├── repository/
│   └── transaccionesRepository.ts
├── controllers/
│   └── transaccionesController.ts
├── routes/
│   └── transaccionesRoutes.ts
├── CraftYourStyle-Transacciones.sql
├── Dockerfile
├── package.json
└── tsconfig.json
```

---

## 4. Modelo de Datos

### Tabla: `transacciones`
| Campo | Tipo | Descripción |
|------|------|-------------|
| id | INT (PK, AI) | Identificador de la cuenta |
| numero_de_cuenta | VARCHAR(100) | Número de cuenta |
| tipo_de_cuenta | ENUM('debito','credito') | Tipo de cuenta |
| banco | VARCHAR(50) | Banco |
| id_user | INT | Usuario propietario |

---

## 5. Variables de Entorno (.env)

```
DB_HOST=host.docker.internal
DB_USER=root
DB_PASSWORD=
DB_NAME=craftyourstyle_transacciones
DB_PORT=3306
PORT=10101
```

---

## 6. Endpoints

**Base URL**
```
http://localhost:10101/transacciones
```

### 6.1 Crear Cuenta
- **POST** `/crearCuenta`

**Body**
```json
{
  "numero_de_cuenta": "1234567890",
  "tipo_de_cuenta": "debito",
  "banco": "Bancolombia",
  "id_user": 1
}
```

**Respuestas**
- **201**
```json
{ "message": "Transacción creada correctamente" }
```
- **400** (duplicado / validación)
```json
{ "message": "Esta cuenta ya existe" }
```

---

### 6.2 Obtener Cuentas por Usuario
- **GET** `/obtenerCuentas/:id_user`

**Respuesta 200**
```json
{
  "usuario": {
    "id": 1,
    "nombre": "Usuario Prueba",
    "email": "usuario@prueba.com"
  },
  "cuentas": [
    {
      "id": 1,
      "numero_de_cuenta": "1234567890",
      "tipo_de_cuenta": "debito",
      "banco": "Bancolombia",
      "id_user": 1
    }
  ]
}
```

> **Nota:** Para pruebas locales se usa **mock** del microservicio de usuarios.

---

### 6.3 Actualizar Cuenta (parcial)
- **PATCH** `/actualizarCuenta/:id_user/:id`

**Body (ejemplo)**
```json
{ "banco": "Davivienda" }
```

**Respuesta 200**
```json
{
  "message": "User updated successfully",
  "user": { "result": { "affectedRows": 1 }, "banco": "Davivienda" }
}
```

---

### 6.4 Eliminar Cuenta
- **DELETE** `/eliminarCuenta/:id/:id_user`

**Respuesta 200**
```json
{ "message": "Cuenta eliminada correctamente" }
```

---

## 7. Validaciones Implementadas
- Campos obligatorios en creación.
- `tipo_de_cuenta` solo admite `debito` o `credito`.
- Prevención de duplicados por `numero_de_cuenta`.
- Seguridad por `id_user` en UPDATE y DELETE.

---

## 8. Docker

**Build**
```bash
docker build -t transacciones-ms .
```

**Run**
```bash
docker run --env-file .env -p 10101:10101 transacciones-ms
```

---

## 9. Pruebas (Postman)

| Método | Endpoint | Resultado |
|------|----------|-----------|
| POST | /crearCuenta | ✅ OK |
| GET | /obtenerCuentas/:id_user | ✅ OK |
| PATCH | /actualizarCuenta/:id_user/:id | ✅ OK |
| DELETE | /eliminarCuenta/:id/:id_user | ✅ OK |

---

## 10. Resultados y Conclusiones
- CRUD completo probado exitosamente.
- Microservicio funcional en Docker.
- Conexión estable a MySQL.
- Arquitectura limpia y escalable.

**Conclusión:** El microservicio de Transacciones cumple con los requisitos funcionales y técnicos del proyecto CraftYourStyle y está listo para integración y despliegue.

---

**Autor:** Mauricio Morales
**Proyecto:** CraftYourStyle
**Fecha:** 2025-12-12

