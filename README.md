<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

<p align="center">
</p>

## Proviemplea - API de vinculacion laboral

Este proyecto es una API desarrollada con Laravel para apoyar un escenario de intermediacion laboral entre personas y empresas. La idea principal es modelar un sistema donde:

- una persona registra su perfil profesional,
- una empresa publica su informacion y datos de contacto,
- y la administracion controla los contactos solicitados entre ambas partes.

Pensado en terminos de ingenieria de software, el sistema resuelve un problema clasico de gestion de datos y flujos de negocio: almacenar informacion estructurada, validar registros, relacionar entidades y exponer operaciones claras mediante endpoints REST.

## De que trata el proyecto

La aplicacion funciona como una pequena plataforma de gestion de talento. No se trata solo de guardar personas y empresas, sino de organizar el proceso completo de conexion entre ellas.

En este caso, el sistema maneja tres piezas principales:

1. Personas: perfiles de postulantes o talento disponible.
2. Empresas: organizaciones que buscan contactar candidatos.
3. Contactos solicitados: registros que representan el interes de una empresa por una persona.

La administracion puede revisar estos contactos, cambiar su estado y obtener estadisticas generales del sistema.

## Como esta organizado

El proyecto sigue una arquitectura tipica de Laravel separada por responsabilidades:

- `routes/api.php` define los endpoints.
- `app/Http/Controllers` contiene la logica de acceso y administracion.
- `app/Models` representa las entidades principales del negocio.
- `database/migrations` define la estructura de la base de datos.

Desde una perspectiva academica, esta separacion permite entender como una aplicacion real divide el problema en capas: ruta, control, dominio y persistencia.

## Entidades principales

### Persona

Representa a una persona con perfil profesional. Guarda datos de contacto, formacion, experiencia, competencias, modalidad de trabajo y otros atributos utiles para construir un CV ciego.

Un detalle interesante es que el modelo incluye un metodo llamado `getCvCiego()`, que devuelve solo la informacion profesional y omite los datos privados. Esto refleja una buena practica de diseno: exponer solo lo necesario segun el contexto.

### Empresa

Representa a una organizacion que publica su informacion institucional y de contacto. Incluye nombre, RUT, rubro, tipo de empresa, beneficios y datos del contacto responsable.

### ContactoSolicitado

Representa la relacion entre una empresa y una persona. No es solo un enlace tecnico: es un objeto de negocio que guarda el estado del proceso, notas administrativas y fechas importantes como contacto, entrevista y resultado.

## Funcionalidades principales

- CRUD de personas.
- CRUD de empresas.
- Validacion de personas y empresas.
- Registro de contactos solicitados entre empresas y personas.
- Cambio de estado de los contactos.
- Consulta de estadisticas generales.
- Endpoint de salud para verificar que la API responde.

## Endpoints disponibles

### Salud

- `GET /api/health`

### Personas

- `GET /api/personas`
- `POST /api/personas`
- `GET /api/personas/{persona}`
- `PUT/PATCH /api/personas/{persona}`
- `DELETE /api/personas/{persona}`
- `PATCH /api/personas/{persona}/validar`

### Empresas

- `GET /api/empresas`
- `POST /api/empresas`
- `GET /api/empresas/{empresa}`
- `PUT/PATCH /api/empresas/{empresa}`
- `DELETE /api/empresas/{empresa}`
- `PATCH /api/empresas/{empresa}/validar`

### Administracion

- `GET /api/admin/contactos`
- `POST /api/admin/contactos`
- `PATCH /api/admin/contactos/{contacto}/estado`
- `GET /api/admin/estadisticas`

## Modelo de datos

La base de datos usa UUID como identificador principal en las tablas mas importantes. Eso ayuda a tener identificadores mas seguros y dificiles de adivinar que un entero incremental clasico.

Tambien se usan campos JSON para guardar informacion que puede variar en cantidad o forma, como competencias, idiomas, beneficios o areas de experiencia. En terminos de diseno de datos, esto permite flexibilidad sin perder estructura general.

## Flujo del sistema

1. Se registra una persona con su perfil profesional.
2. Se registra una empresa con sus datos de contacto.
3. La administracion crea un contacto solicitado entre ambos registros.
4. El estado del contacto avanza por distintas etapas: pendiente, contactado, entrevista, seleccionado, no-seleccionado o proceso-cerrado.
5. El sistema permite consultar estadisticas para obtener una vista general del proceso.

## Tecnologias usadas

- PHP 8.4
- Laravel 11
- MySQL 8
- Docker y Docker Compose
- Nginx

## Como ejecutar el proyecto

### Docker

Esta opcion es la mas comoda si quieres levantar todo el entorno con un solo comando.

```bash
docker compose up -d --build
```

Luego debes instalar dependencias, generar la clave de la aplicacion y ejecutar migraciones dentro del contenedor de la app.

```bash
docker compose exec app composer install
docker compose exec app php artisan key:generate
docker compose exec app php artisan migrate
```

La aplicacion quedara disponible en:

```bash
http://localhost:8080
```

</p>

