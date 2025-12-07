# 🏗️ Arquitectura de la Aplicación de Gestión de Tareas

## 📋 Tabla de Contenidos

1. [Visión General](#visión-general)
2. [Arquitectura de Alto Nivel](#arquitectura-de-alto-nivel)
3. [Stack Tecnológico](#stack-tecnológico)
4. [Estructura del Proyecto](#estructura-del-proyecto)
5. [Backend - API REST](#backend---api-rest)
6. [Frontend - React](#frontend---react)
7. [Base de Datos - SQLite](#base-de-datos---sqlite)
8. [Testing](#testing)
9. [CI/CD Pipeline](#cicd-pipeline)
10. [Flujo de Datos](#flujo-de-datos)
11. [Decisiones de Diseño](#decisiones-de-diseño)
12. [Seguridad](#seguridad)
13. [Escalabilidad y Mejoras Futuras](#escalabilidad-y-mejoras-futuras)

---

## 🎯 Visión General

Esta aplicación es un **sistema de gestión de tareas** (To-Do List) que implementa una arquitectura **cliente-servidor** con separación clara de responsabilidades. El frontend nunca accede directamente a la base de datos; toda la comunicación se realiza a través de una API REST.

### Características Principales

- ✅ **CRUD completo** de tareas (Crear, Leer, Actualizar, Eliminar)
- ✅ **Filtros avanzados** (prioridad, estado, categoría, búsqueda)
- ✅ **Validaciones de negocio** (títulos únicos, límite de tareas alta prioridad)
- ✅ **Dashboard con métricas** (resumen, próximos vencimientos, categorías)
- ✅ **Testing completo** (unitarios, integración, E2E)
- ✅ **CI/CD automatizado** con Azure DevOps

---

## 🏛️ Arquitectura de Alto Nivel

```
┌─────────────────────────────────────────────────────────────┐
│                        USUARIO                               │
└───────────────────────────┬─────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    FRONTEND (React)                        │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Puerto: 5173                                        │  │
│  │  - Componente App.jsx                                │  │
│  │  - Estado local (useState)                          │  │
│  │  - Peticiones HTTP (axios)                          │  │
│  └──────────────────────────────────────────────────────┘  │
└───────────────────────────┬─────────────────────────────────┘
                           │ HTTP/REST API
                           │ (JSON)
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                   BACKEND (Node.js/Express)                │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Puerto: 3001                                        │  │
│  │  - Rutas API REST                                    │  │
│  │  - Validaciones                                      │  │
│  │  - Lógica de negocio                                 │  │
│  │  - Middleware (CORS, JSON parser)                   │  │
│  └──────────────────────────────────────────────────────┘  │
└───────────────────────────┬─────────────────────────────────┘
                           │ SQL Queries
                           │ (Prepared Statements)
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                  BASE DE DATOS (SQLite)                    │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Archivo: database.sqlite                           │  │
│  │  - Tabla: tareas                                     │  │
│  │  - Sin servidor (archivo local)                     │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### Principios Arquitectónicos

1. **Separación de Responsabilidades**: Frontend (UI), Backend (lógica), DB (persistencia)
2. **API RESTful**: Comunicación mediante HTTP estándar
3. **Stateless Backend**: Cada request es independiente
4. **Single Page Application (SPA)**: React maneja el routing interno
5. **Database-First**: SQLite como fuente única de verdad

---

## 🛠️ Stack Tecnológico

### Frontend

| Tecnología | Versión | Propósito |
|------------|---------|-----------|
| **React** | 18.2.0 | Framework UI (componentes, estado, hooks) |
| **Vite** | Latest | Build tool y dev server (HMR, bundling) |
| **Axios** | 1.6.0 | Cliente HTTP para llamadas a la API |
| **Vitest** | Latest | Framework de testing unitario |
| **Testing Library** | 14.3.1 | Utilidades para testing de componentes |
| **Cypress** | Latest | Testing end-to-end (E2E) |

### Backend

| Tecnología | Versión | Propósito |
|------------|---------|-----------|
| **Node.js** | 20.x | Runtime de JavaScript |
| **Express** | 4.18.2 | Framework web (routing, middleware) |
| **SQLite3** | 5.1.6 | Driver para base de datos SQLite |
| **CORS** | 2.8.5 | Middleware para permitir requests cross-origin |
| **Jest** | 29.7.0 | Framework de testing |
| **Supertest** | 6.3.3 | Testing de APIs HTTP |

### DevOps

| Tecnología | Propósito |
|------------|-----------|
| **Azure DevOps** | CI/CD pipeline |
| **SonarCloud** | Análisis estático de código |
| **Jest JUnit** | Reportes de tests para Azure |
| **Coverage Reports** | Cobertura de código (lcov, cobertura) |

---

## 📁 Estructura del Proyecto

```
TP6-y-TP7/
├── backend/
│   ├── server.js                 # Servidor Express principal
│   ├── package.json              # Dependencias backend
│   ├── jest.config.js            # Configuración Jest
│   ├── database.sqlite           # Base de datos (se crea automáticamente)
│   ├── tests/
│   │   ├── setupTests.js         # Configuración global de tests
│   │   ├── validators.test.js    # Tests unitarios de validaciones
│   │   └── tasks.routes.test.js  # Tests de integración (API)
│   ├── coverage/                 # Reportes de cobertura
│   └── reports/                   # Reportes JUnit para CI/CD
│
├── frontend/
│   ├── src/
│   │   ├── App.jsx               # Componente principal
│   │   ├── App.css               # Estilos del componente
│   │   ├── main.jsx              # Punto de entrada React
│   │   ├── index.css             # Estilos globales
│   │   └── __tests__/
│   │       └── App.test.jsx      # Tests unitarios del frontend
│   ├── cypress/
│   │   └── e2e/
│   │       └── tareas.cy.js      # Tests E2E con Cypress
│   ├── package.json              # Dependencias frontend
│   ├── vite.config.js            # Configuración Vite
│   └── vitest.config.ts          # Configuración Vitest
│
├── azure-pipelines.yml           # Pipeline CI/CD
├── sonar-project.properties      # Configuración SonarCloud
└── package.json                  # Scripts raíz (start-backend, start-frontend)
```

---

## 🔧 Backend - API REST

### Arquitectura del Backend

El backend está implementado en un **archivo monolítico** (`server.js`) que contiene:

1. **Configuración del servidor** (Express, CORS, JSON parser)
2. **Inicialización de la base de datos** (creación de tabla, migraciones)
3. **Funciones de validación** (pure functions, exportables)
4. **Funciones de negocio** (verificación de duplicados, conteos)
5. **Rutas API REST** (endpoints HTTP)

### Endpoints Disponibles

| Método | Ruta | Descripción | Códigos de Respuesta |
|--------|------|-------------|---------------------|
| `GET` | `/api/tareas` | Listar tareas (con filtros) | 200, 400, 500 |
| `GET` | `/api/tareas/resumen` | Obtener métricas agregadas | 200, 500 |
| `GET` | `/api/tareas/:id` | Obtener una tarea específica | 200, 404, 500 |
| `POST` | `/api/tareas` | Crear nueva tarea | 201, 400, 409, 422, 500 |
| `PUT` | `/api/tareas/:id` | Actualizar tarea existente | 200, 400, 404, 409, 422, 500 |
| `DELETE` | `/api/tareas/:id` | Eliminar tarea | 200, 404, 500 |
| `GET` | `/health` | Health check del servidor | 200 |

### Parámetros de Consulta (Query Parameters)

El endpoint `GET /api/tareas` soporta múltiples filtros:

- `prioridad`: `alta` | `media` | `baja`
- `estado`: `pendientes` | `completadas`
- `vencidas`: `true` (solo tareas vencidas)
- `orden`: `vencimiento_asc` | `vencimiento_desc`
- `q`: Búsqueda por título o descripción (LIKE)
- `categoria`: Filtro exacto por categoría
- `favoritas`: `true` (solo tareas marcadas como favoritas)

### Validaciones de Negocio

1. **Título único**: No se permiten títulos duplicados (case-insensitive)
2. **Límite de prioridad alta**: Máximo 5 tareas de prioridad alta pendientes
3. **Fecha de vencimiento**: No puede estar en el pasado
4. **Longitudes máximas**:
   - Descripción: 200 caracteres
   - Categoría: 30 caracteres

### Funciones Exportadas (Testeables)

```javascript
// Funciones puras (sin dependencias)
- normalizarPrioridad(valor)
- parsearFechaVencimiento(valor)
- validarPayloadTarea(payload)
- mapearRow(row)

// Funciones con dependencias (requieren mocks)
- verificarTituloDuplicado(db, options, callback)
- contarTareasPrioridadAltaPendientes(db, options, callback)
```

### Inicialización de la Base de Datos

El servidor implementa **migraciones automáticas**:

1. Crea la tabla `tareas` si no existe
2. Agrega columnas faltantes (prioridad, fecha_vencimiento, categoria, favorita)
3. Maneja errores de columnas duplicadas (idempotente)

---

## ⚛️ Frontend - React

### Arquitectura del Frontend

El frontend es una **Single Page Application (SPA)** con un único componente principal (`App.jsx`) que maneja:

1. **Estado local** con `useState`:
   - Lista de tareas
   - Formulario de nueva tarea
   - Filtros y búsqueda
   - Resumen y métricas
   - Estados de carga y error

2. **Efectos secundarios** con `useEffect`:
   - Carga inicial de datos
   - Recarga cuando cambian los filtros

3. **Callbacks optimizados** con `useCallback`:
   - Construcción de queries
   - Carga de tareas y resumen

### Flujo de Datos en el Frontend

```
Usuario interactúa
    ↓
Evento (click, submit, change)
    ↓
Handler (agregarTarea, toggleCompletada, etc.)
    ↓
Llamada HTTP (axios.get/post/put/delete)
    ↓
Actualización de estado (setTareas, setResumen)
    ↓
Re-render automático (React)
    ↓
UI actualizada
```

### Componentes y Funciones

**Componente Principal**: `App.jsx`
- Renderiza toda la UI
- Maneja toda la lógica de negocio del frontend
- No hay componentes hijos (diseño monolítico intencional)

**Funciones Utilitarias**:
- `construirQuery()`: Construye query string para filtros
- `prioridadBadge()`: Retorna clase CSS según prioridad
- `formatearFecha()`: Formatea fechas para mostrar

**Funciones de API**:
- `cargarTareas()`: GET /api/tareas
- `cargarResumen()`: GET /api/tareas/resumen
- `agregarTarea()`: POST /api/tareas
- `toggleCompletada()`: PUT /api/tareas/:id
- `toggleFavorita()`: PUT /api/tareas/:id
- `eliminarTarea()`: DELETE /api/tareas/:id

### Configuración de Vite

- **Puerto**: 5173
- **Proxy**: `/api` → `http://localhost:3001` (para desarrollo)
- **HMR**: Hot Module Replacement activado
- **Build**: Optimización para producción

---

## 🗄️ Base de Datos - SQLite

### Características de SQLite

- **Sin servidor**: Base de datos embebida (archivo único)
- **ACID**: Transacciones completas
- **SQL estándar**: Compatible con SQL estándar
- **Portable**: Un solo archivo (`database.sqlite`)

### Esquema de la Tabla `tareas`

```sql
CREATE TABLE tareas (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  titulo TEXT NOT NULL,
  descripcion TEXT,
  completada INTEGER DEFAULT 0,
  fecha_creacion DATETIME DEFAULT CURRENT_TIMESTAMP,
  prioridad TEXT DEFAULT 'media',
  fecha_vencimiento DATETIME,
  categoria TEXT,
  favorita INTEGER DEFAULT 0
);
```

### Tipos de Datos

| Columna | Tipo | Descripción |
|---------|------|-------------|
| `id` | INTEGER | Clave primaria autoincremental |
| `titulo` | TEXT | Título de la tarea (obligatorio, único) |
| `descripcion` | TEXT | Descripción opcional (máx. 200 caracteres) |
| `completada` | INTEGER | 0 = pendiente, 1 = completada |
| `fecha_creacion` | DATETIME | Timestamp automático de creación |
| `prioridad` | TEXT | `alta`, `media`, `baja` (default: `media`) |
| `fecha_vencimiento` | DATETIME | Fecha opcional (debe ser futura) |
| `categoria` | TEXT | Categoría opcional (máx. 30 caracteres) |
| `favorita` | INTEGER | 0 = no favorita, 1 = favorita |

### Índices y Constraints

- **PRIMARY KEY**: `id` (auto-incremental)
- **NOT NULL**: `titulo`
- **UNIQUE**: `titulo` (validado en aplicación, no en DB)
- **DEFAULT VALUES**: `completada = 0`, `prioridad = 'media'`, `favorita = 0`

### Operaciones SQL Utilizadas

- **SELECT**: Consultas con filtros, ordenamiento, agregaciones
- **INSERT**: Creación de nuevas tareas
- **UPDATE**: Actualización de tareas existentes
- **DELETE**: Eliminación de tareas
- **COUNT**: Conteos para validaciones y métricas
- **LIKE**: Búsqueda por texto (case-insensitive)

---

## 🧪 Testing

### Estrategia de Testing

La aplicación implementa una **pirámide de testing** con tres niveles:

```
        /\
       /  \  E2E (Cypress)
      /____\
     /      \  Integración (Supertest)
    /________\
   /          \  Unitarios (Jest/Vitest)
  /____________\
```

### 1. Tests Unitarios

**Backend** (`backend/tests/validators.test.js`):
- **Funciones puras**: `normalizarPrioridad`, `parsearFechaVencimiento`, `validarPayloadTarea`
- **Sin mocks**: Funciones puras sin dependencias
- **Cobertura**: Validaciones, casos edge, normalizaciones

**Frontend** (`frontend/src/__tests__/App.test.jsx`):
- **Funciones utilitarias**: `formatearFecha`, `prioridadBadge`, `construirQuery`
- **Mocks**: `vi.useFakeTimers()` para fechas, `vi.mock('axios')` para API
- **Cobertura**: Lógica de UI, formateo, construcción de queries

**Patrón utilizado**: **AAA (Arrange-Act-Assert)**

```javascript
// Ejemplo de test unitario
test('normalizarPrioridad - convierte a minúsculas', () => {
  // Arrange
  const input = 'ALTA';
  
  // Act
  const result = normalizarPrioridad(input);
  
  // Assert
  expect(result).toBe('alta');
});
```

### 2. Tests de Integración

**Backend** (`backend/tests/tasks.routes.test.js`):
- **Herramienta**: Supertest (simula requests HTTP)
- **Base de datos**: SQLite en memoria (`:memory:`)
- **Cobertura**: Endpoints completos, flujos CRUD, validaciones de negocio

**Características**:
- Base de datos aislada por test
- Setup/teardown automático
- Tests independientes (no comparten estado)

```javascript
// Ejemplo de test de integración
test('POST /api/tareas - crea tarea exitosamente', async () => {
  // Arrange
  const nuevaTarea = { titulo: 'Test', prioridad: 'media' };
  
  // Act
  const response = await request(app)
    .post('/api/tareas')
    .send(nuevaTarea);
  
  // Assert
  expect(response.status).toBe(201);
  expect(response.body.titulo).toBe('Test');
});
```

### 3. Tests End-to-End (E2E)

**Frontend** (`frontend/cypress/e2e/tareas.cy.js`):
- **Herramienta**: Cypress
- **Cobertura**: Flujos completos de usuario
- **Escenarios**: Crear tarea, marcar como favorita, completar tarea

**Características**:
- Ejecuta en navegador real
- Interactúa con la UI completa
- Valida comportamiento end-to-end

### Configuración de Tests

**Backend (Jest)**:
- Timeout: 10 segundos (global), 5 segundos (tests async individuales)
- Environment: `node`
- Reporters: `default`, `jest-junit` (para CI/CD)
- Coverage: `lcov`, `cobertura`, `text`
- Setup: `setupTests.js` (timezone UTC, timeouts)

**Frontend (Vitest)**:
- Environment: `jsdom` (simula DOM)
- Coverage: `v8` provider
- Reporters: `text`, `lcov`, `cobertura`

### Cobertura de Código

- **Backend**: ~80% (statements, branches, functions, lines)
- **Frontend**: Cobertura de funciones utilitarias y lógica de componentes
- **Reportes**: Generados en formato `lcov` y `cobertura` para CI/CD

---

## 🚀 CI/CD Pipeline

### Azure DevOps Pipeline

El pipeline está configurado en `azure-pipelines.yml` y ejecuta las siguientes etapas:

#### 1. Build Stage

**Análisis Estático (SonarCloud)**:
- Preparación de análisis
- Ejecución de análisis
- Publicación de resultados

**Backend**:
- Instalación de dependencias (`npm ci`)
- Ejecución de tests (`npm run test:ci`)
- Generación de reportes:
  - JUnit XML (`reports/junit/backend-junit.xml`)
  - Coverage (`coverage/cobertura-coverage.xml`, `coverage/lcov.info`)

**Frontend**:
- Instalación de dependencias
- Ejecución de tests (`npm run test:ci`)
- Generación de reportes de cobertura

**Publicación de Artifacts**:
- Frontend build
- Backend (código fuente)

#### 2. Deploy Stage (Opcional)

- Despliegue a Azure App Service
- Configuración de variables de entorno
- Health checks

### Configuración del Pipeline

**Variables**:
- `NODE_VERSION`: `20.x`
- `FRONT_DIR`: `frontend`
- `BACK_DIR`: `backend`
- SonarCloud: Service connection, organización, proyecto

**Triggers**:
- Push a `main`, `master`, `develop`
- Pull requests a `main`, `master`, `develop`

**Reportes Generados**:
- **JUnit XML**: Para visualización de resultados en Azure
- **Coverage (Cobertura)**: Para análisis de cobertura
- **SonarCloud**: Para análisis estático de código

---

## 🔄 Flujo de Datos

### Ejemplo Completo: Crear una Tarea

```
1. USUARIO
   └─> Completa formulario en React
       - Título: "Comprar leche"
       - Prioridad: "alta"
       - Categoría: "Personal"

2. FRONTEND (App.jsx)
   └─> Usuario hace submit
       └─> Handler: agregarTarea(e)
           └─> Valida título no vacío
               └─> Normaliza fecha_vencimiento
                   └─> Construye payload JSON
                       └─> axios.post('http://localhost:3001/api/tareas', payload)

3. RED (HTTP Request)
   └─> POST http://localhost:3001/api/tareas
       Headers: Content-Type: application/json
       Body: {
         "titulo": "Comprar leche",
         "prioridad": "alta",
         "categoria": "Personal"
       }

4. BACKEND (server.js)
   └─> Express recibe request
       └─> Middleware CORS permite request
           └─> Middleware JSON parser extrae body
               └─> Ruta: app.post('/api/tareas', ...)
                   └─> validarPayloadTarea(req.body)
                       └─> Retorna: { data: {...}, error: null }
                           └─> verificarTituloDuplicado(db, {...})
                               └─> SQL: SELECT id FROM tareas WHERE lower(titulo) = lower(?)
                                   └─> Retorna: false (no existe)
                                       └─> contarTareasPrioridadAltaPendientes(db, {...})
                                           └─> SQL: SELECT COUNT(*) FROM tareas WHERE prioridad = 'alta' AND completada = 0
                                               └─> Retorna: 3 (< 5, OK)
                                                   └─> db.run('INSERT INTO tareas ...')
                                                       └─> SQLite escribe en database.sqlite
                                                           └─> Retorna: lastID = 42

5. BACKEND (server.js) - Respuesta
   └─> res.status(201).json({
         id: 42,
         titulo: "Comprar leche",
         prioridad: "alta",
         categoria: "Personal",
         completada: 0,
         fecha_creacion: "2025-12-04T..."
       })

6. RED (HTTP Response)
   └─> Status: 201 Created
       Headers: Content-Type: application/json
       Body: { id: 42, ... }

7. FRONTEND (App.jsx)
   └─> axios.post() resuelve con response.data
       └─> cargarTodo() (recarga tareas y resumen)
           └─> axios.get('/api/tareas')
               └─> Actualiza estado: setTareas([...])
                   └─> React re-renderiza
                       └─> UI muestra nueva tarea

8. USUARIO
   └─> Ve la nueva tarea en la lista
```

### Flujo de Filtros y Búsqueda

```
1. Usuario cambia filtro (ej: prioridad = "alta")
   └─> actualizarFiltro('prioridad', 'alta')
       └─> setFiltros({ ...filtros, prioridad: 'alta' })
           └─> useEffect detecta cambio en filtros
               └─> cargarTareas()
                   └─> construirQuery()
                       └─> Retorna: "?prioridad=alta"
                           └─> axios.get('/api/tareas?prioridad=alta')
                               └─> Backend filtra en SQL
                                   └─> Retorna tareas filtradas
                                       └─> setTareas(tareasFiltradas)
                                           └─> UI actualizada
```

---

## 🎨 Decisiones de Diseño

### 1. Arquitectura Monolítica (Backend)

**Decisión**: Un solo archivo `server.js` con toda la lógica.

**Razones**:
- ✅ Simplicidad para proyecto pequeño
- ✅ Fácil de entender y mantener
- ✅ Sin overhead de estructura compleja

**Trade-offs**:
- ⚠️ Puede crecer y volverse difícil de mantener
- ⚠️ No escala bien para equipos grandes

**Mejora futura**: Separar en módulos (routes, services, repositories)

### 2. Componente Único (Frontend)

**Decisión**: Un solo componente `App.jsx` sin componentes hijos.

**Razones**:
- ✅ Simplicidad para aplicación pequeña
- ✅ Estado centralizado fácil de manejar
- ✅ Sin prop drilling

**Trade-offs**:
- ⚠️ Componente grande (590 líneas)
- ⚠️ Dificulta reutilización

**Mejora futura**: Extraer componentes (TaskCard, TaskForm, Filters, Dashboard)

### 3. SQLite como Base de Datos

**Decisión**: SQLite en lugar de PostgreSQL/MySQL.

**Razones**:
- ✅ Sin servidor (fácil de configurar)
- ✅ Archivo único (portable, fácil de respaldar)
- ✅ Suficiente para aplicación pequeña/mediana
- ✅ ACID completo

**Trade-offs**:
- ⚠️ No soporta concurrencia alta
- ⚠️ No escala horizontalmente
- ⚠️ Sin usuarios/permisos

**Mejora futura**: Migrar a PostgreSQL para producción

### 4. Testing con Base de Datos en Memoria

**Decisión**: Tests de integración usan `:memory:` (SQLite en memoria).

**Razones**:
- ✅ Tests rápidos (sin I/O de disco)
- ✅ Aislamiento completo (cada test tiene DB limpia)
- ✅ No requiere setup/teardown de archivos

**Trade-offs**:
- ⚠️ Diferente comportamiento que archivo real (mínimo)

### 5. Validaciones en Backend

**Decisión**: Validaciones en backend, no solo en frontend.

**Razones**:
- ✅ Seguridad (frontend puede ser manipulado)
- ✅ Consistencia de datos
- ✅ API reutilizable

**Trade-offs**:
- ⚠️ Duplicación de lógica (frontend también valida para UX)

### 6. CORS Habilitado

**Decisión**: CORS habilitado para todos los orígenes.

**Razones**:
- ✅ Desarrollo local (frontend y backend en puertos diferentes)
- ✅ Simplicidad

**Trade-offs**:
- ⚠️ No seguro para producción (debe restringirse)

**Mejora futura**: Configurar CORS por origen en producción

---

## 🔒 Seguridad

### Medidas Implementadas

1. **Prepared Statements (SQL Injection)**:
   ```javascript
   // ✅ Seguro
   db.run('SELECT * FROM tareas WHERE id = ?', [id]);
   
   // ❌ Vulnerable (NO usado)
   db.run(`SELECT * FROM tareas WHERE id = ${id}`);
   ```

2. **Validación de Inputs**:
   - Longitudes máximas
   - Tipos de datos
   - Valores permitidos (prioridad, estado)

3. **Sanitización**:
   - `.trim()` en strings
   - Normalización de valores (prioridad a minúsculas)

### Mejoras de Seguridad Recomendadas

1. **Autenticación y Autorización**:
   - JWT tokens
   - Middleware de autenticación
   - Roles y permisos

2. **Rate Limiting**:
   - Limitar requests por IP
   - Prevenir abuso de API

3. **HTTPS**:
   - Certificados SSL/TLS
   - Encriptación en tránsito

4. **Variables de Entorno**:
   - Secrets en variables de entorno
   - No hardcodear credenciales

5. **CORS Restrictivo**:
   - Solo orígenes permitidos en producción

6. **Validación de Esquema**:
   - Librerías como Joi o Yup
   - Validación más robusta

---

## 📈 Escalabilidad y Mejoras Futuras

### Limitaciones Actuales

1. **Base de Datos**: SQLite no escala horizontalmente
2. **Backend Monolítico**: Un solo proceso Node.js
3. **Frontend Monolítico**: Un solo bundle
4. **Sin Caché**: Cada request va a la base de datos
5. **Sin Paginación**: Todas las tareas se cargan a la vez

### Mejoras Propuestas

#### Corto Plazo

1. **Paginación**:
   - `GET /api/tareas?page=1&limit=20`
   - Mejora rendimiento con muchas tareas

2. **Componentes React**:
   - Extraer `TaskCard`, `TaskForm`, `Filters`
   - Mejor mantenibilidad

3. **Módulos Backend**:
   - Separar en `routes/`, `services/`, `repositories/`
   - Mejor organización

#### Mediano Plazo

1. **Migración a PostgreSQL**:
   - Base de datos más robusta
   - Mejor para producción

2. **Autenticación**:
   - JWT tokens
   - Usuarios y sesiones

3. **Caché**:
   - Redis para queries frecuentes
   - Reducir carga en DB

#### Largo Plazo

1. **Microservicios**:
   - Separar servicios (tasks, users, notifications)
   - Escalabilidad independiente

2. **Frontend Optimizado**:
   - Code splitting
   - Lazy loading
   - Service Workers (PWA)

3. **Monitoreo**:
   - Logging centralizado
   - Métricas (Prometheus)
   - Alertas

4. **CI/CD Avanzado**:
   - Deploy automático
   - Blue-green deployment
   - Rollback automático

---

## 📊 Métricas y Monitoreo

### Métricas Actuales

- **Cobertura de Tests**: ~80% (backend)
- **Tiempo de Build**: ~2-3 minutos (pipeline)
- **Tamaño del Bundle**: ~200KB (frontend, sin optimización)

### Métricas Recomendadas

1. **Performance**:
   - Tiempo de respuesta de API
   - Tiempo de carga de página
   - Throughput (requests/segundo)

2. **Calidad**:
   - Code coverage
   - Code smells (SonarCloud)
   - Deuda técnica

3. **Disponibilidad**:
   - Uptime
   - Error rate
   - Health checks

---

## 🎓 Conclusión

Esta aplicación implementa una **arquitectura cliente-servidor moderna** con:

- ✅ **Separación clara** de responsabilidades
- ✅ **API RESTful** bien diseñada
- ✅ **Testing completo** (unitarios, integración, E2E)
- ✅ **CI/CD automatizado** con Azure DevOps
- ✅ **Código mantenible** y bien estructurado

La arquitectura es **simple pero escalable**, diseñada para crecer según las necesidades del proyecto. Las decisiones tomadas priorizan la **simplicidad y mantenibilidad** sobre la complejidad prematura.

---

## 📚 Referencias y Recursos

- **Express.js**: https://expressjs.com/
- **React**: https://react.dev/
- **SQLite**: https://www.sqlite.org/
- **Jest**: https://jestjs.io/
- **Cypress**: https://www.cypress.io/
- **Azure DevOps**: https://azure.microsoft.com/services/devops/
- **SonarCloud**: https://sonarcloud.io/

---

**Documento generado para defensa oral del proyecto**
