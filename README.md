<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

## Proviemplea - API de vinculación laboral

Este proyecto es una API desarrollada con Laravel para apoyar un escenario de intermediación laboral entre personas y empresas. La idea principal es modelar un sistema donde:

- Una persona registra su perfil profesional.
- Una empresa publica su información y datos de contacto.
- La administración controla los contactos solicitados entre ambas partes.

Pensado en términos de ingeniería de software, el sistema resuelve un problema clásico de gestión de datos y flujos de negocio: almacenar información estructurada, validar registros, relacionar entidades y exponer operaciones claras mediante endpoints REST.

---

## De qué trata el proyecto

La aplicación funciona como una pequeña plataforma de gestión de talento. No se trata solo de guardar personas y empresas, sino de organizar el proceso completo de conexión entre ellas.

El sistema maneja tres piezas principales:

1. **Personas**: perfiles de postulantes o talento disponible.
2. **Empresas**: organizaciones que buscan contactar candidatos.
3. **Contactos solicitados**: registros que representan el interés de una empresa por una persona, con estado y fechas de seguimiento.

La administración puede revisar estos contactos, cambiar su estado y obtener estadísticas generales del sistema.

---

## Cómo está organizado

El proyecto sigue la arquitectura estándar de Laravel separada por responsabilidades:

- `routes/api.php` define los endpoints.
- `app/Http/Controllers` contiene la lógica de acceso y administración.
- `app/Models` representa las entidades principales del negocio.
- `app/Traits/ApiResponse.php` centraliza el formato de respuestas HTTP.
- `database/migrations` define la estructura de la base de datos.

Esta separación permite entender cómo una aplicación real divide el problema en capas: ruta, control, dominio y persistencia.

---

## Entidades principales

### Persona

Representa a una persona con perfil profesional. Guarda datos de contacto, formación, experiencia, competencias, modalidad de trabajo y otros atributos útiles para construir un CV ciego.

Un detalle importante de diseño: el modelo incluye un método `getCvCiego()` que devuelve solo la información profesional y omite los datos privados (como email y teléfono). Esto refleja una buena práctica: exponer solo lo necesario según el contexto, sin duplicar lógica en el controlador.

Además, al crear o actualizar una persona se calcula automáticamente el `porcentaje_completitud`, que mide qué tan completo está el perfil. Esto se hace en tiempo de escritura para no recalcularlo en cada lectura.

### Empresa

Representa a una organización con información institucional y de contacto. Incluye nombre, RUT, rubro, tipo de empresa, beneficios y datos del contacto responsable. El RUT y el email son únicos en la base de datos.

### ContactoSolicitado

Representa la relación entre una empresa y una persona. No es solo un enlace técnico: es un objeto de negocio que guarda el estado del proceso, notas administrativas y fechas importantes como contacto, entrevista y resultado. Las fechas se registran automáticamente según el estado que se asigne.

Los estados posibles son: `pendiente`, `contactado`, `entrevista`, `seleccionado`, `no-seleccionado`, `proceso-cerrado`.

---

## Diseño de las operaciones CRUD

### Decisiones de diseño relevantes

**Soft delete en lugar de eliminación física**: tanto en personas como en empresas, el endpoint `DELETE` no elimina el registro de la base de datos. En su lugar, desactiva el campo `activo`. Esto preserva la integridad referencial (por ejemplo, los contactos solicitados mantienen relación con empresas y personas históricas) y permite recuperar registros si fuera necesario.

**Endpoint de validación separado**: el flujo de validación (`PATCH /validar`) fue diseñado como una acción de negocio separada del CRUD estándar, porque la validación es una responsabilidad administrativa y no debería mezclar con la edición de datos del perfil.

**Separación entre datos públicos y privados**: el endpoint `GET /personas` devuelve CV ciegos (sin datos de contacto), mientras que `GET /personas/{id}` devuelve el perfil completo. Esta diferencia refleja que distintos actores tienen distintos niveles de acceso.

**Unicidad en contactos activos**: al crear un `ContactoSolicitado`, el sistema verifica que no exista una solicitud activa previa entre la misma empresa y persona. Solo se permite una nueva solicitud si la anterior finalizó en `no-seleccionado` o `proceso-cerrado`.

### Adaptación a diferentes tipos de datos

El framework CRUD se adaptó a las tres entidades de formas distintas según su naturaleza:

| Entidad | Particularidades CRUD |
|---|---|
| `Persona` | Campos JSON para competencias, idiomas, áreas de experiencia. UUID como PK. Genera `codigo_talento` automático. Calcula `porcentaje_completitud` en escritura. |
| `Empresa` | Campos JSON para beneficios. Validación de unicidad de RUT y email. UUID como PK. |
| `ContactoSolicitado` | Entidad relacional con máquina de estados. Registra fechas automáticamente según transición de estado. Carga relaciones con eager loading. |

---

## Optimizaciones implementadas

**Eager loading para evitar N+1**: en el endpoint `GET /admin/contactos`, las relaciones con `empresa` y `persona` se cargan en una sola consulta adicional usando `with(['empresa', 'persona'])`, en lugar de generar una consulta por cada registro.

**Cálculo de completitud en escritura**: el `porcentaje_completitud` se calcula una sola vez al crear o actualizar, no en cada lectura. Esto reduce la carga de procesamiento en consultas frecuentes.

**Respuestas estandarizadas con Trait**: el `ApiResponse` trait centraliza el formato de todas las respuestas (éxito y error), asegurando consistencia sin repetir código en cada controlador.

**Campos JSON para datos variables**: se usaron campos JSON para almacenar colecciones de tamaño variable (competencias, idiomas, beneficios) en lugar de crear tablas adicionales. Laravel castea automáticamente estos campos a arrays en PHP.

---

## Depuración y correcciones realizadas

Durante el desarrollo se detectó una discrepancia entre las migraciones y los controladores: las migraciones definían UUID como identificador primario, pero los métodos del controlador y las firmas de las funciones estaban tipadas como `int`. Esto causaría errores al intentar buscar registros con UUID por un parámetro entero.

La corrección implicó:
- Cambiar las firmas de todos los métodos de controladores de `int $id` a `string $id`.
- Actualizar los parámetros OpenAPI de `type: integer` a `type: string, format: uuid`.
- Verificar que los modelos tuvieran `HasUuids`, `public $incrementing = false` y `protected $keyType = 'string'`.

Los comentarios en el código marcan cada punto corregido para trazabilidad.

---

## Funcionalidades principales

- CRUD completo de personas (con soft delete y validación administrativa).
- CRUD completo de empresas (con soft delete y validación administrativa).
- Validación de campos con reglas de Laravel (formato, unicidad, tipos enumerados).
- Registro de contactos solicitados entre empresas y personas.
- Máquina de estados para el seguimiento del proceso de contacto.
- Registro automático de fechas por transición de estado.
- Consulta de estadísticas generales del sistema.
- Endpoint de salud (`/api/health`) para verificar que la API responde.
- Documentación OpenAPI/Swagger generada con atributos PHP.

---

## Endpoints disponibles

### Salud

- `GET /api/health`

### Personas

| Método | Endpoint | Descripción |
|---|---|---|
| GET | `/api/personas` | Lista personas activas en formato CV ciego |
| POST | `/api/personas` | Registra una nueva persona |
| GET | `/api/personas/{id}` | Obtiene perfil completo por UUID |
| PUT/PATCH | `/api/personas/{id}` | Actualiza datos del perfil |
| DELETE | `/api/personas/{id}` | Desactiva el perfil (soft delete) |
| PATCH | `/api/personas/{id}/validar` | Marca como validada (acción administrativa) |

### Empresas

| Método | Endpoint | Descripción |
|---|---|---|
| GET | `/api/empresas` | Lista empresas activas |
| POST | `/api/empresas` | Registra una nueva empresa |
| GET | `/api/empresas/{id}` | Obtiene empresa por UUID |
| PUT/PATCH | `/api/empresas/{id}` | Actualiza datos de la empresa |
| DELETE | `/api/empresas/{id}` | Desactiva la empresa (soft delete) |
| PATCH | `/api/empresas/{id}/validar` | Marca como validada (acción administrativa) |

### Administración

| Método | Endpoint | Descripción |
|---|---|---|
| GET | `/api/admin/contactos` | Lista todos los contactos solicitados |
| POST | `/api/admin/contactos` | Registra una solicitud de contacto |
| PATCH | `/api/admin/contactos/{id}/estado` | Actualiza el estado del proceso |
| GET | `/api/admin/estadisticas` | Estadísticas generales de la plataforma |

---

## Ejemplos de uso

### Crear una persona

```http
POST /api/personas
Content-Type: application/json

{
  "email": "talento@ejemplo.com",
  "nivel_educacional": "universitaria",
  "titulo_carrera": "Ingeniería Informática",
  "anios_experiencia": 3,
  "competencias": ["Laravel", "Docker", "MySQL"],
  "tipo_jornada": "completa",
  "modalidad": "hibrido"
}
```

**Respuesta 201:**
```json
{
  "id": "uuid-generado",
  "codigo_talento": "PROV-2026-XKJM",
  "porcentaje_completitud": 64,
  ...
}
```

### Crear un contacto solicitado

```http
POST /api/admin/contactos
Content-Type: application/json

{
  "empresa_id": "uuid-empresa",
  "persona_id": "uuid-persona",
  "notas_admin": "Candidato con buen perfil para el área de backend."
}
```

### Actualizar estado de un contacto

```http
PATCH /api/admin/contactos/{id}/estado
Content-Type: application/json

{
  "estado": "entrevista",
  "notas_admin": "Entrevista agendada para el martes."
}
```

---

## Modelo de datos

La base de datos usa UUID como identificador principal en todas las tablas. Esto proporciona identificadores más seguros y difíciles de adivinar que un entero incremental clásico, además de facilitar la integración con sistemas externos.

Se usan campos JSON para almacenar información que puede variar en cantidad o forma: `competencias`, `idiomas`, `cursos`, `areas_experiencia` en Persona, y `beneficios` en Empresa. Laravel maneja automáticamente la serialización y deserialización mediante `$casts`.

---

## Flujo del sistema

1. Se registra una persona con su perfil profesional.
2. Se registra una empresa con sus datos de contacto.
3. La administración valida ambos registros.
4. La administración crea un contacto solicitado entre la empresa y la persona.
5. El estado del contacto avanza: `pendiente` → `contactado` → `entrevista` → `seleccionado` / `no-seleccionado` → `proceso-cerrado`.
6. Las fechas de contacto, entrevista y resultado se registran automáticamente al cambiar el estado.
7. El sistema expone estadísticas para obtener una vista general del proceso.

---

## Tecnologías usadas

- PHP 8.4
- Laravel 11
- MySQL 8
- Docker y Docker Compose
- Nginx
- l5-swagger (documentación OpenAPI/Swagger)

---

## Cómo ejecutar el proyecto

### Requisitos previos

Tener instalado Docker y Docker Compose.

### Configuración del entorno

Antes de levantar el proyecto, copia el archivo de ejemplo y ajusta las variables si es necesario:

```bash
cp backend/.env.example backend/.env
```

Las variables principales ya vienen configuradas para el entorno Docker:

```
DB_HOST=db
DB_DATABASE=proviemplea
DB_USERNAME=proviemplea_user
DB_PASSWORD=proviemplea_pass
APP_URL=http://localhost:8080
```

### Levantar el entorno con Docker

```bash
docker compose up -d --build
```

Luego instala dependencias, genera la clave de la aplicación y ejecuta las migraciones:

```bash
docker compose exec app composer install
docker compose exec app php artisan key:generate
docker compose exec app php artisan migrate
```

La aplicación quedará disponible en:

```
http://localhost:8080
```

La documentación Swagger estará disponible en:

```
http://localhost:8080/api/documentation
```

### Verificar que la API responde

```bash
curl http://localhost:8080/api/health
```
