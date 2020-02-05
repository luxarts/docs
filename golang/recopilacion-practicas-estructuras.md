# Golang Standards

- [Naming conventions](#naming-conventions)
- [Formateo de código con `gofmt` <a name="formateo-de-codigo-con-gofmt"></a>](#formateo-de-código-con-gofmt-a-nameformateo-de-codigo-con-gofmta)

- [Estructura básica de un proyecto](#estructura-básica-de-un-proyecto)
  - [`/cmd` <a name="/cmd"></a>](#cmd-a-namecmda)
  - [`/internal`](#internal)
  - [`/pkg`](#pkg)
  - [`/vendor`](#vendor)
  - [`/api`](#api)
  - [`/web`](#web)
  - [`/configs`](#configs)
  - [`/test`](#test)
  - [`/docs`](#docs)
  - [`/third_party`](#third_party)
  - [`/src`](#src)

- [Diferentes formas de estructurar apps](#diferentes-formas-de-estructurar-apps)

***

### Naming conventions

(Basado en el slide de Andrew Gerrand -Google- : https://talks.golang.org/2014/names.slide#1)

- Los nombres en GO usan `MixedCase` (no se usan nombre_con_underscores)
- Las variables deben usar un nombre corto (`i` para `index`, `r` para un `reader`)
- No nombrar las variables con su tipo (ej. `usersMap`, sólamente usar `users`)
- Evitar nombres redundantes, muchas veces repetidos en los nombres de las funciones (ej. conviene usar `contador` en lugar de `contadorLetras` como variable en una función que se llame `ContarLetras`)
- Usar `ok` a la hora de buscar un key en un mapa para verificar que existe. Idem con los type assertion para verificar que el tipo es el indicado.
- Los parámetros se comportan como las variables locales pero también funcionan como documentación. Cuando los tipos son descriptivos, los nombres de los parámetros deben ser cortos (ej. `func AddUser(u User)` en lugar de `func AddUser(user User)`). Cuando los tipos son ambiguos, los nombres pueden servir como documentación (ej. `func UpdateUsername(username string, userId int)`)
- Los nombres en los retornos deben ser usados en funciones que se exporten sólo para documentación (ej. `func ReadFile(w Writer, r Reader) (documento string, err error)`)
- Para los receivers el nombre tiene que ser consistente a lo largo de todos los métodos de ese tipo (el nombre de la variable debería ser igual) y, por lo general, debería ser uno o dos caracteres
- Las interfaces que especifican un sólo método suele agregarse la terminación 'er' a la misma (en inglés)
- A la hora de crear tipos de errores deberían llamarse TipoError (ej. `type InvalidFileError struct {}`), los valores en cambio tienen la forma ErrorFoo (ej. `var ErrInvalidFile = ...`)
- Los nombres de los packages deben poseer algún significado que demuestre su utilidad/función. Mantenerse alejado de `utils`, `commons`, etc.
- Evitar mayúsculas en packages ya que no todos los file systems son case sensitive (ej. FAT32)
- Evitar stuttering (tartamudeo). Esto se genera cuando un paquete que implemente alguna estructura pública con el mismo nombre (ej. estructura `User` en paquete `user`, lo cual termina generando tartamudeo al llamar `user.User`)
- Los acrónimos deben ir en mayúscula (ej. `ServeHTTP`)
- Ser consistente en el estilo a la hora de declarar variables. Ya que Golang permite varias formas de declarar una variable, intentar estandarizar una en un proyecto (aunque se sugiere que cuando se declara una variable pero no se inicializar usar el keyword `var` y `:=` para cuando se declare *e* inicialice,)

***

### Formateo de código con `gofmt` <a name="formateo-de-codigo-con-gofmt"></a>

Golang posee una herramienta interna, que se recomienda usar antes de commitear cambios, para el formateo de código con las convenciones esperadas en el lenguaje.
[`gofmt`](https://golang.org/cmd/gofmt/) se puede utilizar tanto en Goland (Tools -> Go Tools -> GO Fmt File/Project) o desde la consola `gofmt [flags] [file o path]`.

***

### Estructura básica de un proyecto

(Basado en https://github.com/golang-standards/project-layout)

#### `/cmd` <a name="/cmd"></a>

Esta carpeta **almacena los archivos `main.go` de cada aplicación**. El directorio de cada aplicación debe coincidir con el nombre del ejecutable que quieras obtener (`/cmd/aplicacion`).

Se recomienda **no poner demasiado código en este directorio**. Si tiene código que se puede importar a otros proyectos, entonces debería estar en `/pkg`. Si el código no es reutilizable o no se quiere importar, se debería poner en `/internal`

Para ver un ejemplo de diferentes aplicaciones en `/cmd`: https://github.com/Netflix/titus-executor/tree/master/cmd

#### `/internal`

Se utiliza a la hora de escribir **código que no va a ser reutilizado ni importado** (ya que es código que no puede ser visto afuera del root del proyecto). Es posible tener un proyecto en el cual sólo se use `/cmd` e `/internal`.

También se le puede agregar una extra estructuctura para separar código interno compartido y código interno no compartido.

Ejemplo: La aplicación `autenticador-usuarios`, que es parte del conjunto `mi-aplicacion`, puede contener su código en `/internal/autenticador-usuarios` donde se ubicará su código interno (código sólo de `autenticador-usuarios`). Mientras que en `/internal/pkg`, se puede colocar código compartido entre el conjunto de las aplicaciones de `mi-aplicacion`.

**El código dentro de internal no es compartido por Go, el mismo lenguaje fuerza esto.**

Proyecto que utiliza sólo `/cmd` e `/internal`: https://github.com/satellity/satellity

Proyecto que utiliza tanto `/cmd`e `/internal`, pero también `/pkg`: https://github.com/jaegertracing/jaeger


#### `/pkg`

Código que **puede ser compartido con otras aplicaciones**. Otros proyectos pueden importar estas librerías y se asume que estas funcionan correctamente (hay que tener especial **cuidado con esto**). 

Podría considerarse correcto la no utilización de esta carpeta.

Proyecto que utiliza sólo `/cmd` y `/pkg`: https://github.com/minio/minio

#### `/vendor`

Dependencias de la aplicación. No comitear esta carpeta si estás armando una librería.

#### `/api`

Configuración de Swagger/OpenAPI, rules, etc

Proyecto que utiliza `/api`: https://github.com/kubernetes/kubernetes/tree/master/api

#### `/web`

Elementos referidos a la web: templates, web assets, etc

#### `/configs`

Archivos de configuración

#### `/test`

Test externos y datos usados en tests. No hay una estructura definida para `/test`. 

Es una buena práctica tenes un subdirectorio para los datos utilizados en tests, ej. `/test/data`.

#### `/docs`

Documentación y archivos relacionados a la misma.

#### `/third_party`

Herramientas externas, forks y otras utilidades 3rd party (ej. Swagger)

#### `/scripts`

Utilizado para diferentes tipos scripts. Por ejemplo, un bash script para ejecutar cURL.


#### `/src`

**Esta carpeta no es necesaria y no debería crearse. No hay que confundirla con `/src` a nivel proyecto con el `/src` que usa Go como workspace.**

***

# Diferentes formas de estructurar apps

(Basado en `GopherCon 2018: Kat Zien - How Do You Structure Your Go Apps` - https://www.youtube.com/watch?v=oL6JBUk6tj0)

## Introducción

El texto se basa en una GopherCon del año 2018. El ejemplo base se centra en un servicio de reviews de cerveza.
Los casos de uso que debería contemplarse:

- Los usuarios pueden agregar una cerveza
- Los usuarios pueden agregar un review para una cerveza
- Los usuarios pueden listar las cervezas
- Los usuarios pueden listar los reviews para una cerveza en especial
- Hay una opción de guardar data en memoria o en un JSON
- Se debe poder agregar data de ejemplo

## Alternativa 1 - Flat structure

La primer estructura que presenta es `flat estructure`.

En el FS se vería de la siguiente manera:
```json
/
 |--- data.go: data de ejemplo
 |--- handlers.go: HTTP handlers
 |--- main.go: aplicación
 |--- model.go: modelos de cerveza y review
 |--- storage.go: define la funcionalidad del storage
 |--- storage_json.go: implementación del storage
 |--- storage_mem.go: implementación del storage
 ```

### Pros

 - Fácil de navegar
 - Buen comienzo para una aplicación, puede escalar a futuro
 - Funciona bien para pequeños proyectos o librerías
 - Funciona bien con la idiomática de Go (nice & simple)
 - **No hay posibilidad de dependencias cíclicas**

 ### Cons

 - No hay chances de "black-boxear", todo puede ser modificado por todo
 - No describe correctamente lo que hace la aplicación
 
 *Repositorio de ejemplo:* https://github.com/katzien/go-structure-examples/tree/master/flat


## Alternativa 2 - Group by function

 Comunmente conocida como `layered architecture`, la cual se separa en 3 capas diferentes:
 
 - UI / Presentation
 - Business logic
 - Dependencias + Infraestructura

```json
/
 |--- data.go
 |--- handlers
 |  |--- cervezas.go
 |  |--- reviews.go
 |--- main.go
 |--- models
 |  |--- cerveza.go
 |  |--- review.go
 |  |--- storage.go
 |--- storage
    |--- json.go
    |--- memory.go
```

### Pros

- Fácil para determinar dónde va cada cosa
- Fuerza el concepto de agrupar cosas relevantes en un mismo paquete

### Cons

- Si una variable se comparte en múltiples layers hay que saber determinar en qué lugar va a ser implementada o decidir si simplemente hay que duplicarla
- ¿Dónde se inicializan las cosas?
- No permite darle un contexto al modelo, por lo tanto sólo tendremos una sola definición. Ej. cuando se agrega una cerveza, usando la estructura de `cerveza` no deberíamos pasarle un Id, aunque la estructura lo tenga. Como usuario, es posible no saber que no sea necesario y, al ver que existe la propiedad, se genera la duda de si es necesario o no. 
- No da muchas más ideas que la flat structure sobre lo que hace la aplicación

*Repositorio de ejemplo:* https://github.com/katzien/go-structure-examples/tree/master/layered

***

## Alternativa 3 - Group by module

```json
/
 |--- cervezas
 |  |--- cerveza.go
 |  |--- handler.go
 |--- reviews
 |  |--- handler.go
 |  |--- review.go
 |--- main.go
 |--- storage
 |  |--- data.go
 |  |--- json.go
 |  |--- memory.go
 |  |--- storage.go
```

### Pros

- Se evita de forma más fácil la dependencia cíclica
- Se agrupa por lógica

### Cons

- Sigue siendo dificil de determinar la función de la aplicación
- Stuttering / tartamudeo, ya que el paquete `cervezas` tiene un struct `Cerveza`, lo cual genera `cerveza.Cerveza`

*Repositorio de ejemplo:* https://github.com/katzien/go-structure-examples/tree/master/modular

***

## Alternativa 4 - Group by context

Basado en "Implementing DDD" (Vaughn Vernon). Se basa en organizar las cosas de una forma más natural.

¿Qué es DDD y cómo funciona?

- Obliga a pensar en el dominio y el negocio antes de codear
- Cada contexto tiene su modelo con sus propios límites (ej. una aplicación que tiene un `usuario` como entidad, en el contexto de `venta` un `usuario` puede tener propiedades como `costoadquisicion`; pero en el contexto de `cx`, puede tener campos como `tiemporespuesta` o `ticketsabiertos`)

```json
/
 |--- agregar
 |  |--- endpoint.go
 |  |--- service.go
 |--- cervezas
 |  |--- cerveza.go
 |--- listado
 |  |--- endpoint.go
 |  |--- service.go
 |--- main.go
 |--- opinar
 |  |--- endpoint.go
 |  |--- service.go
 |--- reviews
 |  |--- review
 |--- storage
 |  |--- json.go
 |  |--- memory.go
 |  |--- type.go
```

### Pros

- Ayuda a entender el contexto y qué es cada cosa
- Evita las dependencias cíclicas

### Cons

- Es necesario comprender el contexto, lo cual no siempre sucede

*Repositorio de ejemplo:* https://github.com/katzien/go-structure-examples/tree/master/domain

***

## Alternativa 5 - Hexagonal architecture

Separa las partes del dominio (core domain, lógica del negocio) y las dependencias externas. En el centro de nuestra aplicación se encuentra la lógica del negocio. 

La capa intermedia se encarga unir la lógica de negocio con las dependencias más externas, como por ejemplo convertir un request en un lenguaje que la capa de negocio pueda interpretar (o validar los campos antes de pasarlos al core). La capa de dominio va a convertir este input en un output y, la capa intermedia, se va a encargar de transformarla en lo que la capa más externa espera recibir. 

La capa más externa trata a la aplicación como una caja negra, no conoce nada sobre cómo funciona la aplicación. Puede entender cómo conectarse a la base de datos, realizar un llamado hacia alguna API, etc.
El objetivo: permite cambiar una parte de la aplicación sin tener que hacer grandes cambios.

La regla de la arquitectura hexagonal es que las dependencias externas sólo pueden moverse hacia una capa interna del hexágono.
Las capas externas no pueden contener lógica de negocio.
En cambio, el dominio no puede hacer referencia de ninguna manera hacia el exterior, no puede conocer de implementaciones externas, etc; lo cual nos obliga a usar interfaces e IoC.


```json
|--- cmd
|   |--- servidor
|   |   |--- main.go
|--- pkg
|   |--- agregar
|   |   |--- cerveza.go
|   |   |--- service.go
|   |--- http
|   |   |--- rest
|   |   |   |--- handler.go
|   |--- listar
|   |   |--- cerveza.go
|   |   |--- review.go
|   |   |--- service.go
|   |--- opinar
|   |   |--- review.go
|   |   |--- service.go
|   |--- storage
|   |   |--- json
|   |   |   |--- cerveza.go
|   |   |   |--- repositorio.go
|   |   |   |--- review.go
|   |   |--- memory
|   |   |   |--- cerveza.go
|   |   |   |--- repositorio.go
|   |   |   |--- review.go
```

### Pros

- Permite escalar
- Permite intercambiar implementaciones sin problema alguno
- Cada modelo tiene su propio contexto
- Permite determinar el objetivo de la aplicación
- Reutilización de lógica de negocio en diferentes contextos

### Cons

- Para aplicaciones chicas puede considerarse demasiado

*Repositorio de ejemplo:* https://github.com/katzien/go-structure-examples/tree/master/domain-hex

*Repositorio de ejemplo 2* (**muy buen ejemplo**): https://github.com/AkbaraliShaikh/denti

## Video tutorial:

- [Building Hexagonal Microservices with Go - Part One](https://www.youtube.com/watch?v=rQnTtQZGpg8)

- [Building Hexagonal Microservices with Go - Part Two](https://www.youtube.com/watch?v=xUYDkiPdfWs)

- [Building Hexagonal Microservices with Go - Part Three](https://www.youtube.com/watch?v=QyBXz9SpPqE)

***

## Repositorios de ejemplo

- https://github.com/google/mtail
- https://github.com/google/ko - Buen ejemplo del uso de las carpetas mencionadas previamente
- https://github.com/kubernetes/kubernetes
- https://github.com/AkbaraliShaikh/denti/tree/master/pkg - Muy buen ejemplo de hexagonal architecture
- https://github.com/maclav3/cleanarch-hegaxon-demo
- https://github.com/iDevoid/stygis y https://medium.com/@iDevoid/stygis-golang-hexagonal-architecture-a2d89d01f84b
