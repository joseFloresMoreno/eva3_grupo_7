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

- PHP 8.2
- Laravel 11
- MySQL 8
- Docker y Docker Compose
- Nginx

## Como ejecutar el proyecto

### Opcion 1: con Docker

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

### Opcion 2: ejecucion local

Si prefieres correrlo sin Docker, instala dependencias y configura tu archivo `.env` con la base de datos local.

```bash
composer install
php artisan key:generate
php artisan migrate
php artisan serve
```

## Idea didactica para entenderlo mejor

Si lo piensas como un sistema de informacion, este proyecto es un ejemplo de como una aplicacion backend transforma reglas del mundo real en estructuras tecnicas:

- una persona deja de ser solo una ficha y pasa a ser un modelo con validaciones y estados,
- una empresa deja de ser solo un nombre y pasa a tener relaciones y control administrativo,
- un contacto deja de ser un evento informal y se convierte en una entidad rastreable con historial.

Eso es precisamente lo interesante de un backend bien disenado: convertir necesidades humanas en datos organizados, seguros y consultables.

## Observacion final

El proyecto esta orientado a servir como base academica para practicar conceptos de modelado, API REST, relaciones entre entidades, validacion de datos y administracion de procesos.
</p>

## About Laravel

Laravel is a web application framework with expressive, elegant syntax. We believe development must be an enjoyable and creative experience to be truly fulfilling. Laravel takes the pain out of development by easing common tasks used in many web projects, such as:

- [Simple, fast routing engine](https://laravel.com/docs/routing).
- [Powerful dependency injection container](https://laravel.com/docs/container).
- Multiple back-ends for [session](https://laravel.com/docs/session) and [cache](https://laravel.com/docs/cache) storage.
- Expressive, intuitive [database ORM](https://laravel.com/docs/eloquent).
- Database agnostic [schema migrations](https://laravel.com/docs/migrations).
- [Robust background job processing](https://laravel.com/docs/queues).
- [Real-time event broadcasting](https://laravel.com/docs/broadcasting).

Laravel is accessible, powerful, and provides tools required for large, robust applications.

## Learning Laravel

Laravel has the most extensive and thorough [documentation](https://laravel.com/docs) and video tutorial library of all modern web application frameworks, making it a breeze to get started with the framework.

You may also try the [Laravel Bootcamp](https://bootcamp.laravel.com), where you will be guided through building a modern Laravel application from scratch.

If you don't feel like reading, [Laracasts](https://laracasts.com) can help. Laracasts contains thousands of video tutorials on a range of topics including Laravel, modern PHP, unit testing, and JavaScript. Boost your skills by digging into our comprehensive video library.

## Laravel Sponsors

We would like to extend our thanks to the following sponsors for funding Laravel development. If you are interested in becoming a sponsor, please visit the [Laravel Partners program](https://partners.laravel.com).

### Premium Partners

- **[Vehikl](https://vehikl.com/)**
- **[Tighten Co.](https://tighten.co)**
- **[WebReinvent](https://webreinvent.com/)**
- **[Kirschbaum Development Group](https://kirschbaumdevelopment.com)**
- **[64 Robots](https://64robots.com)**
- **[Curotec](https://www.curotec.com/services/technologies/laravel/)**
- **[Cyber-Duck](https://cyber-duck.co.uk)**
- **[DevSquad](https://devsquad.com/hire-laravel-developers)**
- **[Jump24](https://jump24.co.uk)**
- **[Redberry](https://redberry.international/laravel/)**
- **[Active Logic](https://activelogic.com)**
- **[byte5](https://byte5.de)**
- **[OP.GG](https://op.gg)**

## Contributing

Thank you for considering contributing to the Laravel framework! The contribution guide can be found in the [Laravel documentation](https://laravel.com/docs/contributions).

## Code of Conduct

In order to ensure that the Laravel community is welcoming to all, please review and abide by the [Code of Conduct](https://laravel.com/docs/contributions#code-of-conduct).

## Security Vulnerabilities

If you discover a security vulnerability within Laravel, please send an e-mail to Taylor Otwell via [taylor@laravel.com](mailto:taylor@laravel.com). All security vulnerabilities will be promptly addressed.

## License

The Laravel framework is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).
