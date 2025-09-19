# Prueba Técnica – Backend Node.js (Simplificada – 2 días)

## 🎯 Objetivo
Desarrollar una API REST para la gestión de **abogados** y **demandas** en un bufete.
Se evaluará arquitectura, manejo de base de datos, autenticación y buenas prácticas de desarrollo.

---

## 🛠️ Requerimientos Técnicos

**Obligatorios**
- Node.js (v18+), Express.js
- PostgreSQL (con Docker Compose)
- Sequelize como ORM
- JWT para autenticación básica
- Validación de datos y manejo centralizado de errores
- Git para control de versiones

**Deseables (Opcional)**
- Tests unitarios (Jest)
- Logs estructurados
- Documentación con Swagger o Postman

---

## 📂 Modelos de Datos

### Abogado (Lawyer)
```json
{
  "id": "uuid",
  "name": "string",
  "email": "string",
  "phone": "string",
  "specialization": "string",
  "status": "active | inactive",
  "created_at": "datetime",
  "updated_at": "datetime"
}
```

### Demanda (Lawsuit)
```json
{
  "id": "uuid",
  "case_number": "string",
  "plaintiff": "string",
  "defendant": "string",
  "case_type": "civil | criminal | labor | commercial",
  "status": "pending | assigned | resolved",
  "lawyer_id": "uuid | null",
  "created_at": "datetime",
  "updated_at": "datetime"
}
```

### Usuario (User)
(para login con JWT, se pueden hardcodear o crear con seeders)

```json
{
  "id": "uuid",
  "username": "string",
  "password": "string (hashed)",
  "role": "admin | operator",
  "created_at": "datetime",
  "updated_at": "datetime"
}
```

## 📌 Endpoints Requeridos

### 1. Autenticación
- `POST /auth/login` → devuelve JWT válido.

### 2. Gestión de Abogados
- `POST /lawyers` → crear abogado
- `GET /lawyers?page=1&limit=10` → listar abogados (paginado)
- `GET /lawyers/:id` → obtener abogado por ID

### 3. Gestión de Demandas
- `POST /lawsuits` → crear demanda
- `GET /lawsuits` → listar demandas (con filtros opcionales status y lawyer_id)
- `PUT /lawsuits/:id/assign` → asignar abogado a una demanda

### 4. Reportes
- `GET /reports/lawyers/:id/lawsuits` → listado de demandas por abogado

## 🧪 Payloads de Ejemplo

### Login
```json
{
  "username": "admin",
  "password": "admin123"
}
```

### Crear Abogado
```json
{
  "name": "Carlos Pérez",
  "email": "carlos.perez@example.com",
  "phone": "3001234567",
  "specialization": "Laboral",
  "status": "active"
}
```

### Crear Demanda
```json
{
  "case_number": "DEM-2025-001",
  "plaintiff": "Empresa XYZ",
  "defendant": "Juan Rodríguez",
  "case_type": "labor",
  "status": "pending"
}
```

### Asignar Abogado a Demanda
```json
{
  "lawyer_id": "uuid-del-abogado"
}
```

### Reporte de Demandas por Abogado (respuesta esperada)
```json
{
  "lawyer": {
    "id": "uuid-del-abogado",
    "name": "Carlos Pérez"
  },
  "lawsuits": [
    {
      "id": "uuid-demanda",
      "case_number": "DEM-2025-001",
      "status": "assigned"
    }
  ]
}
```

## ✅ Entregables
- Repositorio en GitHub con el código fuente
- Migraciones y seeders con datos de prueba
- Docker Compose funcional para PostgreSQL
- README con instrucciones claras para instalar y ejecutar
- Documentación de API (Swagger o Postman)

## 📅 Tiempo de Entrega
2 días calendario desde la asignación.

## 📝 Evaluación
- Arquitectura y calidad de código
- Correcta implementación de endpoints y manejo de errores
- Uso de Sequelize y PostgreSQL con migraciones
- JWT y middleware de seguridad funcionando
- Documentación mínima (README + Postman/Swagger)
- Extras (para destacar): tests unitarios, logs estructurados.