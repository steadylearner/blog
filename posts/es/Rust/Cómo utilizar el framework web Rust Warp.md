<!-- Closure - cierrre? -->
<!-- Rust shouldn't be translated to ferragem -->

[Steadylearner]: https://www.steadylearner.com
[Rust Website]: https://www.rust-lang.org/
[Learn Rust]: https://doc.rust-lang.org/book/

[Rust async]: https://github.com/steadylearner/Rust-Full-Stack/tree/master/async
[cargo edit]: https://github.com/killercup/cargo-edit
[React Rust]: https://github.com/steadylearner/Rust-Full-Stack/tree/master/React_Rust
[Rust Full Stack]: https://github.com/steadylearner/Rust-Full-Stack

[Warp]: https://github.com/seanmonstar/warp
[Warp official example]: https://github.com/seanmonstar/warp#example
[Warp examples]: https://github.com/steadylearner/Rust-Full-Stack/tree/master/warp
[Warp official examples]: https://github.com/seanmonstar/warp/tree/master/examples
[Warp documentation]: https://docs.rs/warp/0.2.1/warp/
[Warp database example]: https://github.com/steadylearner/Rust-Full-Stack/tree/master/warp/database/2.%20with_db_pool/src

[tokio]: https://github.com/tokio-rs/tokio
[async std]: https://github.com/async-rs/async-std

[Express]: https://expressjs.com

[FP in Rust]: https://github.com/JasonShin/fp-core.rs
[Reading Rust function signatures]: https://hoverbear.org/blog/reading-rust-function-signatures/
[Real world Tacit programming part 1]: https://medium.com/@jesterxl/real-world-uses-of-tacit-programming-part-1-of-2-f2a0c3f9e00c
[Real world Tacit programming part 2]: https://medium.com/@jesterxl/real-world-uses-of-tacit-programming-part-2-of-2-6422da10dc47
[Rust closures are hard]: https://stevedonovan.github.io/rustifications/2018/08/18/rust-closures-are-hard.html

[microservices_with_docker]: https://github.com/steadylearner/Rust-Full-Stack/tree/master/microservices_with_docker

<!-- / -->

<!-- Steadylearner Post -->

[How to install Rust]: https://www.steadylearner.com/blog/read/How-to-install-Rust
[How to use Rust Yew]: https://www.steadylearner.com/blog/read/How-to-use-Rust-Yew
[How to modulize your Rust Frontend]: https://www.steadylearner.com/blog/read/How-to-modulize-your-Rust-Frontend
[How to use Docker with Rust]: https://www.steadylearner.com/blog/read/How-to-use-Docker-with-Rust

<!-- / -->

<!-- Steadylearner Twitter and LinkedIn  -->

[Twitter]: https://twitter.com/steadylearner_p
[LinkedIn]: https://www.linkedin.com/in/steady-learner-3151b7164/
[GitHub]: https://github.com/steadylearner
[Donate]: https://www.paypal.com/donate/?token=MYCfCk47O9tUW-WzxzrampApJJ3hTEweiG3XrGS_04X3y6NH5lMG2CQ9ZMibZEEKKCfX_0&country.x=BR&locale.x=BR

<!--  -->

En este post, we'll learn how to use Rust [Warp]. Empezando por [el ejemplo oficial actual en GitHub][Warp official example]. Luego, vamos a aprender a modulizarlo.

[Si desea probar Warp más después de leer esta publicación, puede consultar esto.](https://github.com/steadylearner/Rust-Warp-Example)

Si ya estás familiarizado con Rust y quieres ahorrarte tiempo, por favor, simplemente clona el [Rust Full Stack] repository y consulta [Warp][Warp examples] y [el microservicio][microservices_with_docker] ejemplos allí.

```console
$git clone https://github.com/steadylearner/Rust-Full-Stack.git
```

Entonces, una vez dentro de la carpeta de **warp**, utiliza esto:

```console
$cd hello_world
$cargo run --release
```

Para construir una aplicación full stack con Rust, consulte [How to use Rust Yew] para la parte de Rust frontend. Puedes utilizar su Fetch API y solicitar datos de los servidores de Rust para renderizar sus páginas.


<br />

<h2 class="red-white">[Requisito previo]</h2>

1. [Cómo instalar el Rust]
2. [Documentación de Warp](Warp documentation) y [ejemplos oficiales de Warp]([Warp official examples])
3. [El artículo que te ayudará a encontrar mejor los Futuros, Servicios y Filtros](https://blog.twitter.com/engineering/en_us/a/2011/finagle-a-protocol-agnostic-rpc-system.html)
4. [FP in Rust]
5. [Leyendo las firmas de las funciones de Rust](Reading Rust function signatures)
6. [Programación tácita en el mundo real parte 1](Real world Tacit programming part 1) y [Programación tácita en el mundo real parte 2 ](Real world Tacit programming part 2)
7. [Rust closures are hard]
8. [Rust Future crate](https://docs.rs/futures), [Future by Example](https://docs.rs/future-by-example/0.1.0/future_by_example/) and [Rust future and_then](https://docs.rs/futures/0.3.1/futures/future/trait.TryFutureExt.html#method.and_then)

---

En primer lugar, debes instalar Rust, si aún no lo tienes. La entrada del blog [Cómo instalar Rust te ayudará](How to install Rust). 
Si es necesario, visita [el sitio web de Rust](Rust Website) para obtener más información.

Asumo que ya estás familiarizado con Rust. Si no lo estás, por favor, [aprende a usarlo primero][Learn Rust].

Antes de leer este post, espero que leas completamente [la documentación de Warp]([Warp documentation]) y [los ejemplos oficiales de Warp]([Warp official examples]).
Después de instalar la dependencia, utilice este comando:

```console
$cargo doc -p warp --open
```

Será útil pasar más tiempo leyendo [el ejemplo de enrutamiento del autor](https://github.com/seanmonstar/warp/blob/master/examples/routing.rs).

Fíjate que el autor utiliza Closures][Rust closures are hard] como API principal del framework. He puesto a tu disposición varias documentaciones para ayudarte a entender mejor su funcionamiento.

[Encuentra más información sobre and_then.](https://www.google.com/search?&q=rust+and_then)

[Para saber más sobre la programación asíncrona con Rust, consulta esto.][Rust async]

**Espero que los leas todos antes de terminar este post.**

<br />

<h2 class="blue">Índice de contenidos</h2>

1. Comience con el ejemplo oficial
2. Refactorizarlo
3. Hacer Rutas para filtrar las peticiones de los usuarios
4. Construir Handlers para responder a ellas
5. Enlazarlos para terminar la API con una macro
6. Probar su funcionamiento con CURL y Rust
7. **Conclusion**

---

La estructura de los proyectos de este post es opinable.

(I hope you also spend time to read [Express documentation.](https://expressjs.com/en/starter/installing.html))

Encontré que los proyectos Warp pueden ser organizados de manera similar a los [Express]. Asumiendo que ya estás familiarizado con los sistemas de módulos de Rust, esto no será difícil de hacer.

(Espero que también dediques tiempo a leer la documentación de [Express]((https://expressjs.com/en/starter/installing.html))).

<br />

## 1. Comience con el ejemplo oficial 

Haremos nuestra primera aplicación Warp usando el ejemplo oficial dado por su autor. Utilizaremos varios comandos y crearemos algunos archivos.

En primer lugar, utilice **$cargo new hello_world** para hacer un mínimo de Rust boilerplate. El cargo debe haber hecho el archivo **src/main.rs** y **Cargo.toml**.

Verifique que Rust está funcionando bien en su máquina usando $cargo c, si es nuevo en Rust.

A continuación, añade las dependencias a tu proyecto Rust pegándolas desde el autor a Cargo.toml. (Si estás familiarizado con JavaScript y NPM, puedes imaginar que esto es similar a package.json).

```toml
tokio = { version = "0.2", features = ["macros"] }
warp = "0.2.1"
```

Además, puedes considerar los comandos de edición de carga en su lugar.
Ahora, copia y pega esto en main.rs.

```rust
use warp::Filter; // 1.

#[tokio::main] // 2.
async fn main() {
    let hello = warp::path!("hello" / String) // 3.
        .map(|name| format!("Hello, {}!", name)); // 4.

    warp::serve(hello) // 5.
        .run(([0, 0, 0, 0], 8000)) // 6.
        .await;
}
```

Todo está listo. **Usa $cargo run --release**. Lee este post en lugar de esperar a que termine.

Cuando termine, puede usar el comando CURL que aparece a continuación para comprobar el punto final /hello/String.

```console
$curl 0.0.0.0:8000/hello/www.steadylearner.com // return "Hello, www.steadylearner.com"
```

Espero que tu primera aplicación Warp haya funcionado. El código utilizado aquí es muy sencillo. Pero, una pequeña ayuda para saber cómo funcionan será muy útil.

**1.** Si has leído su documentación, el autor dice lo siguiente.

```console
El bloque de construcción fundamental de la urdimbre es el filtro
que pueden combinarse y componerse para expresar requisitos ricos en peticiones.
```

Por lo tanto, se utiliza para este ejemplo mínimo para demostrar cómo funciona y lo verás más adelante también.

**2.** Warp utiliza [tokio] como su ejecutor de tareas asíncronas entre bastidores. No tendrás que preocuparte demasiado por ello para que el ejemplo funcione, salvo que "su alternativa es [async std] y no son tan compatibles actualmente".

Para aprender más sobre la programación asíncrona con Rust, por favor, lee toda [la documentación en la carpeta asíncrona][Rust async] en [Rust Full Stack].

**3.** Si usted es nuevo en el marco de Warp, puede ser difícil de averiguar lo que hace. Puede pensar en "hola" como una ruta estática y en String como una parte para mostrar su intención de recibir parámetros dinámicos. Debe ser de tipo String solamente.

Si está familiarizado con otro framework web como Express, compárelo con la parte req(requisitos). La diferencia es que usted expresa sólo lo que quiere permitir. A continuación, utilizar con la API proporcionada por Warp. Compárelo con routes/hello_route.rs más adelante.

**4.** Aquí se utiliza el cierre Rust. (Si está familiarizado con Express, compárelo con la parte res(respond).

Vea que podría usar la variable **nombre** aquí dentro de || para usar el parámetro String que permitimos antes. Después, podrías expresar lo que quieres hacer con esto:

```rust
format!("Hello, {}!", name)
```

Compárelo con **handlers/hello_handler.rs** más adelante.

Si eres nuevo en Rust, puede que te preguntes cómo funciona el cierre sin tener que escribir aquí la variable, los params, el valor de retorno y la firma de la función, etc.

Esto se debe a que el compilador de Rust infiere los tipos para ellos en lugar de usted. Si has leído la documentación sobre esto, será fácil averiguar cómo funcionan.

Con la ayuda de esto, la API de Warp se vuelve muy fácil con los cierres y será similar al uso de funciones de flecha en JavaScript.

Pero, a diferencia de JavaScript, no es fácil hacer que los cierres sean reutilizables en todo el proyecto. Por lo tanto, usaremos **funciones** y **macros** en su lugar.

5. Antes, hicimos un **hello** API y lo guardamos en la variable hello y aprendimos cómo se hacían. Deja que el Warp lo sirva con esto:

**6.** [Prefiero usar 0.0.0.0 en lugar de localhost y su alias 127.0.0.1 para acoplar aplicaciones](https://www.google.com/search?&q=why+use+0.0.0.0+instead+of+localhost) fácilmente para usar [Docker después facilmenete][How to use Docker with Rust].

Espero que esta breve explicación le haya servido de ayuda. Por favor, lea toda la documentación proporcionada como requisito previo.

<br />

## 2. Refactorizarlo

Anteriormente, logramos hacer funcionar el ejemplo oficial de Warp y aprendimos los detalles de cada parte.

Vamos a refactorizar el ejemplo usando funciones en lugar del cierre que se usa allí. Al final del proceso, todo el proyecto se verá así:

```console
├── Cargo.toml
├── src
│   ├── api
│   │   ├── hello.rs
│   │   └── mod.rs
│   ├── handlers
│   │   ├── hello_handler.rs
│   │   └── mod.rs
│   ├── main.rs
│   ├── routes
│   │   ├── hello_route.rs
│   │   └── mod.rs
│   └── tests
│       ├── hello_test.rs
│       └── mod.rs
```

Puedes ver que haremos varias carpetas como routes/, handlers/, api/, tests/ etc y archivos dentro de ellas.

Puedes ver un montón de **mod.rs** allí. Pero, el contenido de esto será muy simple con **pub mod hello_route etc**. Piensa en ellos sólo como ayudantes para exportar otros archivos dentro de cada carpeta.

Si me dejas explicarlo con más información a continuación, será

1. Rust quiere que seas específico con todo, exportando e importando los módulos también. Si encuentras su sistema de módulos difícil, primero investiga qué los representa. Será **mod.rs** en cada carpeta y **main.rs** o **lib.rs** en el directorio de nivel superior de tu proyecto Rust. 

2. **crate** representará a main.rs o lib.rs y funcionará como si se publicaran crate o algo similar a package en JavaScript. **self** se utilizará para representar a **main.rs** o **lib.rs** dentro de ellos mismos y utilizar los módulos importados en ellos. 

3. Es posible que quiera utilizar la palabra clave **crate** pero no funcionará porque ya se utiliza dentro de la sintaxis **extern crate somecrate**.

Si quieres más información sobre esto, por favor, consulta [Cómo modular tu Rust Frontend][How to modulize your Rust Frontend] o [Rust Yew frontend de ejemplo](https://github.com/steadylearner/Rust-Full-Stack/tree/master/web/src) o lee la documentación oficial de Rust para ello.

Así que, sin esos archivos mod.rs, lo que necesitamos será sólo esto:

```console
├── src
│   ├── api
│   │   ├── hello.rs
│   ├── handlers
│   │   ├── hello_handler.rs
│   ├── main.rs
│   ├── routes
│   │   ├── hello_route.rs
│   └── tests
│       ├── hello_test.rs
```

Primero, empezaremos con **main.rs**. Esto le ayudará a encontrar mejor la estructura de todo el proyecto.

```rust
use warp::{self, Filter};

use console::Style;

mod routes; // 1.
mod handlers; // 2.
mod api; // 3.

use self::{
    routes::{
        hello_route,
    },
    handlers::{
        hello_handler
    },
};

// It will only work with $cargo test
// For example, $cargo test hello -- --nocapture
#[cfg(test)] mod tests;

#[tokio::main]
async fn main() {
    let target: String = "0.0.0.0:8000".parse().unwrap();
    let blue = Style::new()
        .blue();

    // 4.
    let end = hello!().with(warp::log("hello"));

    println!("\nRust Warp Server ready at {}", blue.apply_to(&target));
    println!("Use $curl 0.0.0.0:8000/hello/www.steadylearner.com to test the end point.");

    warp::serve(end).run(([0, 0, 0, 0], 8000)).await;
}
```

Puedes ver que es muy sencillo porque todas las partes ya están moduladas.

Primero, incluimos la carpeta (submódulos) que haremos con la palabra clave **mod**. Usted puede reemplazar en su mente con esto.

```md
Carpeta de importación y los archivos de Rust de ella a lo largo en el mismo directorio. Ya debería haber hecho **mod.rs** para representar la carpeta y escribir **pub mod filename** para exportar los archivos de Rust allí, para satisfacer el compilador de Rust.
```

**1.** routes/ incluirá lo que quieres que el servidor acepte de la petición del usuario. Ya sabemos que sólo aceptaremos la ruta **"/hello/String"**. Puedes compararlo con la parte req de Express.

**2.** handlers/ incluirá lo que quieres que el servidor haga con él. Ya sabemos que devolveremos el html (texto) con **format!("Hello, {}!", nombre)** y la parte del nombre será **String** filtrado por **hello_route** que haremos.

Esta será la carga útil de su proyecto.

Más adelante, pasarás la mayor parte del tiempo con las bases de datos y los **models/** para utilizarlos. Consulte [el ejemplo de la base de datos][Warp database example] Warp si lo desea.

**3.** La macro **hello!** que vamos a hacer será utilizable en main.rs y tests/hello_test.rs importando la carpeta api con eso.

Esto es tal vez opcional. Pero, [le ayudará a no escribir el mismo código de abajo en tests/ folder](https://www.reddit.com/r/rust/comments/erx83j/warp_i_made_some_warp_web_framework_examples_i/) que vamos a hacer.

```rust
let hello = hello_route::hello()
    .and_then(hello_handler::hello)
```

Te preguntarás "¿Por qué usar macro aquí, cuando se pueden usar funciones, etc.?"

Esta fue la última solución, y quizás la más sencilla, que se me ocurrió. No fue fácil insertar el código porque algunos tipos utilizados son privados, inestables y no permitidos por el compilador.

[Consulte la carpeta de comparación antes de encontrar la mejor manera.](https://github.com/steadylearner/Rust-Full-Stack/tree/master/warp/hello_world/src/compare)

**4.** El nombre de la variable **end** se utiliza aquí porque se utilizará antes de servirla finalmente con **warp::serve**.

[Puede referirse a esto desde el ejemplo de la base de datos Warp.](Warp database example])

```rust
let post_api = list_posts!()
    .or(get_post!())
    .or(create_post!())
    .or(update_post!())
    .or(delete_post!());

let end = post_api.with(warp::log("post_api"));
```

Otras partes son sólo para ayudarte a depurar mejor la aplicación.

<br />

## 3. Rutas para filtrar las solicitudes de los usuarios 

Anteriormente, definimos rutas mod para importar archivos Rust (hello_route.rs) en routes en el main.rs. Pero, aún no lo hemos hecho.

Así que lo escribiremos con alguna explicación. Será similar a esto.

```rust
// It is equal to use
// let hello = path!("hello" / String);
// in main.rs file.

use warp::{
    filters::BoxedFilter,
    Filter,
};

// 1. "hello"
fn path_prefix() -> BoxedFilter<()> {
    warp::path("hello")
        .boxed()
}

// 2. / String
pub fn hello() -> BoxedFilter<(String, )> {
    warp::get() // 3.
        .and(path_prefix()) // 4.
        .and(warp::path::param::<String>()) // 5.
        .boxed()
}
```

**1.** En primer lugar, extraemos la parte del prefijo **hello** para hacerla reutilizable.

Esto no es tan significativo aquí. Pero, será útil cuando quieras [usar prefijos para REST api](https://github.com/steadylearner/Rust-Full-Stack/blob/master/microservices_with_docker/warp_client/src/routes/user_route.rs) como **api/post/v1**, **api/user/v1** etc.

Puedes pensar en lo que son **BoxedFilter<()>** y .**boxed()** aquí. Piensa en ellos como un pegamento que te ayudará a encadenar otros métodos cuando se usen dentro **and** la API de Warp más adelante en **2.**.

**2.** Esta será la carga útil de este archivo. Aquí se define lo que se quiere **filtrar** de las peticiones de los usuarios.

**3.** Sólo recibe (filtrar) las peticiones GET.
**4.** Estas comienzan con el prefijo /hello que hicimos antes.

**5.**  "Warp, acepta parametro sólo con tipo String".

Puedes ver que será el argumento de la función que haremos en **route_hanlder** más adelante.

También puede utilizar tipos personalizados como [Post, NewPost]((https://github.com/steadylearner/Rust-Full-Stack/blob/master/warp/database/2.%20with_db_pool/src/models/post.rs)).

El código utilizado aquí se explica por sí mismo. Pero, espero que estas explicaciones te hayan ayudado.

<br />

## 4. Construir Manejadores para responder a ellos

En la sección anterior, definimos la ruta para filtrar las peticiones de los usuarios. Vamos a definir cómo manejarlas creando **hello_handler.rs**.

Será similar a esto:

```rust
use warp;

pub async fn hello(name: String) -> Result<impl warp::Reply, warp::Rejection> {
    let reply = format!("Hello, {}!", name);
    // println!("{}", &reply);
    Ok(warp::reply::html(reply))
}
```

El código utilizado aquí es muy sencillo.

En primer lugar, el tipo de retorno de las funciones manejadoras casi siempre será este:

```rust
Result<impl warp::Reply, warp::Rejection>
```

Por lo tanto, sólo tiene que copiar y pegar después de leer la documentación de [Respuesta](https://docs.rs/warp/0.1.0/warp/reply/index.html) y [Rechazo](https://docs.rs/warp/0.1.0/warp/reject/struct.Rejection.html).

Debe tener cuidado de cómo manejar las partes de Rechazo. Pero, no es necesario para este sencillo ejemplo.

También puede ver que el parámetro **String** que filtramos en **hello_route.rs** se ha convertido en el parámetro de la función aquí.

Compárelo con lo que usamos antes en la primera parte

```rust
.map(|name| format!("Hello, {}!", name));
```

Es cierto que los Rust Closures son mucho más sencillos de usar y prototipar tu aplicación Warp. Utilízalos primero en main.rs. Cuando quieras modularla, sepáralos en routes y handlers.

<br />

## 5. Enlazarlos para finalizar la API con la macro 

Con routes/ y handlers/, la lógica de tu aplicación ya está completa. Establece la macro hello! dentro de api/hello.rs de forma similar a esta para enlazarlos:

```rust
#[macro_export]
macro_rules! hello {
    () => {
        hello_route::hello()
        .and_then(hello_handler::hello)
    }
}
```

Con #[macro_export], [las macros así definidas pasan a estar disponibles globalmente a nivel de crate.]((https://stackoverflow.com/questions/29068716/how-do-you-use-a-macro-from-inside-its-own-crate))

Sólo funcionarán cuando el módulo que incluye esté incluido en **main.rs** o **lib.rs** que representan tu proyecto Rust con mod api.

Puedes usar las macros de **api** dentro de **main.rs** o **lib.rs** sin usar esto.

```rust
use crate::{
    hello,
};
```

Si quieres usarlo en otras partes de tu proyecto como vemos en [tests/hello_test.rs](https://github.com/steadylearner/Rust-Full-Stack/blob/master/warp/hello_world/src/tests/hello_test.rs), debes incluir el código anterior para que funcione.

Todos los archivos para compilar tus primeros proyectos Warp están listos.

Primero, usa **$cargo c** para verificar si compila o no. Luego, usa **cargo run --release** para producción o cargo run para compilar rápido.

¡Espero que lo consigas!

<br />

## 6. Pruebe su funcionamiento con CURL y Rust 

Si has seguido bien las partes anteriores, tu consola ya debería haber mostrado esto tras la compilación de tu proyecto.

```console
Rust Warp Server ready at 0.0.0.0:8000
Use $curl 0.0.0.0:8000/hello/www.steadylearner.com to test the end point.
```

Primero, utilice `$curl 0.0.0.0:8000/hello/www.steadylearner.com`. Se mostrará el mismo resultado que puede ver en la primera parte.

CURL es muy útil para probar puntos finales, pero hay problemas.

**1.** En primer lugar, tenemos que tener el servidor Warp listo todo el tiempo. El compilador de Rust le quitará tiempo.
**2.** Luego, debes averiguar qué comandos CURL debes utilizar. Esto puede ser complicado.

Por lo tanto, haremos funciones de prueba de Rust para simplificar y automatizar el proceso. Construiremos **tests/hello_test.rs** equivalente al comando CURL que usamos.

```rust
use warp::Filter;

use crate::{
    handlers::hello_handler,
    routes::hello_route,
    hello,
};

// $cargo test -- --nocapture if you want to use println! etc.

// or test just one function each time.
// For example, $cargo test hello and it passes.

#[cfg(test)]
mod tests {
    use super::*;

    // 1.
    #[tokio::test]
    async fn hello() {
        let res = warp::test::request() // 2.
            .method("GET")
            .path("/hello/www.steadylearner.com")
            .reply(&hello!()) // 3.
            .await;

        // 4.
        assert_eq!(res.status(), 200, "Should return 200 OK.");
        // 5.
        println!("{:#?}", res.body());
    }
}
```

Los puntos importantes son estos:

**1.** Ya hemos aprendido que Warp utiliza [tokio] para manejar los cálculos asíncronos. Por lo tanto, el corredor de pruebas será también **#[tokio:test]**.

**2.** Construir una solicitud de cliente específica para probar antes de **.reply**. No será difícil averiguar lo que hacen porque los mismos pensamientos se utilizan de nuevo.

**3.** Definir cómo va a responder a la solicitud aquí. ¡Ya hemos hecho el **hello!** API para esto.

**4.** Ver que el servidor devuelve **OK** con ello.

**5.** Pruebe la función con **$cargo test hello**.

También puedes usar la bandera -- --nocapture para mostrar las partes de stdout. Por ejemplo, pruébalo de nuevo con $cargo test hello -- --nocapture.

Con CURL y un test con Rust, puedes verificar tu API fácilmente, siempre que modifiques los archivos en **routes/** y **handlers/**.

Si tienes curiosidad, también puedes probar el rendimiento con [loadtest](https://github.com/steadylearner/Rust-Full-Stack/blob/master/warp/hello_world/loadtest.md)..

No será lento y su uso de memoria es muy bajo (4,62 Mb en mi máquina 
Linux Ubuntu 18.04).

Prueba por tu cuenta con este comando en Linux mientras tu servidor Warp está listo en otra consola.

1. Para ver todos los usos de memoria en su sistema, utilice esto: 

```console
$ps -eo size,pid,user,command --sort -size | awk '{ hr=$1/1024; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' | cut -d "" -f2 | cut -d "-" -f1
```

2. También puede incluir **grep** y Linux pipe **|** para ver sólo la cantidad de memoria que necesita Warp. 

```console
$ps -eo size,pid,user,command --sort -size | awk '{ hr=$1/1024; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' | cut -d "" -f2 | cut -d "-" -f1 | grep "hello_world"
```

[Consúltalo para compararlo con otros frameworks web que utilices.](https://github.com/steadylearner/Rust-Full-Stack/tree/master/React_Rust#golang).

<br />

## 7. Conclusión 

Espero que todo haya funcionado y que hayas conseguido aprender a usar Warp con este post. Si quieres aprender más sobre él, puedes encontrar más ejemplos de Warp.

Puedes Dockerizarlo, usarlo con una app React de una sola página, [base de datos][Warp database example], desplegarlos en AWS, etc.

El post era sobre Warp, pero espero que los lectores también hayan aprendido cómo construir una aplicación web con Rust.

El desarrollo web con Rust no es todavía la corriente principal. De hecho, no fue fácil invertir tiempo en él y escribir para ayudar a otros, aunque es mucho más rápido que [la mayoría de otros frameworks web][React Rust].

Mantente al tanto de los últimos contenidos de Steadylearner: sígueme en [Twitter] y [GitHub].

You can also **star** [Rust Full Stack].

**Share with others to help them find the repository better and start programming Rust easily**.

