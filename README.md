![img](https://avatars1.githubusercontent.com/u/5365410?s=75) Usuarios y Resultados REST API
======================================

[![MIT license](http://img.shields.io/badge/license-MIT-brightgreen.svg)](http://opensource.org/licenses/MIT)
[![Minimum PHP Version](https://img.shields.io/badge/php-%5E8.1-blue.svg)](http://php.net/)
[![PHPUnit Tests](https://github.com/FJavierGil/miw-api-usuarios/actions/workflows/php.yml/badge.svg)](https://github.com/FJavierGil/miw-api-usuarios/actions/workflows/php.yml)
> 馃幆 Implementaci贸n de una API REST con el framework Symfony para la gesti贸n de usuarios y resultados.

Esta aplicaci贸n implementa una interfaz de programaci贸n [REST][rest] desarrollada como ejemplo de
utilizaci贸n del framework [Symfony][symfony]. La aplicaci贸n proporciona las operaciones
habituales para la gesti贸n de entidades (usuarios y resultados). Este proyecto
utiliza varios componentes del framework Symfony, [JWT][jwt] (JSON Web Tokens), el _logger_ [Monolog][monolog]
y el [ORM Doctrine][doctrine].

Para hacer m谩s sencilla la gesti贸n de los datos se ha utilizado
el ORM [Doctrine][doctrine]. Doctrine 2 es un Object-Relational Mapper que proporciona
persistencia transparente para objetos PHP. Utiliza el patr贸n [Data Mapper][dataMapper]
con el objetivo de obtener un desacoplamiento completo entre la l贸gica de negocio y la
persistencia de los datos en los sistemas de gesti贸n de bases de datos.

Por otra parte se incluye parcialmente la especificaci贸n de la API (OpenAPI 3.0) . Esta
especificaci贸n se ha elaborado empleando el editor [Swagger][swagger]. Adicionalmente se
incluye la interfaz de usuario (SwaggerUI) de esta fenomenal herramienta que permite
realizar pruebas interactivas de manera completa y elegante.


## 馃殌 Instalaci贸n de la aplicaci贸n

El primer paso consiste en generar un esquema de base de datos vac铆o y un usuario/contrase帽a
con privilegios completos sobre dicho esquema.

A continuaci贸n se deber谩 crear una copia del fichero `./.env` y renombrarla
como `./.env.local`. Despu茅s se debe editar dicho fichero y modificar la variable `DATABASE_URL`
con los siguientes par谩metros:

* Nombre y contrase帽a del usuario generado anteriormente
* Nombre del esquema de bases de datos

Una vez editado el anterior fichero y desde el directorio ra铆z del proyecto se deben ejecutar los comandos:
```
$ composer update
$ php bin/console doctrine:schema:update --dump-sql --force
```
El proyecto base entregado incluye el componente [lexik/jwt-authentication-bundle][lexik] para
la generaci贸n de los t贸kens JWT. Siguiendo las instrucciones indicadas en la [documentaci贸n][1] de
dicho componente se deber谩n generar las claves SSH necesarias con los comandos:
```
$ mkdir -p config/jwt
$ openssl genpkey -out config/jwt/private.pem -aes256 -algorithm rsa -pkeyopt rsa_keygen_bits:4096
$ openssl pkey -in config/jwt/private.pem -out config/jwt/public.pem -pubout
```
En la instalaci贸n de XAMPP el programa *openssl* se encuentra en el directorio `XAMPP/apache/bin`. El
resto de la configuraci贸n ya se ha realizado en este proyecto. Como *pass phrase* se emplear谩 la
especificada en la variable `JWT_PASSPHRASE` en el fichero `.env`.

Para lanzar el servidor con la aplicaci贸n en desarrollo, desde la ra铆z del proyecto
se debe ejecutar el comando: 
```
$ symfony serve [-d]
```
Antes de probar la interfaz de la API es recomendable crear al menos un usuario con permisos de administrador.
Para conseguir este objetivo se ha proporcionado un comando disponible a trav茅s de la consola
de Symfony. La descripci贸n del funcionamiento de este comando puede obtenerse con:
```
$ php bin/console miw:create-user --help
```
A continuaci贸n ya se puede realizar una petici贸n con el navegador a la direcci贸n [https://127.0.0.1:8000/][lh]

## 馃梽锔? Estructura del proyecto:

El contenido y estructura del proyecto es:

* Directorio ra铆z del proyecto `.`:
    - `.env`: variables de entorno locales por defecto
    - `phpunit.xml.dist` configuraci贸n por defecto de la suite de pruebas
    - `README.md`: este fichero
* Directorio `bin`:
    - Ejecutables (*console* y *phpunit*)
* Directorio `src`:
    - Contiene el c贸digo fuente de la aplicaci贸n
    - Subdirectorio `src/Entity`: entidades PHP (incluyen anotaciones de mapeo del ORM)
* Directorio `var`:
    - Ficheros de log y cach茅 (diferenciando entornos).
* Directorio `public`:
    - `index.php` es el controlador frontal de la aplicaci贸n. Inicializa y lanza 
      el n煤cleo de la aplicaci贸n.
    - Subdirectorio `api-docs`: cliente [Swagger][swagger] y especificaci贸n de la API.
* Directorio `vendor`:
    - Componentes desarrollados por terceros (Symfony, Doctrine, JWT, Monolog, Dotenv, etc.)
* Directorio `tests`:
    - Conjunto de scripts para la ejecuci贸n de test con PHPUnit.

## 馃洜锔? Ejecuci贸n de pruebas

La aplicaci贸n incorpora un conjunto de herramientas para la ejecuci贸n de pruebas 
unitarias y de integraci贸n con [PHPUnit][phpunit]. Empleando este conjunto de herramientas
es posible comprobar de manera autom谩tica el correcto funcionamiento de la API completa
sin la necesidad de herramientas adicionales.

Para configurar el entorno de pruebas se debe crear un nuevo esquema de bases de datos vac铆o,
y una copia del fichero `./phpunit.xml.dist` y renombrarla como `./phpunit.xml`. De igual
forma se deber谩 crear una copia del fichero `./.env.test` y renombrarla como
`./.env.test.local`. Despu茅s se debe editar este 煤ltimo fichero para asignar los
siguientes par谩metros:
                                                                            
* Configuraci贸n del acceso a la nueva base de datos (variable `DATABASE_URL`)
* E-mail y contrase帽a de los usuarios que se van a emplear para realizar las pruebas (no
es necesario insertarlos, lo hace autom谩ticamente el m茅todo `setUpBeforeClass()`
de la clase `BaseTestCase`)

Para lanzar la suite de pruebas completa se debe ejecutar:
```
$ ./bin/phpunit [--testdox] [--coverage-text]
```
Adicionalmente, para comprobar la calidad de las pruebas, el proyecto incluye test de mutaciones
generados con la herramienta [Infection][infection].
El funcionamiento es simple: se generan peque帽os cambios en el c贸digo original (_mutantes_), y a continuaci贸n
se ejecuta la bater铆a de pruebas. Si las pruebas fallan, indica que han sido capaces de detectar la modificaci贸n
del c贸digo, y el mutante es eliminado. Si pasa las pruebas, el mutante sobrevive y la fiabilidad de la prueba
queda cuestionada.

Para lanzar los test de mutaciones se ejecutar谩:
```
> composer infection
```

Por 煤ltimo, tambi茅n se han a帽adido dos herramientas para el an谩lisis est谩tico de c贸digo, 
[PHPStan][phpstan] y [PhpMetrics][phpmetrics]. PhpStan es una herramienta de an谩lisis est谩tico de c贸digo, mientras que
PhpMetrics analiza el c贸digo y permite generar informes con diferentes m茅tricas de proyecto.
Estas herramientas pueden ejecutarse a trav茅s de los comandos:
```
> composer phpstan
> composer metrics
```

[dataMapper]: http://martinfowler.com/eaaCatalog/dataMapper.html
[doctrine]: http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/
[infection]: https://infection.github.io/guide/
[jwt]: https://jwt.io/
[lh]: https://127.0.0.1:8000/
[monolog]: https://github.com/Seldaek/monolog
[openapi]: https://www.openapis.org/
[phpunit]: http://phpunit.de/manual/current/en/index.html
[rest]: http://www.restapitutorial.com/
[symfony]: https://symfony.com/
[swagger]: http://swagger.io/
[yaml]: https://yaml.org/
[lexik]: https://github.com/lexik/LexikJWTAuthenticationBundle
[1]: https://github.com/lexik/LexikJWTAuthenticationBundle/blob/master/Resources/doc/index.md#generate-the-ssh-keys
[phpstan]: https://phpstan.org/
[phpmetrics]: https://phpmetrics.org/