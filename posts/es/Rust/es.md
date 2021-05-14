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

In this post, we'll learn how to use Rust [Warp]. Starting from [the current official example at GitHub][Warp official example]. Then, we are going to learn how to modulize it.

[Si desea probar Warp más después de leer esta publicación, puede consultar esto.](https://github.com/steadylearner/Rust-Warp-Example)

If you are already familiar with Rust and want to save your time, please, just clone the [Rust Full Stack] repository and refer to [Warp][Warp examples] and [microservice example][microservices_with_docker] there.

```console
$git clone https://github.com/steadylearner/Rust-Full-Stack.git
```

Then, once inside **warp** folder use these:

```console
$cd hello_world
$cargo run --release
```

To build a Full Stack Rust app, refer to [How to use Rust Yew] for the Rust frontend part. You can use its Fetch API and request data from Rust servers to render its pages.

<br />

<h2 class="red-white">[Prerequisite]</h2>

1. [How to install Rust]
2. [Warp documentation] and [Warp official examples]
3. [The article to help you find Futures, Services and Filters better](https://blog.twitter.com/engineering/en_us/a/2011/finagle-a-protocol-agnostic-rpc-system.html)
4. [FP in Rust]
5. [Reading Rust function signatures]
6. [Real world Tacit programming part 1] and [Real world Tacit programming part 2]
7. [Rust closures are hard]
8. [Rust Future crate](https://docs.rs/futures), [Future by Example](https://docs.rs/future-by-example/0.1.0/future_by_example/) and [Rust future and_then](https://docs.rs/futures/0.3.1/futures/future/trait.TryFutureExt.html#method.and_then)

---

First, you must install Rust, if you haven't it yet. The blog post [How to install Rust] will help you. If necessary, visit [Rust Website] for more information.

I'll assume that you are already familiar with Rust. If you aren't, please, [learn how to use it first][Learn Rust].

Before reading this post, I hope you read [Warp documentation] and [Warp official examples] entirely. After installing the dependency, use this command:

```console
$cargo doc -p warp --open
```

It will be helpful to spend more time reading the [routing example from the author](https://github.com/seanmonstar/warp/blob/master/examples/routing.rs). Notice that the author uses [Closures][Rust closures are hard] as a main API of the framework. I've made several documentations available to help you better understand how they work.

[Find more about and_then.](https://www.google.com/search?&q=rust+and_then)

[To learn more about async programming with Rust, please refer to this.][Rust async]

**Hope you read all of them before you complete this post.**

<br />

<h2 class="blue">Table of Contents</h2>

1. Start with the official example
2. Refactor it
3. Make Routes to filter user requests
4. Build Handlers to respond to them
5. Link them to end the API with macro
6. Test it work with CURL and Rust
7. **Conclusion**

---

The project structure for this post is opinionated.

I found that Warp projects can be organized in a similar way to [Express] ones. Assuming you are already familiar with Rust module systems, this won't be difficult to do.

(I hope you also spend time to read [Express documentation.](https://expressjs.com/en/starter/installing.html))

You're about to learn how to modulize your Rust app along with [Warp]. After that, you will be able to organize your Rust projects easily.

Skip the first and second part if you're already familiar with Rust.

<br />

## 1. Start with the official example

We'll make **our first Warp app** using the official example given by its author. We'll use several commands and create some files.

First, use **$cargo new hello_world** to make a minimal Rust boilerplate. The **cargo** should have made **src/main.rs** and **Cargo.toml** file.

Verify that Rust is working well in your machine using **$cargo c**, if you are new to Rust.

Then, add dependencies to your Rust project by pasting it from the author to **Cargo.toml**. (If you are familar with **JavaScript** and **NPM**, you can imagine that this is similar to **package.json**).

```toml
tokio = { version = "0.2", features = ["macros"] }
warp = "0.2.1"
```

In addition, you can consider [cargo edit] commands instead.

Now, copy and paste this to **main.rs**.

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

Everything is ready. Use **$cargo run --release**. Just read this post instead of waiting for it to finish.

(It took about 17 minutes in my laptop, which is over half a decade old. I hope your machine is faster than that.)

When finished, you can use CURL command below to check the end point **/hello/String**.

```console
$curl 0.0.0.0:8000/hello/www.steadylearner.com // return "Hello, www.steadylearner.com"
```

I hope your first Warp app worked.

The code used here is very simple. But, a little help to find out how they work will be very useful.

**1.** If you've read its documentation, the author says the following.

```console
The fundamental building block of warp is the Filter
they can be combined and composed to express rich requirements on requests.
```

Therefore, it is used for this minimal example to demonstrate how it works and you'll see that later as well.

**2.** Warp uses [tokio] as its async task runner behind the scenes. You won't have to worry about it too much to make the example work except "its alternative is [async std] and they are not that compatible currently".

To learn more about async programming with Rust, please, read [all the documenations at async folder][Rust async] at [Rust Full Stack].

**3.** If you are new to Warp framework, it may be difficult to find out what it does. You can might think "hello" as a static path and **String** as a part to show your intention to receive dynamic parameters. It must be **String** type only.

If you are familar with other web framework such as **Express**, compare it to **req**(request) part.

The difference is that you express only what you want to allow. Then, use with the API provided by **Warp**. Compare it to **routes/hello_route.rs** later.

**4.** The Rust closure is used here. (If you are familar with **Express**, compare it to **res**(respond) part.)

See that you could use **name** variable here inside || to use **String** parameter we allowed before. Afterwards, you could express what you want to do with this:

```rust
format!("Hello, {}!", name)
```

Compare it to **handlers/hello_handler.rs** later.

If you are new to Rust, you may wonder how the closure works without typing the **variable, params, return value and function signature** etc here.

This is because the Rust compiler infers types for them instead of you. If you've read the documentations on this, it will be easy to find out how they work.

With the help of this, the Warp API becomes very easy with closures and will be similar to using arrow functions in JavaScript.

But, unlike JavaScript, it's not easy to make the closures reusable throughout the project. Therefore, we'll use **functions** and **macros** instead.

**5.** Before, we made a hello API and saved it to **hello** variable and learned how they were made. Let the **Warp** serve it with this:

**6.** [I prefer to use 0.0.0.0 instead of localhost and its alias 127.0.0.1](https://www.google.com/search?&q=why+use+0.0.0.0+instead+of+localhost) to [dockerize apps easily][How to use Docker with Rust].

I hope this brief explanation has helped you.

**Please, read all the documentation provided at prerequisite.**

<br />

## 2. Refactor it

Previously, we managed to make Warp's official example work and learned the details of each part.

We'll refactor the example using functions instead of the closure used there. At the end of the process, the entire project will look like this:

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

You can see that we will make several folders like routes/, handlers/, api/, tests/ etc and files inside them.

You can see a lot of **mod.rs** there. But, the contents of this will be very simple with **pub mod hello_route** etc. Think about them just as helpers to export other files inside each folder.

If you let me explain it with more information below, it will be 

1. Rust wants you to be specific with everything, exporting and importing the modules too. If you find its module system difficult, first research what represents them. It will be **mod.rs** in each folder and **main.rs** or **lib.rs** on the top level directory of your Rust project. 

2. **crate** will represent main.rs or lib.rs and will work as if they were published **crate** or **similar to package in JavaScript**. **self** will be used to represent **main.rs** or **lib.rs itself** inside themselves and use modules imported in them. 

3. You may want to use **crate** keyword but it won't work because it is already used inside **extern crate somecrate** syntax.

If you want more information about this, please refer to [How to modulize your Rust Frontend] or [Rust Yew frontend example](https://github.com/steadylearner/Rust-Full-Stack/tree/master/web/src) or read offical Rust documenation for it.

So, without those mod.rs files, what we need will be just these:

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

First, we'll start with **main.rs**. This will help you to find the structure of the entire project better.

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

You can see that it is very simple because all parts are already modulized.

First, we include folder (submodules) we will make with **mod** keyword. You can replace it in your mind with this.

```md
Import folder and the Rust files of it along in the same directory. I should have already done **mod.rs** to represent the folder and written **pub mod filename** to export Rust files there, to satisfy the Rust compiler.
```

**1.** routes/ will include what you want the server to accept from the user's request. We already know that we will only accept **"/hello/String** route. You can compare it with the **req part of Express**.

**2.** handlers/ will inlcude what you want server to do with it. We already know that we will return the html (text) with **format!("Hello, {}!", name)** and name part will be **String** filtered by **hello_route** we will make.

This will be the payload of your project.

Later, you'll spend most of time with databases and **models/** to use them. Refer to [Warp database example] if you want.

**3.** The macro **hello!** that we are going to do will be usable in main.rs and tests/hello_test.rs by importing **api** folder with that.

This is maybe optional. But, [it will help you not to write the same code below](https://www.reddit.com/r/rust/comments/erx83j/warp_i_made_some_warp_web_framework_examples_i/) in **tests/** folder we'll make.

```rust
let hello = hello_route::hello()
    .and_then(hello_handler::hello)
```

You may wonder "Why use macro here, when **functions**, etc can be used?"

This was the last, and perhaps the simplest solution I came up with. It wasn't easy to insert the code because some types used are **private**, **unstable** and **not allowed** by the compiler.

Consult [compare](https://github.com/steadylearner/Rust-Full-Stack/tree/master/warp/hello_world/src/compare) folder before you would finding the best way.

**4.** The variable name **end** is used here because it will be used before you finally serve it with **warp::serve**.

You can refer to this from [Warp database example].

```rust
let post_api = list_posts!()
    .or(get_post!())
    .or(create_post!())
    .or(update_post!())
    .or(delete_post!());

let end = post_api.with(warp::log("post_api"));
```

Other parts are just to help you debug the app better.

<br />

## 3. Routes to filter user requests

Previously, we defined **mod routes** to import Rust files (**hello_route.rs**) into **routes** in the main.rs. But, we haven't done that yet.

So we will write it with some explanation. It will be similar to this.

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

**1.** First, we extract **hello** prefix part to make it reusable.

This is not so meaningful here. But, it will be useful [when you want to use prefixes for REST api](https://github.com/steadylearner/Rust-Full-Stack/blob/master/microservices_with_docker/warp_client/src/routes/user_route.rs) like **api/post/v1**, **api/user/v1** etc.

You may think of what are **BoxedFilter<()>** and **.boxed()** here. Think of them as a glue to help you chain together other methods when they are used inside **and** API of Warp later in **2.**

**2.** This will be the payload of this file. You define what you want to **filter** (use) of the user requests here.

**3.** Only receive (filter) GET requests.
**4.** This starts with /hello prefix we did before.

**5.** "Warp, accept param with **String** type only."

You can see that it will be the argument of the function we will make in **route_handler** later.

[You can also use custom types such as Post, NewPost.](https://github.com/steadylearner/Rust-Full-Stack/blob/master/warp/database/2.%20with_db_pool/src/models/post.rs)

The code used here is self-explanatory. But, I hope these explanations have helped you.

<br />

## 4. Build Handlers to respond to them

In the previous section, we defined the route to filter user requests. We'll define how to handle them by creating **hello_handler.rs**

It will be similar to this:

```rust
use warp;

pub async fn hello(name: String) -> Result<impl warp::Reply, warp::Rejection> {
    let reply = format!("Hello, {}!", name);
    // println!("{}", &reply);
    Ok(warp::reply::html(reply))
}
```

The code used here is very simple.

First, the return type of handler functions will almost always be this:

```rust
Result<impl warp::Reply, warp::Rejection>
```

**Therefore, just copy and paste** after reading the documentation for [Reply](https://docs.rs/warp/0.1.0/warp/reply/index.html) and [Rejection](https://docs.rs/warp/0.1.0/warp/reject/struct.Rejection.html).

You must be careful for how to handle Rejection parts. But, it is not necessary for this simple example.

You can also see that the **String** parameter we filtered in **hello_route.rs** has became the function parameter here.

Compare it with what we used before in the first part.

```rust
.map(|name| format!("Hello, {}!", name));
```

It is true that **Rust Closures** are much simpler to use and prototype your Warp app. Use them first in **main.rs**. When you want to modulize it, separate them to **routes** and **handlers**.

<br />

## 5. Link them to end the API with macro

With **routes/** and **handlers/**, your application logic is already complete. Set hello! macro inside **api/hello.rs** similar to this to link them:

```rust
#[macro_export]
macro_rules! hello {
    () => {
        hello_route::hello()
        .and_then(hello_handler::hello)
    }
}
```

With **#[macro_export]**, [the macros defined this way become globally available at crate level.](https://stackoverflow.com/questions/29068716/how-do-you-use-a-macro-from-inside-its-own-crate).

They will only work when the module it is including is included at **main.rs** or **lib.rs** that represent your Rust project with **mod api**.

You can use the macros from **api** inside **main.rs** or **lib.rs** without using this.

[comment]: # (ou usar lib.rs sem macros?)

```rust
use crate::{
    hello,
};
```

If you want to use it in other parts of your project as we see in [tests/hello_test.rs](https://github.com/steadylearner/Rust-Full-Stack/blob/master/warp/hello_world/src/tests/hello_test.rs), you must include the code above for it to work.

All the files to compile your first Warp projects are ready.

First, use **$cargo c** to verify whether or not it will compile. Then, use **cargo run --release** for production or **cargo run** to compile fast.

I hope you can make it!

<br />

## 6. Test it work with CURL and Rust

If you followed the previous parts well, your console should have already shown these after your project compiled.

```console
Rust Warp Server ready at 0.0.0.0:8000
Use $curl 0.0.0.0:8000/hello/www.steadylearner.com to test the end point.
```

First, use **$curl 0.0.0.0:8000/hello/www.steadylearner.com**. It will show the same result you can see in the first part.

**CURL** is very useful for testing end points, but there are problems.

**1.** First, we have to get the Warp server ready all the time. The Rust compiler will take up your time.
**2.** Then, you should find out which CURL commands to use. This can be complicated.

Therefore, we'll do Rust test functions to simplify and automate the process. We'll build **tests/hello_test.rs** equivalent to the CURL command we used.

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

The important points are these:

**1.** We already learned that **Warp** uses [tokio] to handle async computations. Thus, the test runner will be also **#[tokio:test]**.

**2.** Build a specific client request to test before **.reply**. It won't be difficult to find out what they do because the same thoughts are used again.

**3.** Define how you will reply to the request here. We already made **hello!** API for this.

**4.** See the server return **OK** with it.

**5.** Test the function with **$cargo test hello**.

You can also use -- --nocapture flag to show **stdout** parts. For example, test it again with **$cargo test hello -- --nocapture**.

With CURL and a test with Rust, you can verify your API easily, whenever you modify files in **routes/** and **handlers/**.

If you're curious, you can also test performance with [loadtest](https://github.com/steadylearner/Rust-Full-Stack/blob/master/warp/hello_world/loadtest.md).

That won't be slow and its memory usage is very low.(**4.62 Mb** in my Linux Ubuntu 18.04 machine.)

Test on your own with this command on Linux while your Warp server is ready in another console.

1. To see all memory usages in your system, use this:

```console
$ps -eo size,pid,user,command --sort -size | awk '{ hr=$1/1024; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' | cut -d "" -f2 | cut -d "-" -f1
```

2. You can also include **grep** and Linux pipe **|** to see only how much memory Warp needs.

```console
$ps -eo size,pid,user,command --sort -size | awk '{ hr=$1/1024; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' | cut -d "" -f2 | cut -d "-" -f1 | grep "hello_world"
```

[Consult this to compare it with other web frameworks you use](https://github.com/steadylearner/Rust-Full-Stack/tree/master/React_Rust#golang).

<br />

## 7. Conclusion

I hope you made it all work and managed to learn how to use [Warp] with this post. If you want to learn more about it, you can find more [Warp examples].

[You can Dockerize it](https://www.steadylearner.com/blog/read/How-to-use-Docker-with-Rust), use it with a [React single page app][React Rust], [database][Warp database example], [deploy them to AWS][Steadylearner] etc.

The post was about [Warp], but I hope **you readers have also learned how to build a web app with Rust also**.

Web development with Rust is not mainstream yet. In fact, it wasn't easy to invest time in it again and write to help others, although it is [much faster than most other web frameworks][React Rust].

Stay on top of the latest Steadylearner content: follow me on [Twitter] and [GitHub].

You can also **star** [Rust Full Stack].

**Share with others to help them find the repository better and start programming Rust easily**.

