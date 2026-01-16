# Prueba Técnica – Backend Node.js

## 🎯 Objetivo
Desarrollar una API REST para la gestión de **abogados** y **casos legales** en un bufete.
Se evaluará arquitectura de código, manejo de base de datos, operaciones transaccionales, autenticación y buenas prácticas de desarrollo.

---

## 🎯 Lo Más Importante
Buscamos evaluar:
1. **Arquitectura limpia** - código mantenible y escalable con separación de responsabilidades
2. **Manejo de errores** - casos felices Y casos de borde bien resueltos
3. **Operaciones transaccionales** - consistencia de datos en operaciones complejas
4. **Buenas prácticas** - validaciones, seguridad, legibilidad del código

**No es una carrera de velocidad.** Preferimos código bien pensado que una solución rápida y frágil.

---

## 🛠️ Requerimientos Técnicos

**Obligatorios**
- Node.js (v18+), Express.js
- PostgreSQL (con Docker Compose)
- Sequelize como ORM
- JWT para autenticación básica
- Validación de datos (joi, zod, etc)
- Manejo centralizado de errores
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
  "name": "string (requerido)",
  "email": "string (único, requerido)",
  "phone": "string",
  "specialization": "string",
  "status": "active | inactive",
  "created_at": "datetime",
  "updated_at": "datetime"
}
```

### Caso Legal (LegalCase)
```json
{
  "id": "uuid",
  "case_number": "string (único, requerido)",
  "plaintiff": "string (requerido)",
  "defendant": "string (requerido)",
  "case_type": "civil | criminal | labor | commercial",
  "status": "pending | assigned | in_progress | resolved",
  "description": "text",
  "lawyer_id": "uuid | null",
  "created_at": "datetime",
  "updated_at": "datetime"
}
```

### Usuario (User)
```json
{
  "id": "uuid",
  "username": "string (único, requerido)",
  "password": "string (hashed con bcrypt)",
  "role": "admin | operator",
  "is_active": "boolean",
  "created_at": "datetime",
  "updated_at": "datetime"
}
```

## 📌 Endpoints Requeridos

### 1. Autenticación
- `POST /api/auth/login` 
  - Body: `{ "username": "string", "password": "string" }`
  - Response: `{ "token": "jwt", "user": { "id", "username", "role" } }`

### 2. Gestión de Abogados
- `POST /api/lawyers` 
  - Crear abogado (requiere autenticación)
  - Validar email único y formato válido
  
- `GET /api/lawyers?page=1&limit=10&status=active`
  - Listar abogados con paginación y filtro opcional de status
  - Response incluye metadata de paginación
  
- `GET /api/lawyers/:id`
  - Obtener abogado por ID con sus casos asignados

### 3. Gestión de Demandas
- `POST /api/legal-cases`
  - Crear caso legal
  - Validar case_number único
  
- `GET /api/legal-cases?page=1&limit=10&status=pending&lawyer_id=uuid`
  - Listar casos con paginación y filtros opcionales
  
- `GET /api/legal-cases/:id`
  - Obtener caso por ID con información del abogado asignado
  
- `PUT /api/legal-cases/:id/assign`
  - Asignar abogado a un caso
  - Body: `{ "lawyer_id": "uuid" }`
  - Validar que el abogado exista y esté activo
  - Cambiar status del caso a "assigned"
  
- `PUT /api/legal-cases/:id/transfer`
  - Transferir caso de un abogado a otro **(usar transacción)**
  - Body: `{ "new_lawyer_id": "uuid" }`
  - Validar que ambos abogados existan y estén activos
  - Validar que el caso esté asignado actualmente
  - **Debe ser atómico:** si algo falla, hacer rollback completo

### 4. Reportes
- `GET /api/reports/lawyers/:id/cases`
  - Listado de casos asignados a un abogado
  - Incluir estadísticas básicas: total casos, casos por estado

## 🧪 Payloads de Ejemplo

### Login
```json
{
  "username": "admin",
  "password": "Admin123!"
}
```

### Crear Abogado
```json
{
  "name": "Carlos Pérez García",
  "email": "carlos.perez@bufete.com",
  "phone": "+57 300 123 4567",
  "specialization": "Derecho Laboral",
  "status": "active"
}
```

### Crear Caso Legal
```json
{
  "case_number": "CASE-2025-001",
  "plaintiff": "Empresa XYZ S.A.S.",
  "defendant": "Juan Rodríguez Martínez",
  "case_type": "labor",
  "description": "Demanda por despido injustificado",
  "status": "pending"
}
```

### Asignar Abogado a Caso
```json
{
  "lawyer_id": "550e8400-e29b-41d4-a716-446655440000"
}
```

### Transferir Caso
```json
{
  "new_lawyer_id": "660e8400-e29b-41d4-a716-446655440001"
}
```

### Respuesta Esperada: Reporte de Casos por Abogado
```json
{
  "lawyer": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Carlos Pérez García",
    "specialization": "Derecho Laboral"
  },
  "statistics": {
    "total_cases": 8,
    "by_status": {
      "assigned": 2,
      "in_progress": 4,
      "resolved": 2
    }
  },
  "cases": [
    {
      "id": "770e8400-e29b-41d4-a716-446655440002",
      "case_number": "CASE-2025-001",
      "plaintiff": "Empresa XYZ S.A.S.",
      "status": "in_progress",
      "case_type": "labor"
    }
  ]
}
```
---

## 📦 Datos de Prueba (Seeders)

### Requeridos:
- **Usuarios:** 
  - 1 admin: `username: admin, password: Admin123!`
  - 1 operator: `username: operator, password: Oper123!`
  
- **Abogados:** 
  - 5 abogados (3 activos, 2 inactivos)
  - Variedad de especializaciones
  
- **Casos Legales:** 
  - 10 casos con diferentes estados y tipos
  - Al menos 3 casos sin asignar (pending)
  - Al menos 5 casos asignados a diferentes abogados

---

## ✅ Entregables

### 1. Repositorio en GitHub
- Código fuente limpio y bien organizado
- `.gitignore` configurado (node_modules, .env)
- Commits descriptivos

### 2. Base de Datos
- Migraciones de Sequelize funcionando
- Seeders con datos de prueba
- Script para resetear BD: `npm run db:reset`

### 3. Docker Compose
- PostgreSQL configurado y funcional
- Comando único para levantar todo: `docker-compose up`

### 4. README Completo
Debe incluir:
```markdown
# Nombre del Proyecto

## Prerequisitos
- Node.js 18+
- Docker y Docker Compose

## Instalación
1. Clonar repositorio
2. npm install
3. Configurar .env (incluir .env.example)
4. docker-compose up -d
5. npm run db:migrate
6. npm run db:seed

## Ejecución
- Desarrollo: npm run dev
- Producción: npm start

## Testing (si aplica)
- npm test

## Endpoints
Ver colección de Postman o /api/docs
```

### 5. Documentación de API
- Colección de Postman exportada, O
- Swagger/OpenAPI en `/api/docs`

---

## 📅 Tiempo de Entrega

**18 de enero de 2026** antes de las 11:59 pm

---

## 💡 Consejos

1. **Lee toda la prueba antes de empezar**
2. **Prioriza que funcione** antes de optimizar
3. **El endpoint de transferencia es clave** - demuestra tu conocimiento de transacciones
4. **Documenta mientras desarrollas** - no lo dejes para el final

---

## 📞 Contacto

Si tenés dudas sobre la prueba, podés contactarnos a: [contacto@legalsuitelatam.com]

**¡Éxito con la prueba! Esperamos ver código del que estés orgulloso.** 🚀

---
