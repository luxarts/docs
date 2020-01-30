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

***

<<<<<<< HEAD
### Naming conventions
=======
 ### Naming conventions
>>>>>>> 4b7052f262bb6466b61dcb39c770a48d7b52fd59

(Basado en el slide de Andrew Gerrand -Google-): https://talks.golang.org/2014/names.slide#1)

- Los nombres en GO usan `MixedCase` (no se usan nombre_con_underscores)
- Las variables deben usar un nombre corto (`i` para `index`, `r` para un `reader`)
- No nombrar las variables con su tipo (ej. `usersMap`, sólamente usar `users`)
- Evitar nombres redundantes, muchas veces repetidos en los nombres de las funciones (ej. conviene usar `contador` en lugar de `contadorLetras` como variable en una función que se llame `ContarLetras`)
- Usar `ok` a la hora de buscar un key en un mapa para verificar que existe. Idem con los casteos.
- Los parámetros se comportan como las variables locales pero también funcionan como documentación. Cuando los tipos son descriptivos, los nombres de los parámetros deben ser cortos (ej. `func AddUser(u User)` en lugar de `func AddUser(user User)`). Cuando los tipos son ambiguos, los nombres pueden servir como documentación (ej. `func UpdateUsername(username string, userId int)`)
- Los nombres en los retornos deben ser usados en funciones que se exporten sólo para documentación (ej. `func ReadFile(w Writer, r Reader) (documento string, err error)`)
- Para los receivers el nombre tiene que ser consistente a lo largo de todos los métodos de ese tipo (el nombre de la variable debería ser igual) y, por lo general, debería ser uno o dos caracteres
- Las interfaces que especifican un sólo método suele agregarse la terminación 'er' a la misma (en inglés)
- A la hora de crear tipos de errores deberían llamarse TipoError (ej. `type InvalidFileError struct {}`), los valores en cambio tienen la forma ErrorFoo (ej. `var ErrInvalidFile = ...`)
- Los nombres de los packages deben poseer algún significado que demuestre su utilidad/función. Mantenerse alejado de `utils`, `commons`, etc.
- Evitar mayúsculas en packages ya que no todos los file systems son case sensitive
- Evitar stuttering (tartamudeo). Esto se puede evitar haciendo un paquete que implemente alguna estructura pública con el mismo nombre (ej. estructura `User` en paquete `user`, lo cual termina generando tartamudeo al llamar `user.User`)
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
Esta carpeta almacena los archivos `main.go` de cada aplicación. El directorio de cada aplicación debe coincidir con el nombre del ejecutable que quieras obtener (`/cmd/aplicacion`).

Se recomienda no poner demasiado código en este directorio. Si tiene código que se puede importar a otros proyectos, entonces debería estar en `/pkg`. Si el código no es reutilizable o no se quiere importar, se debería poner en `/internal`

Para ver un ejemplo de diferentes aplicaciones en `/cmd`: https://github.com/Netflix/titus-executor/tree/master/cmd

#### `/internal`
Se utiliza a la hora de escribir código que no va a ser reutilizado ni importado (ya que es código que no puede ser visto afuera del root del proyecto). Es posible tener un proyecto en el cual sólo se use `/cmd` e `/internal`.

También se le puede agregar una extra estructuctura para separar código interno compartido y código interno no compartido.

Ejemplo: La aplicación `autenticador-usuarios`, que es parte del conjunto `mi-aplicacion`, puede contener su código en `/internal/autenticador-usuarios` donde se ubicará su código interno (código sólo de `autenticador-usuarios`). Mientras que en `/internal/pkg`, se puede colocar código compartido entre el conjunto de las aplicaciones de `mi-aplicacion`.

**El código dentro de internal no es compartido por Go, el mismo lenguaje fuerza esto.**

#### `/pkg`
Código que puede ser compartido con otras aplicaciones. Otros proyectos pueden importar estas librerías y se asume que estas funcionan correctamente (hay que tener especial **cuidado con esto**). 

Podría considerarse correcto la no utilización de esta carpeta.

#### `/vendor`

Dependencias de la aplicación. No comitear esta carpeta si estás armando una librería.

#### `/api`
Configuración de Swagger/OpenAPI, rules, etc

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

#### `/src`
**Esta carpeta no es necesaria. No hay que confundirla con `/src` a nivel proyecto con el `/src` que usa Go como workspace.**

***
